# Souei

Souei是一个Python异步代理池。

[English README](README_en.md)

Souei提供简单的API和基于squid的动态转发功能。理论上只需要设置为使用Souei提供的http代理地址，即可使用代理池。

Souei是依赖Docker设计，希望将复杂功能集为一个简单的整体。

## Features
* 基于Python异步、pyppeteer自动获取开放代理。
* 基于squid的动态转发功能。
* 提供简明的API。
* 使用sqlite存储数据。
* 定时验证代理是否可用。
* 提供Prometheus Metrics。

## Get start
创建Docker容器：
```shell
docker run -d -p 8000:8000 -p 3128:3218 --name souei zhshch/souei 
```

使用Docker Compose：

```yaml
version: '3'

services:
  souei:
    image: zhshch/souei
    restart: always
    ports:
      - 8000:8000 # API
      - 3128:3128 # Dynamic http proxy
      - 8001:8001 # Prometheus
    volumes:
      - ./souei:/app/data
```

创建并启动：

```shell
docker-composeup up -d
```

查看API：

```shell
curl 'http://localhost:8000'
```

使用动态转发：
```shell
curl -x http://localhost:3128 "https://api.ipify.org/?format=json"
```

### 环境变量
* SQUID_CONFIG：squid配置文件路径。
* http_proxy：用于爬取数据的代理，留空为不使用。默认：""
* VERIFY_LIMIT：验证代理通过比例。验证器会尝试访问多个网站，超过这个数值的网站成功视为通过。默认：0.5
* DOUBLE_VERIFY_ENABLE：是否启用延时验证。默认：True
* DOUBLE_VERIFY_DELAY：延时验证等待时间。单位：秒，默认：5
* PROXY_TIMEOUT：验证超时时间。单位：秒，默认：5
* VERIFY_ERROR_LIMIT：当可用代理小于该数值时，尝试从无效代理中寻找可用代理。默认：100
* MAX_ERROR_PROXIES：保留无效代理的数量。当无效代理过多时，将删除更新时间最远的代理。默认：2048


### Prometheus Metrics
* souei_available_proxy
* souei_error_proxy
* souei_new_proxy