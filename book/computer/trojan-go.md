# trojan

## install trojan-go

```bash
wget https://github.com/p4gefau1t/trojan-go/releases/download/v0.10.6/trojan-go-linux-amd64.zip

unzip trojan-go-linux-amd64.zip
```

## 创建证书

```bash
curl https://get.acme.sh | sh
cd /root/.acme.sh

# 注册ZeroSSL
acme.sh --register-account -m xxx@xxx.com --server zerossl

# acme.sh --issue t.alvinhtml.com -w ~/.acme.sh/www.alvinhtml.com/
# acme.sh --issue t.alvinhtml.com -w ~/.acme.sh/www.alvinhtml.com/

./acme.sh --debug --issue -d t.alvinhtml.com -w ~/.acme.sh/www.alvinhtml.com/
```

## nohup ./trojan-go -config config.json &

## 加入 systemd 自启动

1. 创建 `/etc/systemd/system/trojan-go.service`

```
[Unit]
Description=Trojan-Go - An unidentifiable mechanism that helps you bypass GFW
Documentation=https://p4gefau1t.github.io/trojan-go/
After=network.target nss-lookup.target

[Service]
User=nobody
CapabilityBoundingSet=CAP_NET_ADMIN CAP_NET_BIND_SERVICE
AmbientCapabilities=CAP_NET_ADMIN CAP_NET_BIND_SERVICE
NoNewPrivileges=true
ExecStart=/usr/bin/trojan-go -config /etc/trojan-go/config.json
Restart=on-failure
RestartSec=10s
LimitNOFILE=infinity

[Install]
WantedBy=multi-user.target
```

2. 将 `trojan-go` 复制到 `/usr/bin` 下，并将 `config.json` 复制到 `/etc/trojan-go` 下

```
cp trojan-go /usr/bin
cp config.json /etc/trojan-go/
```

3. 启用 trojan-go

```
systemctl enable trojan-go
```

## 注意事项

1. 域名必须对应正确的证书
