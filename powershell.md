## 2.1 powershell管道和重定向
1. 管道
把上一条命令的输出作为下一条命令的输入
例如通过ls获取当前目录的所有文件信息，然后通过sort-descending对文件信息按照name降序排列，最后将排序好的文件的name和mode格式化成table输出。
