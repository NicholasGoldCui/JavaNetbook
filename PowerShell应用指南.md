# windows PowerShell 使用指南

## 结构化命令

除了支持传统的Windows可执行文件，PowerShell还推出了一个功能强大的新的命令叫作cmdlet。所有的cmdlet命名规则遵循**动词-名词**这种语法结构，如Get-Process、Get-Content和Stop-Process。

```powershell
Ps C:\User>Get-Process -Name lsass
Handles     NPM(K)  PM(K)      WS(K)  VM(M)    CPU(s)      Id  ProcessName
-------     ------  -----      -----  -----    -------     ---  -------------
    688     13       6228      1660    46                  932      lsass
```

在上面的这个例子中，你为ProcessName参数提供了一个值，来获得指定名称的进程。
**注意**
一旦你掌握了一些PowerShell中常用的动词以后，学习如何使用新的名词就变得非常容易了。虽然你以前可能从来没有和某一个对象打过交道(例如，一个服务)，标准的动作如Get、Set、Start和Stop仍然适用。在输入命令时，不需要总是键入完整的命令名，可以用Tab键补全。
为提高效率，PowerShell为所有常用的命令定义了别名机制，并允许你自己定义别名。在这种机制下，PowerShell只需要你输入尽可能少的参数名称，只要不和后面的参数产生冲突就行。**PowerShell还区分大小写**。使用内置的gps别名(表示Get-Process命令)可以键入如下命令：

```powershell
ps > gps -n lsass
```

进一步，PowerShell支持在cmdlet上使用位置参数。位置参数允许你在参数指定的位置输入响应的参数，而不用输入参数名称。Get-Process命令支持名称的参数，让参数位于第一位，这个参数甚至支持通配符：

```powershell
PS>gps l*s
```

## 与对象深层次的集成

PowerShell在处理结构化的数据和对象时，向我们展示了强大的能力。例如，生成简单的文本字符串。由于没有输出，PowerShell直接将显示下面的内容：
        PS>"Hello World"
        Hello World

你刚刚生成的字符，事实上调用了.NET框架生成的对象，我们可以访问它的Length属性，这可以告诉你字符串中有多少个字符。要访问一个属性，在对象和属性名之间使用点(.)，如下：
PS>"Hello World".Length
11

所有powerShell命令都将生成的输出作为对象。例如，Get-Process cmdlet生成一个System.Dianostics.Process对象，你可以把它存储在一个变量中。在PowerShell中，**变量的名称都以$字符开头**。如果你有一个正在运行的记事本的实例，下面的命令存储对它的引用：
    $process = Get-Process notepad

由于这是一个进程对象，在.NET Framework中，你可以调用对象上的方法，对其执行操作。下面的**命令调用Kill()方法**，停止一个进程。若要访问一个方法，在对象和它的方法名之间放置一个圆点：
    $process.Kill()
PowerShell通过Stop-Process cmdlet更直接地支持此功能，但此示例演示的一个重要方面是有关你与这些丰富的对象进行交互的能力。

## 作为一流的系统管理员

虽然PowerShell对.NET Framework中对象的支持令大多数用户非常振奋，但PowerShell还是将重点放在管理员的任务上。例如，PowerShell支持MB(兆字节)和GB(千兆字节)作为标准的管理常量。例如，若要将一个40GB硬盘驱动器备份到CD-ROM需要多少磁盘？

```powershell
    PS>40GB / 650MB
    63.0153846153846
```

PowerShell是一个主要用于管理员的shell程序，但这并不意味着你不能使用.NET Framework用于管理任务！事实上，PowerShell提供了一个很好的日历。例如，要判断2008年是闰年？PowerShell可以告诉你：

```powershell
PS >[DateTime]::IsLeapYear(2008)
True
```

进一步，可能你要确定到夏季还有多久到来？下面的命令将“06/21/2008”(夏季的开始)转换为一个日期，然后用它减去当前日期。将结果存储在$result变量中，然后访问TotalDays属性。

```powershell
PS >$result = [DateTime] "06/21/2008" - [DateTime]::Now
PS >$result.TotalDays
283.0549284662616
```

## 可组合命令

每当一个命令生成输出时，可以使用一个管道字符(|)直接把该输出传递到另一个命令作为输入。如果第二个命令理解第一个命令所产生的对象，它可以操作结果。你可以通过作为这种方式把许多命令链接在一起，创建功能强大的输出。例如，下面的命令获取Path1目录中所有项并将它们移到Path2目录：

```powershell
Get-Item Path1\* | Move-Item -Destination Path2
```

通过将其他命令(cmdlet)添加到该管道中，你可以创建更复杂的命令。在例T-3中，第一个命令获取系统中运行的所有进程。然后将结果传递给Where-Object命令，针对每个传入的项进行比较。在这种情况下，比较的条件是$_.Handles -ge 500，检查当前对象(由$_变量表示)的Handles属性是否大于或等于500。对于此中比较结果为true的对象，将结果传递给Sort-Object命令，按它们的Handles属性排序。最后，将该对象传递给Format-Table命令生成一个表，其中包含进程的Handles、Name和Description。
例T-3：你可以通过使用管道链接cmdlet，构建更复杂的PowerShell命令，如下所示使用Get-Process、Where-Object、Sort-Object和Format-Table

```powershell
PS >Get-Process |
>>      Where-Object {$_.Handles -ge 500}
>>      Sort-Object Handles |
>>      Format-Table Hnadles,Name,Description -Auto
>>

Handles  Name       Description
-------  ----       -----------
    588  winlogon
    592  svchost
    667  lsass
    725  csrss
    742  System
    964  WINWORD    Microsoft Office Word
    1112 OuTLOOK    Microsoft Office Outlook
    2063 svchost
```

## 防止误操作的技术

虽然别名、通配符和串组合管道是功能强大的，但是在修改系统信息的命令中使用它们是非常伤脑筋的。毕竟，这个命令有什么作用是要考虑的，但是不能轻易尝试：

```powershell
PS > gps [b-t]*[c-r] | Stop-Process
```

它似乎是要停止所有以字母b到t为开头和以字母c到r结尾的进程。你如何确保命令的执行？PowerShell可以做到。对于那些修改数据的命令，PowerShell支持-WhatIf和-Confirm参数，允许你查看一个命令将执行什么操作：

```powershell
PS >gps [b-t]*[c-r] | Stop-Process -WhatIf
What if: performing operation "Stop-Process" on target "ctfmon (812)".
What if: performing operation "Stop-Process" on target "Ditto (1916)".
What if: performing operation "Stop-Process" on target "dsamin (316)".
What if: performing operation "Stop-Process" on target "ehrecvr (1832)".
What if: performing operation "Stop-Process" on target "ehSched (1852)".
What if: performing operation "Stop-Process" on target "EXCEL (2092)".
What if: performing operation "Stop-Process" on target "explorer (1900)".
(……)
```

在这个交互中，使用命令Stop-Process的-Whatif参数，允许你预览在实际执行该操作之前你的系统上的哪些进程将被停止。
**注意**
不要尝试允许上面的命令：
    该命令会尝试结束所有进程，但是在Vista中，还会强制执行关机，只留给你1分钟时间！
    这很有趣吧，至少我有足够的时间来保存全部内容！

## 常用的发现命令

虽然通过阅读一个指导性的教程很有帮助，但我发现大多数学习是以特殊的方式实现的。若要查找与给定的通配符匹配的所有命令，使用Get-command命令。例如，通过输入以下命令，你可以找到PowrShell命令(和Windows应用程序)中包含单词process的命令。

```powershell
PS >Get-Command *process*
CommandType     Name            Definition
----------      ----            -----------
Cmdlet          Get-Process     Get-Process [[-Name] <Str...
Application     qprocess.exe    c:\windows\system32\qproc...
Cmdlet          Stop-Process    Stop-Process [-Id] <Int32...
```

若要查看一个命令可以做什么，如Get-Process，可以使用Get-Help命令，如下：

```powershell
PS >Get-Help Get-Process
```

由于PowreShell允许你使用.NET Framework中的对象，所以它提供了Get-Member命令来检索一个对象的属性和方法，例如，一个.NET System.String支持的信息。将一个字符串用管道传给Get-Member命令，将显示它的类型名称和成员：

```powershell
PS C:\Users\你大爷> "Hello World" | Get-Member


   TypeName:System.String

Name             MemberType            Definition                                                                                                            
----             ----------            ----------                                                                                                            
Clone            Method                System.Object Clone(), System.Object ICloneable.Clone()
CompareTo        Method                int CompareTo(System.Object value), int CompareTo(string strB), int IComparable.CompareTo(System.Object obj), int I...
Contains         Method                bool Contains(string value)   
CopyTo           Method                void CopyTo(int sourceIndex, char[] destination, int destinationIndex, int count)
EndsWith         Method                bool EndsWith(string value), bool EndsWith(string value, System.StringComparison comparisonType), bool EndsWith(str...
Equals           Method                bool Equals(System.Object obj), bool Equals(string value), bool Equals(string value, System.StringComparison compar...
GetEnumerator    Method                System.CharEnumerator GetEnumerator(), System.Collections.IEnumerator IEnumerable.GetEnumerator(), System.Collectio...
GetHashCode      Method                int GetHashCode()
GetType          Method                type GetType()
GetTypeCode      Method                System.TypeCode GetTypeCode(), System.TypeCode IConvertible.GetTypeCode()
IndexOf          Method                int IndexOf(char value), int IndexOf(char value, int startIndex), int IndexOf(string value), int IndexOf(string val...
IndexOfAny       Method                int IndexOfAny(char[] anyOf), int IndexOfAny(char[] anyOf, int startIndex), int IndexOfAny(char[] anyOf, int startI...
Insert           Method                string Insert(int startIndex, string value)
IsNormalized     Method                bool IsNormalized(), bool IsNormalized(System.Text.NormalizationForm normalizationForm)
LastIndexOf      Method                int LastIndexOf(char value), int LastIndexOf(char value, int startIndex), int LastIndexOf(string value), int LastIn...
LastIndexOfAny   Method                int LastIndexOfAny(char[] anyOf), int LastIndexOfAny(char[] anyOf, int startIndex), int LastIndexOfAny(char[] anyOf...
Normalize        Method                string Normalize(), string Normalize(System.Text.NormalizationForm normalizationForm)
PadLeft          Method                string PadLeft(int totalWidth), string PadLeft(int totalWidth, char paddingChar)
PadRight         Method                string PadRight(int totalWidth), string PadRight(int totalWidth, char paddingChar)
Remove           Method                string Remove(int startIndex, int count), string Remove(int startIndex)
Replace          Method                string Replace(char oldChar, char newChar), string Replace(string oldValue, string newValue)
Split            Method                string[] Split(Params char[] separator), string[] Split(char[] separator, int count), string[] Split(char[] separat...
StartsWith       Method                bool StartsWith(string value), bool StartsWith(string value, System.StringComparison comparisonType), bool StartsWi...
Substring        Method                string Substring(int startIndex), string Substring(int startIndex, int length)
ToBoolean        Method                bool IConvertible.ToBoolean(System.IFormatProvider provider)
ToByte           Method                byte IConvertible.ToByte(System.IFormatProvider provider)
ToChar           Method                char IConvertible.ToChar(System.IFormatProvider provider)
ToCharArray      Method                char[] ToCharArray(), char[] ToCharArray(int startIndex, int length)
ToDateTime       Method                datetime IConvertible.ToDateTime(System.IFormatProvider provider)
ToDecimal        Method                decimal IConvertible.ToDecimal(System.IFormatProvider provider)
ToDouble         Method                double IConvertible.ToDouble(System.IFormatProvider provider)
ToInt16          Method                int16 IConvertible.ToInt16(System.IFormatProvider provider)
ToInt32          Method                int IConvertible.ToInt32(System.IFormatProvider provider)
ToInt64          Method                long IConvertible.ToInt64(System.IFormatProvider provider)
ToLower          Method                string ToLower(), string ToLower(cultureinfo culture)
ToLowerInvariant Method                string ToLowerInvariant()   
ToSByte          Method                sbyte IConvertible.ToSByte(System.IFormatProvider provider)
ToSingle         Method                float IConvertible.ToSingle(System.IFormatProvider provider)
ToString         Method                string ToString(), string ToString(System.IFormatProvider provider), string IConvertible.ToString(System.IFormatPro...
ToType           Method                System.Object IConvertible.ToType(type conversionType, System.IFormatProvider provider)
ToUInt16         Method                uint16 IConvertible.ToUInt16(System.IFormatProvider provider)
ToUInt32         Method                uint32 IConvertible.ToUInt32(System.IFormatProvider provider)
ToUInt64         Method                uint64 IConvertible.ToUInt64(System.IFormatProvider provider)
ToUpper          Method                string ToUpper(), string ToUpper(cultureinfo culture)
ToUpperInvariant Method                string ToUpperInvariant()   
Trim             Method                string Trim(Params char[] trimChars), string Trim()
TrimEnd          Method                string TrimEnd(Params char[] trimChars)
TrimStart        Method                string TrimStart(Params char[] trimChars)         
Chars            ParameterizedProperty char Chars(int index) {get;}           
Length           Property              int Length {get;}
```

## 无处不在的脚本

在命令行上键入的命令和在脚本中的命令之间没有区别。在脚本中，你喜欢的命令和你喜欢的脚本技术(例如，foreach语句)直接作用在命令上。例如，要**统计**所有正在运行在进程的**句柄数**：

```powershell
PS>$handleCount = 0
PS>foreach($process in Get-Process) { $handleCount += $process.Handles}
PS>$handleCount
19403
```

PowerShell提供一个命令(Measure-Object)，以测量统计集合的信息，下面这个简短的示例演示了PowerShell允许你应用的通常需要一种单独的脚本或编程语言完成的技术。除了使用PowerShell脚本关键字之外，你也可以直接创建和处理.NET FrameWork中的对象。PowerShell几乎成为了Visual Studio中的与C#一样的即时模式。

## 特殊开发

通过模糊交互管理和编写脚本之间的界线的方法，PowerShell会话的历史记录缓冲区很快就会变成特殊脚本开发的基础。在此示例中，调用Get-History命令来检索你的会话的历史纪录。对于每一项来说，你都可以获得它的CommandLine属性(你键入的操作)，并将输出内容发送给新的脚本文件。

```powershell
PS>Get-History | Foreach-object {$_.CommandLine} > C:\temp\script.psl(save the content you want to keep)
PS>C:\temp\script.psl
```

**注意**

如果这是你第一次在PowerShell中运行一个脚本，你需要配置你的执行策略。

## 技术的桥梁

我们已经看到PowerShell如何让你可以完全利用.NET Framework，但它对常见的技术提供了进一步的支持。

例如，PowerShell支持XML:

```powershell
PS>$xmlContent = [xml] $content
PS>$xmlContent
xml           xml-stylesheet        rss
---           --------------        ---

PS>$xmlContent.rss
version : 2.0
dc : http://purl.org/dc/elements/1.1
slash : http://purl.org/rss/1.0/modules/slash
wfw : http://wellformedweb.org/CommentAPI
channel : channel

PS>$xmlContent.rss.channel.item | select Title
title
-----
CMD.exe compatibility
Time Stamping Log Files
Microsoft Compute Cluster now has a PowerShell Provider and Cmdlets
The Virtuous Cycle:.NET Developers using PowerShell
(...)
```

PowerShell也允许你使用Windows管理设备(Windows Management Instrumentation,WMI)：

```powershell
PS C:\Users\你大爷> Get-WmiObject Win32_Bios


SMBIOSBIOSVersion : N.1.20MRV14
Manufacturer      : American Megatrends Inc.
Name              : N.1.20MRV14
SerialNumber      : xxxxxxxxxxxxxxxxxxxx
Version           : ALASKA - 1072009
```

例如，显示了对活动目录服务接口(Active Directory Service Interfaces, ADSI)的支持：

PowerShell中使用活动目录

```powershell
PS C:\Users\你大爷> [ADSI] "WinNT://./Administrator" | Format-List *


UserFlags                  : {66051}
MaxStorage                 : {-1}
PasswordAge                : {0}
PasswordExpired            : {0}
LoginHours                 : {255 255 255 255 255 255 255 255 255 255 255 255 255 255 255 255 255 255 255 255 255}
FullName                   : {}
Description                : {管理计算机(域)的内置帐户}
BadPasswordAttempts        : {0}
LastLogin                  : {2020/12/18 14:21:48}
HomeDirectory              : {}
LoginScript                : {}
Profile                    : {}
HomeDirDrive               : {}
Parameters                 : {}
PrimaryGroupID             : {513}
Name                       : {Administrator}
MinPasswordLength          : {0}
MaxPasswordAge             : {3628800}
MinPasswordAge             : {0}
PasswordHistoryLength      : {0}
AutoUnlockInterval         : {1800}
LockoutObservationInterval : {1800}
MaxBadPasswordsAllowed     : {0}
objectSid                  : {1 5 0 0 0 0 0 5 21 0 0 0 223 138 17 218 155 46 108 14 94 7 240 247 244 1 0 0}
AuthenticationType         : Secure
Children                   : {}
Guid                       : {D83F1060-1E71-11CF-B1F3-02608C9E7553}
ObjectSecurity             : 
NativeGuid                 : {D83F1060-1E71-11CF-B1F3-02608C9E7553}
NativeObject               : System.__ComObject
Parent                     : WinNT://WORKGROUP/.
Password                   : 
Path                       : WinNT://./Administrator
Properties                 : {UserFlags, MaxStorage, PasswordAge, PasswordExpired...}
SchemaClassName            : User
SchemaEntry                : System.DirectoryServices.DirectoryEntry
UsePropertyCache           : True
Username                   : 
Options                    : 
Site                       : 
Container                  : 
```

正如例T-7显示的，甚至可以对传统的COM对象编写脚本：

例T-7：PowerShell中使用COM对象

```powershell
PS C:\Users\你大爷> $firewall = New-Object -com HNetCfg.FwMgr

PS C:\Users\你大爷> $firewall.LocalPolicy.CurrentProfile


Type                                         : 1
FirewallEnabled                              : True
ExceptionsNotAllowed                         : False
NotificationsDisabled                        : False
UnicastResponsesToMulticastBroadcastDisabled : False
RemoteAdminSettings                          : System.__ComObject
IcmpSettings                                 : System.__ComObject
GloballyOpenPorts                            : System.__ComObject
Services                                     : System.__ComObject
AuthorizedApplications                       : System.__ComObject
```

## 通过提供程序导航命名空间

PowerShell提供了另一个处理系统的方法是提供程序(providers)。PowerShell提供程序允许我们使用与操作文件系统相同的技术来导航和管理数据存储去，如例T-8所示。

例T-8：导航文件系统:

```powershell
PS C:\Users\你大爷> Set-Location c:\

PS C:\> Get-ChildItem


    目录: C:\


Mode                 LastWriteTime         Length Name                                                                                                       
----                 -------------         ------ ----                                                                                                       
d-----         2021/6/14  上午 11:16                AMD                                                                                                        
d-----          2021/9/3  下午 09:11                MyDrivers                                                                                                  
d-----          2021/8/6  下午 07:25                NVIDIA                                                                                                     
d-----        2021/10/24  下午 08:17                PerfLogs                                                                                                   
d-r---        2021/10/30  上午 12:49                Program Files                                                                                              
d-r---        2021/10/30  下午 10:27                Program Files (x86)                                                                                        
d-r---        2021/10/30  上午 12:50                Users                                                                                                      
d-----        2021/10/31  上午 11:12                Windows                                                                                                    
d-----        2021/10/30  上午 12:55                Windows.old                                                                                                
-a----         2021/6/21  下午 05:23           2358 Program1    
```

也可用于注册表，如例T-9所示：

例T-9：导航注册表

```powershell
PS C:\> Set-Location HKCU:\Software\Microsoft\Windows\

PS HKCU:\Software\Microsoft\Windows\> Get-ChildItem


    Hive: HKEY_CURRENT_USER\Software\Microsoft\Windows


Name                           Property                                                                                                                      
----                           --------                                                                                                                      
AssignedAccessConfiguration                                                                                                                                  
CurrentVersion                                                                                                                                               
DWM                            Composition                  : 1                                                                                              
                               ColorizationGlassAttribute   : 1                                                                                              
                               AccentColor                  : 4280104024                                                                                     
                               ColorPrevalence              : 1                                                                                              
                               EnableAeroPeek               : 1                                                                                              
                               ColorizationColor            : 3294114845                                                                                     
                               ColorizationColorBalance     : 89                                                                                             
                               ColorizationAfterglow        : 3294114845                                                                                     
                               ColorizationAfterglowBalance : 10                                                                                             
                               ColorizationBlurBalance      : 1                                                                                              
                               EnableWindowColorization     : 1                                                                                              
Shell                                                                                                                                                        
TabletPC                                                                                                                                                     
Windows Error Reporting                                                                                                                                      
Winlogon                           
```

甚至可用于计算机的证书存储区，如例T-10所示。

例T-10：浏览证书存储区

```powershell
PS C:\Users\你大爷> Set-Location Cert:\CurrentUser\Root

PS Cert:\CurrentUser\Root> Get-ChildItem


   PSParentPath:Microsoft.PowerShell.Security\Certificate::CurrentUser\Root

Thumbprint                                Subject                                                                                                            
----------                                -------                                                                                                            
CDD4EEAE6000AC7F40C3802C171E30148030C072  CN=Microsoft Root Certificate Authority, DC=microsoft, DC=com                                                      
BE36A4562FB2EE05DBB3D32323ADF445084ED656  CN=Thawte Timestamping CA, OU=Thawte Certification, O=Thawte, L=Durbanville, S=Western Cape, C=ZA                  
A43489159A520F0D93D032CCAF37E7FE20A8B419  CN=Microsoft Root Authority, OU=Microsoft Corporation, OU=Copyright (c) 1997 Microsoft Corp.                       
92B46C76E13054E104F230517E6E504D43AB10B5  CN=Symantec Enterprise Mobile Root for Microsoft, O=Symantec Corporation, C=US                                     
8F43288AD272F3103B6FB1428485EA3014C0BCFE  CN=Microsoft Root Certificate Authority 2011, O=Microsoft Corporation, L=Redmond, S=Washington, C=US               
7F88CD7223F3C813818C994614A89C99FA3B5247  CN=Microsoft Authenticode(tm) Root Authority, O=MSFT, C=US                                                         
3B1EFD3A66EA28B16697394703A72CA340A05BD5  CN=Microsoft Root Certificate Authority 2010, O=Microsoft Corporation, L=Redmond, S=Washington, C=US               
31F9FC8BA3805986B721EA7295C65B3A44534274  CN=Microsoft ECC TS Root Certificate Authority 2018, O=Microsoft Corporation, L=Redmond, S=Washington, C=US        
245C97DF7514E7CF2DF8BE72AE957B9E04741E85  OU=Copyright (c) 1997 Microsoft Corp., OU=Microsoft Time Stamping Service Root, OU=Microsoft Corporation, O=Micr...
18F7C1FCC3090203FD5BAA2F861A754976C8DD25  OU="NO LIABILITY ACCEPTED, (c)97 VeriSign, Inc.", OU=VeriSign Time Stamping Service Root, OU="VeriSign, Inc.", O...
06F1AA330B927B753A40E68CDF22E34BCBEF3352  CN=Microsoft ECC Product Root Certificate Authority 2018, O=Microsoft Corporation, L=Redmond, S=Washington, C=US   
0119E81BE9A14CD8E22F40AC118C687ECBA3F4D8  CN=Microsoft Time Stamp Root Certificate Authority 2014, O=Microsoft Corporation, L=Redmond, S=Washington, C=US    
FEB8C432DCF9769ACEAE3DD8908FFD288665647D  OU=Security Communication EV RootCA1, O="SECOM Trust Systems CO.,LTD.", C=JP                                       
FD1ED1E2021B0B9F73E8EB75CE23436BBCC746EB  CN=D-TRUST Root Class 3 CA 2007, O=D-Trust GmbH, C=DE                                                              
FAB7EE36972662FB2DB02AF6BF03FDE87C4B2F9B  OU=certSIGN ROOT CA, O=certSIGN, C=RO                                                                              
FAA7D9FB31B746F200A85E65797613D816E063B5  CN=VRK Gov. Root CA, OU=Varmennepalvelut, OU=Certification Authority Services, O=Vaestorekisterikeskus CA, S=Fin...
FA0882595F9CA6A11ECCBEAF65C764C0CCC311D0  CN=Certeurope Root CA 2, OU=0002 434202180, O=Certeurope, C=FR                                                     
F9DD19266B2043F1FE4B3DCB0190AFF11F31A69D  CN=Correo Uruguayo - Root CA, OU=SERVICIOS ELECTRONICOS, O=ADMINISTRACION NACIONAL DE CORREOS, C=UY                
F9CD0E2CDA7624C18FBDF0F0ABB645B8F7FED57A  C=IL, O=ComSign, CN=ComSign Secured CA                                                                             
F9B5B632455F9CBEEC575F80DCE96E2CC7B278B7  CN=AffirmTrust Commercial, O=AffirmTrust, C=US                                                                     
F48B11BFDEABBE94542071E641DE6BBE882B40B9  O=Government Root Certification Authority, C=TW                                                                    
F44095C238AC73FC4F77BF8F98DF70F8F091BC52  CN=Class 3TS Primary CA, O=Certplus, C=FR                                                                          
F18B538D1BE903B6A6F056435B171589CAF36BF2  CN=thawte Primary Root CA - G3, OU="(c) 2008 thawte, Inc. - For authorized use only", OU=Certification Services ...
EC93DE083C93D933A986B3D5CDE25ACB2FEECF8E  CN=NetLock Platina (Class Platinum) Főtanúsítvány, OU=Tanúsítványkiadók (Certification Services), O=NetLock Kft....
E621F3354379059A4B68309D8A2F74221587EC79  CN=GeoTrust Universal CA, O=GeoTrust Inc., C=US                                                                    
E2B8294B5584AB6B58C290466CAC3FB8398F8483  CN=CFCA EV ROOT, O=China Financial Certification Authority, C=CN                                                   
E1A45B141A21DA1A79F41A42A961D669CD0634C1  C=IL, O=ComSign, CN=ComSign CA                                                                                     
E12DFB4B41D7D9C32B30514BAC1D81D8385E2D46  CN=UTN-USERFirst-Object, OU=http://www.usertrust.com, O=The USERTRUST Network, L=Salt Lake City, S=UT, C=US        
E0B4322EB2F6A568B654538448184A5036874384  C=ES, O=EDICOM, OU=PKI, CN=ACEDICOM Root                                                                           
DF646DCB7B0FD3A96AEE88C64E2D676711FF9D5F  CN=TWCA Root Certification Authority, OU=Root CA, O=TAIWAN-CA, C=TW                                                
DF3C24F9BFD666761B268073FE06D1CC8D4F82A4  CN=DigiCert Global Root G2, OU=www.digicert.com, O=DigiCert Inc, C=US                                              
DE990CED99E0431F60EDC3937E7CD5BF0ED9E5FA  CN=Cisco Root CA 2048, O=Cisco Systems                                                                             
DE3F40BD5093D39B6C60F6DABC076201008976C9  CN=QuoVadis Root Certification Authority, OU=Root Certification Authority, O=QuoVadis Limited, C=BM                
DE28F4A4FFE5B92FA3C503D1A349A7F9962A8212  CN=GeoTrust Global CA, O=GeoTrust Inc., C=US                                                                       
DDE1D2A901802E1D875E84B3807E4BB1FD994134  CN=e-Guven Kok Elektronik Sertifika Hizmet Saglayicisi, O=Elektronik Bilgi Guvenligi A.S., C=TR                    
DD83C519D43481FAD4C22C03D702FE9F3B22F517  E=acraiz@suscerte.gob.ve, OU=Superintendencia de Servicios de Certificacion Electronica, O=Sistema Nacional de C...
DAC9024F54D8F6DF94935FB1732638CA6AD77C13  CN=DST Root CA X3, O=Digital Signature Trust Co.                                                                   
D8C5388AB7301B1B6ED47AE645253A6F9F1A2761  CN=SwissSign Gold CA - G2, O=SwissSign AG, C=CH                                                                    
D8A6332CE0036FB185F6634F7D6A066526322827  CN=AffirmTrust Premium, O=AffirmTrust, C=US                                                                        
D6DAA8208D09D2154D24B52FCB346EB258B28A58  CN=Hongkong Post Root CA 1, O=Hongkong Post, C=HK                                                                  
D6BF7994F42BE5FA29DA0BD7587B591F47A44F22  CN=Posta CA Root, CN=AIA, CN=Public Key Services, CN=Services, CN=Configuration, DC=ca, DC=posta, DC=rs            
D69B561148F01C77C54578C10926DF5B856976AD  CN=GlobalSign, O=GlobalSign, OU=GlobalSign Root CA - R3                                                            
D4DE20D05E66FC53FE1A50882C78DB2852CAE474  CN=Baltimore CyberTrust Root, OU=CyberTrust, O=Baltimore, C=IE                                                     
D2EDF88B41B6FE01461D6E2834EC7C8F6C77721E  CN=Class 3 Primary CA, O=Certplus, C=FR                                                                            
D2441AA8C203AECAA96E501F124D52B68FE4C375  OU=I.CA - Accredited Provider of Certification Services, O="První certifikační autorita, a.s.", CN="I.CA - Quali...
D23209AD23D314232174E40D7F9D62139786633A  OU=Equifax Secure Certificate Authority, O=Equifax, C=US                                                           
D1EB23A46D17D68FD92564C2F1F1601764D8E349  CN=AAA Certificate Services, O=Comodo CA Limited, L=Salford, S=Greater Manchester, C=GB                            
D1CBCA5DB2D52A7F693B674DE5F05A1D0C957DF0  CN=USERTrust ECC Certification Authority, O=The USERTRUST Network, L=Jersey City, S=New Jersey, C=US               
CF9E876DD3EBFC422697A3B5A37AA076A9062348  CN=TWCA Root Certification Authority, OU=Root CA, O=TAIWAN-CA, C=TW                                                
CEA9890D85D80753A626286CDAD78CB566D70CF2  CN=ANF Server CA, SERIALNUMBER=G63287510, OU=ANF Clase 1 CA, O=ANF Autoridad de Certificación, L=Barcelona (see ...
CBA1C5F8B0E35EB8B94512D3F934A2E90610D336  CN=AC Raíz Certicámara S.A., O=Sociedad Cameral de Certificación Digital - Certicámara S.A., C=CO                  
CB658264EA8CDA186E1752FB52C397367EA387BE  CN=Echoworx Root CA2, OU=Certification Services, O=Echoworx Corporation, L=Toronto, S=Ontario, C=CA                
CB44A097857C45FA187ED952086CB9841F2D51B5  CN=Common Policy, OU=FBCA, O=U.S. Government, C=us                                                                 
CABD2A79A1076A31F21D253635CB039D4329A5E8  CN=ISRG Root X1, O=Internet Security Research Group, C=US                                                          
CABB51672400588E6419F1D40878D0403AA20264  CN=SecureSign RootCA1, O="Japan Certification Services, Inc.", C=JP                                                
CA3AFBCF1240364B44B216208880483919937CF7  CN=QuoVadis Root CA 2, O=QuoVadis Limited, C=BM                                                                    
C9A8B9E755805E58E35377A725EBAFC37B27CCD7  E=pki@sk.ee, CN=EE Certification Centre Root CA, O=AS Sertifitseerimiskeskus, C=EE                                 
C9321DE6B5A82666CF6971A18A56F2D3A8675602  CN=E-ME SSI (RCA), OU=Sertifikacijas pakalpojumu dala, C=LV                                                        
C09AB0C8AD7114714ED5E21A5A276ADCD5E7EFCB  CN=ANCERT Certificados Notariales, O=Agencia Notarial de Certificacion S.L. Unipersonal - CIF B83395988, C=ES      
BEB5A995746B9EDF738B56E6DF437A77BE106B81  CN=S-TRUST Authentication and Encryption Root CA 2005:PN, O=Deutscher Sparkassen Verlag GmbH, L=Stuttgart, S=Bad...
BE36A4562FB2EE05DBB3D32323ADF445084ED656  CN=Thawte Timestamping CA, OU=Thawte Certification, O=Thawte, L=Durbanville, S=Western Cape, C=ZA                  
B94294BF91EA8FB64BE61097C7FB001359B676CB  CN=Certification Authority of WoSign, O=WoSign CA Limited, C=CN                                                    
B865130BEDCA38D27F69929420770BED86EFBC10  OU=AC RAIZ FNMT-RCM, O=FNMT-RCM, C=ES                                                                              
B8236B002F1D16865301556C11A437CAEBFFC3BB  CN=AffirmTrust Premium ECC, O=AffirmTrust, C=US                                                                    
B80186D1EB9C86A54104CF3054F34C52B7E558C6  CN=XRamp Global Certification Authority, O=XRamp Security Services Inc, OU=www.xrampsecurity.com, C=US             
B51C067CEE2B0C3DF855AB2D92F4FE39D4E70F0E  CN=Starfield Root Certificate Authority - G2, O="Starfield Technologies, Inc.", L=Scottsdale, S=Arizona, C=US      
B38FECEC0B148AA686C3D00F01ECC8848E8085EB  CN=AC RAIZ DNIE, OU=DNIE, O=DIRECCION GENERAL DE LA POLICIA, C=ES                                                  
B31EB1B740E36C8402DADC37D44DF5D4674952F9  CN=Entrust Root Certification Authority, OU="(c) 2006 Entrust, Inc.", OU=www.entrust.net/CPS is incorporated by ...
B1EAC3E5B82476E9D50B1EC67D2CC11E12E0B491  OU=POSTArCA, O=POSTA, C=SI                                                                                         
B1BC968BD4F49D622AA89A81F2150152A41D829C  CN=GlobalSign Root CA, OU=Root CA, O=GlobalSign nv-sa, C=BE                                                        
B172B1A56D95F91FE50287E14D37EA6A4463768A  CN=UTN-USERFirst-Client Authentication and Email, OU=http://www.usertrust.com, O=The USERTRUST Network, L=Salt L...
B12E13634586A46F1AB2606837582DC4ACFD9497  CN=Certigna, O=Dhimyotis, C=FR                                                                                     
AFE5D244A8D1194230FF479FE2F897BBCD7A8CB4  CN=COMODO RSA Certification Authority, O=COMODO CA Limited, L=Salford, S=Greater Manchester, C=GB                  
AEC5FB3FC8E1BFC4E54F03075A9AE800B7F7B6FA  CN=Autoridad de Certificacion Firmaprofesional CIF A62634068, C=ES                                                 
AD7E1C28B064EF8F6003402014C3D0E3370EB58A  OU=Starfield Class 2 Certification Authority, O="Starfield Technologies, Inc.", C=US                               
ACED5F6553FD25CE015F1F7A483B6A749F6178C6  CN=NetLock Kozjegyzoi (Class A) Tanusitvanykiado, OU=Tanusitvanykiadok, O=NetLock Halozatbiztonsagi Kft., L=Buda...
AB16DD144ECDC0FC4BAAB62ECF0408896FDE52B7  O=Prvni certifikacni autorita a.s., CN=I.CA - Standard root certificate, C=CZ                                      
AADBBC22238FC401A127BB38DDF41DDB089EF012  CN=thawte Primary Root CA - G2, OU="(c) 2007 thawte, Inc. - For authorized use only", O="thawte, Inc.", C=US       
A9E9780814375888F20519B06D2B0D2B6016907D  CN=GeoTrust Global CA 2, O=GeoTrust Inc., C=US                                                                     
A8985D3A65E5E5C4B2D7D66D40C6DD2FB19C5436  CN=DigiCert Global Root CA, OU=www.digicert.com, O=DigiCert Inc, C=US                                              
A59C9B10EC7357515ABB660C4D94F73B9E6E9272  CN=Certipost E-Trust Primary Normalised CA, O=Certipost s.a./n.v., C=BE                                            
A1E7C600AA4170E5B74BC94F9B9703EDC261B4B9  CN=SwissSign Platinum Root CA - G3, O=SwissSign AG, C=CH                                                           
A0F8DB3F0BF417693B282EB74A6AD86DF9D448A3  CN=PostSignum Root QCA 2, O="Česká pošta, s.p. [IČ 47114983]", C=CZ                                                
A073E5C5BD43610D864C21130A855857CC9CEA46  CN=Root CA Generalitat Valenciana, OU=PKIGVA, O=Generalitat Valenciana, C=ES                                       
9F744E9F2B4DBAEC0F312C50B6563B8E2D93C311  CN=COMODO ECC Certification Authority, O=COMODO CA Limited, L=Salford, S=Greater Manchester, C=GB                  
9C615C4D4D85103A5326C24DBAEAE4A2D2D5CC97  CN=KEYNECTIS ROOT CA, OU=ROOT, O=KEYNECTIS, C=FR                                                                   
9BAAE59F56EE21CB435ABE2593DFA7F040D11DCB  CN=SwissSign Silver CA - G2, O=SwissSign AG, C=CH                                                                  
97817950D81C9670CC34D809CF794431367EF474  CN=GTE CyberTrust Global Root, OU="GTE CyberTrust Solutions, Inc.", O=GTE Corporation, C=US                        
971D3486FC1E8E6315F7C6F2E12967C724342214  CN=VI Registru Centras RCSC (RootCA), OU=Registru Centro Sertifikavimo Centras, O=VI Registru Centras - I.k. 124...
968338F113E36A7BABDD08F7776391A68736582E  OU=Application CA G2, O=LGPKI, C=JP                                                                                
925A8F8D2C6D04E0665F596AFF22D863E8256F3F  CN=Starfield Services Root Certificate Authority - G2, O="Starfield Technologies, Inc.", L=Scottsdale, S=Arizona...
91C6D6EE3E8AC86384E548C299295C756C817B81  CN=thawte Primary Root CA, OU="(c) 2006 thawte, Inc. - For authorized use only", OU=Certification Services Divis...
9158C5EF987301A8903CFDAB03D72DA1D88909C9  CN=Actalis Authentication CA G1, O=Actalis S.p.A./03358520967, L=Milano, C=IT                                      
912198EEF23DCAC40939312FEE97DD560BAE49B1  CN=Verizon Global Root CA, OU=OmniRoot, O=Verizon Business, C=US                                                   
90DECE77F8C825340E62EBD635E1BE20CF7327DD  OU=I.CA - Provider of Certification Services, O="První certifikační autorita, a.s.", CN="I.CA - Standard Certifi...
905F942FD9F28F679B378180FD4F846347F645C1  CN=Federal Common Policy CA, OU=FPKI, O=U.S. Government, C=US                                                      
8EB03FC3CF7BB292866268B751223DB5103405CB  CN=SecureSign RootCA3, O="Japan Certification Services, Inc.", C=JP                                                
8D1784D537F3037DEC70FE578B519A99E610D7B0  CN=GeoTrust Primary Certification Authority - G2, OU=(c) 2007 GeoTrust Inc. - For authorized use only, O=GeoTrus...
8D08FC43C0770CA84F4DCCB2D41A5D956D786DC4  CN=SwissSign Silver Root CA - G3, O=SwissSign AG, C=CH                                                             
8CF427FD790C3AD166068DE81E57EFBB932272D4  CN=Entrust Root Certification Authority - G2, OU="(c) 2009 Entrust, Inc. - for authorized use only", OU=See www....
8C96BAEBDD2B070748EE303266A0F3986E7CAE58  C=TR, O=EBG Bilişim Teknolojileri ve Hizmetleri A.Ş., CN=EBG Elektronik Sertifika Hizmet Sağlayıcısı               
8BAF4C9B1DF02A92F7DA128EB91BACF498604B6F  CN=CNNIC ROOT, O=CNNIC, C=CN                                                                                       
89DF74FE5CF40F4A80F9E3377D54DA91E101318E  E=info@e-szigno.hu, CN=Microsec e-Szigno Root CA 2009, O=Microsec Ltd., L=Budapest, C=HU                           
8782C6C304353BCFD29692D2593E7D44D934FF11  CN=SecureTrust CA, O=SecureTrust Corporation, C=US                                                                 
85A408C09C193E5D51587DCDD61330FD8CDE37BF  CN=Deutsche Telekom Root CA 2, OU=T-TeleSec Trust Center, O=Deutsche Telekom AG, C=DE                              
85371CA6E550143DCE2803471BDE3A09E8F8770F  OU=VeriSign Trust Network, OU="(c) 1998 VeriSign, Inc. - For authorized use only", OU=Class 3 Public Primary Cer...
8250BED5A214433A66377CBC10EF83F669DA3A67  CN=UCA Root, O=UniTrust, C=CN                                                                                      
80BF3DE9A41D768D194B293C85632CDBC8EA8CF7  CN=ComSign Advanced Security CA                                                                                    
8094640EB5A7A1CA119C1FDDD59F810263A7FBD1  CN=GlobalSign, O=GlobalSign, OU=GlobalSign Root CA - R6                                                            
8025EFF46E70C8D472246584FE403B8A8D6ADBF5  CN=TC TrustCenter Class 3 CA II, OU=TC TrustCenter Class 3 CA, O=TC TrustCenter GmbH, C=DE                         
7FBB6ACD7E0AB438DAAF6FD50210D007C6C0829C  CN=Halcom CA PO 2, O=Halcom, C=SI                                                                                  
7FB9E2C995C97A939F9E81A07AEA9B4D70463496  OU=sigov-ca, O=state-institutions, C=si                                                                            
7F8AB0CFD051876A66F3360F47C88D8CD335FC74  OU=ApplicationCA, O=Japanese Government, C=JP                                                                      
7F8A77836BDC6D068F8B0737FCC5725413068CA4  CN=Autoridad de Certificacion de la Abogacia, O=Consejo General de la Abogacia NIF:Q-2863006I, C=ES                
7E784A101C8265CC2DE1F16D47B440CAD90A1945  CN=Equifax Secure Global eBusiness CA-1, O=Equifax Secure Inc., C=US                                               
786A74AC76AB147F9C6A3050BA9EA87EFE9ACE3C  CN=Chambers of Commerce Root - 2008, O=AC Camerfirma S.A., SERIALNUMBER=A82743287, L=Madrid (see current address...
75E0ABB6138512271C04F85FDDDE38E4B7242EFE  CN=GlobalSign, O=GlobalSign, OU=GlobalSign Root CA - R2                                                            
742CDF1594049CBF17A2046CC639BB3888E02E33  CN=Certipost E-Trust Primary Qualified CA, O=Certipost s.a./n.v., C=BE                                             
742C3192E607E424EB4549542BE1BBC53E6174E2  OU=Class 3 Public Primary Certification Authority, O="VeriSign, Inc.", C=US                                        
74207441729CDD92EC7931D823108DC28192E2BB  CN=Class 2 Primary CA, O=Certplus, C=FR                                                                            
705D2B4565C7047A540694A79AF7ABB842BDC161  CN=Autoridade Certificadora Raiz Brasileira v1, OU=Instituto Nacional de Tecnologia da Informacao - ITI, O=ICP-B...
70179B868C00A4FA609152223F9F3E32BDE00562  CN=Visa eCommerce Root, OU=Visa International Service Association, O=VISA, C=US                                    
6E3A55A4190C195C93843CC0DB722E313061F0B1  CN=Chambers of Commerce Root, OU=http://www.chambersign.org, O=AC Camerfirma SA CIF A82743287, C=EU                
6B81446A5CDDF474A0F800FFBE69FD0DB6287516  CN=AdminCA-CD-T01, OU=Certification Authorities, OU=Services, O=admin, C=CH                                        
6A174570A916FBE84453EED3D070A1D8DA442829  CN=Class 1 Primary CA, O=Certplus, C=FR                                                                            
67650DF17E8E7E5B8240A4F4564BCFE23D69C6F0  OU=ePKI Root Certification Authority, O="Chunghwa Telecom Co., Ltd.", C=TW                                         
64902AD7277AF3E32CD8CC1DC79DE1FD7F8069EA  O="První certifikační autorita, a.s.", CN=I.CA - Qualified root certificate, C=CZ                                  
627F8D7827656399D27D7F9044C9FEB3F33EFA9A  E=premium-server@thawte.com, CN=Thawte Premium Server CA, OU=Certification Services Division, O=Thawte Consultin...
6252DC40F71143A22FDE9EF7348E064251B18118  CN=Certum CA, O=Unizeto Sp. z o.o., C=PL                                                                           
61EF43D77FCAD46151BC98E0C35912AF9FEB6311  CN=VeriSign Class 2 Public Primary Certification Authority - G3, OU="(c) 1999 VeriSign, Inc. - For authorized us...
60D68974B5C2659E8A0FC1887C88D246691B182C  E=igca@sgdn.pm.gouv.fr, CN=IGC/A, OU=DCSSI, O=PM/SGDN, L=Paris, S=France, C=FR                                     
5FB7EE0633E259DBAD0C4C9AE6D38F1A61C7DC25  CN=DigiCert High Assurance EV Root CA, OU=www.digicert.com, O=DigiCert Inc, C=US                                   
5F43E5B1BFF8788CAC1CC7CA4A9AC6222BCC34C6  CN=Cybertrust Global Root, O="Cybertrust, Inc"                                                                     
5F3B8CF2F810B37D78B4CEEC1919C37334B9C774  OU=Security Communication RootCA2, O="SECOM Trust Systems CO.,LTD.", C=JP                                          
5F3AFC0A8B64F686673474DF7EA9A2FEF9FA7A51  CN=Swisscom Root CA 1, OU=Digital Certificate Services, O=Swisscom, C=ch                                           
5D003860F002ED829DEAA41868F788186D62127F  CN=Starfield Services Root Certificate Authority, OU=http://certificates.starfieldtech.com/repository/, O="Starf...
5A5A4DAF7861267C4B1F1E67586BAE6ED4FEB93F  CN=SSC Root CA A, OU=Certification Authority, O=Skaitmeninio sertifikavimo centras, C=LT                           
5A4D0E8B5FDCFDF64E7299A36C060DB222CA78E4  CN=Visa Information Delivery Root CA, OU=Visa International Service Association, O=VISA, C=US                      
59AF82799186C7B47507CBCF035746EB04DDB716  CN=Staat der Nederlanden Root CA - G2, O=Staat der Nederlanden, C=NL                                               
5922A1E15AEA163521F898396A4646B0441B0FA9  CN=OISTE WISeKey Global Root GA CA, OU=OISTE Foundation Endorsed, OU=Copyright (c) 2005, O=WISeKey, C=CH           
56E0FAC03B8F18235518E5D311CAE8C24331AB66  CN=SwissSign Platinum CA - G2, O=SwissSign AG, C=CH                                                                
55C86F7414AC8BDD6814F4D86AF15F3710E104D0  OU=Netrust CA1, O=Netrust Certificate Authority 1, C=SG                                                            
51501FBFCE69189D609CFAF140C576755DCC1FDF  CN=Hotspot 2.0 Trust Root CA - 03, O=WFA Hotspot 2.0, C=US                                                         
503006091D97D4F5AE39F7CBE7927D7D652D3431  CN=Entrust.net Certification Authority (2048), OU=(c) 1999 Entrust.net Limited, OU=www.entrust.net/CPS_2048 inco...
4F99AA93FB2BD13726A1994ACE7FF005F2935D1E  CN=China Internet Network Information Center EV Certificates Root, O=China Internet Network Information Center, ...
4EB6D578499B1CCF5F581EAD56BE3D9B6744A5E5  CN=VeriSign Class 3 Public Primary Certification Authority - G5, OU="(c) 2006 VeriSign, Inc. - For authorized us...
4ABDEEEC950D359C89AEC752A12C5B29F6D6AA0C  CN=Global Chambersign Root - 2008, O=AC Camerfirma S.A., SERIALNUMBER=A82743287, L=Madrid (see current address a...
4A3F8D6BDC0E1ECFCD72E377DEF2D7FF92C19BC7  E=Info@izenpe.com, CN=Izenpe.com, L=Avda del Mediterraneo Etorbidea 3 - 01010 Vitoria-Gasteiz, O=IZENPE S.A. - C...
4A058FDFD761DB21B0C2EE48579BE27F42A4DA1C  CN=D-TRUST Root Class 2 CA 2007, O=D-Trust GmbH, C=DE                                                              
47BEABC922EAE80E78783462A79F45C254FDE68B  CN=Go Daddy Root Certificate Authority - G2, O="GoDaddy.com, Inc.", L=Scottsdale, S=Arizona, C=US                  
43F9B110D5BAFD48225231B0D0082B372FEF9A54  OU=FNMT Clase 2 CA, O=FNMT, C=ES                                                                                   
4313BB96F1D5869BC14E6A92F6CFF63469878237  CN=TeliaSonera Root CA v1, O=TeliaSonera                                                                           
42EFDDE6BFF35ED0BAE6ACDD204C50AE86C4F4FA  CN=A-Trust-Qual-03, OU=A-Trust-Qual-03, O=A-Trust Ges. f. Sicherheitssysteme im elektr. Datenverkehr GmbH, C=AT    
409D4BD917B55C27B69B64CB9822440DCD09B889  CN=Juur-SK, O=AS Sertifitseerimiskeskus, C=EE, E=pki@sk.ee                                                         
3E84D3BCC544C0F6FA19435C851F3F2FCBA8E814  CN=SSC Root CA B, OU=Certification Authority, O=Skaitmeninio sertifikavimo centras, C=LT                           
3E42A18706BD0C9CCF594750D2E4D6AB0048FDC4  OU=sigen-ca, O=state-institutions, C=si                                                                            
3E2BF7F2031B96F38CE6C4D8A85D3E2D58476A0F  CN=StartCom Certification Authority, OU=Secure Digital Certificate Signing, O=StartCom Ltd., C=IL                  
3BC49F48F8F373A09C1EBDF85BB1C365C7D811B3  CN=SecureSign RootCA11, O="Japan Certification Services, Inc.", C=JP                                               
3BC0380B33C3F6A60C86152293D9DFF54B81C004  OU=Trustis FPS Root CA, O=Trustis Limited, C=GB                                                                    
3A44735AE581901F248661461E3B9CC45FF53A1B  CN=Secure Global CA, O=SecureTrust Corporation, C=US                                                               
398EBE9C0F46C079C3C7AFE07A2FDD9FAE5F8A5C  E=acraiz@suscerte.gob.ve, OU=Superintendencia de Servicios de Certificacion Electronica, O=Sistema Nacional de C...
3921C115C15D0ECA5CCB5BC4F07D21D8050B566A  CN=America Online Root Certification Authority 1, O=America Online Inc., C=US                                      
3913853E45C439A2DA718CDFB6F3E033E04FEE71  CN=ECRaizEstado, O=SCEE, C=PT                                                                                      
37F76DE6077C90C5B13E931AB74110B4F2E49A27  CN=Sonera Class2 CA, O=Sonera, C=FI                                                                                
379A197B418545350CA60369F33C2EAF474F2079  CN=GeoTrust Universal CA 2, O=GeoTrust Inc., C=US                                                                  
36B12B49F9819ED74C9EBC380FC6568F5DACB2F7  OU=Security Communication RootCA1, O=SECOM Trust.net, C=JP                                                         
3679CA35668772304D30A5FB873B0FA77BB70D54  CN=VeriSign Universal Root Certification Authority, OU="(c) 2008 VeriSign, Inc. - For authorized use only", OU=V...
34D499426F9FC2BB27B075BAB682AAE5EFFCBA74  E=acrse@economia.gob.mx, O=Secretaria de Economia, OU=Direccion General de Normatividad Mercantil, CN=Autoridad ...
342CD9D3062DA48C346965297F081EBC2EF68FDC  E=info@globaltrust.info, CN=GLOBALTRUST, OU=GLOBALTRUST Certification Service, O=ARGE DATEN - Austrian Society f...
339B6B1450249B557A01877284D9E02FC3D2D8E9  CN=Global Chambersign Root, OU=http://www.chambersign.org, O=AC Camerfirma SA CIF A82743287, C=EU                  
323C118E1BF7B8B65254E2E2100DD6029037F096  CN=GeoTrust Primary Certification Authority, O=GeoTrust Inc., C=US                                                 
30779E9315022E94856A3FF8BCF815B082F9AEFD  CN=Izenpe.com, O=IZENPE S.A., C=ES                                                                                 
2E14DAEC28F0FA1E8E389A4EABEB26C00AD383C3  CN=Certinomis - Autorité Racine, OU=0002 433998903, O=Certinomis, C=FR                                             
2B8F1B57330DBBA2D07A6C51F70EE90DDAB9AD8E  CN=USERTrust RSA Certification Authority, O=The USERTRUST Network, L=Jersey City, S=New Jersey, C=US               
2AC8D58B57CEBF2F49AFF2FC768F511462907A41  CN=CA Disig, O=Disig a.s., L=Bratislava, C=SK                                                                      
293621028B20ED02F566C532D1D6ED909F45002F  CN=AffirmTrust Networking, O=AffirmTrust, C=US                                                                     
28903A635B5280FAE6774C0B6DA7D6BAA64AF2E8  CN=EC-ACC, OU=Jerarquia Entitats de Certificacio Catalanes, OU=Vegeu https://www.catcert.net/verarrel (c)03, OU=...
2796BAE63F1801E277261BA0D77770028F20EEE4  OU=Go Daddy Class 2 Certification Authority, O="The Go Daddy Group, Inc.", C=US                                    
253F775B0E7797AB645F15915597C39E263631D1  CN=Admin-Root-CA, OU=Certification Authorities, OU=Services, O=admin, C=ch                                         
24A40A1F573643A67F0A4B0749F6A22BF28ABB6B  OU=VeriSign Commercial Software Publishers CA, O="VeriSign, Inc.", L=Internet                                      
23E833233E7D0CC92B7C4279AC19C2F474D604CA  CN=SSC Root CA C, OU=Certification Authority, O=Skaitmeninio sertifikavimo centras, C=LT                           
23E594945195F2414803B4D564D2A3A3F5D88B8C  E=server-certs@thawte.com, CN=Thawte Server CA, OU=Certification Services Division, O=Thawte Consulting cc, L=Ca...
2388C9D371CC9E963DFF7D3CA7CEFCD625EC190D  CN=Microsec e-Szigno Root CA, OU=e-Szigno CA, O=Microsec Ltd., L=Budapest, C=HU                                    
22D5D8DF8F0231D18DF79DB7CF8A2D64C93F6C3A  CN=VeriSign Class 3 Public Primary Certification Authority - G4, OU="(c) 2007 VeriSign, Inc. - For authorized us...
216B2A29E62A00CE820146D8244141B92511B279  CN=Class 3P Primary CA, O=Certplus, C=FR                                                                           
211165CA379FBB5ED801E31C430A62AAC109BCB4  CN=Registradores de España - CA Raíz, OU=Certificado Propio, O=Colegio de Registradores de la Propiedad y Mercan...
204285DCF7EB764195578E136BD4B7D1E98E46A5  CN=VeriSign Class 1 Public Primary Certification Authority - G3, OU="(c) 1999 VeriSign, Inc. - For authorized us...
1F4914F7D874951DDDAE02C0BEFD3A2D82755185  CN=QuoVadis Root CA 3, O=QuoVadis Limited, C=BM                                                                    
1F24C630CDA418EF2069FFAD4FDD5F463A1B69AA  CN=GlobalSign, O=GlobalSign, OU=GlobalSign ECC Root CA - R5                                                        
1B4B396126276B6491A2686DD70243212D1F1D96  CN=TÜBİTAK UEKAE Kök Sertifika Hizmet Sağlayıcısı - Sürüm 3, OU=Kamu Sertifikasyon Merkezi, OU=Ulusal Elektronik...
16D86635AF1341CD34799445EB603E273702965D  CN=SITHS CA v3, O=Carelink, C=SE                                                                                   
132D0D45534B6997CDB2D5C339E25576609B5CC6  CN=VeriSign Class 3 Public Primary Certification Authority - G3, OU="(c) 1999 VeriSign, Inc. - For authorized us...
11C5B5F75552B011669C2E9717DE6D9BFF5FA810  CN=ANCERT Certificados CGN, O=Agencia Notarial de Certificacion S.L. Unipersonal - CIF B83395988, C=ES             
101DFA3FD50BCBBB9BB5600C1955A41AF4733A04  CN=Staat der Nederlanden Root CA, O=Staat der Nederlanden, C=NL                                                    
0CFD83DBAE44B9A0C8F676F3B570650B94B69DBF  CN=ANCERT Corporaciones de Derecho Publico, O=Agencia Notarial de Certificacion S.L. Unipersonal - CIF B83395988...
0B972C9EA6E7CC58D93B20BF71EC412E7209FABF  CN=UCA Global Root, O=UniTrust, C=CN                                                                               
0B7199A1C7F3ADDF7BA7EAB8EB574AE80D60DDDE  CN=SwissSign Gold Root CA - G3, O=SwissSign AG, C=CH                                                               
086418E906CEE89C2353B6E27FBD9E7439F76316  CN=VAS Latvijas Pasts SSI(RCA), OU=Sertifikacijas pakalpojumi, O=VAS Latvijas Pasts - Vien.reg.Nr.40003052790, C=LV
07E032E020B72C3F192F0628A2593A19A70F069E  CN=Certum Trusted Network CA, OU=Certum Certification Authority, O=Unizeto Technologies S.A., C=PL                 
06083F593F15A104A069A46BA903D006B7970991  CN=NetLock Arany (Class Gold) Főtanúsítvány, OU=Tanúsítványkiadók (Certification Services), O=NetLock Kft., L=Bu...
0563B8630D62D75ABBC8AB1E4BDFB5A899B24D43  CN=DigiCert Assured ID Root CA, OU=www.digicert.com, O=DigiCert Inc, C=US                                          
0560A2C738FF98D1172A94FE45FB8A47D665371E  CN=Certipost E-Trust TOP Root CA, O=Certipost s.a./n.v., C=BE                                                      
0483ED3399AC3608058722EDBC5E4600E3BEF9D7  CN=UTN-USERFirst-Hardware, OU=http://www.usertrust.com, O=The USERTRUST Network, L=Salt Lake City, S=UT, C=US      
0456F23D1E9C43AECB0D807F1C0647551A05F456  O=ACNLB, C=SI                                                                                                      
0409565B77DA582E6495AC0060A72354E64B0192  CN=Halcom CA FO, O=Halcom, C=SI                                                                                    
039EEDB80BE7A03C6953893B20D2D9323A4C2AFD  CN=GeoTrust Primary Certification Authority - G3, OU=(c) 2008 GeoTrust Inc. - For authorized use only, O=GeoTrus...
02FAF3E291435468607857694DF5E45B68851868  CN=AddTrust External CA Root, OU=AddTrust External TTP Network, O=AddTrust AB, C=SE                                
027268293E5F5D17AAA4B3C3E6361E1F92575EAA  CN=KISA RootCA 1, OU=Korea Certification Authority Central, O=KISA, C=KR                                           
016897E1A0B8F2C3B134665C20A727B7A158E28F  E=info@netlock.hu, CN=NetLock Minositett Kozjegyzoi (Class QA) Tanusitvanykiado, OU=Tanusitvanykiadok, O=NetLock...
F8DB7E1C16F1FFD4AAAD4AAD8DFF0F2445184AEB  CN=Microsoft Development Root Certificate Authority 2014, O=Microsoft Corporation, L=Redmond, S=Washington, C=US   
6CA22E5501CC80885FF281DD8B3338E89398EE18  CN=Microsoft ECC Development Root Certificate Authority 2018, O=Microsoft Corporation, L=Redmond, S=Washington, ...
```

## 更多的内容

这个指导性的教程是令人振奋的，介绍了PowerShell可以是你提高工作效率和系统管理技能的一些亮点。

<br/>

# 第二部分

## 基础知识

# 第1章 Windows PowerShell 交互界面

### 1.1 运行程序、脚本和已有的工具

**问题**

你依赖着那些已经花费你很多精力来学习使用的工具。想使用PowerShell，但又不想放弃已经有的工具。

**解决方案**

在系统路径下运行程序、脚本、批处理文件或其他可执行文件，可以键入其文件名。对于可执行类型的文件，可以不用输入扩展名，如：

```powershell
Program.exe arguments
ScripName.psl arguments
BatchFile.cmd arguments
```

运行那些在命名中包含空格的命令，需要用引号(')将命令括起来，同时在前面加上符号(&)，这在PowerShell中称作调用操作(Invoke operator)，如：

```powershell
&'C:\Program Files\Program\Program.exe' arguments
```

运行当前目录下的命令，在文件名前添加.\，如：

```powershell
.\Program.exe arguments
```

在当前目录下运行那些命令名中包含空格的命令，同时加上符合(&)和 .\，如：

```powershell
& '.\Program With Spaces.exe' arguments
```

**讨论**

在这个案例中，解决方案主要关注的是如何使用你以前使用过的工具。

在PowerShell中，将文本用引号括起来，可以让你在提示符下进行复杂的运算，如例1-1所示：

例1-1：在PowerShell提示符下，进行表达式运算

```powershell
PS>1+1
2
PS>"Hello World"
Hello World
```

所以说，在引号中的程序的名称与其他在引号中的字符没有区别，都是一个表达式。如前面解决方案所示，运行一个包含在字符串中的命令的方法是在字符串前面加上前缀(&)。

**注意**

默认情况下，PowerShell的安全策略会阻止脚本的运行。一旦你开始编写或运行脚本的时候，你就需要配置策略来减少限制。

<br/>

## 1.2 运行PowerShell命令

**问题**

你要运行PowerShell命令

**解决方案**

在命令提示符下键入你要运行的命令的名字，如：

```powershell
PS C:\Users\你大爷> Get-Process

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName                                                                                        
-------  ------    -----      -----     ------     --  -- -----------                                                                                        
     95       6     1448       7256              5440   0 AggregatorHost                                                                                     
    310      19     6288      32772       0.52  10480   1 ApplicationFrameHost                                                                               
    254      13     2440      12088              2960   1 atieclxx                                                                                           
    181       9     1516       6704              2668   0 atiesrxx                                                                                           
    586      15    12600      21292      34.47   1716   0 audiodg                                                                                            
    145       8     1480       9024       0.02   4152   1 ChsIME                                                                                             
    107       7     5348       1656       0.02   4320   1 conhost                                                                                            
     83       7     5196       9500              4548   0 conhost                                                                                            
    623      22     1968       6292               836   0 csrss                                                                                              
    751      22     4172       7328               976   1 csrss                                                                                              
    735      32    16756      59628      26.80  10540   1 ctfmon                                                                                             
    292      14     3492      10776              4860   0 dasHost                                                                                            
    253      25     6540      15180       0.27   8660   1 dllhost                                                                                            
   1583    1354   138292     119416              1436   1 dwm                                                                                                
   2795     122   104952     194280     102.75   7848   1 explorer                                                                                           
     38       8     5136      29356              1204   1 fontdrvhost       
```

**讨论**

Get-Process命令是一个内置的PowerShell命令，我们称之为cmdlet。它与传统命令的区别在于，cmdlet为管理员和开发人员提供了重要的功能：

- 采用统一的命令语法。

- 支持丰富的管道功能(将一个命令的输出作为另外一个命令的输入)。

- 输出易于管理的对象，而不是容易出错的文本。

由于Get-Process命令生成了丰富的基于对象的输出，所以你可以利用这个输出来执行一些与进程相关的任务。

Get-Process命令仅仅是PowerShell支持的命令之一。

## 1.3 自定义shell、配置文件与提示符

**问题**

你希望通过个性化的提示符、别名来定制PowerShell的交换shell。

**解决方案**

你可以通过将个性化的内容放到你的个人配置脚本文件中来实现PowerShell shell的个性化。PowerShell将脚本文件的地址存放在$profile变量中，你可以通过这个变量来访问你的脚本文件。

**注意**

默认情况下，PowerShell的安全策略会阻止脚本(包括你的配置脚本)的运行。一旦你开始编写或运行脚本，你就需要配置策略来减少限制。

<br/>

创建一个新的配置脚本（或者覆盖已经存在的配置脚本），可以i输入下面的命令：

```powershell
New-Item -type file -force $profile
```

编辑已经存在的配置脚本，可以输入下面的命令：

```powershell
notepad $profile
```

查看你的配置脚本文本的内容，可以输入下面的命令：

```powershell
get-ChileItem $profile
```

当你创建了配置脚本文件后，你可以添加一个名为Prompt的函数，用来返回一个字符串。PowerShell会将Prompt函数返回的字符串作为提示符。

```powershell
function Prompt
{
  "PS [$env:COMPUTENAME] >"
}
```

这个例子会在提示符中显示计算机的名字，看起来像：PS[计算机的名字] >

你会发现在配置文件中加入别名会对你有很大帮助。别名可以让你根据自己选择的名字来引用常用的命令。你可以在个人的配置脚本文件中定义别名、函数、变量或其他你可能在PowerShell提示符下设置的参数。别名可能是你在脚本文件中最常配置的项，通过别名，你可以把PowerShell的命令(或你的脚本)用容易输入的名字来引用。

**注意**

如果你在为命令定义别名的同时，需要修改命令的参数，建议你用函数来替代别名。

例如：

```powershell
Set-Alias new New-Object
Set-Alias iexplore 'C:\Program Files\Interent Explorer\iexplore.exe'
```

你对配置文件的修改会在你保存配置文件并重新启动PowerShell后生效。如果你希望修改马上生效，可以键入下面的命令：

```powershell
,$profile
```

函数也经常用来进行个性化定制，比较受欢迎的是自定义Prompt函数。
