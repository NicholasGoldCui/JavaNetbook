# 第1章 Windows PowerShell 外壳
## 1.4 配置Windows PowerShell
### 1.4.2 配置Windows PowerShell 启动选项
+ 启动不带标志的Windows PowerShell，可以使用-nologo参数：
```powershell
powershell -nologo
```
+ 启动特定版本的Windows PowerShell，可以使用-version参数：
```powershell
powershell -version 1.0
```
+ 使用特定配置文件启动Windows PowerShell，可以使用-psconsolefile参数：
```powershell
powershell -psconsolefile myconsole.psc1
```
+ 启动Windows PowerShell，执行一个特定命令，然后退出，可以使用-command参数。其中要执行的命令必须以“&”记号开头，并且包含在大括号内：
```powershell
powershell -command "& {get-process}"
```

## 1.5 Windows PowerShell 的安全问题
启动Windows PowerShell 后，该工具会打开当前用户的"Users\userName"文件夹，这就确保了在默认情况下，我们会位于自己由权限执行特定操作和活动的位置，这种方法比直接打开硬盘或者操作系统的根目录安全得多。
默认情况下，脚本的运行会被禁用，不过这个功能可以通过组策略或登录脚本管理。
### 1.5.1 控制cmdlet的执行方式
输入“Format C:\”，我们可以格式化C盘。有很多参数可以传递给cmdlet以控制其执行方式。
**提示**
大部分Windwos PowerShell cmdlet支持一种叫“原型”(prototype)的模式，可以使用-whatif参数启动。是否允许使用whatif参数是由cmdlet的开发人员决定的。对此，Windows PowerShell开发团队的建议是：如果cmdlet要修改系统设置，那么最好提供whatif选项。

虽然并非所有的cmdlet都支持这些参数，不过Windows PowerShell中自带的大部分cmdlet都可以。控制cmdlet执行凡是的三种方法包括：-whatif、-confirm以及suspend。suspend并不提供给cmdlet的参数，但可以在命令中执行该操作，因此也可以算是一种控制cmdlet运行方式的方法。

要使用-whatif，首先需要在Windows PowerShell窗口中输入cmdlet的名称，然后在名称的后面输入-whatif参数。例，
打开记事本程序，。随后使用Get-Process cmdlet搜索名称以“note”开头的所有进程。在本例中，有两个名称以“none”开头的notepad进程。接着使用Stop-process cmdlet停止一个notepad进程，但因为结果未知，因此可以使用-whatif参数。whatif会告诉我们要关闭两个进程，这两个进程的名称都是notepad，同时该参数还会显示进程的ID，以便判断这个进程是否需要关闭的。以此类推，如果希望使用Stop-Process cmdlet关闭名称以“n”开头的所有进程，最好先使用whatif参数看一下执行这个命令会产生什么后果。
```powershell
PS C:\Users\edwils> notepad
PS C:\Users\edwils> Get-process note*

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
    669      38    31436      77752       0.78   9964   1 Notepad

PS C:\Users\edwils> Stop-Process -processName notepad -Whatif
What if: Performing operation "Stop-Process" on Target "notepad (3052)".
What if: Performing operation "Stop-Process" on Target "notepad (3140)".

PS C:\Users\edwils> Stop-Process -ProcessName n* -Whatif
What if: Performing peration "Stop-process" on Taraget "notepad (3052)".
What if: Performing peration "Stop-process" on Taraget "notepad (3140)".
```
**最佳实践**
网络管理员的大部分工作都可以借助于-whatif参数，后者是必要的辅助工具。在执行命令之前使用这种方式来了解后果，可以帮助我们节约大量的时间。

### 1.5.2 确认命令
正如前文所述，可以使用-whatif创建Windows PowerShell中的cmdlet原型，这有助于检查命令可能导致的结果。然而，为了在命令执行之前得到确认，还可以使用-confirm参数。实际上，使用-confirm参数即可完全代替-whatif，因为在操作发生之前会要求我们确认。例如，
在confirmit.txt文件中，首先启动计算器(calc.exe)。该文件位于可执行的路径下，所以不需要将要执行的文件的路径硬编码到脚本中。随后，使用Get-Process配合使用“c*”通配符寻找所有名称以字母“c”开头的进程。注意，有很多进程复合这一条件。下一步则需要针对calc.exe进行检索，这样可以获得一个更可控的结果集。随后使用Stop-Process cmdlet配合-confirm参数，关闭该进程，cmdlet会返回下列信息。
```powershell
Confirm
Are you sure you want to perform this action?
Performing operation "Stop-Process" on Target "calc (2924)".
[Y] Yes [A] yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```
可以看出，这些信息就和使用whatif参数后看到的信息一样，不过这次我们可以控制操作的执行，从而为日常工作节约大量的时间。

### 1.5.3 cmdlet的挂起确认
对于cmdlet的执行,“可挂起”功能非常有用，而且在某些时候还有助于节约大量时间。
例如在有时候，我们输入一串很长的命令，然后才想起来必须首先执行其他操作。在这样的情况下，只需要将当前命令的执行挂起即可。用于挂起cmdlet执行的命令以及相应的输出内容，都会在下文中的SuspendConfirmation.txt文件中进行示范。
在SuspendConfirmation.txt文件中，首先启动画图程序(mspaint.exe)。因为mspaint.exe位于可执行路径下，因此不需要在脚本中提供任何路径信息。随后使用Get-Process cmdlet获得进程信息，并使用“ms*”通配符找到名称以“ms”字母开头的所有进程。在找到需要的进程后，使用Stop-Process cmdlet以及confirm参数关闭该进程。在对确认信息回答“是”之前，我们可以挂起该命令的执行，这样就能运行其他命令(也许忘了该进程的ID编号，需要查询)。在运行完随后需要的命令后，输入exit即可返回上一个被挂起的命令。而且在关闭mspaint进程后，还可以再次使用Get-Process cmdlet确认该进程已经被关闭。
```powershell
PS C:\Users\edwils> mspaint
PS C:\Users\edwils> Get-Process ms*

Handles   NPM(K)  PM(K)   WS(K)   VM(M)   CPU(s)    Id   ProcessName
-------   ------  -----   -----   -----   ------    --   -----------
     98       4    5404   10492     72      0.09  3064        mspaint

PS C:\Users\edwils> Stop-Process -id 3064 -Confirm

Confirm
Are you sure you want to perform this action?
Performing operation "Stop-Process" on Target "mspaint (3064)".
[Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"): s
PS C:\Users\edwils>>> Get-Process ms*

Handles   NPM(K)    PM(K)   WS(K)   VM(M)   CPU(s)    Id   ProcessName
-------   ------    -----   -----   -----   ------    ---  -----------
97            4     5404    10496     72     0.09     3064  mspaint

PS C:\Users\edwils>>> exit

Confirm
Are you sure you want to perform this action?
Performing operation "Stop-Process" on Target "mspaint (3064)".
[Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"): Y
PS C:\Users\edwils> Get-Process ms*
```
## 1.6 cmdlet的提供选项
如前所述，我们可以使用-whatif和-confirm参数控制cmdlet的执行。很多人也许会问这样一个问题，“我怎么知道有哪些参数可用”？答案是这样的：Windows PowerShell开发团队创建了一系列标准的选项，这些标准选项叫公共参数(Common Parameter)。在查看cmdlet的语法描述时，通常都会看到该cmdlet支持公共参数。例如对于Get-Process cmdlet，语法描述如下：
```powershell
SYNTAX
Get-Process [[-name] <string[]>] [<CommonParameters>]
Get-Process -id <Int32[]> [<CommonParameters>]
Get-Process -inputObject <Process[]> [<CommonParameters>]
```
Windows PowerShell的有用功能之一就是cmdlet可用语法的标准化。这个功能极大地简化了新外壳和语言的学习难度。然而要注意，并非所有cmdlet都支持下列每个参数。然而如果可以支持其中的某个，那么能够实现的功能就是完全一样的，因为Windows PowerShell引擎会对这些参数进行统一解释。
| 参数 | 含义 |
| --- | --- |
| -whatif | 让cmdlet不要执行，而是告诉我们执行会发生什么后果 |
| -confirm | 让cmdlet在执行之前要求得到我们的确认 |
| -verbose | 让cmdlet提供更详细的信息(相较于不使用该参数的) |
| -debug | 让cmdlet提供调试信息 |
| -erroraction | 让cmdlet在发生错误时执行某些操作，可用操作包括：continue(继续)、stop(停止)、SilentlyContinue(静默继续)以及inquire(询问) |
| -errorvariable | 让cmdlet使用特定变量保存错误信息，这个方法和标准的$error变量有所不同 |
| -outvariable | 让cmdlet使用特定变量保存输出信息 |
| -outbuffier | 让cmdlet在调用管道中的下一个cmdlet之前保存特定数量的对象 |

## 1.7 使用Get-Help
Windows PowerShell非常易于使用，容易上手，同时附带的联机帮助功能使得程序的使用变得更简单。Windows PowerShell中的帮助系统可以通过下列几种方法使用，要了解Windows PowerShell的更多使用方法，请使用Get-Help cmdlet，方法如下：
```PowerShell
Get-help get-help
```
这个命令可以显示有关Get-Help cmdlet的帮助信息，如下所示：
```powershell
PS C:\Users\你大爷> get-help get-help

名称
    Get-Help

摘要
    Displays information about PowerShell commands and concepts.


语法
    Get-Help [[-Name] <System.String>] [-Category {Alias | Cmdlet | Provider | General | FAQ | Glossary | HelpFile | Sc
    riptCommand | Function | Filter | ExternalScript | All | DefaultHelp | Workflow | DscResource | Class | Configurati
    on}] [-Component <System.String[]>] -Detailed [-Functionality <System.String[]>] [-Path <System.String>] [-Role <Sy
    stem.String[]>] [<CommonParameters>]

    Get-Help [[-Name] <System.String>] [-Category {Alias | Cmdlet | Provider | General | FAQ | Glossary | HelpFile | Sc
    riptCommand | Function | Filter | ExternalScript | All | DefaultHelp | Workflow | DscResource | Class | Configurati
    on}] [-Component <System.String[]>] -Examples [-Functionality <System.String[]>] [-Path <System.String>] [-Role <Sy
    stem.String[]>] [<CommonParameters>]

    Get-Help [[-Name] <System.String>] [-Category {Alias | Cmdlet | Provider | General | FAQ | Glossary | HelpFile | Sc
    riptCommand | Function | Filter | ExternalScript | All | DefaultHelp | Workflow | DscResource | Class | Configurati
    on}] [-Component <System.String[]>] [-Full] [-Functionality <System.String[]>] [-Path <System.String>] [-Role <Syst
    em.String[]>] [<CommonParameters>]

    Get-Help [[-Name] <System.String>] [-Category {Alias | Cmdlet | Provider | General | FAQ | Glossary | HelpFile | Sc
    riptCommand | Function | Filter | ExternalScript | All | DefaultHelp | Workflow | DscResource | Class | Configurati
    on}] [-Component <System.String[]>] [-Functionality <System.String[]>] -Online [-Path <System.String>] [-Role <Syst
    em.String[]>] [<CommonParameters>]

    Get-Help [[-Name] <System.String>] [-Category {Alias | Cmdlet | Provider | General | FAQ | Glossary | HelpFile | Sc
    riptCommand | Function | Filter | ExternalScript | All | DefaultHelp | Workflow | DscResource | Class | Configurati
    on}] [-Component <System.String[]>] [-Functionality <System.String[]>] -Parameter <System.String> [-Path <System.St
    ring>] [-Role <System.String[]>] [<CommonParameters>]

    Get-Help [[-Name] <System.String>] [-Category {Alias | Cmdlet | Provider | General | FAQ | Glossary | HelpFile | Sc
    riptCommand | Function | Filter | ExternalScript | All | DefaultHelp | Workflow | DscResource | Class | Configurati
    on}] [-Component <System.String[]>] [-Functionality <System.String[]>] [-Path <System.String>] [-Role <System.Strin
    g[]>] -ShowWindow [<CommonParameters>]


说明
    The `Get-Help` cmdlet displays information about PowerShell concepts and commands, including cmdlets, functions, Co
    mmon Information Model (CIM) commands, workflows, providers, aliases, and scripts.

    To get help for a PowerShell cmdlet, type `Get-Help` followed by the cmdlet name, such as: `Get-Help Get-Process`.

    Conceptual help articles in PowerShell begin with about_ , such as about_Comparison_Operators . To see all about_ a
    rticles, type `Get-Help about_*`. To see a particular article, type `Get-Help about_<article-name>`, such as `Get-H
    elp about_Comparison_Operators`.

    To get help for a PowerShell provider, type `Get-Help` followed by the provider name. For example, to get help for
    the Certificate provider, type `Get-Help Certificate`.

    You can also type `help` or `man`, which displays one screen of text at a time. Or, `<cmdlet-name> -?`, that is ide
    ntical to `Get-Help`, but only works for cmdlets.

    `Get-Help` gets the help content that it displays from help files on your computer. Without the help files, `Get-He
    lp` displays only basic information about cmdlets. Some PowerShell modules include help files. Beginning in PowerSh
    ell 3.0, the modules that come with the Windows operating system don't include help files. To download or update th
    e help files for a module in PowerShell 3.0, use the `Update-Help` cmdlet.

    You can also view the PowerShell help documents online in the Microsoft Docs. To get the online version of a help f
    ile, use the Online parameter, such as: `Get-Help Get-Process -Online`. To read all the PowerShell documentation, s
    ee the Microsoft Docs PowerShell Documentation (/powershell).

    If you type `Get-Help` followed by the exact name of a help article, or by a word unique to a help article, `Get-He
    lp` displays the article's content. If you specify the exact name of a command alias, `Get-Help` displays the help
    for the original command. If you enter a word or word pattern that appears in several help article titles, `Get-Hel
    p` displays a list of the matching titles. If you enter any text that doesn't appear in any help article titles, `G
    et-Help` displays a list of articles that include that text in their contents.

    `Get-Help` can get help articles for all supported languages and locales. `Get-Help` first looks for help files in
    the locale set for Windows, then in the parent locale, such as pt for pt-BR , and then in a fallback locale. Beginn
    ing in PowerShell 3.0, if `Get-Help` doesn't find help in the fallback locale, it looks for help articles in Englis
    h, en-US , before it returns an error message or displaying autogenerated help.

    For information about the symbols that `Get-Help` displays in the command syntax diagram, see about_Command_Syntax
    (./About/about_Command_Syntax.md). For information about parameter attributes, such as Required and Position , see
    about_Parameters (./About/about_Parameters.md).

    >[!NOTE] > In PowerShell 3.0 and PowerShell 4.0, `Get-Help` can't find About articles in modules unless > the modul
    e is imported into the current session. This is a known issue. To get About articles > in a module, import the modu
    le, either by using the `Import-Module` cmdlet or by running a cmdlet > that's included in the module.


相关链接
    Online Version: https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-help?view=powershell-5.1
    &WT.mc_id=ps-gethelp
    about_Command_Syntax
    about_Comment_Based_Help
    Get-Command
    Supporting Updatable Help
    Update-Help
    Writing Comment-Based Help Topics
    Writing Help for PowerShell Cmdlets

备注
    若要查看示例，请键入: "get-help Get-Help -examples".
    有关详细信息，请键入: "get-help Get-Help -detailed".
    若要获取技术信息，请键入: "get-help Get-Help -full".
    有关在线帮助，请键入: "get-help Get-Help -online"
```
Windows Powershell联机帮助中最受欢迎的是，该功能并不只显示有关命令的帮助(这是大部分人需要的)，而且可以显示三种不同级别的信息：Normal(常规)、Detailed(详细)以及Full(完整)。另外，我们还可以获得有关Windwos Powershell的概念信息，这个功能就等于让我们有了一个详细的联机使用手册。为了获得所有帮助信息的列表，可以使用Get-Help about`*`命令，如下所示：
```powershell
get-help about*
```
这种使用通配符操作的 方法还可以进一步扩展。如果记得使用的是一个“get”cmdlet，而且首字母是“p”，就可以使用下列命令查看符合要求的所有cmdlet：
```powershell
get-help get-p*
```
然而，假设知道要使用的cmdlet的准确名称，但是忘了相应的语法，这种情况下则可以使用-examples参数。例如，为了获得Get-PSDrive cmdlet的使用语法信息，可以将Get-Help配合-example参数使用，如下所示：
```powershell
get-help get-psdrive -exmaples
```
为了一次只看到一页帮助内容，还可以让help函数通过more函数显示帮助内容，如果不希望通过拖动滚动条看到完整的帮助内容，那么这个方法就很有用。如下所示：
```powershell
get-help get-help | more
```

为了获得有关get-help cmdlet的详细帮助，可以使用-detailed参数，如下所示：
```powershell
get-help get-help -detailed
```
如果希望获得有关get-help cmdlet的技术信息，可以使用-full参数，如下所示：
```powershell
get-help get-help -full
```

如果厌倦了重复输入Get-Help，只需为Get-Help创建别名即可。别名(alias)可以代替键盘输入内容的快捷方式，可以用于启动程序或cmdlet。例如，如果需要创建Get-Help的别名，可以为Get-Help指派“gh”键作为别名。
**提示**
在为cmdlet创建别名之前，请确认该cmdlet没有其他已经设定的别名。***确认方法***是运行Get-Alias命令。确认无误后可以使用Set-Alias命令为cmdlet指定一个唯一的按键组合。

## 1.8 使用别名以及cmdlet指定别名
别名功能可以让我们为cmdlet指定快捷键，这样可以简化Windows PowerShell中的输入，并可以让我们按照自己的习惯对命令行语法进行自定义。例如，希望为Get-Help cmdlet创建别名，而不需要每次都输入完整的内容，或者更希望输入“gh”代替，那么只要通过简单的4步操作就可以实现。
1. 首先，确认希望使用的按键目前没有被指派给其他功能作为别名使用，这样可以避免冲突。
2. 随后可能还需要复查有关Set-Alias cmdlet的帮助内容。
3. 完成上述工作后，调用Set-Alias cmdlet，并输入希望创建的名称，以及希望应用别名的目标cmdlet的名称。
4. 创建好别名后，使用Get-Alias验证别名是否成功创建。
5. 这一系列操作的完整过程已经包含在本书配套资源chapter01目录下的GhAlias.txt文件中。

(1) 获取目前已经定义的别名的列表，并检查Get-Help cmdlet是否已经设置了别名，或者希望使用的“gh”是否已经被其他别名使用。方法如下：
```powershell
get-alias | sort
```
(2) 一旦发现Get-Help cmdlet没有创建别名，而且要使用的“gh”没有被指派给其他别名，则需要复查Set-Alias cmdlet的使用语法。这时候可以结合-full参数使用Get-help cmdlet，方法如下：
```powershell
get-help set-alias -full
```
(3) 使用Set-Alias cmdlet将组合键“gh”指派给Get-Help cmdlet，方法如下：
```powershell
set-alias gh get-help
```
(4) 使用Get-Alias cmdlet验证别名是否创建成功，方法如下：
```powershell
get-alias gh
```
**提示**
如果认为Set-Alias的使用语法有些难以理解，还可以使用命名参数代替默认的绑定位置。不仅如此，还建议使用-whatif参数或confirm参数。我们可以为别名指定描述内容，如果要这样做，需要运行的命令可能与下面类似：
```powershell
Set-Alias -Name gh -Value Get-Help -Description "mred help alias" -WhatIf
```

## 1.9 cmdlet的其他用途
了解帮助工具和别名的使用后，可以了解Windows PowerShell的cmdlet还有哪些用法。
**提示**
Tab键可以自动帮助我们补全没有输入的内容，

因为cmdlet返回的是对象，而不是“字符串值”，因此可以通过返回的对象获得额外的信息。如果处理的只是字符串数据，那么这些额外的信息可能不存在。为获得额外的信息，还可以**使用**管道符(|)，从一个cmdlet中获得信息，并将其填充到其他cmdlet中。

组为最基本的级别，我们可以用获得目录内容列表并调整输出格式的操作作为例子。在获得目录的内容列表后，可能希望调整列表的显示格式，也许希望显示为表格或者列表的形式。在这个过程中有两个独立的操作：获得目录内容列表和调整列表格式。在获得文件夹列表信息后，这个调整格式的任务会在管道的右侧进行。这就是管道的工作方式。

### 1.9.1 使用Get-ChildItem cmdlet
前面提到，可以使用dir命令获得目录下的文件内容列表。这个命令可用的主要原因在于，Windows PowerShell中为Get-ChildItem cmdlet默认指派了别名，并且使用“dir”按键组合。我们可以通过使用Get-Alias cmdlet确认这一点，相应的过程可以参考GetDirAlias.txt文件。
```powershell
PS C:\> Get-Alias dir

CommandType     Name                                               Version    Source                                                                         
-----------     ----                                               -------    ------                                                                         
Alias           dir -> Get-ChildItem                                                                                                                         
```
在Windows PowerShell中，其实并没有名为“dir”的cmdlet，而我们使用的实际上也并不是“dir”命令。“dir”作为别名被指派给Get-ChildItem cmdlet，因此Windows PowerShell中dir的输出内容和cmd.exe中的有很大不同。在使用Get-Alias cmdlet查看别名的关联情况，会看到这一点。
**提示**
在使用Get-ChildItem生成目录列表时，如果希望看到隐藏的和系统的文件以及文件夹，可以使用force参数，即Get-ChildItem -Force。

### 1.9.2 调整输出格式
在Windows PowerShell中有4个和格式有关的cmdlet，对于这些cmdlet，最常用的有3个：Format-List、Format-Wide以及Format-Table，而第4个Format-Custom则主要用于控制不属于列表、表格或其他格式的内容的输出样式。这个工作是通过使用*.format.ps1 XML文件实现的，我们可以使用*.for mat.ps1 XML文件中定义的默认样式，或者创建自己的format.ps1 XML文件。
先来看这些格式中cmdlet最常用的Format-List的用法。
1. Format-List
Format-List可能是最常用的核心cmdlet之一。例如，使用Get-WmiObject cmdlet查看Win32_LogicalDisk类的属性，那么将看到这个类的默认属性的最小列表。列表内容如下：
```powershell
PS C:\> Get-WmiObject Win32_LogicalDisk

DeviceID     : C:
DriveType    : 3
ProviderName : 
FreeSpace    : 38417653760
Size         : 107381518336
VolumeName   : 

DeviceID     : D:
DriveType    : 3
ProviderName : 
FreeSpace    : 265053827072
Size         : 403661910016
VolumeName   : 

```
虽然大部分情况下这样做没什么问题，不过有时候我们可能希望浏览该类中的其他属性。如果希望浏览其他属性，首先需要使用通配符“`*`”，这样可以列出所有可用的属性，如下所示：
```powershell
PS C:\> Get-WmiObject Win32_LogicalDisk | Format-List *

PSComputerName               : DESKTOP-MA9I14O
Status                       : 
Availability                 : 
DeviceID                     : C:
StatusInfo                   : 
__GENUS                      : 2
__CLASS                      : Win32_LogicalDisk
__SUPERCLASS                 : CIM_LogicalDisk
__DYNASTY                    : CIM_ManagedSystemElement
__RELPATH                    : Win32_LogicalDisk.DeviceID="C:"
__PROPERTY_COUNT             : 40
__DERIVATION                 : {CIM_LogicalDisk, CIM_StorageExtent, CIM_Logical
                               Device, CIM_LogicalElement...}
__SERVER                     : DESKTOP-MA9I14O
__NAMESPACE                  : root\cimv2
__PATH                       : \\DESKTOP-MA9I14O\root\cimv2:Win32_LogicalDisk.D
                               eviceID="C:"
Access                       : 0
BlockSize                    : 
Caption                      : C:
Compressed                   : False
ConfigManagerErrorCode       : 
ConfigManagerUserConfig      : 
CreationClassName            : Win32_LogicalDisk
Description                  : 本地固定磁盘
DriveType                    : 3
ErrorCleared                 : 
ErrorDescription             : 
ErrorMethodology             : 
FileSystem                   : NTFS
FreeSpace                    : 37714276352
InstallDate                  : 
LastErrorCode                : 
MaximumComponentLength       : 255
MediaType                    : 12
Name                         : C:
NumberOfBlocks               : 
PNPDeviceID                  : 
PowerManagementCapabilities  : 
PowerManagementSupported     : 
ProviderName                 : 
Purpose                      : 
QuotasDisabled               : 
QuotasIncomplete             : 
QuotasRebuilding             : 
Size                         : 107381518336
SupportsDiskQuotas           : False
SupportsFileBasedCompression : True
SystemCreationClassName      : Win32_ComputerSystem
SystemName                   : DESKTOP-MA9I14O
VolumeDirty                  : 
VolumeName                   : 
VolumeSerialNumber           : 9EE2B42D
Scope                        : System.Management.ManagementScope
Path                         : \\DESKTOP-MA9I14O\root\cimv2:Win32_LogicalDisk.D
                               eviceID="C:"
Options                      : System.Management.ObjectGetOptions
ClassPath                    : \\DESKTOP-MA9I14O\root\cimv2:Win32_LogicalDisk
Properties                   : {Access, Availability, BlockSize, Caption...}
SystemProperties             : {__GENUS, __CLASS, __SUPERCLASS, __DYNASTY...}
Qualifiers                   : {dynamic, Locale, provider, UUID}
Site                         : 
Container                    : 

PSComputerName               : DESKTOP-MA9I14O
Status                       : 
Availability                 : 
DeviceID                     : D:
StatusInfo                   : 
__GENUS                      : 2
__CLASS                      : Win32_LogicalDisk
__SUPERCLASS                 : CIM_LogicalDisk
__DYNASTY                    : CIM_ManagedSystemElement
__RELPATH                    : Win32_LogicalDisk.DeviceID="D:"
__PROPERTY_COUNT             : 40
__DERIVATION                 : {CIM_LogicalDisk, CIM_StorageExtent, CIM_Logical
                               Device, CIM_LogicalElement...}
__SERVER                     : DESKTOP-MA9I14O
__NAMESPACE                  : root\cimv2
__PATH                       : \\DESKTOP-MA9I14O\root\cimv2:Win32_LogicalDisk.D
                               eviceID="D:"
Access                       : 0
BlockSize                    : 
Caption                      : D:
Compressed                   : False
ConfigManagerErrorCode       : 
ConfigManagerUserConfig      : 
CreationClassName            : Win32_LogicalDisk
Description                  : 本地固定磁盘
DriveType                    : 3
ErrorCleared                 : 
ErrorDescription             : 
ErrorMethodology             : 
FileSystem                   : NTFS
FreeSpace                    : 266487422976
InstallDate                  : 
LastErrorCode                : 
MaximumComponentLength       : 255
MediaType                    : 12
Name                         : D:
NumberOfBlocks               : 
PNPDeviceID                  : 
PowerManagementCapabilities  : 
PowerManagementSupported     : 
ProviderName                 : 
Purpose                      : 
QuotasDisabled               : 
QuotasIncomplete             : 
QuotasRebuilding             : 
Size                         : 403661910016
SupportsDiskQuotas           : False
SupportsFileBasedCompression : True
SystemCreationClassName      : Win32_ComputerSystem
SystemName                   : DESKTOP-MA9I14O
VolumeDirty                  : 
VolumeName                   : 
VolumeSerialNumber           : C4E3057A
Scope                        : System.Management.ManagementScope
Path                         : \\DESKTOP-MA9I14O\root\cimv2:Win32_LogicalDisk.D
                               eviceID="D:"
Options                      : System.Management.ObjectGetOptions
ClassPath                    : \\DESKTOP-MA9I14O\root\cimv2:Win32_LogicalDisk
Properties                   : {Access, Availability, BlockSize, Caption...}
SystemProperties             : {__GENUS, __CLASS, __SUPERCLASS, __DYNASTY...}
Qualifiers                   : {dynamic, Locale, provider, UUID}
Site                         : 
Container                    : 

```
在看到特定类的所有可用属性后，还可以选择只显示自己感兴趣的属性。这时候可以使用上述列表中属性的名称代替通配符“`*`”，如下所示：
```powershell
PS C:\> Get-WmiObject Win32_LogicalDisk | Format-List Name, FileSystem, FreeSpace

Name       : C:
FileSystem : NTFS
FreeSpace  : 37712777216

Name       : D:
FileSystem : NTFS
FreeSpace  : 266487349248

```
在这里并不需要输入完整的属性名列表，我们可以通过通配符指定属性名称的范围。例如，如果希望看到所有名称以字母“f”开头的属性，可以如下输入：
```powershell
PS C:\> Get-WimiObject Win32_LogicalDisk | Format-List f*

FileSystem : NTFS
FreeSpace  : 37703110656

FileSystem : NTFS
FreeSpace  : 266455224320

```
如果希望看到名称**以**字母“n”**以及**“f”开头的属性，则可以如下输入：
```powershell
PS C:\> Get-WmiObject Win32_LogicalDisk | Format-List [nf]*

FileSystem     : NTFS
FreeSpace      : 37702369280
Name           : C:
NumberOfBlocks : 

FileSystem     : NTFS
FreeSpace      : 266455175168
Name           : D:
NumberOfBlocks : 

```
2. Format-Table
Format-Table cmdlet提供的一系列功能非常适合用于网络管理功能，尤其是该cmdlet可以提供便于快速查看的属性列信息。对于Format-List和Format-Wide，我们可以选择要显示的属性，而在这样做的时候，还可以排除其中不需要的数据。在下面的例子中，首先对硬盘进行查询，以便找到日志文件(具有.log的扩展名)。在考虑输出的内容的时候，可以对要输出的内容进行调整，这时候就可以使用Format-Table cmdlet调整Get-ChildItem cmdlet输出的内容的格式：
```powershell
PS C:\> Get-ChildItem C:\ -Recurse -Include *.log | Format-Table
    目录: C:\Program Files\AMD\CIM\Log


Mode                 LastWriteTime         Length Name                                                                   
----                 -------------         ------ ----                                                                   
-a----          2021/8/4  上午 09:00          10018 Install.log                                                            
-a----        2020/12/18  上午 11:34           6861 Install.log_2021-7-16_7_46_26.log                                      
-a----         2021/7/16  上午 07:52          10568 Install.log_2021-8-4_8_45_31.log                                       

```
除了使用cmdlet的默认行为，还可以选择特定的选项。调整后的格式会使用当前的Windows屏幕分辨率，因此有时候可能会遇到属性列出现在窗口另一边的情况。对于只是希望查看内容的应用，这个问题不是很严重，但如果希望将数据保存起来，就需要关注这个问题。
```powershell
PS C:\> Get-ChildItem C:\ -Recurse -Include *.log | Format-Table -Property name, length, lastWriteTime

Name                               Length LastWriteTime      
----                               ------ -------------      
Install.log                         10018 2021/8/4 09:00:52  
Install.log_2021-7-16_7_46_26.log    6861 2020/12/18 11:34:49
Install.log_2021-8-4_8_45_31.log    10568 2021/7/16 07:52:26 
atikmdag_dce.log                  1463128 2021/12/30 08:58:46
debug.log                             910 2021/12/26 16:34:40
auxlog.log                          73289 2019/9/5 13:20:58  
ps.log                                320 2019/9/5 13:20:58  
windows-wmic.log                   171668 2019/9/5 13:20:58  
wmic.log                             6513 2019/9/5 13:20:58  
isolate-0x102801e00-74163-v8.log   500526 2018/3/22 23:49:30 
isolate-0x103000000-74139-v8.log   660871 2018/3/22 23:48:36 
isolate-0x103000000-74561-v8.log   633352 2018/3/22 23:50:08 
auxlog.log                          73289 2016/10/18 08:29:06
ps.log                                320 2016/4/26 12:52:04 
windows-wmic.log                   171668 2016/5/5 05:57:42  
wmic.log                             6513 2016/4/26 14:38:02 
Install.log                        107322 2021/8/7 21:01:54  
kfcdetect.dll.log                    3734 2021/9/3 21:04:01  
rmb.log                                 2 2020/12/18 12:07:34
PhotoshopPrefsManager-20210807...    1258 2021/8/7 21:01:37  
000003.log                           2515 2021/10/6 21:24:02 
000003.log                            517 2021/10/6 21:35:53 
000003.log                              0 2021/10/6 21:23:04 
Dfrg.log                              530 2021/9/3 21:04:41  
.log                                42377 2021/12/22 15:05:31
000003.log                          15230 2021/9/25 10:51:57 
000003.log                        1406826 2021/7/31 13:37:44 
000003.log                          15330 2021/9/25 10:51:50 
000003.log                           2119 2021/9/25 10:51:57 
000003.log                              0 2021/6/14 19:42:07 
```
为了生成能够更有效利用窗口面积的列表，可以使用autosize参数。在使用该参数的时候只有一个问题需要**注意**：该参数需要**知道**每个列中被保存的最大项目的长度。为此参数必须**首先**等待所有对象都被枚举到，然后才判断其中最长列表的长度，接着判断列表的大小。这个过程可能会导致命令的执行结果在所有项目都被枚举到之前无法显示出来，因此可能会需要一点时间。如果很着急，例如服务器崩溃后，对于包含大量项目的集合，我们肯定不希望等待autosize完成对集合中所有对象的枚举。对于小的集合，性能问题还不是很突出，但对于需要长时间才能完成的命令，例如下面这个命令，性能的差异就很明显了。另外，在输出内容上的不同也很明显(但我们可能会觉得花些时间换来更美观的输出是有价值的)。
```powershell
PS C:\> Get-ChildItem C:\ -Recourse -Include *.log | Format-Table -property name, length, lastWriteTime -autosize
Name                                                                   Length LastWriteTime      
----                                                                   ------ -------------      
Install.log                                                             10018 2021/8/4 09:00:52  
Install.log_2021-7-16_7_46_26.log                                        6861 2020/12/18 11:34:49
Install.log_2021-8-4_8_45_31.log                                        10568 2021/7/16 07:52:26 
atikmdag_dce.log                                                      1463128 2021/12/30 08:58:46
debug.log                                                                 910 2021/12/26 16:34:40
auxlog.log                                                              73289 2019/9/5 13:20:58  
ps.log                                                                    320 2019/9/5 13:20:58  
windows-wmic.log                                                       171668 2019/9/5 13:20:58  
wmic.log                                                                 6513 2019/9/5 13:20:58  
isolate-0x102801e00-74163-v8.log                                       500526 2018/3/22 23:49:30 
isolate-0x103000000-74139-v8.log                                       660871 2018/3/22 23:48:36 
isolate-0x103000000-74561-v8.log                                       633352 2018/3/22 23:50:08 
auxlog.log                                                              73289 2016/10/18 08:29:06
ps.log                                                                    320 2016/4/26 12:52:04 
windows-wmic.log                                                       171668 2016/5/5 05:57:42  
wmic.log                                                                 6513 2016/4/26 14:38:02 
Install.log                                                            107322 2021/8/7 21:01:54  
kfcdetect.dll.log                                                        3734 2021/9/3 21:04:01  
rmb.log                                                                     2 2020/12/18 12:07:34
PhotoshopPrefsManager-20210807-210137.log                                1258 2021/8/7 21:01:37  
000003.log                                                               2515 2021/10/6 21:24:02 
000003.log                                                                517 2021/10/6 21:35:53 
000003.log                                                                  0 2021/10/6 21:23:04 
Dfrg.log                                                                  530 2021/9/3 21:04:41  
.log                                                                    42377 2021/12/22 15:05:31
000003.log                                                              15230 2021/9/25 10:51:57 
000003.log                                                            1406826 2021/7/31 13:37:44 
000003.log                                                              15330 2021/9/25 10:51:50 
000003.log                                                               2119 2021/9/25 10:51:57 
000003.log                                                                  0 2021/6/14 19:42:07 
```
在配合使用Format-Table时最后一个需要注意的问题是为Sort-Object cmdlet做准备。Sort-Object可以让我们通过属性对数据进行分类，然后用分类的方式显示出来。在本例中，我们为Sort-Object使用别名“sort”，这样可以减少不必要的输入。
在查看下列命令的时候，请注意一点：数据在发送给Format-Table cmdlet之前就已经被分类了，而且在默认情况下，Sort-Object cmdlet会进行升序排序(由小到大)。如果需要，可以使用-descending参数对组织好的的数据进行降序排列。
```powershell
PS C:\> Get-ChildItem C:\ -Recourse -Include *.log | Sort -Property length | Format-Table name, lastwritetime, length -AutoSize

Name                                                                  LastWriteTime        Length -AutoSize
----                                                                  -------------        ------ ---------
PASSWD.LOG                                                            2021/12/31 19:06:42       0          
setuperr.log                                                          2021/12/19 07:13:42       0          
000003.log                                                            2021/10/6 21:23:04        0          
setuperr.log                                                          2021/12/29 21:18:24       0          
setuperr.log                                                          2021/12/19 07:24:53       0          
000003.log                                                            2021/6/14 19:42:07        0          
setuperr.log                                                          2021/12/19 07:00:02       0          
setuperr.log                                                          2021/12/19 06:56:35       0          
setuperr.log                                                          2021/12/30 08:58:51       0          
PASSWD.LOG                                                            2021/12/20 16:17:53       0          
rmb.log                                                               2020/12/18 12:07:34       2          
setuperr.log                                                          2021/12/19 13:22:38     122          
DtcInstall.log                                                        2021/12/19 07:37:29     192
```
其实还有其他排列方法，例如，可以将日志文件的列表按照修改日期降序排列。这样做，就可以看到最近被修改过的日志文件。要实现这个目的，需要修改sort对象，而命令中的其他内容则是一样的。该命令的输出内容如下，从中可以看到，这些日志文件中的大部分都是在登录过程中修改的。
```powershell
PS C:\> Get-ChildItem C:\ -Recurse -Include *.log | Sort -Property lastwriteTime -descending | Format-Table name, lastwriteTime, length -AutoSize
Name                                                                  LastWriteTime        Length
----                                                                  -------------        ------
PASSWD.LOG                                                            2021/12/31 19:06:42       0
setuperr.log                                                          2021/12/19 07:13:42       0
000003.log                                                            2021/10/6 21:23:04        0
setuperr.log                                                          2021/12/29 21:18:24       0
setuperr.log                                                          2021/12/19 07:24:53       0
000003.log                                                            2021/6/14 19:42:07        0
setuperr.log                                                          2021/12/19 07:00:02       0
setuperr.log                                                          2021/12/19 06:56:35       0
setuperr.log                                                          2021/12/30 08:58:51       0
PASSWD.LOG                                                            2021/12/20 16:17:53       0
rmb.log                                                               2020/12/18 12:07:34       2
setuperr.log                                                          2021/12/19 13:22:38     122
```
在查看本书的资源时，可能会注意到，日志文件中有很多错误，这是因为Get-ChildItem cmdlet尝试访问受保护的目录和文件，因此导致有关拒绝访问的信息。
在开发过程中，这些错误信息非常有用，因为可以让我们知道无法访问的文件和文件夹，然而在分析数据的时候这些信息则会造成不小的困扰。
典型此类错误信息含有以下内容：
```powershell
Get-ChildItem : Access to the path 'C:\Windows\CSC' is denied. At line:1char:14
```
在告诉我们导致该错误的cldmet的名称以及导致了该错误的操作方面，这个错误信息很有用，不过我们可以对Get-ChildItem cmdlet使用-ErrorAction 公共参数，并指定SilentlyContinue的方式过滤这些信息。部分代码如下：
```powershell
PS C:\> Get-ChildItem C:\ -Recurse -include *.log -errorAction SilentlyContinue | Sort -Property lastwriteTime -descending | Format-Table name, lastwritetime, length -AutoSize
```
3. Format-Wide

Format-Wide cmdlet不像Format-Table或Format-List那么有用，因为每个对象只显示一个属性的显示方式存在的局限。但对于某些列表，该cmdlet也很有用。例如，假设只需要本机运行中的进程的列表，那么就可以使用Get-Process cmdlet，并通过管道将结果对象传递给Format-wide cmdlet。具体如下：
```powershell
PS C:\> Get-Process | Format-Wide

AggregatorHost                                                                             audiodg                                                                                   
ChsIME                                                                                     conhost                                                                                   
conhost                                                                                    csrss                                                                                     
csrss                                                                                      ctfmon                                                                                    
dasHost                                                                                    dllhost                                                                                   
dwm                                                                                        explorer                                                                                  
fontdrvhost                                                                                fontdrvhost                                                                               
Idle                                                                                       ielowutil                                                                                 
Intel_PIE_Service                                                                          lsass                                                                                     
Memory Compression                                                                         MicrosoftEdgeUpdate                                                                       
msedge                                                                                     msedge                                                                                    

```
输出的内容在控制台中使用了很多行，并导致了很多屏幕空间的浪费。因此这时更好的做法是使用-column参数，如下所示：
```powershell
PS C:\> Get-Process | Format-Wide -Column 4
```
虽然4列输出的方式将列表的长度减少了一半，不过依然没有充分利用屏幕空间。这时候可以考虑编写用于判断-column参数最适合值的脚本，例如，下文的DemoFormatWide.ps1脚本，虽然多了一些步骤，不过这些努力都是值得的。
DemoFormatWide.ps1
```powershell
function funGetProcess()
{
    if ($args)
    {
        Get-Process | 
        Format-Wide -autosize
    }
    else
    {
        Get-Process |
        Format-Wide -cloumn $i
    }
}

cls
$i= 1
for ($i ; $i -le 10 ; $i++)
{
    Write-Host -ForegroundColor red "`$i is equal to $i"
    funGetProcess
}
    Write-Host -ForeGroundColor red "Now use format-wide -autosize"
    funGetProcess("auto")
```
另一种判断最适合的屏幕输出选项的方式是对Format-Wide使用-autosize参数，如下所示：
```powershell
PS C:\> Get-Process | Format-Wide -AutoSize
```

### 1.9.3 使用Get-Command cmdlet
想使用Windows PowerShell，只需要记着最重要的3个cmdlet，即Get-Help、Get-Command以及Get-Member。通过调用3个cmdlet，我们就可以完全掌握Windows PowerShell。
Get-Command最基本的用法是生成Windows PowerShell可用命令的列表。如果希望快速了解有哪些cmdlet可用，那么这个命令会很有用。Get-Command的基本用法，
```powershell
PS C:\> Get-Command

CommandType     Name                                               Version    Source                                                                                                 
-----------     ----                                               -------    ------                                                                                                 
Alias           Add-AppPackage                                     2.0.1.0    Appx                                                                                                   
Alias           Add-AppPackageVolume                               2.0.1.0    Appx                                                                                                   
Alias           Add-AppProvisionedPackage                          3.0        Dism                                                                                                   
Alias           Add-ProvisionedAppPackage                          3.0        Dism                                                                                                   
Alias           Add-ProvisionedAppSharedPackageContainer           3.0        Dism                                                                                                   
Alias           Add-ProvisionedAppxPackage                         3.0        Dism                                                                                                   
Alias           Add-ProvisioningPackage                            3.0        Provisioning                                                                                           
Alias           Add-TrustedProvisioningCertificate                 3.0        Provisioning                                                                                           
Alias           Apply-WindowsUnattend                              3.0        Dism                                                                                                   
Alias           Disable-PhysicalDiskIndication                     2.0.0.0    Storage                                                                                                
```
默认情况下，Get-Command在创建cmdlet的列表方面功能有限，因此输出的内容非常繁杂。对于这个列表，最好的形式应该是用管道将结果对象传递给Format-List cmdlet，然后只选择显示其中的名称和描述信息。正如在下文的代码中可以看到的，这样输出的内容更容易读，而且对每个命令的定义也更直观明了：
```powershell
PS C:\> Get-Command | Format-List name, definition

Name       : Add-AppPackage
Definition : 

Name       : Add-AppPackageVolume
Definition : 

Name       : Add-AppProvisionedPackage
Definition : 

Name       : Add-ProvisionedAppPackage
Definition : 

Name       : Add-ProvisionedAppSharedPackageContainer
Definition : 

Name       : Add-ProvisionedAppxPackage
Definition : 

```
目前为止，我们已经了解Get-Command cmdlet的一些常规用法，然而对于cmdlet，还可以充分利用对动词和名词的认识，实现更有趣的做法。通过这些信息，我们可以寻找所有在cmdlet的名称中需要调用的“名词”的cmdlet，例如：
```powershell
PS C:\> Get-Command -Noun process

CommandType     Name                                               Version    Source                                                                                                 
-----------     ----                                               -------    ------                                                                                                 
Cmdlet          Debug-Process                                      3.1.0.0    Microsoft.PowerShell.Management                                                                        
Cmdlet          Get-Process                                        3.1.0.0    Microsoft.PowerShell.Management                                                                        
Cmdlet          Start-Process                                      3.1.0.0    Microsoft.PowerShell.Management                                                                        
Cmdlet          Stop-Process                                       3.1.0.0    Microsoft.PowerShell.Management                                                                        
Cmdlet          Wait-Process                                       3.1.0.0    Microsoft.PowerShell.Management                                                                        
```
如果希望找到名称的名词部分包含字母“p”的cmdlet，则可以通过使用通配符实现目的。这样做可以减少输入的工作量，并帮助查看所有可用cmdlet。该命令内容如下：
```powershell
PS C:\> Get-Command -Noun p*

CommandType     Name                                               Version    Source                                                                                                 
-----------     ----                                               -------    ------                                                                                                 
Alias           Add-ProvisionedAppPackage                          3.0        Dism                                                                                                   
Alias           Add-ProvisionedAppSharedPackageContainer           3.0        Dism                                                                                                   
Alias           Add-ProvisionedAppxPackage                         3.0        Dism                                                                                                   
Alias           Add-ProvisioningPackage                            3.0        Provisioning                                                                                           
Alias           Disable-PhysicalDiskIndication                     2.0.0.0    Storage                                                                                                
Alias           Disable-PhysicalDiskIndication                     1.0.0.0    VMDirectStorage                                                                                        
Alias           Enable-PhysicalDiskIndication                      2.0.0.0    Storage                                                                                                
Alias           Enable-PhysicalDiskIndication                      1.0.0.0    VMDirectStorage                                                                                        
Alias           Get-PhysicalDiskSNV                                2.0.0.0    Storage                                                                                                
Alias           Get-PhysicalDiskSNV                                1.0.0.0    VMDirectStorage                                                                                        
Alias           Get-ProvisionedAppPackage                          3.0        Dism                                                                                                   
Alias           Get-ProvisionedAppSharedPackageContainer           3.0        Dism                                                                                                   
Alias           Get-ProvisionedAppxPackage                         3.0        Dism                                                                                                   
Alias           Optimize-ProvisionedAppPackages                    3.0        Dism                                                                                                   
Alias           Optimize-ProvisionedAppxPackages                   3.0        Dism                                                                                                   
Alias           Remove-ProvisionedAppPackage                       3.0        Dism                                                                                                   
Alias           Remove-ProvisionedAppSharedPackageContainer        3.0        Dism                                                                                                   
Alias           Remove-ProvisionedAppxPackage                      3.0        Dism                                                                                                   
Alias           Remove-ProvisioningPackage                         3.0        Provisioning                                                                                           
Alias           Set-ProvisionedAppPackageDataFile                  3.0        Dism                                                                                                   
Alias           Set-ProvisionedAppXDataFile                        3.0        Dism                                                                                                   
Function        Add-PartitionAccessPath                            2.0.0.0    Storage                                                                                                
Function        Add-PhysicalDisk                                   2.0.0.0    Storage                                                                                                
Function        Add-Printer                                        1.1        PrintManagement                                                                                        
Function        Add-PrinterDriver                                  1.1        PrintManagement                                                                                        
Function        Add-PrinterPort                                    1.1        PrintManagement                                                                                        
Function        Clear-PcsvDeviceLog                                1.0.0.0    PcsvDevice                                                                                             
Function        Disable-PhysicalDiskIdentification                 2.0.0.0    Storage                                                                                                
````
默认情况下，Get-Command cmdlet只显示cmdlet的信息，然而该命令也可用于收集其他项目的信息，甚至.exe文件以及.dll文件。这是因为Get-Command会尝试显示可以在Windows Powershell中运行的所有内容的信息。例如在下面的例子中，会列出所有名称中包含“file”字样的命令。有一点需要记住：这样做只能显示Windwos Powershell中的实体。
```powershell
PS C:\> Get-Command -Name *file*

CommandType     Name                                               Version    Source                                                                                                 
-----------     ----                                               -------    ------                                                                                                 
Alias           Set-AppPackageProvisionedDataFile                  3.0        Dism                                                                                                   
Alias           Set-ProvisionedAppPackageDataFile                  3.0        Dism                                                                                                   
Alias           Set-ProvisionedAppXDataFile                        3.0        Dism                                                                                                   
Alias           Write-FileSystemCache                              2.0.0.0    Storage                                                                                                
Alias           Write-FileSystemCache                              1.0.0.0    VMDirectStorage                                                                                        
Function        Block-FileShareAccess                              2.0.0.0    Storage                                                                                                
Function        Clear-FileStorageTier                              2.0.0.0    Storage                                                                                                
Function        Close-SmbOpenFile                                  2.0.0.0    SmbShare                                                                                               
Function        Debug-FileShare                                    2.0.0.0    Storage                                                                                                
Function        Disable-NetIPHttpsProfile                          1.0.0.0    NetworkTransition                                                                                      
Function        Enable-NetIPHttpsProfile                           1.0.0.0    NetworkTransition                                                                                      
```
可以使用-commandType参数并限制只搜索cmdlet，以纠正这个问题，修改后的代码内容如下：
```powershell
PS C:\> Get-Command -Name *file* -CommandType cmdlet

CommandType     Name                                               Version    Source                                                                                                 
-----------     ----                                               -------    ------                                                                                                 
Cmdlet          Add-BitsFile                                       2.0.0.0    BitsTransfer                                                                                           
Cmdlet          New-FileCatalog                                    3.0.0.0    Microsoft.PowerShell.Security                                                                          
Cmdlet          New-PSRoleCapabilityFile                           3.0.0.0    Microsoft.PowerShell.Core                                                                              
Cmdlet          New-PSSessionConfigurationFile                     3.0.0.0    Microsoft.PowerShell.Core                                                                              
Cmdlet          Out-File                                           3.1.0.0    Microsoft.PowerShell.Utility                                                                           
Cmdlet          Set-AppXProvisionedDataFile                        3.0        Dism                                                                                                   
Cmdlet          Test-FileCatalog                                   3.0.0.0    Microsoft.PowerShell.Security                                                                          
Cmdlet          Test-PSSessionConfigurationFile                    3.0.0.0    Microsoft.PowerShell.Core                                                                              
Cmdlet          Unblock-File                                       3.1.0.0    Microsoft.PowerShell.Utility                                                                           
```
这些例子充分解释了使用Get-Command cmdlet可以搜索的内容类型。

### 1.9.4 使用Get-Member
Windows PowerShell提供的第3个重要的cmdlet是Get-Member。在将Get-Member作为3个重要的cmdlet之一介绍时，该命令能干什么。
使Get-Member如此有用的原因在于，该命令可以告诉我们某个对象支持哪些属性和方法。如果还记得上文提到过，Windows PowerShell中的所有东西都是对象，那么就可以领会这个命令的重要性了。不过也许一个简单的例子能更好地说明该命令的价值。
假设有一个名为“mytest”的文件夹，使用Get-Item cmdlet获得代表该文件夹的对象，以将该引用保存在名为$a的变量中，具体如下所示：
```powershell
PS C:\> $a = Get-Item C:\mytest
```
$a变量中有一个文件夹对象的实例后，可以用管道将该对象传递给Get-Member cmdlet，以了解这个文件夹对象可以支持的方法和属性。这行命令以及相应的输出内容如下：
```powershell
PS C:\> $a | Get-Member


   TypeName:System.IO.DirectoryInfo

Name                      MemberType     Definition                                                                                                                                  
----                      ----------     ----------                                                                                                                                  
LinkType                  CodeProperty   System.String LinkType{get=GetLinkType;}                                                                                                    
Mode                      CodeProperty   System.String Mode{get=Mode;}                                                                                                               
Target                    CodeProperty   System.Collections.Generic.IEnumerable`1[[System.String, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]] Ta...
Create                    Method         void Create(), void Create(System.Security.AccessControl.DirectorySecurity directorySecurity)                                               
CreateObjRef              Method         System.Runtime.Remoting.ObjRef CreateObjRef(type requestedType)                                                                             
CreateSubdirectory        Method         System.IO.DirectoryInfo CreateSubdirectory(string path), System.IO.DirectoryInfo CreateSubdirectory(string path, System.Security.AccessCo...
Delete                    Method         void Delete(), void Delete(bool recursive)                                                                                                  
EnumerateDirectories      Method         System.Collections.Generic.IEnumerable[System.IO.DirectoryInfo] EnumerateDirectories(), System.Collections.Generic.IEnumerable[System.IO....
EnumerateFiles            Method         System.Collections.Generic.IEnumerable[System.IO.FileInfo] EnumerateFiles(), System.Collections.Generic.IEnumerable[System.IO.FileInfo] E...
EnumerateFileSystemInfos  Method         System.Collections.Generic.IEnumerable[System.IO.FileSystemInfo] EnumerateFileSystemInfos(), System.Collections.Generic.IEnumerable[Syste...
Equals                    Method         bool Equals(System.Object obj)                                                                                                              
GetAccessControl          Method         System.Security.AccessControl.DirectorySecurity GetAccessControl(), System.Security.AccessControl.DirectorySecurity GetAccessControl(Syst...
```
从文件夹成员的列表中可以看到，有一个parent属性。可以使用parent属性信息了解mytest文件夹的父文件夹。具体如下：
```powershell
PS C:\> $a.parent

Mode                 LastWriteTime         Length Name                                                                                                                               
----                 -------------         ------ ----                                                                                                                               
d--hs-          2022/1/1  上午 09:23                C:\                                                                                                                                
```
假设需要知道该文件夹的上次访问时间，可以使用LastAccessTime属性，具体方式如下：
```powershell
PS C:\> $a.LastAccessTime

2021年12月8日 17:29:28

```
如果需要确保在$a变量中的对象确实是文件夹，则可以使用PsIsContainer属性。Get-Member输出内容中的PsIsContainer是布尔值，因此只能表示为true或false。具体如下所示：
```powershell
PS C:\> $a.PsIsContainer
True
```
如果希望使用返回的某种方法。例如，可以使用moveTo方法将文件夹移动到其他位置，Get-Member会告诉我们moveTo方法必须具有字符串输入，用以指出要移动到的目标位置。因此可以将“mytest”文件夹移动到“C:\moved-Folder”，然后使用Test-Path cmdlet检查文件夹是否被移动到新的位置。这些命令的内容如下：
```powershell
PS C:\> $a.MoveTo("C:\movedFolder")
PS C:\> Test-Path C:\movedFolder
True
PS C:\> Test-Path C:\mytest
False
PS C:\>
```
为了确认保存在$a变量中的对象对应的文件夹就是希望操作的文件夹，则可以使用Name属性，这个命令和相关的输出内容如下：
```powershell
PS C:\> $a.name
movedFolder
```
如果需要删除文件夹，则可以用delete方法，如下所示。为了确认文件夹已经被删除，还可以使用“dir m*”命令查看该文件夹是否依然存在。这些命令如下所示。请注意文件夹的删除结果。
```powershell
PS C:\> $a.Delete()
PS C:\> dir m*
```

# 第2章 Windows PowerShell脚本
本章目标：
+ 为Windows PowerShell配置脚本策略
+ 运行Windows PowerShell脚本
+ 使用Winows PowerShell流控制语句
+ 使用判断和条件分支语句
+ 识别和使用数据类型
+ 使用正则表达式提供高级匹配功能
+ 使用命令行参数

## 2.1 为什么使用脚本
对于很多网络管理来说，编写脚本(任何类型的脚本)，相较于服务器的管理，都是一项很头疼的工作。而且大部分公司都有专门的“脚本作者”，有时候甚至不止一个。
首先，脚本使得对特定命令进行记录变得很容易，如果需要对计算机上的所有共享资源生成列表，那么可以使用Win32_share WMI类，并使用Get-WmiObject cmdlet 获得所需的结果，具体如下：
```powerShell
PS C:\> Get-WmiObject Win32_share

Name   Path       Description
----   ----       -----------
ADMIN$ C:\WINDOWS 远程管理       
C$     C:\        默认共享       
D$     D:\        默认共享       
IPC$              远程 IPC     

```
但假设只是需要对所有共享文件创建列表又该怎么样？我们可能并不确定某个共享文件是否是type0共享。因此可能需要在Internet上搜索这些信息，在获得这些信息后，可以使用下列修改后的命令：
```powershell
PS C:\> Get-WmiObject win32_share -Filter "type = '0'"
Name………………

```
从上面的例子可以看出，只有type0的共享被列了出来，而相应的命令则只有一些很小的变化。

将命令配置为脚本的另一个优势在于更容易修改。相反，以前使用的命令存在限制，只能报告文件共享，但通过修改脚本可以让脚本同时还报告打印机共享、远程管理共享、IPC共享，甚至其他希望看到的共享类型。需要使用“if……else”语句查看是否为脚本提供了命令行参数。
**提示**
要检查命令行参数，请寻找$args变量，这是一个用于保存命令行脚本的自动变量。

如果存在命令行参数，则可以使用提供给命令行的值。如果在启动脚本的时候没有提供值，那莪必须给脚本提供默认值。对于这个脚本，可以列出文件共享，并提醒用户将使用默认值。Get-WmiObject的语法则和以前使用VBScript时的情况一样。在些脚本的时候，最好还设置显示usage字符串。下面的脚本GetSharesWithArgs.ps1中就包含协助用户输入正确语法的命令范例。
GetSharesWithArgs.ps1
```powershell
if($args)
{
    $type = $args
    Get-WmiObject win32_share -Filter "type = $type"
}
ELSE
{
    Write-Host
    "
    Using defaults values,file shares type = 0.
    Other valid types are:
    2147483651 for disk drive adminshare
    2147483649for print gueue admin share
    2147483650 for device admin share
    2147483651 for ipcs admin share
    Example:C:\GetSharesWithArgs.ps1'2147483651
    "
    Stype='0
    Get-WmiObject win32_share -Filter"type= $type"
}
```
网络管理员使用Windows PowerShell脚本的另一个原因在于，可以将脚本作为计划任务使用。在Windows的世界中存在很多任务计划引擎，例如通过使用Win32_ScheduledJob WMI类，可以创建、修改和删除计划任务。这个WMI类从Windows NT 4.0时代就提供了。
对于下文介绍的ListProcessesSortResults.ps1脚本，我们可能就会需要指定计划，每天运行该脚本多次。这个脚本可以对当前进程生成列表，并将结果写入文本文件中的表格里。
ListProcessSortResults.ps1
```powershell
$args = "localhost","loopback","127.0.0.1"

foreach ($i in $args)
{
$strFile = "C:\mytest\"+ $i +"Processes.txt"
Write-Host "Testing" $i "please wait……";
Get-WmiObject -computername $i -class win32_process |
Select-Object name, processID, Priority, ThreadCount, PageFaults,
    PageFileUsage |
Where-Object {!$_.processID -eq 0} | Sort-Object -property name | 
Format-Table | Out-File $strFile
}
```

## 2.2 配置脚本策略
因为Windows PowerShell中的脚本在默认情况下是不启动的，所以在部署任何脚本或命令之前，需要验证平台对脚本的支持级别。若没有启动支持就尝试运行脚本，就会看到错误信息，脚本也无法运行。这就是所谓的**限制执行策略**。
在Windows PowerShell中，通过使用Set-ExecutionPolicy cmdlet，可以设置4个不同级别的执行策略。
限制执行策略可以通过Active Directory目录服务组策略中的“打开脚本执行”策略进行设置，并且可以针对计算机对象和用户对象进行设置，同时计算机独享的策略设置会优先于用户对象的设置。
**提示**
要获得脚本执行策略的设置信息，请使用Get-ExecutionPolicy cmdlet。

















