[教程] 如何创建一个SA-MP插件
================================

> 来源：SA-MP 论坛
> 原帖链接：https://sampforum.blast.hk/showthread.php?tid=253436
> 作者：RyDeR`
> 发布日期：2011年5月6日

---

**介绍**

很多人都想知道如何使用 Microsoft Visual C++ 2008/2010 创建一个插件。有些人认为这很难，但实际上，如果你知道如何开始，这非常简单。

下面我将逐步解释如何创建你的第一个原生函数。

**开始前你需要了解的事项**

*   你必须安装 Microsoft Visual C++。
*   你需要有一些 PAWN 脚本编写经验才能理解相关内容。
*   需要 SA-MP 软件开发工具包 (SDK)。

---

### **一步一步教你创建插件**

1.  **运行 Microsoft Visual C++** (2008/2010 版本)。

2.  转到 **“文件”** 选项卡，然后点击 **“新建”**，并选择 **“项目”**。

3.  你会看到以下对话框：
    *（此处原帖应有图片，显示新建项目对话框）*

4.  选择 **“Win32 项目”**，输入一个项目名称，然后按 **“确定”** 继续。

5.  将出现以下对话框：
    *（此处原帖应有图片，显示Win32应用程序向导）*
    直接按 **“下一步”** 继续。

6.  在下一个对话框中，你需要在 **“应用程序类型”** 下选择 **“DLL”**，在 **“附加选项”** 下选择 **“空项目”**，然后按 **“完成”** 按钮。

7.  现在，你唯一能看到的就是左侧的 **“解决方案资源管理器”**。（如果看不到解决方案资源管理器，请按 **“视图”** 选项卡，然后选择 **“其他窗口”**，再点击 **“解决方案资源管理器”**）。

8.  如你所见，我把我的项目命名为 **“Test”**。在解决方案资源管理器中右键点击 **“Test”**，然后选择 **“属性”**。

9.  你会看到以下对话框：
    *（此处原帖应有图片，显示项目属性页对话框）*

10. 在左侧，点击 **“链接器”**，然后选择 **“输入”**，在 **“模块定义文件”** 旁边输入你的项目名称或其他名字，并在后面加上 **“.def”** 扩展名，然后按 **“确定”**。

11. 现在回到解决方案资源管理器，再次右键点击你的项目名称，选择 **“添加”**，然后点击 **“新建项”**。

12. 在弹出的对话框中，选择 **“C++ 文件 (.cpp)”**，然后在下面的 **“名称”** 旁边输入你在**步骤 11** 中写在“模块定义文件”旁边的名字。并按 **“添加”**。

13. 再次右键点击你的项目名称，选择 **“添加”**，并再次点击 **“新建项”**。

14. 这次你同样选择 **“C++ 文件 (.cpp)”**，但现在，在 **“名称”** 旁边输入你之前选择的名字，但这次使用 **“.cpp”** 扩展名。然后按 **“添加”**。

15. 现在你会看到打开了两个选项卡：**“YourProjectName.def”** 和 **“YourProjectName.cpp”**。

16. 打开 **“YourProjectName.def”** 并将以下内容粘贴进去：
    ```def
    EXPORTS
        Supports
        Load
        Unload
        AmxLoad
        AmxUnload
    ```

17. 现在打开 **“YourProjectName.cpp”** 并将以下内容粘贴进去：
    ```cpp
    #include "../SDK/plugin.h"

    typedef void (*logprintf_t)(char* format, ...);
    logprintf_t logprintf;
    void **ppPluginData;
    extern void *pAMXFunctions;

    PLUGIN_EXPORT bool PLUGIN_CALL Load(void **ppData)
    {
        pAMXFunctions = ppData[PLUGIN_DATA_AMX_EXPORTS];
        logprintf = (logprintf_t)ppData[PLUGIN_DATA_LOGPRINTF];
        return 1;
    }

    PLUGIN_EXPORT void PLUGIN_CALL Unload()
    {
    }

    AMX_NATIVE_INFO projectNatives[] =
    {
        { 0, 0 }
    };

    PLUGIN_EXPORT unsigned int PLUGIN_CALL Supports()
    {
        return SUPPORTS_VERSION | SUPPORTS_AMX_NATIVES;
    }

    PLUGIN_EXPORT int PLUGIN_CALL AmxLoad(AMX *amx)
    {
        return amx_Register(amx, projectNatives, -1);
    }

    PLUGIN_EXPORT int PLUGIN_CALL AmxUnload(AMX *amx)
    {
        return AMX_ERR_NONE;
    }
    ```

18. 现在回到解决方案资源管理器，再次右键点击你的项目名称，选择 **“添加”**，然后点击 **“新建筛选器”**。你会看到创建了一个新文件。将其名称更改为 **“SDK”**。

19. 将你下载的 **“SDK”** 文件解压到 **“文档/Visual Studio 2010/Projects/YourProjectName/”** 目录下。

20. 回到解决方案资源管理器，右键点击你创建的 **“SDK”** 筛选器，选择 **“添加”**，然后点击 **“现有项”**。

21. 会弹出一个对话框，现在浏览到你复制的 **“SDK”** 目录，选择 **“amxplugin.cpp”** 文件。

22. **基本完成了！** 现在按 **F7** 编译它。

23. 你可以在 **Debug** 目录中找到你的 **.dll** 文件。

---

### **添加你的第一个原生函数**

1.  在你的 **.cpp** 脚本中的某处添加以下代码：
    ```cpp
    static cell AMX_NATIVE_CALL YourNativeNameHere(AMX *amx, cell *params)
    {
        logprintf("我做了我的第一个插件！！ :)");
        // 你的代码写在这里
        return 1; // 如果需要，更改返回值
    }
    ```

2.  找到以下代码：
    ```cpp
    AMX_NATIVE_INFO projectNatives[] =
    {
        { 0, 0 }
    };
    ```
    将其修改为：
    ```cpp
    AMX_NATIVE_INFO projectNatives[] =
    {
        { "YourNativeNameHere", YourNativeNameHere } // 在第一个数组元素中，你写在PAWN中要调用的原生函数名称。在第二个元素中，你写在.cpp文件中的函数名称。在这个例子中，它们是相同的！
    };
    ```

3.  **编译**，然后转到 **Debug** 文件夹。

4.  选择你的 **.dll** 文件并将其复制到你的服务器目录。

5.  创建一个新的 **.pwn** 脚本，并在顶部添加 **`native YourNativeNameHere();`**。然后在 **`OnFilterScriptInit`** 下调用它。
    ```pawn
    #include <a_samp>

    native YourNativeNameHere();

    public OnFilterScriptInit()
    {
        YourNativeNameHere();
        return 1;
    }
    ```

6.  当你运行服务器时，它应该会打印出：**“我做了我的第一个插件！！ ”**。

7.  这只是基础。现在你可以创建其他独特的功能了！

> **注意：** 如果你想要带参数的原生函数，可以使用 `params[]`，从索引 1 开始。

---

### **最后的话**

*   尝试自己弄清楚如何做其他事情。我只是教你如何开始的基础。
*   你可以在下面提问，但我不会回答所有问题。

---
