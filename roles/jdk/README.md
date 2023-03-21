部署jdk
==================
# 1. 部署jdk
## 1.1. 部署前准备
### 1.1.1. http文件服务器
用于存放jdk二进制包，
比如jdk-8u361-linux-x64.tar.gz的下载地址为：
http://file_server_ip/jdk/jdk-8u361-linux-x64.tar.gz
### 1.2 部署步骤
#### 1.2.1. 新建playbook入口文件
```yaml
- hosts: jdk
  # 安装的配置参数，按需修改
  vars:
    # jdk版本
    version: 8
    subversion: 361

    # jdk二进制包的下载地址
    download_url: "http://192.168.123.222/jdk/{{ tarball }}"

    # jdk安装路径
    install_path: "/usr/local/java"

    # jdk安装后的目录【不要修改】
    java_home: "{{ install_path }}/jdk1.{{ version }}.0_{{ subversion }}"

    # jdk二进制包的名称【不要修改】
    tarball: "jdk-{{ version }}u{{ subversion }}-linux-x64.tar.gz"
  roles:
    - jdk
```
#### 1.2.2. 执行playbook
```bash
ansible-playbook -i jdk jdk.yml
```
### 1.3. 验证
```bash
[root@centos7 ~]# java -version
java version "1.8.0_361"
Java(TM) SE Runtime Environment (build 1.8.0_361-b09)
Java HotSpot(TM) 64-Bit Server VM (build 25.361-b09, mixed mode)
```

