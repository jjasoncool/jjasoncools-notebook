## 新手上路 ##
- 安裝 [document](https://docs.docker.com/engine/install/centos/)
    - CENTOS 8 需要依賴 **containerd.io**
    - `dnf install -y https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.13-3.2.el7.x86_64.rpm`

- 基本上安裝好docker後，僅會有root在docker的群組權限之內，因此若要讓一般或者其他帳號使用
  - 需要將使用者加入docker群組
    `usermod -aG docker $USER`

- 基本指令
  - 搜尋要安裝的容器名稱
    `docker search contos`
  - 下載要安裝的容器名稱
    `docker pull contos`
  - 查詢目前下載image
    `docker images`
  - 查詢目前安裝容器
    `docker ps -all`
  - 創建並執行容器
    `docker run --name dockermariadb -e MYSQL_ROOT_PASSWORD=admin -d mariadb`
  - 停止執行容器
    `docker stop my_container`

- LAMP
  - mariadb
    `docker run --name dockermariadb -e MYSQL_ROOT_PASSWORD=admin -d mariadb`


## 指令參數說明 ##
