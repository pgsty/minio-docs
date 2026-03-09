# 使用 Certbot 为 MinIO 生成 Let's Encrypt 证书 [![Slack](https://slack.min.io/slack?type=svg)](https://slack.min.io)
[Let’s Encrypt](https://letsencrypt.org/) 是一个新的免费、自动化且开源的证书颁发机构（Certificate Authority）。

[Certbot](https://certbot.eff.org/) 是一个基于控制台的 Let's Encrypt 证书生成工具。

在本教程中，我们将使用 Certbot 生成 Let's Encypt 证书。随后将把该证书部署到 MinIO Server 中使用。

## 1. 前置条件
- 从[这里](https://minio.pigsty.io/index.html#procedure)安装 MinIO Server。
- 从[这里](https://certbot.eff.org/)安装 Certbot

## 2. 依赖要求
- 在执行 `certbot` 时，https 使用的 443 端口需要处于开放且可用状态。
- Certbot 执行时需要 root 访问权限，因为只有 root 可以绑定 1024 以下的端口。
- 本教程将使用我们自己的域名 ``myminio.com`` 作为示例。请在你的环境中替换为你自己的域名。

## 3. 操作步骤

### 第 1 步：安装 Certbot
请按照 https://certbot.eff.org/ 上的文档安装 Certbot。

### 第 2 步：生成 Let's Encrypt 证书
```sh
# certbot certonly --standalone -d myminio.com --staple-ocsp -m test@yourdomain.io --agree-tos
```

### 第 3 步：验证证书
列出保存在 `/etc/letsencrypt/live/myminio.com` 目录中的证书。
```sh
$ ls -l /etc/letsencrypt/live/myminio.com
total 4
lrwxrwxrwx 1 root root  37 Aug  2 09:58 cert.pem -> ../../archive/myminio.com/cert4.pem
lrwxrwxrwx 1 root root  38 Aug  2 09:58 chain.pem -> ../../archive/myminio.com/chain4.pem
lrwxrwxrwx 1 root root  42 Aug  2 09:58 fullchain.pem -> ../../archive/myminio.com/fullchain4.pem
lrwxrwxrwx 1 root root  40 Aug  2 09:58 privkey.pem -> ../../archive/myminio.com/privkey4.pem
-rw-r--r-- 1 root root 543 May 10 22:07 README
```

### 第 4 步：使用证书在 MinIO Server 上配置 SSL。
通过 Certbot 生成的证书和密钥需要放置在用户的主目录中。
```sh
$ cp /etc/letsencrypt/live/myminio.com/fullchain.pem /home/user/.minio/certs/public.crt
$ cp /etc/letsencrypt/live/myminio.com/privkey.pem /home/user/.minio/certs/private.key
```

### 第 5 步：更改证书的所有权。
```sh
$ sudo chown user:user /home/user/.minio/certs/private.key
$ sudo chown user:user /home/user/.minio/certs/public.crt
```

### 第 6 步：使用 HTTPS 启动 MinIO Server。

如果你不打算以 `root` 权限运行 MinIO，则需要通过以下命令为 MinIO 赋予监听小于 1024 端口的能力：

```sh
sudo setcap 'cap_net_bind_service=+ep' ./minio
```

现在，你可以在 "443" 端口上启动 MinIO Server。

```sh
$ ./minio server --address ":443" /mnt/data
```

如果你使用的是 MinIO 的 dockerized 版本，那么你需要
```sh
$ sudo docker run -p 443:443 -v /home/user/.minio:/root/.minio/ -v /home/user/data:/data minio/minio server --address ":443" /data
```

### 第 7 步：在浏览器中访问 <https://myminio.com>。
![Letsencrypt](https://github.com/minio/cookbook/blob/master/docs/screenshots/letsencrypt-certbot-minio.jpg?raw=true)
