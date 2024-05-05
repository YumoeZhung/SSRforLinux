- [在Linux服务器中使用代理--ver.2024.5](#在linux服务器中使用代理--ver20245)
  - [配置环境](#配置环境)
    - [请注意！.99和.11已经配置了基本环境，大家使用请直接从下载并修改原项目开始](#请注意99和11已经配置了基本环境大家使用请直接从下载并修改原项目开始)
  - [下载并修改原项目](#下载并修改原项目)
  - [节点信息配置](#节点信息配置)
  - [启动/停止脚本编写](#启动停止脚本编写)
  - [启动/关闭脚本](#启动关闭脚本)

# 在Linux服务器中使用代理--ver.2024.5
今天想下点东西，结果差点忘了自己配置的代理怎么用，以后我会多写文档做记录，分享给大家，助人助己。
## 配置环境
### 请注意！.99和.11已经配置了基本环境，大家使用请直接从[下载并修改原项目](#下载并修改原项目)开始
需要管理员权限，来安装libsodium和privoxy：

```shell
#ubuntu
sudo apt install libsodium
#centos
sudo yum install libsodium
```
```shell
#ubuntu
sudo apt install -y privoxy
#centos
sudo yum install -y provoxy
```
## 下载并修改原项目

``` shell
git clone https://github.com/dreamhunter2333/shadowsocksr.git
```
更正：原项目代码中有一处需要修改，等网络修好后，我会上传改好的代码至仓库。

这里先贴出，仅此一处，右侧为更改结果：

![alt text](image.png)

## 节点信息配置
```shell
cd yourpath/shadowsocksr/
vim yourpath/shadowsocksr/config.json
```
执行这两个命令后，会看到配置文件内容如下：

![alt text](./fujian/image1.png)

这时候，如果你有代理软件，比如SSR，或者Clash，又或者......总之，去查看每个节点的具体信息，填入这个配置文件。填写时遵循一个标准：`有就填，没有就不填，别乱填`

为了保证大家都能填对，下面举例演示：

这是我的订阅节点信息：

![alt text](./fujian/image2.png)

对照上面的原始配置文件中的字段:

`server`对应`服务器IP`

`server_port`对应`服务器端口`

`password`对应`密码`

`method`对应`加密`

`protocol`对应`协议`

`protocol_param`对应`协议参数`

`obfs`对应`混淆`

其余的选项，保持原文件的内容不变即可。
## 启动/停止脚本编写
我们每个用户的文件夹下都有一个隐藏文件夹`.local`，如果看不见，可用
```shell
ls -a
```
查看。在`.local`下，新建文件夹`bin`,放入启动和停止脚本（也可以自己新建脚本，复制内容进去即可）。

![bin](./fujian/image3.png)

唯一需要注意的是，大家需要根据自己的路径修改runssr，只改这一行：
```shell
nohup python /cluster/home/zhangyumeng/shadowsocksr/shadowsocks/local.py -c /cluster/home/zhangyumeng/shadowsocksr/config.json >> /cluster/home/zhangyumeng/shadowsocksr/shadowsocks/ssserver.log 2>&1 &
```
将这一行的三个路径都更改为你的路径下对应文件的位置。
## 启动/关闭脚本
首次启动需要先让环境变量生效，以后便不需要。
```shell
source .bashrc
```
运行上面的命令后，环境变量生效，不起效就关闭这个命令行，新开一个命令行。

这里应当注意，你需要有一个合适的虚拟环境！！！！，毕竟命令中有个python xxx.py。

建议先单独运行
```shell
python /cluster/home/zhangyumeng/shadowsocksr/shadowsocks/local.py -c /cluster/home/zhangyumeng/shadowsocksr/config.json
```
来查看报错，缺少哪个库装哪个。

环境彻底完成后，`source runssr`启动应用，效果如图：

注意：如果是在root账号下操作，启动privoxy服务时并不会弹出密码输入对话。

![alt text](./fujian/image4.png)

输入服务器密码，会启动`privoxy.service`服务(默认为所有用户安装)。

出现`COMPLETE`，启动成功。


![alt text](./fujian/image5.png)

使用
```shell
curl www.google.com
```
测试网络是否通畅。

![alt text](./fujian/image6.png)

返回了页面内容，成功。如果不返回，关闭应用，命令见下面，然后启动，再测试，往往就成功。

使用`source stopssr`关闭应用，同样的，会再输入一次密码。

经过测试，网络代理只在某一用户的账号下生效，不影响其他用户。