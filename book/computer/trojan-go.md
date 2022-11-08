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

acme.sh --issue t.alvinhtml.com -w ~/.acme.sh/www.alvinhtml.com/
```

## nohup ./trojan-go -config config.json &
