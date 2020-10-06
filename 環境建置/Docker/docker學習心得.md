## 新手上路 ##
- 安裝 [document](https://docs.docker.com/engine/install/centos/)
    - CENTOS 8 需要依賴 **containerd.io**
    - `dnf install -y https://download.docker.com/linux/centos/8/$(uname -m)/stable/Packages/containerd.io-1.3.7-3.1.el8.$(uname -m).rpm`

- 基本上安裝好docker後，僅會有root在docker的群組權限之內，因此若要讓一般或者其他帳號使用
  - 需要將使用者加入docker群組
    `usermod -aG docker $USER`
    `gpasswd -a $USER docker`
  - 瀏覽使用者群組
    `groups [使用者名稱]`
  - 需要將使用者移除docker群組
    `gpasswd -d $USER docker`

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

### 容器(containers) ###
  - 查詢目前安裝容器
    `docker ps -a`
  - 創建並執行容器
    `docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=admin --name dockermariadb mariadb:latest`
    `docker run -d -it --name dk_centos centos:latest`
  - 執行目前已存在之容器
    `docker start my_container`
  - 停止執行容器
    `docker stop my_container`
  - 進入容器指令介面
    `docker exec -it [容器Id] bash`

### 容器(volume) ###
  - 新增儲存倉庫
    `docker volume create [volume_name]`
  - 都會存放在 host 特定位置 **/var/lib/docker/volumes/**


### 網路(network) ###
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

- LAMP
  - mariadb
    ```
    docker volume create mariadb_conf
    docker volume create mariadb_data
    docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=admin --name dockermariadb --restart always -v mariadb_conf:/etc/mysql -v mariadb_data:/var/lib/mysql mariadb:latest
    ```

  - Apache
    ```
    ```

  - PHP
    ```
    ```

  - docker-compose
    ```yml
    ```


## 指令參數說明 ##
- **docker volumn**
  - `create` 創建volume
    - `-o` 其他選項(但目前還沒用到)
  - `inspect` 列出詳細資訊關於某個 volume
  - `ls` 列出volume
  - `prune` 刪除所有沒用到的volume
  - `rm` 刪除一個或多個volume

- **docker run**
  - `--rm` 暫時執行，若退出容器container即刪除
  - `-d` 在背景執行
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


## docker-compose ##
- 安裝
- x86_64
  - `sudo curl -L "https://github.com/docker/compose/releases/download/1.27.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
  - `sudo chmod +x /usr/local/bin/docker-compose`
- arm
  - `sudo curl -L --fail https://github.com/docker/compose/releases/download/1.27.2/run.sh -o /usr/local/bin/docker-compose`
  - `sudo chmod +x /usr/local/bin/docker-compose`

- 設定檔 (.env) file
  - 此設定檔可以預先設定需要使用的程式以及版本
    ```env

    ```
