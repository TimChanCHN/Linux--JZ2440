# PC环境搭建

1. 安装VMWare Tools:
   1. 打开DVD按键，复制压缩文件到本地一个文件夹（例如：/tools/）
   2. 解压文件并进入该文件夹，直接执行vmware-install.pl
   3. 需要先进入root模式，否则不能随意把该文件夹复制到任何路径
   4. 遇到[yes/no]的输入y，其他按enter即可

2. 安装交叉编译器
   1. 下载指定编译器，解压并存放到指定路径仲（例如：/usr/local/arm/arm-linux-gcc-4.3.2）
   2. 添加该编译器至环境中（/etc/environment）
   3. 当ubuntu是64位时，需要添加相应的转换库：`lib32ncurses5`,`lib32z1`,
   4. 重启，利用命令`arm-linux-gcc -v`验证

3. ubuntu增加root用户
   1. vim /usr/share/lightdm/lightdm.conf.d/50-unity-greeter.conf
    > user-session=ubuntu  
    > greeter-show-manual-login=true  
    > all-guest=false
   2. vim /root/.profile
    > 把最后一句修改为： tty -s && mesg n || true
   3. 设置root账户密码： sudo passwd root

4. 配置vim（打开文件：/etc/vim/vimrc）
   1. 显示行号：set nu
   2. 取消高亮：set nohls
   3. 设置tab为4个空格：set ts=4, set expandtab, set autoindent【该方式设置会令makefile无法正常使用】
   4. 设置tab为4空格同时不影响makefile的格式：`set tabstop=4`

5. 防火墙相关设置
   1. 查看状态： ufw status
   2. 关闭防火墙：ufw disable
   3. 打开防火墙：ufw enable

6. SSH服务器--openssh-server

7. 支持menuconfig--安装库`libncurses5-dev`

8. 命令提示符设置
   1. 对文件~/.bashrc进行编辑，对第55行的PS1进行修改，把w更改为W，只显示当前路径名称而不显示完整路径名称。具体请参考：
   [ubuntu终端命令提示符设置](https://blog.csdn.net/Fan0920/article/details/102731584)

9.  固定ip地址
   1.  编辑--虚拟网络编辑器：设置vmnet0为桥接模式，并选中当前网络中的网卡；
   2.  虚拟机--设置--网络适配器：选择桥接模式
   3.  点击右上角网络标志，进入编辑网络，选择IPv4，方法--手动，地址为要设置的地址（注意，要和当前网络为同一网关）
   4.  重启网络服务即可：ifconfig ens33 down, ifconfig ens33 up【ens33是当前机器的网卡】

10. 开发板、主机、虚拟机互ping
    1. 确定所使用的网卡A（用网线直接连电脑，则是有线网卡);
    2. VMware选择网卡A作为桥接网卡（即有线网卡）；
    3. 设置三者IP为同一网段IP。
    > 备注： 利用有线网卡时，会发现虚拟机网络连接失败、本地的有线网络适配器提示网线被拔掉，这是正常现象，能够Ping通即可。
    
11. 开发板和虚拟机通讯，需要配置vmnet0为桥接模式，此时使用的是有线网卡，物理连接，在9.2的设置重选择桥接，则可以与开发板通信。  
    但是此时虚拟机无法上网。需要把vmnet8设置为nat模式，并把9.2选择成NAT，同时需要把网络连接设置为可以上网的网段，最好设置成  
    固定网段，这样就可以在虚拟机上上网。



## 注意：
1. 系统移植遇到的问题：
   1. nfs系统挂载不成功：
      1. 网卡驱动是否能正常工作；  
      2. [windows防火墙操作](https://blog.csdn.net/WilliamW0ng/article/details/83663067)
      3. 开发板和主机、虚拟机ping不通
2. 利用nfs挂载文件系统，本地适配器和无线适配器不属于同一个网段，而本地适配器无法访问互联网，若需要用到apt安装相关服务，则需要修改ubuntu的桥接网卡，以及当前网络的ip地址和网关地址。
