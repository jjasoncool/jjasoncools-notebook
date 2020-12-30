## nvm nodejs 版本管理系統 ##
- 基本套件安裝 (管理 nodejs 版本的系統)
  - nvm [github](https://github.com/nvm-sh/nvm)
    `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash`
  - `nvm ls-remote | grep "Latest LTS"` 列出可以選用安裝的選項
  - `nvm install {version}` version 可以填版本號，如12
  - `nvm use {version}` 切換版本

## nodejs 套件管理 yarn ##
  - Ubuntu repo
    `curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -`
    `echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list`
  - Centos / Fedora repo
    `curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo`
  - 安裝
    `sudo apt update && sudo apt install --no-install-recommends yarn` (suggestion)
    `npm install -g yarn`

  - yarn 安裝套件位置 **不推薦，只有自己的電腦環境能用** (windows 要加到 path 內才有作用)
    `yarn global bin`
  - yarn 安裝套件
    `yarn add <package name>`
  - yarn 安裝開發環境套件
    `yarn add -D webpack webpack-cli`
  - 安裝到指定的資料夾 (不使用預設的 node_modules)
    `yarn install --modules-folder <path>`

  - 放到 production 環境，Yarn 將不安裝列於 devDependencies 的套件
    `yarn --production`

## 開發環境 ##
- nest
  -

## webpack ##
