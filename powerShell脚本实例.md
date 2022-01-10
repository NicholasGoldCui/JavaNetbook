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
