# discourse 开源论坛搭建

该项目包含前端后台数据库, 通过 docker 部署

## 在 centos 上安装

- `yum install -y git`：安装 git

- `sudo wget -qO- https://get.docker.com/ | sh`： 安装 docker

- 配置 docker 国内镜像

  ```shell
  # Configure docker to use Chinese mirrors$ sudo vim /etc/docker/daemon.json
    {
        "registry-mirrors": ["https://registry.docker-cn.com","http://hub.c.163.com"]
    }
    $ sudo systemctl restart docker.service
  ```

- `git clone https://github.com/discourse/discourse_docker.git /var/discourse`: 下载 discourse

- `cpp /var/discourse/samples/standalone.yml /var/discourse/container/app.yml`: 修改默认配置, 否则最后发送邮件会失败

  - `- exec: rails r "SiteSetting.notification_email='xxx@qq.com'"` 添加发送邮箱

  - 要使用 https, 需要添加 `- "templates/web.letsencrypt.ssl.template.yml"`, 在 `/var/discourse/shared/standalone/ssl/` 下存放 ssl 相关密钥文件

  - nginx 下配置需要将 ssl 相关配置和端口注释, 使用 socketed 配置

    ```yml
      # expose
      # - "80:80"   # http
      # - "43:443"  # https
      #  - "templates/web.ssl.template.yml"
      #  - "templates/web.letsencrypt.ssl.template.yml"
      - "templates/web.socketed.template.yml"`
    ```

  - 在 nginx 中的配置 https， http 的可以直接将 location 放入 80 端口的 server 配置下

    ```nginx
    server {
      listen 80; listen [::]:80;
      server_name xxx.xxx.com;

      return 301 https://$host$request_uri;
    }

    server {
      listen 443 ssl http2;  listen [::]:443 ssl http2;
      server_name forum.example.com;

      ssl_certificate cert/xxx.pem;
      ssl_certificate_key cert/xxx.key;
      ssl_session_cache shared:SSL:1m;
      ssl_session_timeout  5m;
      ssl_ciphers HIGH:!aNULL:!MD5;
      ssl_prefer_server_ciphers on;

      http2_idle_timeout 5m; # up from 3m default

      location / {
          proxy_pass http://unix:/var/discourse/shared/standalone/nginx.http.sock:;
          proxy_set_header Host $http_host;
          proxy_http_version 1.1;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header X-Forwarded-Proto https;
          proxy_set_header X-Real-IP $remote_addr;
      }
    }
    ```

- `./discourse-setup`: 开始配置

  - Hostname for your Discourse?
    - 填写域名
  - Email address for admin account(s)?
    - 合法邮箱
  - SMTP server address?
    - 邮件服务地址(qq 邮箱: smtp.qq.com qq 企业邮箱: smtp.exmail.com)
  - SMTP port?
    - smtp 端口：587 (目前 qq 邮箱、企业邮箱都设置为 587 即可)
  - SMTP user name?
    - smtp 邮箱账户
  - SMTP password?
    - smtp 邮箱授权码

- `/var/discourse/launcher rebuild app` 如果有修改配置(app.yml), 可以重新编译

- 创建管理员账号 (也可直接访问论坛, 通过管理员邮箱激活管理员账号)

  - `./launcher enter app`

  - `rake admin:create`

  - 输入邮箱与密码
