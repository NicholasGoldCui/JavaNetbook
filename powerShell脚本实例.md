## 2.7 for语句
PingARange.ps1
该脚本是一个很有用的小工具，它可以用于ping一系列IP地址，并告诉我们目标计算机是否响应Internet控制消息协议(Internet Control Message Protocal,ICMP)数据包。这个脚本非常适用于网络**发现**或确保计算机可以正常进行网络通信。
$intPing变量被设置为“10”，并被定义为整数。随后，$intNetWork变量会被指派字符串127.0.0，并被定义为字符串。

随后需要使用for语句执行代码块，执行的次数由$intPing变量的值决定。这个计数器变量是在for语句行创建的，该计数器变量名为$i，并且被赋值“1”。只要$i的值不超过$intPing变量设置的值，脚本就会持续运行。最后一步，还需要完成for语句中的求值部分，这部分会给$i变量的值加“1”。

这部分代码块以大括号开始，在代码块内部，首先需要创建一个名为$strQuery的变量，该变量中的字符串保存着WMI查询的内容。将这部分内容放在一个单独的变量中，可以令配合$intNetwork使用$i计数器变量的操作变得更容易，这些内容可以用于为对ping结果进行的WMI查询创建有效的IP地址。

随后使用$wmi变量保存由Get-WmiObject cmdlet返回的对象集合。通过使用Get-WmiObject cmdlet的optional query参数，就可以进行所需的WMI查询。另外，StatusCode属性中包含了ping操作的结果，“0”代表成功，任何其他数值代表ping操作失败。为了用更清晰的方式显示这些信息，还可以使用“if……else”语句计算StatusCode属性。
PingARange.ps1
```powershell
[int]$intping =10 [string]$intNetwork = "172.0.0."
for ($i=1; $i -le $intPing; $i++)
{
  $strQuery = "select * from win32_pingstatus where address = '" + $intNetwork + $i + "'"
  $wmi = Get-WmiObject -query $strQuery
  "Pingping $intNetwork...."
  if ($wmi.statuscode -eq 0)
    {"success"}
    else
      {"error: " + $wmi.statuscode + " occrred"}
}
```

### 3.2.1 输出到文本
将事件日志内容保存到文本文件中，然后就可以使用记事本的查找工具搜索，并获得日志中的特定内容。
虽然这种方法相对更有用一些，但还有一种更有趣的方法，可以利用switch语句的文本处理功能。这种方法可以参考下面的ParseAppTextLog.ps1脚本。该方法就像统计所有事件日志类型的数量一样简单，例如在ParseAppTextLog.ps1中的用法，或者也可以用在更复杂的脚本中，使用正则表达式对详细内容执行复杂的搜索。
在ParseAppTextLog.ps1中，首先声明一个名为$strLog的变量，该变量用于保存导出的事件日志文件的路径。然后，声明下列计数器变量：$e、$i和$w，这个语法非常简单，如下所示：
```powershell
$e=$i=$w=0
```
将所有计数器变量都设置为0后，开始处理switch语句。在这里可以使用switch的一些高级功能，例如通过-file参数为其指定文本文件，或者使用-wildcard参数在搜索中使用通配符。switch会对文本文件的内容进行遍历检查，并搜索所有包含“error”字样的字符串内容。如果搜索到“error”字样，就将变量$e的值增加1.搜索功能还会寻找包含“info”字样的内容，如果找到了，就将$i变量的值增加1.最后，搜索功能会寻找包含“warn”字样的内容，如果找到了，则将$w变量的值增加1。
在到达Applog.txt文件的结尾后，脚本会使用Write-Output cmdlet 将摘要信息输出。在Write-Output 后有一个开始的双引号，脚本移动到下一行，并输出包含在$strLog变量中的路径。最后，脚本会给输出的错误、信息以及警告信息中输入必要的说明文字。
PareseAppTextLog.ps1
```powershell
$strLog = "c:\fso\applog.txt" $e=$i=$w=0
switch -wildcard -file $strLog {
"*error*" { $e++ }
"*info*" { $i++ }
"*warn*" { $w++ }
}
Write-Output "
$strLog contains the following:
    errors     $e
    warnings   $w
    information   $i
"
```
