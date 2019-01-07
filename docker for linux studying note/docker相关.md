#### 第一种制作镜像方式: centos7.5+vim+net-tools+iproute+sshd

1. 启动新容器

   docker images查询已存在的镜像

   docker run -it --name "centos7.5" [存在的镜像的ID]

2. 优化yum源

   mv /etc/yum.repos.d/*.repo /tmp
   echo -e "[ftp]\nname=ftp\nbaseurl=ftp://10.0.0.100/pub/centos7\ngpgcheck=0">/etc/yum.repos.d/ftp.repo

3. 安装必须软件包

   yum install -y vim net-tools  iproute   openssh-*   -y

4. 启动SSHD

    mkdir /var/run/sshd
    echo 'UseDNS no' >> /etc/ssh/sshd_config
    sed -i -e '/pam_loginuid.so/d' /etc/pam.d/sshd
    echo 'root:123456' | chpasswd
    /usr/bin/ssh-keygen -A
    /usr/sbin/sshd -D 

    注意: 以上操作做完之后,会一直不退出,需要用以下命令退回到宿主机,并不关闭容器
    ctrl+p+q

5. 制作镜像

   docker commit ['step 1 name'] {'new name'}:{new tag}

   eg: docker commit centos7.5 huojibufei/centos7_sshd:v1

#### 第二种制作镜像方式: centos7.5+vim+net-tools+iproute+sshd

1. cd /opt/dockerfile

2. vim  dockerfile 



   ```
   FROM centos:7.5.1804
   RUN mv /etc/yum.repos.d/*.repo /tmp
   RUN echo -e "[ftp]\nname=ftp\nbaseurl=ftp://10.0.0.100/pub/centos7\ngpgcheck=0">/etc/yum.repos.d/ftp.repo
   RUN yum install -y openssh-server
   RUN yum install -y openssh-clients
   RUN yum install net-tools* -y
   RUN yum install iproute-* -y
   RUN mkdir /var/run/sshd
   RUN echo 'UseDNS no' >> /etc/ssh/sshd_config
   RUN sed -i -e '/pam_loginuid.so/d' /etc/pam.d/sshd
   RUN echo 'root:123456' | chpasswd
   RUN /usr/bin/ssh-keygen -A
   EXPOSE 22
   CMD ["/usr/sbin/sshd", "-D"]
   ```

3. docker build -t "huoji/centos7_sshd:v1" /opt/dockerfile

#### 制作私有仓库

1. 配置私有仓库

   1. docker run -d -p 5000:5000 --restart=always --name registry -v /opt/Registry:/var/lib/registry  registry

      ps:-v参数指定持久化地址 系统数据卷:容器的数据卷

   2. vim /etc/docker/daemon.json



      ```
      
      {
         "registry-mirrors": ["https://68rmyzg7.mirror.aliyuncs.com"],
         "insecure-registries": ["10.0.0.100:5000"]
      }
      ```

   3. systemctl  restart docker

2. 使用本地镜像

   1. 制作本地镜像并push到

      docker tag nginx 10.0.0.100:5000/huoji/nginx:v1

      docker images

      docker push 10.0.0.100:5000/huoji/nginx:v1