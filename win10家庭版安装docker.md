# 执行安装Hyper-V命令

新建文件以.cmd结尾，粘贴以下内容到文件中，然后以管理员身份运行。

```powershell
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```



# 启用Hyper-V

找到windows10 功能中心 勾选所有Hyper-V相关的内容



# 修改注册表

计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion 下的EditionID由原来的Code改为Professional



重新执行安装程序即可

```shell
bcdedit /set hypervisorlaunchtype off

```

