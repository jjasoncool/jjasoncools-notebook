## 新手上路 ##
- ubuntu安裝 [document](https://docs.docker.com/engine/install/ubuntu/)
- centos安裝 [document](https://docs.docker.com/engine/install/centos/)
  - [安裝包網址](https://download.docker.com/linux/)
  - CENTOS 8 需要依賴 **containerd.io**
  - `dnf install -y https://download.docker.com/linux/centos/8/$(uname -m)/stable/Packages/containerd.io-1.4.9-3.1.el8.$(uname -m).rpm`

- 基本上安裝好docker後，僅會有root在docker的群組權限之內，因此若要讓一般或者其他帳號使用
  - 需要將使用者加入docker群組
    `usermod -aG docker $USER`
    `gpasswd -a $USER docker`
  - 瀏覽使用者群組
    `groups [使用者名稱]`
  - 需要將使用者移除docker群組
    `gpasswd -d $USER docker`

  - container 若有對應需要的 user id ，使用
    `sudo useradd -u [id] -G [group] -Ml [username]`

- docker 使用 iptables 配置，若使用新版 redhat 系產品，需修改`/etc/firewalld/firewalld.conf`
  - 將 `FirewallBackend` 選項修改為 **iptables**，避免一些網路問題
  - 或者將 docker 網路介面新增至 trust `firewall-cmd --permanent --zone=trusted --add-interface=docker0`

## 指令參數說明 ##
- **docker volumn**
  - `create` 創建volume
    - `-o` 其他選項(但目前還沒用到)
  - `inspect` 列出關於某個 volume 的詳細資訊
  - `ls` 列出volume
  - `prune` 刪除所有沒用到的volume
  - `rm` 刪除一個或多個volume

- **docker run**
  - `-rm` 暫時執行，若退出容器container即刪除
  - `-e` 系統變數，對於此 container 生效，尤其是時區等需要預先設定
  - `-d` 在背景執行
  - `-w` 工作資料夾
  - `-name` 別名
  - `--restart` 當 docker 服務重啟或者重新啟動容器時，此設定可影響 container 的動作
    | flag           | description                                                       |
    | -------------- | ----------------------------------------------------------------- |
    | no             | 預設不重啟 container                                              |
    | on-failure     | 重啟 container 當錯誤碼 非0值時(也就是出錯才重啟，重開機不會重啟) |
    | always         | 總是重啟 container 當守護進程重啟或是手動重啟                     |
    | unless-stopped | 只會在手動停止container 時重啟                                    |
  - `-v` volume 將 container 中的一個區域對照到本機中 [host 路徑或 volume 名稱]:[container 路徑]:[options]
    - *options* {rw:正常讀寫, ro:唯讀 (Note that the folder is then read-only in the container and read-write on the host)}
  - example: `docker run --name chia-farm -d -v chia_temp:/temp_plots -v chia_plots:/plots -e TZ=Asia/Taipei ghcr.io/chia-network/chia`

## 基本指令 ##
### 映像(images) ###
  - 搜尋要安裝的映像名稱
    `docker search contos`
  - 下載要安裝的映像名稱 (加註 tag 可以指定版本)
    `docker pull contos`
    `docker pull contos:6.10`
  - 查詢目前下載image
    `docker images`
  - 刪除目前下載image
    `docker rmi [image]`
  - 自己建立映像擋
    `docker build ./ -t php:phpcs`

### 容器(containers) ###
  - 查詢目前安裝容器
    `docker ps -a`
  - 創建並執行容器
    `docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=admin --name dockermariadb mariadb:latest`
    `docker run -d -it --name dk_centos centos:latest`
  - 執行目前已存在之容器
    `docker start my_container`
  - 修改容器名稱
    `docker rename CONTAINER NEW_CONTAINER_NAME`
  - 停止執行容器
    `docker stop my_container`
  - 進入容器指令介面
    `docker exec -it [容器Id] bash`

### 倉儲(volume) ###
  - 新增儲存倉庫
    `docker volume create [volume_name]`
  - 都會存放在 host 特定位置 **/var/lib/docker/volumes/**
  - 指定 volume 位置 `docker volume create [vol_name] -o type=none -o o=bind -o device=[host_mount_point]`


### 網路(network) ###
  - docker 透過 proxy 連線下載 image
    1. 區域設定(for user)
       - `vi ~/.docker/config.json`
       - 輸入以下內容
            ```json
            {
                "proxies": {
                    "default": {
                    "httpProxy": "http://domain.com:8080",
                    "httpsProxy": "http://domain.com:8080",
                    "noProxy": "localhost,127.0.0.0/8"
                    }
                }
            }
            ```
    3. 全域設定(for service)
       - `sudo mkdir -p /etc/systemd/system/docker.service.d`
       - `vi /etc/systemd/system/docker.service.d/http-proxy.conf`
       - 輸入以下內容
            ```conf
            [Service]
            Environment="HTTP_PROXY=http://domain.com:8080"
            Environment="HTTPS_PROXY=http://domain.com:8080"
            Environment="NO_PROXY=localhost,127.0.0.1"
            ```

  - 新增用戶自訂網路
    `docker network create sk_service`

  - 取得 container 在 host 內的 ip
    `docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name_or_id`
    `docker inspect [containerID] | grep "IPAddress"`

  - 容器可連外網
    - 一般來說 container 不會直接使用 host 的網路，除非有 **port mapping** 或者下 `--network host` 參數，否則都是透過 host 內的 docker0網卡互相溝通
    - 防火牆問題
      1. 將 docker 網卡加入信任清單 `firewall-cmd --permanent --zone=trusted --change-interface=docker0 && firewall-cmd --reload`

    - DNS問題
      1. 容器要想存取外部網路，需要本地系統的轉發支援。在Linux 系統中，檢查轉發是否打開。
        `sysctl net.ipv4.ip_forward`
        `net.ipv4.ip_forward = 1`
      2. 如果為 0，說明沒有開啟轉發，則需要手動打開。
        `sysctl -w net.ipv4.ip_forward=1`
      3. 如果在啟動 Docker 服務的時候設定 --ip-forward=true, Docker 就會自動設定系統的 ip_forward 參數為 1。
      4. 檢查 iptables 是否有開放 docker 可連線至外網

  - 容器互相連接
    - CENTOS
      - 需要下此指令 container 之間網路才會通
        - `sudo vi /etc/firewalld/firewalld.conf` -> 修改 nftables to iptables
        - `sudo firewall-cmd --zone=public --add-masquerade --permanent` (開啟轉發，較不安全)

  - 容器新增 hostname
    - docker-compose 使用 network 項目，這樣在同一個 network 的 container 都會認為 html_12 就是 apache (可以配合 vhost 使用)
        ```yml
          apache:
            image: httpd:${APACHE_VERSION}
            ...
            networks:
              default:
                aliases:
                  - html_12
                  - CI
                  - ...
        ```

- LAMP
  - mariadb
    ```
    docker volume create mariadb_conf
    docker volume create mariadb_data
    docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=admin -e TZ=Asia/Taipei --name dockermariadb --restart always -v mariadb_conf:/etc/mysql -v mariadb_data:/var/lib/mysql mariadb:latest
    ```

  - PHP-CLI
    - 簡單來說，就是使用 docker run 把 host 目錄掛到 container 裡面，再使用該目錄執行 container 內的 php
        `docker run -it --rm -v $(pwd):/src -w /src php:cli php`
    - 掛到 host alias (但若程式指定要 php path 這招無效)
        `alias php="docker run --rm -v \$PWD:/source -w /source php:cli-alpine php"`
    - 使用 file 當作 php executable
        `sudo echo "exec docker run --rm -i -v \$PWD:/source -v /tmp:/tmp -v /opt:/opt -v /home:/home -w /source php:cli-alpine php \"\$@\"" > /usr/local/bin/php`

  - phpcs
        `sudo echo "exec docker run --rm -i -v \$PWD:/code -v /tmp:/tmp -v /opt:/opt -v /home:/home -w /code php:phpcs phpcs \"\$@\"" > /usr/local/bin/phpcs`

  - docker-compose
    ```yml

    ```

- node express + mongodb
  - mongodb
    `docker run -d -p 27017:27017 -e MONGO_INITDB_ROOT_PASSWORD=admin -e MONGO_INITDB_ROOT_USERNAME=root --name dkmongo mongo:latest`

## dockerfile ##
```dockerfile
# Use the official image as a parent image.
FROM node:current-slim

# Set the working directory.
WORKDIR /usr/src/app

# ADD has some features (like local-only tar extraction and remote URL support) that are not immediately obvious. Consequently, the best use for ADD is local tar file auto-extraction into the image
ADD package.json .

# Copy the file from your host to your current location.
COPY package.json .

# Run the command inside your image filesystem.
RUN npm install

# Add metadata to the image to describe which port the container is listening on at runtime.
EXPOSE 8080

# Run the specified command within the container.
CMD [ "npm", "start" ]

# 每次 container run 的時候都要執行的指令
ENTRYPOINT

# Copy the rest of your app's source code from your host to your image filesystem.
COPY . .
```

## docker-compose ##
- 安裝有三種方式，可自選，arm只能用第三種
  1. x86_64
     - `sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
     - `sudo chmod +x /usr/local/bin/docker-compose`
  2. INSTALL AS A CONTAINER (x86_64)
     - `sudo curl -L --fail https://github.com/docker/compose/releases/download/1.29.2/run.sh -o /usr/local/bin/docker-compose`
     - `sudo chmod +x /usr/local/bin/docker-compose`
  3. pip install
     - `sudo apt-get install python3-pip`
     - `sudo pip3 install docker-compose`

- 設定檔 (.env) file
  - 此設定檔可以預先設定需要使用的程式以及版本
    ```env
    MY_IMAGE_VERSION=latest
    ```
  - 在 docker-compose.yaml 中就可以使用 ${MY_IMAGE_VERSION}

- yaml 設定
    ```yaml
        #
        networks:
          default:
            aliases:
              - html_12
              - CI
              - symfony
        # An entry with the ip address and hostname is created in /etc/hosts inside containers
        extra_hosts:
        - "somehost:162.242.195.82"
        - "otherhost:50.31.209.229"
    ```
  - network

    In the example below, three services are provided (web, worker, and db), along with two networks (new and legacy). The db service is reachable at the hostname db or database on the new network, and at db or mysql on the legacy network.

    ```yaml
    version: "3.9"

    services:
    web:
        image: "nginx:alpine"
        networks:
        - new

    worker:
        image: "my-worker-image:latest"
        networks:
        - legacy

    db:
        image: mysql
        networks:
        new:
            aliases:
            - database
        legacy:
            aliases:
            - mysql

    networks:
    new:
    legacy:
    ```


- 操作指令
  - `build` 重新建立 image file
  - `config` 檢視要執行的 config 檔案，會將所有參數套用的結果顯示出來
  - `up` 啟動 docker-compose 服務
    - `-d` 背景執行
    - `--build` 重新依據 dockerfile build image
    - `--force-recreate` 強制重新 create container
  - `run`
    - `-d` 背景執行
