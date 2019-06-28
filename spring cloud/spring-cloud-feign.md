# 开启gzip压缩请求

```yaml
feign:
  compression: # 压缩
      request: # 请求
        enabled: true # 开启
        mime-types: text/xml,application/xml,application/json   # 压缩类型
        min-request-size: 2048 #压缩大小
      response:
        enabled: true #压缩响应
```

