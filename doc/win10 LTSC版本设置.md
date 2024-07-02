# Win10 LTSC版本设置

## 激活
- 管理员权限打开CMD
- 输入 `slmgr -ipk M7XTQ-FN8P6-TTKYV-9D4CC-J462D`
```
Win10企业版用户可以尝试输入以下代码：
1.W269N-WFGWX-YVC9B-4J6C9-T83GX
2：NYW94-47Q7H-7X9TT-W7TXD-JTYPM
3：PBHCJ-Q2NYD-2PX34-T2TD6-233PK
4：DPH2V-TTNVB-4X9Q3-TJR4H-KHJW4
5：FWN7H-PF93Q-4GGP8-M8RF3-MDWWW
```
- 输入 `slmgr -skms kms.03k.org`
- 输入 `slmgr -ato`
- 输入 `slmgr -dlv`
- 查看是否已经激活

## 安装Store

- 打开网页 `https://store.rg-adguard.net/`
- 在搜索栏选择搜索方式 “PackageFamilyName”
- 输入搜索关键字 “Microsoft.WindowsStore_8wekyb3d8bbwe”
- 根据系统类型下载安装包，一共四个包都要下载。
- 然后打开PowerShell（管理员权限）
- 进入安装包位置，使用安装命令：Add-AppxPackage *
- 这样就能在开始菜单栏看到安装完了。
