---
refcn: chapter_02/transport/websocket
refen: configuration/transport/websocket
---

# WebSocket

Use standard WebSocket to transport data. Websocket connections can be proxied by HTTP server such as Nginx.

## WebSocketObject

`WebSocketObject` is used as `wsSettings` in `TransportObject` or `StreamSettingsObject`.

{% hint style='info' %}

Websocket recognizes HTTP header X-Forwarded-For, and uses it as inbound source address.

{% endhint %}

```javascript
{
  "path": "/",
  "headers": {
    "Host": "v2ray.com"
  }
}
```

> `path`: string

Path used for WebSocket. Default to root, as `"/"`.

> `headers`: map{string, string}

Custom HTTP header. An array where each entry is a key value pair in string, for header and value in HTTP header. Default is empty.
