部署mysql5.7单实例
==================
# 1. 部署mysql5.7单实例
## 1.1. 部署前准备
### 1.1.1. http文件服务器
用于存放mysql安装包，
比如mysql-5.7.41-linux-glibc2.12-x86_64.tar.gz的下载地址为：
http://file_server_ip/mysql/mysql-5.7.41-linux-glibc2.12-x86_64.tar.gz
### 1.2 部署步骤
#### 1.2.1. 新建playbook入口文件
```yaml
- hosts: mysql
  # 安装的配置参数，按需修改
  vars:
    mysql_version: "5.7.41"       # mysql版本，用于下载mysql二进制包
    mysql_port: "3306"            # mysql端口
    mysql_pass: "3141"            # mysql密码
    download_url: "http://file_server_ip/mysql/{{ tarball }}"  # mysql二进制包的下载地址
    install_path: "{{ ansible_user_dir }}" # mysql安装路径,默认为ansible用户的家目录
    basedir: "{{ install_path }}/mysql"    # 软链接，指向"{{ install_path }}/{{ extractdir }}"，方便切换mysql版本，默认为{{ install_path }}下的mysql
    datadir: "{{ install_path }}/data"     # mysql数据存放的路径，默认{{ install_path }}下的data目录
    user: "{{ ansible_ssh_user }}"         # mysql运行的用户
    group: "{{ ansible_ssh_user }}"        # mysql运行的用户组
    innodb_buffer_pool_size: "{{ (ansible_memtotal_mb * 0.5) | int }}M" # innodb缓存池大小，默认使用主机的内存大小的50%
    innodb_log_file_size: "{{ (ansible_memtotal_mb * 0.1) | int }}M"    # innodb日志文件大小，默认使用主机的内存大小的10%
    server_id: "{{ ansible_default_ipv4.address.split('.')[3] }}"       # mysql服务的id号，默认使用ansible默认IP地址的第四个数字作为id号
    # 不要修改下面的参数
    tarball: "mysql-{{ mysql_version }}-linux-glibc2.12-x86_64.tar.gz"  # mysql二进制包的名称
    extractdir: "mysql-{{ mysql_version }}-linux-glibc2.12-x86_64"      # mysql二进制包解压后的目录名称
  roles:
    - mysql5.7-standalone
```
#### 1.2.2. 执行playbook
```bash
ansible-playbook -i mysql mysql.yml
```
### 1.3. 验证
```bash
[root@centos7 ~]# tree -L 1
.
├── data
├── mysql -> mysql-5.7.41-linux-glibc2.12-x86_64
├── mysql-5.7.41-linux-glibc2.12-x86_64
└── mysql_manager.sh

3 directories, 1 file
[root@centos7 ~]# ./mysql_manager.sh -h
Usage: mysql_manager.sh  {start|stop|restart|reload|force-reload|status}  [ MySQL server options ]
```

