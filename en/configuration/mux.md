---
refcn: chapter_02/mux
refen: configuration/mux
---

# Multiplexing

Multiplexing, or Mux, is to use one physical TCP connections for multiple virtual TCP connections.

Mux is designed to reduce TCP handshake latency. It is NOT for high throughput. When used for downloading large files or speed measurement, Mux is usually slower than a normal TCP connection.

## MuxObject

```javascript
{
  "enabled": false,
  "concurrency": 8
}
```

> `enabled`: true | false

Whether or not to enable Mux on an outbound.

> `concurrency`: number

Max number of multiplexed connections that one physical connection can handle at a time. Max value `1024`, min value `1`, default `8`.
