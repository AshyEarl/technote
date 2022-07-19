## 安装
参见[这里](https://about.gitlab.cn/install/)

1. 安装依赖项
    ```bash
    sudo apt-get update
    sudo apt-get install -y curl openssh-server ca-certificates tzdata perl
    ```
2. 下载安装`GitLab`
    ```bash
    // 配置GitLab 软件源镜像
    curl -fsSL https://packages.gitlab.cn/repository/raw/scripts/setup.sh | /bin/bash
    // 安装，这里的地址就是访问地址
    sudo EXTERNAL_URL="http://gitlab.example.com" apt-get install gitlab-jh
    ```
3. 访问`GitLab`: 
    默认情况下root用户的密码会保留24小时在`/etc/gitlab/initial_root_password`, 我使用`sudo gitlab-ctl reconfigure`给重新配置了，需要使用下面的方法重置root密码：
    ```bash
    sudo gitlab-rake "gitlab:password:reset"
    ```
4. 安装中遇到的问题
   ```bash
   // 安装时卡在 ruby_block[wait for logrotate service socket] action run
   systemctl restart gitlab-runsvdir.service

   // 卸载
   sudo apt purge gitlab-jh
   sudo rm -rf /etc/gitlab
   sudo rm -rf /opt/gitlab
   sudo rm -rf /var/opt/gitlab

   // 系统内存只有4G，不够，开启swap
   // 创建一个4GB的swap文件
   sudo fallocate -l 4G /swapfile
   // 不允许普通用户访问这个文件
   sudo chmod 600 /swapfile
   // 用这个文件创建swap空间
   sudo mkswap /swapfile
   // 添加分区配置，重启后swap分区也有效
   sudo vi /etc/fstab
   /swapfile swap swap defaults 0 0
   // 查看当前的swap信息
   sudo swapon --show
   ```

5. 添加ssh-key用于访问
    ```bash
    ssh-keygen -t rsa -b 2048 -C "comment-as-you-need"
    ```

6. 服务器迁移：[这里](https://www.mikestreety.co.uk/blog/migrating-gitlab-from-one-server-to-another/)