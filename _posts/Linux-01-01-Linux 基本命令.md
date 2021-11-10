# **1. 主机**



> #### 查看主机名
>
> ```shell
> ~]# hostname
> ```
>
> #### 修改主机名  
>
> ```shell
> ~]# hostname  + 主机名
> ```
>
> #### 查看主机IP
>
> ```shell
> ~]# ifconfig   |   ip a
> ```
>
> ####    配置IP
>
> ```shell
> vim      /etc/sysconfig/network-scripts/ifcfg-ens33
> 
> TYPE=Ethernet
> PROXY_METHOD=none
> BROWSER_ONLY=no
> BOOTPROTO=static        静态ip                  //dhcp
> DEFROUTE=yes
> IPV4_FAILURE_FATAL=no
> IPV6INIT=yes
> IPV6_AUTOCONF=yes
> IPV6_DEFROUTE=yes
> IPV6_FAILURE_FATAL=no
> IPV6_ADDR_GEN_MODE=stable-privacy
> NAME=ens33
> UUID=33c8d6e2-7e18-49e1-a790-6d9df73fd427
> DEVICE=ens33
> ONBOOT=yes
> IPADDR=192.168.45.10                        //ip配置
> NETMASK=255.255.255.0
> GATEWAY=192.168.45.2                           
> DNS1=114.114.114.114
> ```

# **2.目录**

> -----------
>
> #### 新建目录  
>
> ```shell
>  mkdir   +  目录名......
> ```
>
> #### 查看目录
>
> ```shell
>  ls   +  目录名.....
> ```
>
> > ###### ls   选项
> >
> > ```shell
> > -l    --------       查看目录详细信息
> > -ld  --------    查看当前目录详细信息
> > -A   ---------     以 ' . ' 开头隐藏的文档
> > ls    /boot /v*    -------    多个以v开头的文件
> > ls    /Boot  /*top   -------- 多个以top结尾的文件
> > ls   /dev/tty?      -------------tty后面只能出现一个字符
> > ls   /dev/tty??     -------------tty 后面只能出现两个字符
> > ```
>
> #### 删除目录
>
> ```shell
>  rm-rf    +    目录名   # -f 强制删除
> ```
>
> #### 转换目录  
>
> ```shell
> ~]# cd    目录名
> ```
>
> > ###### cd    选项
> >
> > ```shell
> > .     --------       当前目录
> > 
> > ..     --------       上一级目录
> > ```
>
> #### 移动（剪切）
>
> ```shell
> mv    起始路径     终点路径
> ```
>
> #### 复制
>
> ```shell
> cp      目录名    路径
> ```



# **3.文本文件**

> #### 新建文件  
>
> ```shell
> ~]# touch     /目录/文件
> ```
>
> #### 查看文件
>
> ```shell
> cat/less/head/tail/grep     +    /目录/文件
> ```
>
> > ###### cat     选项
> >
> > ```shell
> > -n    +   文件内容前加序号
> > ```
> >
> > ###### less
> >
> > ```shell
> > less + 文件   #  翻页查看           Q   -----   退出查看
> > ```
> >
> > ###### head  
> >
> > ```shell
> > head   -n    + 文件名    #查看头部
> > ```
> >
> > ###### tail 
> >
> > ```shell
> > tail -n + 文件     # 查看尾部
> > ```
>
> #### 查看指定文件
>
> ##### grep
>
> ```shell
> grep   aaa   /opt      #    查看指定文件（或内容）
> grep ^root /etc/passwd    #     查看必须以root开头的文件
> grep   root$  /etc/passwd  # 查看必须以root结尾的文件
> 
> grep 文件内容过滤：显示文件有效信息（去除注释信息，去除空行）
> 注释信息：大多数以# 开头
> grep   ^#     文件 ....      以# 开头的行
> grep   - v ^#   文件......    开头无# 的行、
> 匹配空行：
> grep   ^$  文件...........     只要空行
> grep    -v  ^$   文件........   不要空行
> 显示文件有效信息：
> grep     -v  ^#    /文件.......      |      grep     -v   ^$
> grep   -v ^#  /etc/login.defs    |    grep  -v ^$
> 总结：
> grep   -v ^#   /文件路径       去除注释
> grep   -v  ^$  /文件路径        去除空行
> ```
>
> #### find  命令
>
> ```shell
> 作用：
> 
> - 根据预设的条件递归查找对应的文件
> 
>  -------   find    【目录】【条件1】 【-a|-o】【条件2】.........
>  
>  -a  【同时满足】   -o   【满足其一】
>  
>  常用条件
> 
> --type   类型（f： 文本文件               d：  目录  l：  快捷方式）
> 
> find  /boot/                  -type  d   【只查找目录】   f   【只查找文件】
> 
> -- name "文档名称"
> 
> find  /etc/   -name   "passwd"   【查找目录下是否有passwd 】
> 
> find  /root / -name "nsd*" -a -type f   【以nsd开头且是文件】
> 
> find  /root/   -name "*nsd"  -o  -type  d    【以nsd结尾后、或者是目录】
> 
> --size +|-  文件大小（k.M.G）
> 
> find  /boot/  -size +300k
> 
> --user  用户名[文件所有者]
> 
> find  /boot/   -user student
> 
> --mtime   根据文件修改的时间查找
> 
> find  /文件      -mtime +10   【10天之前】  -10   【10天之内】的数据
> ```
>
> #### 高级使用
>
> ```shell
>  ----find   ....    .....     -exec  处理命令  {}   ;
> 
>  ----  优势：以{} 代替每一个结果，逐个处理，遇; 结束
> 
>   find  /boot /     -size +10M
> 
>   find  /boot /    -size + 10M      -exec    cp  {}   /opt ****;                   #将查找的文件复制到opt
> 
>   ls   /opt/
> 
>   [root@localhost ~]# find     /boot/     -size +10M      -exec     cp    {}       /opt    \  ;
> 
>   [root@localhost ~]# ls /opt
> 
>   initramfs-0-rescue-ba77529a31204802b29b6be3cf43c78e.img  initramfs-3.10.0-862.el7.x86_64.img 
> 
>   rh
> ```

# **4.软件管理**

```shell
rpm  -q       软件名               【查看是否安装软件】   -ql      【安装清单】
yum  -y install     软件名         【安装】
yum    remove    软件名            【卸载】
yum     repolist       列出仓库信息
yum     clean  all    清空缓存
```

# **5. SELINUX运行模式**

> #### 运行模式的切换
>
> ```shell
> enforcing(强制)
> permissive（宽松）
> disabled （彻底禁用）
> ```
>
> #### 注意：
>
> ```shell
> 任何模式切换到disabled(彻底禁用)，都必须经历重启系统
> [root@localhost ~]# getenforce              //查看SELINUX状态
> Enforcing                                                          //  强制
> /etc/selinux/config                //配置文件
> ```
>
> #### 切换模式
>
> ```shell
> 临时切换：setenforce  1【开启】|0【关闭】[宽松模式]
> 
> [root@localhost ~]# getenforce
> 
> Enforcing
> 
> [root@localhost ~]# setenforce 0      //修改为宽松
> 
> [root@localhost ~]# getenforce
> 
> Permissive
> 
> [root@localhost ~]# vim /etc/selinux/config      //强制修改
> ```

# **6. 防火墙**



#### 软件防火墙：本机防护

> ###### firewalld 服务基础< 关闭 与 开启>
>
> ```shell
> systemctl stop    firewalld   # 关闭
> 
> systemctl restart firewalld   # 开启
> ```

# **7. 构建web服务**



> #### 安装httpd包【提供一个页面内容】
>
> ```shell
> yum -y install httpd
> ```
>
> #### 重启httpd软件
>
> ```shell
> [root@localhost ~]# systemctl restart httpd                       //重启
> [root@localhost ~]# systemctl enable httpd                       //设置开机自动启动
> [root@localhost ~]# systemctl   status httpd                         //查看是否安装
> ```
>
> #### 测试访问
>
> > **书写页面文件**
> >
> > ```shell
> > 存放网页文件位置   /var/www/html/
> > 
> > 网页文件名字：     index.html
> > ```
