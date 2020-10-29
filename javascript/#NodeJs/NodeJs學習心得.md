## nvm nodejs 版本管理系統 ##
- 基本套件安裝
  - nvm [github](https://github.com/nvm-sh/nvm)
    `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash`
  - `nvm install {version}` version 可以填版本號，如12
  - `nvm use {version}` 切換版本


## nodejs 套件管理 yarn ##
  - 安裝
    `sudo apt update && sudo apt install --no-install-recommends yarn`
    `npm install -g yarn`
  - yarn 安裝套件位置 (windows 要加到 path 內才有作用)
    `yarn global bin`
  - yarn 安裝套件
    `yarn add -D webpack webpack-cli`
    - 安裝到指定的資料夾 (不使用預設的 node_modules)
    `yarn install --modules-folder <path>`

## 開發環境 ##
- nest
  -

## webpack ##
