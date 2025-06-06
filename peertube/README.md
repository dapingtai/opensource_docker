# PeerTube

**PeerTube**是一個[自由](https://zh.wikipedia.org/wiki/%E8%87%AA%E7%94%B1%E8%BD%AF%E4%BB%B6 "自由軟體")、[去中心化](https://zh.wikipedia.org/wiki/%E5%8E%BB%E4%B8%AD%E5%BF%83%E5%8C%96 "去中心化")、[互聯](https://zh.wikipedia.org/wiki/%E4%BA%92%E8%81%AF "互聯")的視訊平臺，其使用[對等網路](https://zh.wikipedia.org/wiki/%E5%B0%8D%E7%AD%89%E7%B6%B2%E8%B7%AF "對等網路")來減少單一伺服器的負載。其使用[Affero通用公眾授權條款](https://zh.wikipedia.org/wiki/Affero%E9%80%9A%E7%94%A8%E5%85%AC%E5%85%B1%E8%AE%B8%E5%8F%AF%E8%AF%81 "Affero通用公眾授權條款")釋出。

PeerTube的開發於2015年開始，目前由法國的非營利組織[Framasoft](https://zh.wikipedia.org/w/index.php?title=Framasoft&action=edit&redlink=1)支援。其目標是提供集中式平臺（如[YouTube](https://zh.wikipedia.org/wiki/YouTube "YouTube")、[Vimeo](https://zh.wikipedia.org/wiki/Vimeo "Vimeo")或[Dailymotion](https://zh.wikipedia.org/wiki/Dailymotion "Dailymotion")）的替代品。

## 部屬位置:

### Server:

1. Dev/Uat
2. Master

### DB: 備份?

### Ps. 先確認系統特性，統一volumn

## Wiki:

### <https://zh.wikipedia.org/zh-tw/PeerTube>

### 架構: SPA

#### 前端: Angular

#### 後端: NodeJs

### DB: PostgreSQL

#### Nginx

#### Redis

## GIT: <https://github.com/Chocobozzz/PeerTube/tree/develop>

## Docker: <https://hub.docker.com/r/chocobozzz/peertube/dockerfile>

## GUID:

## <https://docs.joinpeertube.org/install/docker>

## 內部部屬指引:

```
1. curl https://raw.githubusercontent.com/Chocobozzz/PeerTube/master/support/docker/production/.env > .env
2. vim docker-compose.yml
version: "3.3"

services:

  # You can comment this webserver section if you want to use another webserver/proxy or test PeerTube in local
  webserver:
    image: chocobozzz/peertube-webserver:latest
    # If you don't want to use the official image and build one from sources:
    # build:
    #   context: .
    #   dockerfile: Dockerfile.nginx
    env_file:
      - .env
    ports:
     - "80:80"
     - "443:443"
    volumes:
      - type: bind
        # Switch sources if you downloaded the whole repository
        #source: ../../nginx/peertube
        source: ./docker-volume/nginx/peertube
        target: /etc/nginx/conf.d/peertube.template
      - assets:/var/www/peertube/peertube-latest/client/dist:ro
      - ./docker-volume/data:/var/www/peertube/storage
      - ./docker-volume/nginx/ssl:/etc/nginx/ssl
    depends_on:
      - peertube
    restart: "always"

  peertube:
    # If you don't want to use the official image and build one from sources:
    # build:
    #   context: .
    #   dockerfile: ./support/docker/production/Dockerfile.bullseye
    image: chocobozzz/peertube:production-bullseye
    # Use a static IP for this container because nginx does not handle proxy host change without reload
    # This container could be restarted on crash or until the postgresql database is ready for connection
    networks:
      default:
        ipv4_address: 172.18.0.42
    env_file:
      - .env

    ports:
     - "1935:1935" # Comment if you don't want to use the live feature
    #  - "9000:9000" # Uncomment if you use another webserver/proxy or test PeerTube in local, otherwise not suitable for production
    volumes:
      - assets:/app/client/dist
      - ./docker-volume/data:/data
      - ./docker-volume/config:/config
    depends_on:
      - postgres
      - redis
      - postfix
    restart: "always"

  postgres:
    image: postgres:13-alpine
    env_file:
      - .env
    volumes:
      - ./docker-volume/db:/var/lib/postgresql/data
    restart: "always"

  redis:
    image: redis:6-alpine
    volumes:
      - ./docker-volume/redis:/data
    restart: "always"

  postfix:
    image: mwader/postfix-relay
    env_file:
      - .env
    volumes:
      - ./docker-volume/opendkim/keys:/etc/opendkim/keys
    restart: "always"

networks:
  default:
    ipam:
      driver: default
      config:
      - subnet: 172.18.0.0/16

volumes:
  assets:

3. mkdir -p docker-volume
```

1. mkdir -p docker-volume/nginx

   curl https://raw.githubusercontent.com/Chocobozzz/PeerTube/master/support/nginx/peertube > docker-volume/nginx/peertube
2. mkdir -p docker-volume/nginx/ssl

   放入憑證 /${WEBSERVER_HOST}/fullchain.crt

   放入密鑰 /${WEBSERVER_HOST}/privkey.key
3. vim docker-volume/peertub

   憑證位置更換成第5項的location
4. [Configure Docker to use a proxy server ](https://docs.docker.com/network/proxy/)=> 方能安裝Plugin

## 非官方部屬指引(上傳檔案時會有9000port問題):

```
peertube是開源的串流影音平台, 很適合拿來管理私人影音檔案. 
本文安裝的先決條件是:
已備有httpd反向代理, 以及lets encrypt 
因此不使用peertube官方自帶的webserver與cerbot, 以免造成管理上的困擾

1. 下載設定檔案
// develop
curl https://raw.githubusercontent.com/chocobozzz/PeerTube/develop/support/docker/production/docker-compose.yml > docker-compose.yml 
// production
curl https://raw.githubusercontent.com/chocobozzz/PeerTube/master/support/docker/production/docker-compose.yml > docker-compose.yml

修改docker-compose.yml
取消webserver
#  webserver:
#    image: chocobozzz/peertube-webserver:latest
    # If you don't want to use the official image and build one from sources:
    # build:
    #   context: .
    #   dockerfile: Dockerfile.nginx
#    env_file:
#      - .env
...
...

取消letsencrypt
  # You can comment this certbot section if you want to use another webserver/proxy
#  certbot:
#    container_name: certbot
#    image: certbot/certbot
#    volumes:
...
...

修改 
#- "1935:1935" # 我不用live所以遮掉, If you don't want to use the live feature, you can comment this line
- "9000:9000" # 這個要讓我外面的web server做反向代理使用, If you provide your own webserver and reverse-proxy, otherwise not suitable for production


2. 
// develop
curl https://raw.githubusercontent.com/Chocobozzz/PeerTube/develop/support/docker/production/.env > .env
// master
curl https://raw.githubusercontent.com/Chocobozzz/PeerTube/master/support/docker/production/.env > .env

修改.env
修改以下的變數
<MY POSTGRES USERNAME>:資料庫名稱
<MY POSTGRES PASSWORD>:密碼
<MY DOMAIN>:主機完整名稱xxx.yyy.zz(不需要加上https://)
<MY EMAIL ADDRESS>:管理者外部可接收到信箱

3. 執行
  mkdir -p docker-volume
  docker-compose up -d 
若出現以下錯誤, 代表使用的ip網段已經有人使用
Creating network "peertube_default" with the default driver
ERROR: Pool overlaps with other one on this address space
這時候可以下以下指令,查看目前docker網路使用狀況, 再修改步驟1,2設定檔案裡面的網路設定
docker network inspect $(docker network ls -q)|grep -E "IPv(4|6)A"

4. 取得首次登入的帳號與密碼
docker-compose logs peertube | grep -A1 root

5. 測試
http://ip:9000

6. 將先前已有的httpd反向代理指向9000, 搭配lets encrypt就可以完成了
https://ip

--
william 20210701
docker 20.10.3
CentOS 7.9.2009
peertube 3.x
```