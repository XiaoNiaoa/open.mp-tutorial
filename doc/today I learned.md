**SA-MP Pawn 脚本知识文档**

本文档汇总了 SA-MP 论坛线程“Today I learned”（TID: 359953）全部 18 页的实用知识点。这些内容全部来自社区开发者分享，涵盖 Pawn 语言语法、性能优化、回调机制、字符串处理、枚举、位运算、预处理器、定时器、MySQL/SQLite、文本绘图、对话框等各个方面。每个知识点都附带清晰解释和实际代码示例，适合新手快速上手，也方便老手查阅技巧。

### 1. 公共函数的正确转发方式
在 Pawn 中，转发公共函数（用于回调或外部调用）时，必须写成 `forward public 函数名(参数);` 的形式。单纯的 `forward` 不会强制额外限制，而加上 `public` 后，参数就不能设置默认值，从而避免许多隐藏错误。

```pawn
forward public OnSomethingHappen(a, b, c);
public OnSomethingHappen(a, b, c)
{
    printf("事件触发，参数：%d %d %d", a, b, c);
}
```

### 2. 使用 # 代替字符串引号
Pawn 允许用 `#` 符号直接代表字符串字面量，尤其在 SetPVarInt、SetPVarString 等函数中非常方便，能省去引号转义。

```pawn
SetPVarInt(playerid, #MyVar, 42);  // 等价于 "MyVar"
```

### 3. 提取低位字节（位掩码）
要从 32 位 cell 中截取低 16 位或 8 位，只需用位与操作即可，常用于颜色、坐标打包。

```pawn
new cell = 0x12345678;
new low16 = cell & 0x0000FFFF;  // 0x5678
new low8  = cell & 0x000000FF;  // 0x78
```

### 4. 枚举允许尾随逗号
枚举定义最后一行可以加逗号，方便后续添加新成员，不会报错。

```pawn
enum {
    ABC,
    DEF,
    GHI,
}
```

### 5. 颜色嵌入宏定义
用宏定义颜色代码，字符串中直接拼接，消息颜色更干净。

```pawn
#define BLUE_EMBED "{0000FF}"
SendClientMessage(playerid, -1, "白色变成"BLUE_EMBED"蓝色。");
```

### 6. strcat 比 format 快很多
字符串拼接用 `strcat` 比 `format` 快约 2 倍，适合高频日志或对话框内容拼接。

```pawn
new str[128];
strcat(str, "Hello ");
strcat(str, "World!");
```

### 7. 在枚举中定义对话框 ID
把所有对话框 ID 统一放在枚举里，代码更清晰，便于管理。

```pawn
enum {
    DIALOG_LOGIN,
    DIALOG_REGISTER
};
ShowPlayerDialog(playerid, DIALOG_LOGIN, DIALOG_STYLE_INPUT, "登录", "请输入密码", "确定", "取消");
```

### 8. 使用 #emit 执行汇编级操作
`#emit` 可以直接操作 Pawn 虚拟机，常用于低级优化或内存操作（需谨慎使用）。

```pawn
#emit LOAD.S.pri 0   // 示例：加载栈中第一个参数
```

### 9. 枚举器的本质
枚举（enum）只是定义一组常量名称，并不占用实际内存空间，常用来做状态、ID、配置。

```pawn
enum PlayerStatus {
    STATUS_IDLE,
    STATUS_DRIVING
};
new PlayerStatus:status[MAX_PLAYERS];
```

### 10. OnGameModeExit 不一定被调用
关闭服务器控制台时 `OnGameModeExit` 不会触发，清理代码最好同时写在 `OnFilterScriptExit` 或手动处理。

### 11. 三元运算符中执行多条语句
用逗号运算符可以在三元里放多条语句，需用括号包裹。

```pawn
new cond = true;
cond ? (printf("真1"), printf("真2")) : (printf("假1"), printf("假2"));
```

### 12. 位移打包与提取颜色
SA-MP 颜色是 ARGB 32 位，可用位移快速打包和拆分。

```pawn
const color = (183 << 24) | (115 << 16) | (222 << 8);  // 紫色
new r = (color >> 24) & 0xFF;
new g = (color >> 16) & 0xFF;
new b = (color >> 8) & 0xFF;
```

### 13. #error 强制编译检查
用 `#error` 在编译时强制要求某些宏必须定义，常用于库依赖检查。

```pawn
#if !defined MAX_SLOTS
    #error 你必须在脚本中定义 MAX_SLOTS
#endif
```

### 14. 过滤脚本在 GM 重启时仍保持运行
过滤脚本里的 public 回调（如 OnPlayerText）在 `gmx` 重启游戏模式时不会被卸载，聊天等功能依然可用，实现无缝过渡。

### 15. 过滤脚本不会随服务器重启自动加载
服务器重启后过滤脚本必须手动用 `reloadfs` 命令加载。

### 16. 三元运算符简化条件赋值
一行代码完成 if-else 赋值，代码更简洁。

```pawn
SetPlayerColor(playerid, (team[playerid] == TEAM_RED) ? COLOR_RED : COLOR_BLUE);
```

### 17. 函数返回数组的内部机制
返回数组时实际上返回的是数组地址，存储在栈帧特定偏移处。

```pawn
stock GetArray()
{
    new arr[10] = {1, 2, 3};
    return arr;  // 返回地址
}
```

### 18. 枚举标签在 format 中使用
枚举值可以用标签转换后直接放入格式化字符串。

```pawn
enum Something { hi, bye }
printf("%s", Something:hi);
```

### 19. 枚举名称代表总大小
枚举名本身就是枚举中最后一个值的下一个值，可直接用于数组大小。

```pawn
enum { A, B, C }  // enum = 3
new arr[enum];
```

### 20. 链式比较语法
Pawn 支持 `0 < var < 5` 这种链式写法，等价于 `var > 0 && var < 5`。

```pawn
if (0 < var < 5) { ... }
```

### 21. 取余运算符 %
`%` 返回除法的余数，常用于判断奇偶、循环分页等。

```pawn
new rem = 9 % 4;  // rem = 1
```

### 22. GetPlayerPos 用数组更简洁
不用单独声明 X、Y、Z，直接用一个 Float 数组。

```pawn
new Float:pos[3];
GetPlayerPos(playerid, pos[0], pos[1], pos[2]);
```

### 23. forward + public 空函数可注册到 public 表
利用编译器特性，可用 `forward public Func(); Func(){}` 把函数加入 public 函数表。

### 24. stock 函数的用途
`stock` 定义的函数只有被调用时才会编译进脚本，适合做工具库。

```pawn
stock KillPlayer(playerid)
{
    SetPlayerHealth(playerid, 0.0);
}
```

### 25. forward 中指定返回数组大小
在 forward 中写 `[128]` 可强制函数必须返回指定大小的数组。

```pawn
forward [128] GetMessage(playerid);
stock GetMessage(playerid)
{
    new msg[128] = "Hello";
    return msg;
}
```

### 26. 传递不定大小数组时用 sizeof 默认参数
函数参数数组大小不确定时，加上默认 `= sizeof(array)` 即可使用 sizeof。

```pawn
stock ProcessArray(arr[], size = sizeof(arr))
{
    for(new i = 0; i < size; i++) { ... }
}
```

### 27. 类说明符组合规则
`public` 和 `static` 不能同时使用，`const` 可以和 `stock` 组合，但不能重复。

### 28. native 可指定任意负索引
`native Func() = -123;` 可为 native 分配自定义负索引（用于底层调用）。

### 29. 编译器自动包含 default.inc
所有脚本都会自动包含 include 目录下的 default.inc。

### 30. defined 关键字检查符号是否存在
`defined(symbol)` 在编译时判断变量、函数、宏是否存在。

```pawn
if(defined MyVar) { ... }
```

### 31. 循环内 static 比 new 更高效
循环里用 `static` 初始化变量比 `new` 更快，因为只分配一次。

### 32. while 和 for 循环性能相同
`while(cond)` 和 `for(;cond;)` 编译后指令完全一样，速度一致。

### 33. 数组初始化可省略行数
二维数组初始化时可用 `new Var[][128]`，编译器自动推断行数。

```pawn
new RandomVar[][128] = { "字符串1", "字符串2" };
```

### 34. sscanf 可选字符串参数要加 ()
可选字符串参数必须写成 `S[24]()` 形式，否则控制台会警告。

```pawn
sscanf(params, "s[24]S[24]()", required, optional);
```

### 35. foreach 区分大小写
`foreach (new i : Player)` 必须小写 `player`，否则报错。

### 36. GetPlayerPos 支持数组参数
直接传数组比单独变量更整洁。

### 37. 命名参数调用
函数调用时可以用 `.参数名 = 值` 指定参数，顺序不重要。

```pawn
Teleport(playerid, .x = 100.0, .y = 200.0, .z = 10.0);
```

### 38. 数值字面量可用下划线分隔
提高可读性：`1_000_000`、`0x_DEAD_BEEF`。

### 39. 字符范围检查用链式比较
判断字符是否为数字：`'0' <= ch <= '9'`。

### 40. loadfs 支持任意相对路径
可以用 `loadfs ../文件夹/脚本` 加载任意位置的过滤脚本。

### 41. GetPlayerFPS 需要自己实现
没有原生 GetPlayerFPS，必须用醉酒等级等方法自定义。

### 42. sscanf 一次解析多个参数
不要多次调用 sscanf，直接用 `"dd"` 格式一次解析多个整数。

```pawn
sscanf(params, "dd", id, hour);
```

### 43. 宏中用 \32; 处理空格
在宏参数里用 `\32;` 可以检测并去除多余空格，常用于 foreach 等高级宏。

### 44. 大数组直接初始化比循环快
```pawn
new bigArray[1024] = { 0, ... };
```

### 45. 向下 for 循环注意边界
向下循环推荐用 `--i != 0` 避免 off-by-one 错误。

```pawn
for(new i = 10; --i != 0; ) { ... }  // 从 9 到 1
```

### 46. 用 ~strfind 判断找到字符串
`~strfind(...)` 如果找到（非 -1）则为真，代码更短。

```pawn
if(~strfind(text, "hello")) { ... }
```

### 47. gmx 时玩家不会真正断开连接
玩家在 `gmx` 后仍保持连接，OnPlayerDisconnect 不会触发。

### 48. 字符串字面量不要直接修改
修改字符串字面量会导致越界，改写其他常量，务必复制到新数组再操作。

### 49. 函数参数默认值可忽略引用参数
引用参数也能给默认值，不传时自动使用默认。

```pawn
stock DoSomething(&Float:x = 0.0) { x = 5.0; }
DoSomething();  // 忽略引用参数
```

### 50. switch-case 比长 if-else 链更清晰
```pawn
switch(skill)
{
    case 1: return 35;
    case 2: return 100;
}
```

### 51. strtok 的含义与用法
strtok 用于分割字符串，第二个参数是索引，用于连续取 token。

### 52. 宏跨行用反斜杠
```pawn
#define add(%0,%1) \
    ((%0)+(%1))
```

### 53. printf 格式化可变宽度与精度
用 `*` 实现动态宽度和精度：

```pawn
printf("%*.*f", width, precision, value);
```

### 54. 随机函数 min==max 时行为特殊
`random(0)` 会返回异常值，务必处理 min >= max 的情况。

### 55. 用 SVar 快速检测重复 IP
把 IP 作为 SVar 键值，SetSVarInt 即可快速判断是否重复。

### 56. 枚举跨文件冲突解决方法
用偏移（如 `D_XXX = 100`）或把所有枚举放到同一个 include 文件中。

### 57. 数据库去重用 VALUES()
MySQL ON DUPLICATE KEY UPDATE 中用 `VALUES(列名)` 避免重复写值。

### 58. 编译器允许数字紧挨符号
`0xFF`、`0b1010` 等可以紧接宏名，无需空格。

### 59. #pragma tabsize 可被绕过
`#undef tabsize` 后再 `#pragma tabsize 0` 即可强制无缩进警告。

### 60. 距离计算可省略开方
判断范围内时用 `dist*dist < range*range` 更快。

这些知识点基本覆盖了整个 18 页线程的核心内容。如果你需要某个知识点的更深入扩展、完整示例脚本，或按主题（字符串、性能、MySQL、枚举等）重新整理，请随时告诉我，我可以继续完善这份文档！
