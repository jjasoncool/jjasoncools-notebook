## 新手上路 ##
- 安裝 [document](https://docs.docker.com/engine/install/centos/)
    - CENTOS 8 需要依賴 **containerd.io**
    - `dnf install -y https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.13-3.2.el7.x86_64.rpm`

- 基本上安裝好docker後，僅會有root在docker的群組權限之內，因此若要讓一般或者其他帳號使用
  - 需要將使用者加入docker群組
    `usermod -aG docker $USER`

## 基本指令 ##
### 映像(images) ###
  - 搜尋要安裝的映像名稱
    `docker search contos`
  - 下載要安裝的映像名稱
    `docker pull contos`
  - 查詢目前下載image
    `docker images`
  - 刪除目前下載image
    `docker rmi [image]`

### 容器(containers) ###
  - 查詢目前安裝容器
    `docker ps -a`
  - 創建並執行容器
    `docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=admin --name dockermariadb mariadb`
  - 執行目前已存在之容器
    `docker start my_container`
  - 停止執行容器
    `docker stop my_container`
  - 進入容器指令介面
    `docker exec -it 容器Id bash`

### 網路(network) ###
  - 取得 container 在 host 內的 ip
    `docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name_or_id`
  - 容器可連外網
    - 一般來說 container 都是與 host 網路隔絕，除非有 **port mapping** 或者下 `--network host` 參數

    - DNS問題
      1. 容器要想存取外部網路，需要本地系統的轉發支援。在Linux 系統中，檢查轉發是否打開。
          `sysctl net.ipv4.ip_forward`
          `net.ipv4.ip_forward = 1`
      2. 如果為 0，說明沒有開啟轉發，則需要手動打開。
          `sysctl -w net.ipv4.ip_forward=1`
      3. 如果在啟動 Docker 服務的時候設定 --ip-forward=true, Docker 就會自動設定系統的 ip_forward 參數為 1。
      4. 檢查 iptables 是否有開放 docker 可連線至外網

- LAMP
  - mariadb
    `docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=admin --name dockermariadb mariadb`


## 指令參數說明 ##
