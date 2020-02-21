 ## 无需root权限
下面代码中的的url指的是下载地址
[nodejs下载地址](https://npm.taobao.org/mirrors/node/v12.10.0/node-v12.10.0-linux-x64.tar.xz).
以下例子中我在家目录下，将安装包放在downloads文件夹下，然后把安装文件放到apps/nodejs中
```git
wget url   //下载压缩包
tar -xvf v12.10.0/node-v12.10.0-linux-x64.tar.xz  //解压 
//解压完成后输入ls命令看一下会多出来一个目录，我们假设他叫file_name
mv file_name /apps/nodejs //移动文件
```
至此，就安装好了，接下来我们要配置输命令时自动搜索的路径。
然后去~下面找.bashrc 编辑配置，代码如下
```git
cd ~/apps/nodejs/bin 
vi .bashrc         //ubuntu一般是这个文件夹，centos可能是输入 vi .bash_profile
```
vi命令指的是用vim编辑器去编辑文件，具体用法不再介绍。一般新系统应该只有一句
PATH=$PATH:$HOME/.local/bin:$HOME/bin
直接在后面加上一句`：~/apps/nodejs/bin`：指的是分隔前面的命令
然后重启就ok了
## 拥有root权限
如果你拥有root权限，且有yum（一般常在centos中自带）那么方法如下

```linux
sudo yum install -y gcc-c++ make
curl --location https://rpm.nodesource.com/setup_8.x | sudo bash -
sudo yum install -y nodejs

# 检查 node.js 安装是否成功
node -v

# 检查 npm 是否安装成功
npm -v
```