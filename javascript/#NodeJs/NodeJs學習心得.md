## nvm nodejs 版本管理系統 ##
- 基本套件安裝 (管理 nodejs 版本的系統)
  - nvm [github](https://github.com/nvm-sh/nvm)

    `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.2/install.sh | bash`
  - `nvm ls-remote | grep "Latest LTS"` 列出可以選用安裝的選項
  - `nvm install {version}` version 可以填版本號，如12
  - `nvm use {version}` 切換版本
  - `nvm alias default {version}` 預設版本

## nodejs 套件管理 yarn ##
  - 安裝
    1. 使用 repo 安裝
      - Ubuntu repo

        `curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -`
        `echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list`

      - Centos / Fedora repo

        `curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo`
        `sudo apt update && sudo apt install --no-install-recommends yarn`

    2. 使用 npm 安裝 (v2 suggestion)

        `npm install -g yarn`

    3. 使用 npm 內建 corepack
       - Node.js >=16.10
       - Corepack is included by default with all Node.js installs, but is currently opt-in. To enable it, run the following command:

        `corepack enable`

       - Node.js <16.10
       - Corepack isn't included with Node.js in versions before the 16.10; to address that, run:

        `npm i -g corepack`

  - proxy 連線問題

    ``

  - yarn 安裝套件位置 **不推薦，只有自己的電腦環境能用** (windows 要加到 path 內才有作用)

    `yarn global bin`

  - yarn 目錄初始化

    `yarn init`

    `yarn init -y`(如果不要問一堆的話)

  - yarn 安裝套件

    `yarn add <package name>`

  - yarn 安裝開發環境套件

    `yarn add -D webpack webpack-cli`

  - 安裝到指定的資料夾 (不使用預設的 node_modules)

    `yarn install --modules-folder <path>`

  - 放到 production 環境，Yarn 將不安裝列於 devDependencies 的套件

    `yarn --production`

## 基本語法 ##


## 開發環境 ##
### koa ###
  - `npx `

    執行 node server
  - `nodemon src/server.js`

## webpack ##
