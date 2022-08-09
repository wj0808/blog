# 在 Ubuntu 18.04 安装jenkins
参考地址：https://www.digitalocean.com/community/tutorials/how-to-install-jenkins-on-ubuntu-18-04
## 1.安装jenkins前的准备
* CPU:
* RAM:
* java
## 2.安装jenkins
```|json
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
```
## 3.启动jenkins 前的操作
更改jenkins_home，默认的是/var/lib/jenkins文件夹，该目录空间可能有限，需要变更目录
可以执行
```|json
systemctl cat jenkins
systemctl edit jenkins
```
打开是空的，没有问题，它会覆盖原来的
```|json
[Service]
Environment="JENKINS_PORT=8081"
[Service]
Environment="JENKINS_HOME=/data/jenkins"
```
参考https://www.jenkins.io/doc/book/installing/linux/

## 4.启动jenkins
打开http://your_server_ip_or_domain:8080 （防火墙端口要开放）

按步骤操作，如果报离线的问题，可以配置代理（代理host没有http前缀，可以advance:输入地址测试），或者改update_url
参考：https://www.jianshu.com/p/0d8764bffb27
