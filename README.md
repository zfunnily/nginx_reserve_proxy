
## 项目说明
反向代理+wss

## 目录说明
- conf: 配置文件目录
- conf/nginx.conf: 启动配置
- conf/conf.d/default.conf
- conf/conf.d/reserve_proxy.conf: 核心
- html
- logs
- openssl
- docker-compose.yml
- README.md


## reserve_proxy.conf说明
```text
# 反向代理 ip 池子, 轮询方式, 后续有新的节点，加入下面的池子即可
upstream pool {
server 10.0.0.13:51001 weight=10 max_fails=2 fail_timeout=30s;
}

server {
listen       61001;
listen       443 ssl; # wss 加密协议
#server_name  192.168.16.190;

    ssl_certificate /etc/nginx/cert/server.crt;#的证书位置
    ssl_certificate_key /etc/nginx/cert/ca_key.key.unsecure;

    ssl_session_timeout 5m;
    ssl_session_cache shared:SSL:10m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers on;
    ssl_verify_client off;

    location / {
        proxy_redirect off;
        proxy_pass http://pool; #这里变量 pool, 固定变量
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forward-For $remote_addr:$remote_port;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade; # 使用的是ws协议
        proxy_set_header Connection upgrade;
    }
}
```
