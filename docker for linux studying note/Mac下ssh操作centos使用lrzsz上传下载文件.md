##### 注：本文根据网上现有博文整理

###### 1.<http://www.iterm2.cn/download> 下载iterm2软件 代替mac自带终端

###### 2.没有安装lrzsz的话

```
yum -y install lrzsz 或 brew install lrzsz
(brew)需要替换安装源 比较麻烦
```

###### 3.<https://github.com/mmastrac/iterm2-zmodem> 将文件下载下来

```
若1 3链接失效  请食用某盘
https://pan.baidu.com/s/13yD1FHsbig0uIqvKCH3A0Q 
密码:ozxv
```

###### 4.mac 本机进入`/usr/local/bin`目录 将下载的压缩包解压 得到的文件夹中的`iterm2-recv-zmodem.sh` 和 `iterm2-send-zmodem.sh`两个文件放入mac本机`/usr/local/bin`目录中

###### 5.点击 iTerm2 的设置界面 `Perference-> Profiles -> Default -> Advanced -> Triggers`的 Edit 按钮

###### 6.点击+按钮 添加两行数据 根据下面：后面的信息填入

```
Regular expression: rz waiting to receive.\*\*B0100
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-send-zmodem.sh

Regular expression: \*\*B00000000000000
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-recv-zmodem.sh
```

###### 7.在centos中

```
vim /etc/environment
LC_ALL=en_US.UTF-8
LANG=en_US.UTF-8
```

###### 8.同上面

```
vim /etc/locale.conf
LANG=en_US.UTF-8
```

###### 9.执行`reboot`命令重启服务器

###### 10.服务器中 `rz`命令下载文件 `sz` `文件全名` 下载文件到本地