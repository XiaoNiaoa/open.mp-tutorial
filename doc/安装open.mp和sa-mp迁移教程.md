**本教程面向那些希望将游戏模式从 SA:MP 服务器迁移到 open.mp 服务器的用户。**

:::tip

**FCNPC 用户有个好消息！** 原有的 FCNPC 插件已被官方的 **open.mp NPC 组件** 所取代，该组件提供了相同的功能，但性能更佳且集成度更高。只需将您的代码转换为内置的 NPC 组件即可。

:::

## 第一步

从 [https://github.com/openmultiplayer/open.mp/releases](https://github.com/openmultiplayer/open.mp/releases/latest) 下载最新版本的 open.mp 服务器文件。

![](<https://raw.githubusercontent.com/adib-yg/openmp-server-installation/refs/heads/main/screenshots/Screenshot%20(1).png>)

- `open.mp-win-x86.zip` **Windows** 服务器
- `open.mp-linux-x86.tar.gz` **Linux** 服务器
- `open.mp-linux-x86-dynssl.tar.gz` **Linux** 服务器（动态 SSL）

## 第二步

将 `.zip` 或 `.tar.gz` 压缩包内容解压到您的磁盘上。

![](<https://raw.githubusercontent.com/adib-yg/openmp-server-installation/refs/heads/main/screenshots/Screenshot%20(3).png>)

:::note

- **components:** open.mp 组件
- **filterscripts:** 您的服务器脚本文件（辅助脚本）
- **gamemodes:** 您的服务器游戏模式文件（主脚本）
- **models:** 您的服务器自定义模型（纹理 .txd .dff）
- **plugins:** 您的服务器插件文件（旧版插件）
- **qawno:** Pawn 编辑器程序及您的服务器包含文件
- **scriptfiles:** INI 文件或其他资料。
- **bans.json:** 封禁列表文件
- **config.json:** 服务器配置文件
- **omp-server.exe:** open.mp 服务器程序
- **omp-server.pdb:** open.mp 服务器调试文件

:::

## 第三步

将您的游戏模式 `.pwn` 文件放入 **gamemodes** 文件夹。

## 第四步

将所需的包含文件（例如 `sscanf2.inc`, `streamer.inc`）放入 **qawno/include** 文件夹。

:::note

如果您在游戏模式中使用了 YSI-4 包含文件，请更新至 [YSI-5.x](https://github.com/pawn-lang/YSI-Includes/releases)。

:::

## 第五步

将所需的插件（例如 `sscanf.dll`, `streamer.dll`）放入 **plugins** 文件夹。

<hr />

:::warning

如果您使用下表中的插件，则必须放置与 omp 兼容的插件版本！

:::

| 插件               | OMP                                                                       |
| ------------------ | ------------------------------------------------------------------------- |
| rustext            | https://github.com/ziggi/rustext/releases/tag/v2.0.11 (nomemhack)         |
| keylistener        | https://github.com/edgyaf/keylistener/releases/tag/1.1.2-pr               |

:::warning

将以下插件放入 **../components** 文件夹，而不是 **../plugins** 文件夹！

:::

| 插件               | OMP                                                                                 |
| ------------------ | ----------------------------------------------------------------------------------- |
| Pawn.CMD           | https://github.com/katursis/Pawn.CMD/releases/tag/3.4.0-omp                         |
| Pawn.RakNet        | https://github.com/katursis/Pawn.RakNet/releases/tag/1.6.0-omp                      |
| sampvoice          | https://github.com/AmyrAhmady/sampvoice/releases/tag/v3.1.5-omp                     |
| discord-connector  | https://github.com/maddinat0r/samp-discord-connector/releases/tag/v0.3.6-pre        |
| sscanf             | https://github.com/Y-Less/sscanf/releases/tag/v2.13.8                               |
| SKY                | 请改用 Pawn.RakNet                                                                   |
| YSF                | 您不再需要 YSF，因为 open.mp 已经声明了大多数相同的原生函数。                       |
| FCNPC              | 已被官方的 open.mp NPC 组件取代。                                                   |

## 第六步

打开位于 **Server/qawno/qawno.exe** 的 qawno IDE 程序。

![](<https://raw.githubusercontent.com/adib-yg/openmp-server-installation/refs/heads/main/screenshots/Screenshot%20(5).png>)

## 第七步

按 **CTRL + O**，然后进入 **../gamemodes** 文件夹，打开您的游戏模式 `.pwn` 文件。

## 第八步

找到

```pawn
#include <a_samp>
```

替换为

```pawn
#include <open.mp>
```

然后按 **F5** 进行编译。

## 第九步

使用记事本或其他 IDE 打开 **[config.json](https://www.open.mp/docs/server/config.json)** 文件。

![](<https://raw.githubusercontent.com/adib-yg/openmp-server-installation/refs/heads/main/screenshots/Screenshot%20(9).png>)

## 第十步

编辑 **config.json**。

![](<https://raw.githubusercontent.com/adib-yg/openmp-server-installation/refs/heads/main/screenshots/Screenshot%20(11).png>)

找到

```json
"main_scripts": [
    "gungame 1"
],
```

替换为

```json
"main_scripts": [
    "您的游戏模式_amx文件名 1"
],
```

<hr />

找到

```json
"legacy_plugins": [],
```

指定所需的插件

```json
"legacy_plugins": [
    "crashdetect",
    "mysql",
    "sscanf",
    "streamer",
    "PawnPlus",
    "pawn-memory"
],
```

<hr />

找到

```json
"side_scripts": []
```

指定您的滤镜脚本

```json
"side_scripts": [
    "filterscripts/文件名称"
]
```

<hr />

找到

```json
"rcon": {
    "allow_teleport": false,
    "enable": false,
    "password": "changeme1"
},
```

为 rcon 密码输入强密码：

```json
"rcon": {
    "allow_teleport": false,
    "enable": false,
    "password": "151sd80hgse32q1oi0v8dsge166"
},
```

按 **CTRL + S** 保存更改。

:::tip

有关如何将 `server.cfg` 转换为 `config.json` 的指南，请访问 https://www.open.mp/docs/server/config.json

:::

## 第十一步

运行服务器。

- **Windows**

打开 `omp-server.exe` 程序。

![](<https://raw.githubusercontent.com/adib-yg/openmp-server-installation/refs/heads/main/screenshots/Screenshot%20(10).png>)

- **Linux**

```bash
./omp-server
```

## 编译器错误和警告

- **warning 213: tag mismatch: expected tag "?", but found none ("_")**：

例如：

```pawn
TogglePlayerControllable(playerid, 1);
// ->
TogglePlayerControllable(playerid, true);
```

<br />

```pawn
TextDrawFont(textid, 1);
// ->
TextDrawFont(textid, TEXT_DRAW_FONT_1);
```

<br />

```pawn
GivePlayerWeapon(playerid, 4, 1);
// ->
GivePlayerWeapon(playerid, WEAPON_KNIFE, 1);
```

但您可以暂时忽略它：

```pawn
#define NO_TAGS
#include <open.mp>

// 如果警告仍然存在
// 使用 #pragma warning disable 213
```

<br />

<hr />

- **warning 234: function is deprecated (symbol "TextDrawColor") Use `TextDrawColour`**

在 qawno 中按 **CTRL + F**，将所有 `TextDrawColor` 替换为 `TextDrawColour`。

![](<https://raw.githubusercontent.com/adib-yg/openmp-server-installation/refs/heads/main/screenshots/Screenshot%20(7).png>)

<br />

或者，如果您愿意，可以使用混合拼写：

```pawn
#define MIXED_SPELLINGS
#include <open.mp>
```

<br />

<hr />

- **warning 234: function is deprecated (symbol "GetPlayerPoolSize") This function is broken.**
- **warning 234: function is deprecated (symbol "GetVehiclePoolSize") This function is broken.**
- **warning 234: function is deprecated (symbol "GetActorPoolSize") This function is broken.**

将 `GetPlayerPoolSize()` 替换为 `MAX_PLAYERS`

将 `GetVehiclePoolSize()` 替换为 `MAX_VEHICLES`

将 `GetActorPoolSize()` 替换为 `MAX_ACTORS`

<hr />

- **warning 234: function is deprecated (symbol "SHA256_PassHash") Use BCrypt for hashing passwords**

使用 [samp-bcrypt](https://github.com/Sreyas-Sreelal/samp-bcrypt) 插件进行密码哈希。SHA-256 不安全。

<hr />

- **warning 214: possibly a "const" array argument was intended: "?"**
- **warning 239: literal array/string passed to a non-const parameter**

例如：

```pawn
public MyFunction(string[])
// ->
public MyFunction(const string[])
```

<br />

<hr />

- **error 025: function heading differs from prototype**

例如：

```pawn
public OnPlayerDeath(playerid, killerid, reason)
// ->
public OnPlayerDeath(playerid, killerid, WEAPON:reason)
```

<br />

```pawn
public OnPlayerEditAttachedObject(playerid, response, index, modelid, boneid, Float:fOffsetX, Float:fOffsetY, Float:fOffsetZ, Float:fRotX, Float:fRotY, Float:fRotZ, Float:fScaleX, Float:fScaleY, Float:fScaleZ)
// ->
public OnPlayerEditAttachedObject(playerid, EDIT_RESPONSE:response, index, modelid, boneid, Float:fOffsetX, Float:fOffsetY, Float:fOffsetZ, Float:fRotX, Float:fRotY, Float:fRotZ, Float:fScaleX, Float:fScaleY, Float:fScaleZ)
```

<br />

<hr />

:::note

还有一个升级工具，它会尝试查找旧的、无标签和 const 不正确的代码并对其进行升级。

https://github.com/openmultiplayer/upgrade

已包含在 `/qawno/upgrader` 文件夹中。

:::

## 运行时错误和警告

```
[Info] Couldn't announce legacy network to open.mp list.
[Info] [Server Error] Status: 406
[Info] [Server Error] Message: {"error":"failed to query server: socket read timed out"}
[Info] This won't affect the server's behaviour.
```

- 您的服务器无法从 open.mp 网站访问。
- 您可能正在本地运行服务器。
- 防火墙阻止了连接。

**此警告不会影响服务器的行为。**

<br />

<hr />

```
[Warning] Insufficient specifiers given to `format`: "?" < 1
```

说明符少于您在 format 中传递的参数数量。例如：

```pawn
new string[32];
new mp[32] = ".MP";

format(string, sizeof(string), "OPEN", mp);
// [Warning] Insufficient specifiers given to `format`: "OPEN" < 1

// 应该是：
format(string, sizeof(string), "OPEN%s", mp);
//                                  ^^
```

## 有用的文档

查看新的脚本函数和回调：https://www.open.mp/docs/server/omp-functions

如果您是 Pawn 编程的完全新手：[readme-beginner](https://github.com/openmultiplayer/omp-stdlib/blob/master/documentation/readme-beginner.md)

如果您是 Pawn 编程的中级用户：[readme-intermediate](https://github.com/openmultiplayer/omp-stdlib/blob/master/documentation/readme-intermediate.md)

如果您是 Pawn 编程的专家：[readme-expert](https://github.com/openmultiplayer/omp-stdlib/blob/master/documentation/readme-expert.md)

博客文章：[移植到 open.mp](https://www.open.mp/blog/porting)

## 帮助

如果您在运行服务器时仍有问题，请加入官方的 open.mp Discord 服务器：https://discord.gg/samp

在 [#openmp-support](https://discord.com/channels/231799104731217931/966398440051445790) 频道提问。
