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
Windows PowerShell的有用功能之一就是cmdlet可用语法的标准化。这个功能极大地简化了新外壳和语言的学习难度。


















