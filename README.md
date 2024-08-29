# sing-box 旁路由 透明代理 配置

此教程适用于直接在 linux 上部署了 sing-box，想要让 sing-box 代理局域网内的所有设备的场景。也即是 linux 机器作为旁路由。

下面假设主路由的 ip 为 192.168.31.1，旁路由的 ip 为 192.168.31.6，你应该根据你的情况，在下面的的配置中将它们替换成你自己的。

- 首先你要确保旁路由本身已经可以科学上网，sing-box 的配置可以参考我的，重点在 inbounds：

  ```json
  {
    "log": { "disabled": false, "level": "info", "timestamp": true },
    "dns": {
      "servers": [
        {
          "tag": "remote",
          "address": "https://1.1.1.1/dns-query",
          "detour": "select"
        },
        {
          "tag": "local",
          "address": "https://1.12.12.12/dns-query",
          "detour": "direct"
        },
        { "tag": "block", "address": "rcode://success" }
      ],
      "rules": [
        { "outbound": "any", "server": "local", "disable_cache": true },
        { "clash_mode": "Global", "server": "remote" },
        { "clash_mode": "Direct", "server": "local" },
        {
          "rule_set": "geosite-category-ads-all",
          "server": "block",
          "disable_cache": true
        },
        { "rule_set": ["geosite-apple"], "server": "local" },
        {
          "rule_set": ["geosite-geolocation-!cn", "geosite-anthropic"],
          "server": "remote"
        }
      ],
      "independent_cache": true
    },
    "inbounds": [
      {
        "type": "tun",
        "inet4_address": "172.19.0.1/30",
        "inet6_address": "fdfe:dcba:9876::1/126",
        "auto_route": true,
        "strict_route": false,
        "sniff": true,
        "sniff_override_destination": true
      }
    ],
    "outbounds": [
      {
        "tag": "direct",
        "type": "direct"
      },
      {
        "tag": "block",
        "type": "block"
      },
      {
        "tag": "dns-out",
        "type": "dns"
      }
      // 添加你自己的节点
    ],
    "route": {
      "rules": [
        { "protocol": "dns", "outbound": "dns-out" },
        { "clash_mode": "Direct", "outbound": "direct" },
        { "clash_mode": "Global", "outbound": "select" },
        { "rule_set": ["geosite-apple"], "outbound": "direct" },
        {
          "rule_set": [
            "geosite-geolocation-!cn",
            "geosite-anthropic",
            "geoip-cloudflare",
            "geoip-cloudfront",
            "geoip-facebook",
            "geoip-fastly",
            "geoip-google",
            "geoip-netflix",
            "geoip-telegram",
            "geoip-twitter"
          ],
          "outbound": "select"
        }
      ],
      "rule_set": [
        {
          "tag": "geosite-category-ads-all",
          "type": "remote",
          "format": "binary",
          "url": "https://cdn.jsdelivr.net/gh/lyc8503/sing-box-rules@rule-set-geosite/geosite-category-ads-all.srs",
          "download_detour": "direct",
          "update_interval": "1d"
        },
        {
          "tag": "geosite-geolocation-!cn",
          "type": "remote",
          "format": "binary",
          "url": "https://cdn.jsdelivr.net/gh/lyc8503/sing-box-rules@rule-set-geosite/geosite-geolocation-!cn.srs",
          "download_detour": "direct",
          "update_interval": "1d"
        },
        {
          "tag": "geosite-apple",
          "type": "remote",
          "format": "binary",
          "url": "https://cdn.jsdelivr.net/gh/lyc8503/sing-box-rules@rule-set-geosite/geosite-apple.srs",
          "download_detour": "direct",
          "update_interval": "1d"
        },
        {
          "tag": "geosite-anthropic",
          "type": "remote",
          "format": "binary",
          "url": "https://cdn.jsdelivr.net/gh/lyc8503/sing-box-rules@rule-set-geosite/geosite-anthropic.srs",
          "download_detour": "direct",
          "update_interval": "1d"
        },
        {
          "tag": "geoip-cloudflare",
          "type": "remote",
          "format": "binary",
          "url": "https://cdn.jsdelivr.net/gh/lyc8503/sing-box-rules@rule-set-geoip/geoip-cloudflare.srs",
          "download_detour": "direct",
          "update_interval": "1d"
        },
        {
          "tag": "geoip-cloudfront",
          "type": "remote",
          "format": "binary",
          "url": "https://cdn.jsdelivr.net/gh/lyc8503/sing-box-rules@rule-set-geoip/geoip-cloudfront.srs",
          "download_detour": "direct",
          "update_interval": "1d"
        },
        {
          "tag": "geoip-facebook",
          "type": "remote",
          "format": "binary",
          "url": "https://cdn.jsdelivr.net/gh/lyc8503/sing-box-rules@rule-set-geoip/geoip-facebook.srs",
          "download_detour": "direct",
          "update_interval": "1d"
        },
        {
          "tag": "geoip-fastly",
          "type": "remote",
          "format": "binary",
          "url": "https://cdn.jsdelivr.net/gh/lyc8503/sing-box-rules@rule-set-geoip/geoip-fastly.srs",
          "download_detour": "direct",
          "update_interval": "1d"
        },
        {
          "tag": "geoip-google",
          "type": "remote",
          "format": "binary",
          "url": "https://cdn.jsdelivr.net/gh/lyc8503/sing-box-rules@rule-set-geoip/geoip-google.srs",
          "download_detour": "direct",
          "update_interval": "1d"
        },
        {
          "tag": "geoip-netflix",
          "type": "remote",
          "format": "binary",
          "url": "https://cdn.jsdelivr.net/gh/lyc8503/sing-box-rules@rule-set-geoip/geoip-netflix.srs",
          "download_detour": "direct",
          "update_interval": "1d"
        },
        {
          "tag": "geoip-telegram",
          "type": "remote",
          "format": "binary",
          "url": "https://cdn.jsdelivr.net/gh/lyc8503/sing-box-rules@rule-set-geoip/geoip-telegram.srs",
          "download_detour": "direct",
          "update_interval": "1d"
        },
        {
          "tag": "geoip-twitter",
          "type": "remote",
          "format": "binary",
          "url": "https://cdn.jsdelivr.net/gh/lyc8503/sing-box-rules@rule-set-geoip/geoip-twitter.srs",
          "download_detour": "direct",
          "update_interval": "1d"
        }
      ],
      "auto_detect_interface": true,
      "final": "direct"
    }
  }
  ```

- 修改旁路由的 ip 获取方式为手动。

  地址：192.168.31.6

  子网掩码：255.255.255.0

  网关：192.168.31.1

  DNS：192.168.31.1

- 修改主路由的 DHCP。

  网关：192.168.31.6

  DNS：192.168.31.6

以上是大多数旁路由或者透明代理教程会说到的。

相信很多人这些都做完了，但是局域网设备完全不能上网，下面是重点。

- 在旁路由上修改转发规则。

  修改/etc/nftables.conf，添加以下规则

```
# 定义表
table ip nat {
    chain postrouting {
        type nat hook postrouting priority 100; policy accept;

        # 对192.168.31.0/24网段(除了192.168.31.1)的流量进行NAT
        ip saddr 192.168.31.0/24 ip saddr != 192.168.31.1 oif "tun0" masquerade
    }
}
```

让它生效

```
sudo nft -f /etc/nftables.conf
```

- 在旁路由上修改 DNS 监听规则。

  修改/etc/systemd/resolved.conf，添加以下规则

```
DNSStubListenerExtra=0.0.0.0
DNSStubListenerExtra=::
```

让它生效

```

sudo systemctl restart systemd-resolved

```

好了，恭喜你 🎉🎉🎉，现在你局域网的设备都可以科学上网了。

帮到你的话，请点点右上角的 Star。
