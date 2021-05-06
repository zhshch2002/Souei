# Souei
Souei is a Python asynchronous proxy pool.

Souei provides a simple API and dynamic forwarding function based on squid. Theoretically, only need to set the http proxy address provided by Souei to use the proxy pool.

Souei is dependent on Docker and wants to set complex functionality into a simple whole.

## Features
* Python asynchronous, pyppeteer based automatic fetching of open proxies.
* Squid based dynamic forwarding function.
* Provides a clear and concise API.
* Use sqlite to store data.
* Verifies proxy availability at scheduled intervals.
* Provides Prometheus Metrics。

## Get start
To create a Docker container.
```shell
docker run -d -p 8000:8000 -p 3128:3218 --name souei zhshch/souei 
```

Using Docker Compose.

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

Create and start.

```shell
docker-composeup up -d
```

Check API.

```shell
curl 'http://localhost:8000'
```

Using dynamic forwarding.
```shell
curl -x http://localhost:3128 "https://api.ipify.org/?format=json"
```

### 环境变量
* SQUID_CONFIG：The path to the squid configuration file.
* http_proxy：Proxy for crawling data, leave blank for not using. Default: ""
* VERIFY_LIMIT：Verify the proxy pass ratio. The validator will try to access multiple sites, and sites that exceed this value are successfully considered passed. Default: 0.5
* DOUBLE_VERIFY_ENABLE：Whether to enable delayed authentication. Default: True
* DOUBLE_VERIFY_DELAY：Delay verification wait time. Unit: seconds, default: 5
* PROXY_TIMEOUT：Authentication timeout. Unit: seconds, default: 5
* VERIFY_ERROR_LIMIT：Attempts to find available proxies from invalid ones when the available proxies are less than this value. Default: 100
* MAX_ERROR_PROXIES：The number of invalid proxy to keep. When there are too many invalid proxy, the proxy with the farthest update time will be deleted. Default: 2048


### Prometheus Metrics
* souei_available_proxy
* souei_error_proxy
* souei_new_proxy