---
refcn: chapter_02/03_routing
refen: configuration/routing
---
# 라우팅

V2Ray는 내부 라우팅 메커니즘을 가지고 있습니다. 규칙에 따라 인바운드 연결을 다양한 아웃 바운드로 라우팅합니다. 일반적인 시나리오는 국가별로 트래픽을 분할하는 것입니다. V2Ray는 연결의 대상 국가 (Geo IP 기준)를 감지하고 해당 아웃 바운드 프록시에 연결을 보냅니다.

## RoutingObject

`RoutingObject` 은 최상위 레벨 구성에서 `라우팅` 으로 사용됩니다.

```javascript
{
  "domainStrategy": "AsIs",
  "rules": [],
  "balancers": []
}
```

> `domainStrategy`: "AsIs"| "IPIfNonMatch"| "IPOnDemand"

도메인 분석 전략. 선택 사항은 다음과 같습니다.

* `"AsIs"`: 라우팅에만 도메인을 사용하십시오. 기본값.
* `"IPIfNonMatch"`: 현재 도메인과 일치하는 규칙이 없으면 V2Ray가이를 IP 주소 (A 또는 AAAA 레코드)로 확인하고 모든 규칙을 다시 시도합니다. 
  * 도메인에 여러 IP 주소가있는 경우 V2Ray는 모든 IP 주소를 시도합니다.
  * 해결 된 IP는 라우팅 결정에만 사용되며 트래픽은 여전히 ​​원래 도메인 주소로 전송됩니다.
* `"IPOnDemand"`: IP 기반 규칙이있는 한 V2Ray는 도메인을 즉시 IP로 해석합니다.

> `규칙`: \ [[RuleObject](#ruleobject)\]

An array of rules. For each inbound connection, V2Ray tries these rules from top down one by one. If a rule takes effect, the connection will be routed to the `outboundTag` (or `balancerTag`, V2Ray 4.4+) of the rule.

> `balancers`: \[ [BalancerObject](#balancerobject) \]

(V2Ray 4.4+) An array of load balancers. When a routing rule points to a load balancer, the balancer will select an outbound based on configuration. Then traffic will be sent to that outbound.

### RuleObject

```javascript
{
  "type": "field",
  "domain": [
    "baidu.com",
    "qq.com",
    "geosite:cn"
  ],
  "ip": [
    "0.0.0.0/8",
    "10.0.0.0/8",
    "fc00::/7",
    "fe80::/10",
    "geoip:cn"
  ],
  "port": "53,443,1000-2000",
  "network": "tcp",
  "source": [
    "10.0.0.1"
  ],
  "user": [
    "love@v2ray.com"
  ],
  "inboundTag": [
    "tag-vmess"
  ],
  "protocol":["http", "tls", "bittorrent"],
  "outboundTag": "direct",
  "balancerTag": "balancer"
}
```

{% hint style='info' %}

When multiple fields are specified, these fields have to be all satisfied, in order to make the rule effective. If you need both `domain` and `ip` rules, it is highly likely you need put them into separate rules.

{% endhint %}

> `type`: "field"

The only valid value for now is `"field"`.

> `domain`: \[ string \]

An array of domains. Available formats are:

* 일반 텍스트 :이 문자열이 타겟팅 도메인의 일부와 일치하는 경우이 규칙은 효력을 갖습니다. 예 : 규칙 `"sina.com"` 은 도메인 `"sina.com"`, `"sina.com.cn"` 및 `"www.sina.com"`타겟팅하지만 `"sina.cn은 타겟팅하지 않습니다.`.
* 정규 표현식 : `"regexp :"`, 나머지는 정규 표현식입니다. 정규 표현식이 타겟팅 도메인과 일치하면이 규칙이 적용됩니다. 예 : 규칙 `"regexp : \\. goo. * \\. com $"` 은 `"www.google.com"` 및 `"fonts.googleapis.com"`와 일치하지만 `"google.com"`과 일치하지 않습니다. </code> .
* 하위 도메인 (권장) : `"도메인 :"` 로 시작하고 나머지는 도메인입니다. 타겟팅 도메인이 정확히 값이거나 값의 하위 도메인 인 경우이 규칙이 적용됩니다. 예 : 규칙 `"도메인 : v2ray.com"` 은 `"www.v2ray.com"`, `"v2ray.com"`와 일치하지만 `"xv2ray.com"은 일치하지 않습니다.`.
* 전체 도메인 : `"전체 :"` 나머지는 도메인입니다. 타겟팅 도메인이 정확히 값일 경우 규칙이 적용됩니다. 예 : 규칙 `"도메인 : v2ray.com"` 은 `"v2ray.com"`과 일치하지만 `"www.v2ray.com"과 일치하지 않습니다.`.
* Pre-defined domain list: Begining with `"geosite:"` and the rest is a name, such as `geosite:google` or `geosite:cn`. See [Pre-defined domain list](#pre-defined-domain-lists) for more detail.
* Domains from file: Such as `"ext:file:tag"`. The value must begin with `ext:` (lowercase), and followed by filename and tag. The file is placed in [resource directory](env.md#location-of-v2ray-asset), and has the same format of `geosite.dat`. The tag must exist in the file.

> `ip`: \[string\]

An array of IP ranges. When the targeting IP is in one of the ranges, this rule takes effect. Available formats:

* IP : 예 : `"127.0.0.1"`.
* [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing):과 같은 `"127.0.0.0/8"`.
* GeoIP : `"geoip : cn"`. 그것은으로 시작된다. `geoip :` (소문자)를 입력하고 2 자의 국가 코드를 입력하십시오. 
  * 특별 값 `"geoip : 비공개"`: `와 같은 모든 비공개 주소 127.0.0.1`.
* 파일에서 IP : `"ext : file : tag"`. 값은 `ext :` (소문자)로 시작해야하며 그 다음에 파일 이름과 태그가 와야합니다. 파일은 [리소스 디렉토리](env.md#location-of-v2ray-asset)에 저장되며 `geoip.dat와 같은 형식을가집니다.`. 태그는 파일에 존재해야합니다.

{% hint style='info' %}

`"ext:geoip.dat:cn"` is equivalent to `"geoip:cn"`.

{% endhint %}

> `port`：number | string

Port range. Formats are:

* `"a-b"`: Both `a` and `b` are positive integers and less than 65536. When the targeting port is in [`a`, `b`), this rule takes effect.
* `a`: `a` is a positive integer, and less than 65536. When the targeting port is `a`, this rule takes effect.
* Mix of the two above, separated by ",". Such as `"53,443,1000-2000"`.

> `network`: "tcp" | "udp" | "tcp,udp"

When the connection has in the chosen network, this rule take effect.

> `source`: \[string\]

An array of IP ranges. Same format as `ip`. When the source IP of the connection is in the IP range, this rule takes effect.

> `user`: \[string\]

An array of email address. When the inbound connection uses an user account of the email address, this rule takes effect. For now Shadowsocks and VMess support user with email.

> `inboundTag`: \[string\]

An array of string as inbound proxy tags. When the connection comes from one of the specified inbound proxy, this rule takes effect.

> `protocol`: \[ "http" | "tls" | "bittorrent" \]

An array of string as protocol types. When the connection uses one of the protocols, this rule takes effect. To recognize the protocol of a connection, one must enable `sniffing` option in inbound proxy.

> `outboundTag` string

[Tag of the outbound](protocols.md) that the connection will be sent to, if this rule take effect.

> `balancerTag`: string

Tag of an load balancer. Then this rule takes effect, V2Ray will use the balancer to select an outbound. Either `outboundTag` or `balancerTag` must be specified. When both are specified, `outboundTag` takes priority.

### BalancerObject

Configuration for a load balancer. When a load balancer takes effective, it selects one outbound from matching outbounds. This outbound will be used for send out-going traffic.

```javascript
{
  "tag": "balancer",
  "selector": []
}
```

> `tag`: string

Tag of this `BalancerObject`, to be matched from `balancerTag` in `RuleObject`.

> `selector`: \[ string \]

An array of strings. These strings are used to select outbounds with prefix matching. For example, with the following outbound tags: `[ "a", "ab", "c", "ba" ]`，selector `["a"]` matches `[ "a", "ab" ]`.

When multiple outbounds are selected, load balancer for now picks one final outbound at random.

## Pre-defined domain lists

This is a domain lists maintained by [domain-list-community](https://github.com/v2ray/domain-list-community) project. It provides a file named `geosite.dat` for some predefined domain lists. Notably:

* `category-ads`: Common ads domains.
* `category-ads-all`: Common ads domains and ads providers' domains.
* `cn`: Equivalent to an union of `geolocation-cn` and `tld-cn`.
* `google`: All Google domains.
* `facebook`: All Facebook domains.
* `geolocation-cn`: Common domains that serve in China.
* `geolocation-!cn`: Common domains that don't serve in China
* `speedtest`: All domains used by Speedtest.
* `tld-cn`: All .cn and .中国 domains.