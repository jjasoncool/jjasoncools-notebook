
## git server 建置 ##
這個作者很懶，什麼都沒有留下

## 開始使用 ##
在你安裝 Git 後首先應該做的事是設定使用者名稱及電子郵件。 這一點非常重要，因為每次 Git 的提交會使用這些資訊，而且提交後不能再被修改：
>   `git config --global user.name "jjasoncool"`\
>   `git config --global user.email forwork.jan@gmail.com`

再次提醒，若你有傳遞 --global 參數，只需要做這工作一次，因為在此系統，不論 Git 做任何事都會採用此資訊。
若你想指定不同的名字或電子郵件給特定的專案，只需要在該專案目錄內執行此命令，並確定未加上 --global 參數。

若你想檢查設定值，可使用 git config --list 命令列出所有 Git 在目前位置能找到的設定值：
>   `git config --list`

若你想編輯 git 設定檔，可由下列指令編輯
>   `git config --global --edit`


## EOL ##
- 取消 LF 與 CRLF 轉換
  - true 依據平台轉換該平台使用的換行字元
  - input 只轉換 push 的時候的 CRLF 為 LF
  - false 不轉換
    >   `git config --global core.autocrlf false`

## UTF8化(windows) ##
將git commit 內容以及設定都設定為utf-8
>   `git config --global gui.encoding utf-8`
>   `git config --global i18n.commitencoding utf-8`
>   `git config --global i18n.logoutputencoding utf-8`

避免中文文件或是檔案名被自動轉譯成路徑
>   `git config --global core.quotepath false`

# git ssh 連線方式 #
## 產生公私鑰 ##
- `ssh-keygen -t rsa -b 4096 -C "webserver_replica(註解)"`
- 預設將會把產生的金鑰存到 **/home/.ssh** 之下
- 可以自行取名 e.g. `~/.ssh/id_rsa.github`
- 執行後將會產出兩個檔案，一個為 xxx(私鑰) 與 xxx.pub(公鑰)，將xxx.pub內容複製到 gitlab/github 上
- 啟動 ssh-agent (加入~/.bashrc下) `eval "$(ssh-agent -s)" > /dev/null 2>&1`
- 列出目前加入清單的私鑰 `ssh-add -l`
- 手動加入私鑰`ssh-add <私鑰位置>`
  - 用指令一次加入的方式(加入~/.bashrc下) `ls ~/.ssh/id_rsa* | grep -v "\.pub" | xargs ssh-add > /dev/null 2>&1`
- 記得 `git remote add <使用ssh位址>`

# git bash 語法參考 #
## clone ##
克隆倉庫的命令格式是 git clone [url]
若你想要將倉儲克隆到「libgit2」以外名字的資料夾，只需要再多指定一個參數即可：
>   `git clone https://github.com/libgit2/libgit2 mylibgit`

這個命令做的事與上一個命令大致相同，只不過在本地創建的倉庫名字變為 mylibgit

## 連線到remote repository(簡稱repo) ##

可以查閱目前有遠端連接的repo
>   `git remote -v`

新增 remote
>   `git remote add <repo名稱> <repo連結>`

移除 remote
>   `git remote rm <repo名稱>`

更改 remote url
>   `git remote set-url <remote-name> <remote-url>`

這個命令會連到遠端專案，然後從遠端專案中將你還沒有的資料全部拉下來；
執行完成後，你應該會有那個遠端版本庫中所有分支的參照（reference）
（譯註：再次強調，遠端的分支在本地端的分身——遠端追蹤分支），可以隨時用來合併或檢視
>   `git fetch <repo名稱>`

刪除本地遠端分支 (config 可以設定)
>   `git config --global fetch.prune true`
>   `git fetch --prune <repo名稱>`

從遠端拉資料
>   `git checkout --track <remote>/<branch_name>`
>   `git pull <remote> <branch_name>`
拉錯分支可以這樣返回
>   `git reset <remote>/<branch_name>`

更新至遠端
-u : 若成功 push 之後，該 branch 將會被設定為預設 track 參照
-f : 強制覆寫遠端 git 資料
>   `git push -u <remote> <branch_name>`

遠端名稱更動

    你可以執行 git remote rename 來重新命名遠端的簡稱。
    例如：如果你想要將 pb 重新命名為 paul，
    你可以這樣使用 git remote rename：

>   `git remote rename pb paul`

移除遠端
>   `git remote rm paul`

## gitignore ##
編寫 .gitignore 檔案的模式規則如下：
- 空白列，或者以 # 開頭的列會被忽略。
- 可使用標準的 Glob 模式。
- 以斜線（/）開頭以避免路徑遞迴。（譯注：只忽略特定路徑；如果不以斜線開頭，則不管同名檔案或同名資料夾在哪一層都會被忽略。）
- 以斜線（/）結尾代表是目錄。
- 以驚嘆號（!）開頭表示將模式規則反向。

Glob 模式就像是 Shell 所使用的簡化版正規運算式（regular expressions）； 一個星號（*）匹配零個或多個字元、[abc] 匹配中括弧內的其中一個字元（此例為 a、b、c）、問號（?）匹配單一個字元、中括孤內的字以連字號連接（如：[0-9]）用來匹配任何在該範圍內的字元（此例為 0 到 9）； 你也可以使用二個星號用來匹配巢狀目錄；a/**/z 將會匹配到 a/z、a/b/z、a/b/c/z 等等。

以下是另一個 .gitignore 範例檔案：
```ini
    # 不要追蹤檔名為 .a 結尾的檔案
    *.a
    # 但是要追蹤 lib.a，即使上面已指定忽略所有的 .a 檔案
    !lib.a
    # 只忽略根目錄下的 TODO 檔案，不包含子目錄下的 TODO
    /TODO
    # 忽略 build/ 目錄下所有檔案
    build/
    # 忽略 doc/notes.txt，但不包含 doc/server/arch.txt
    doc/*.txt
    # 忽略所有在 doc/ 目錄底下的 .pdf 檔案
    doc/**/*.pdf
```
- 另一種不將本地文件的變更提交的方式
    將文件從 git status 忽略掉
    >   `git update-index --assume-unchanged `

    恢復忽略
    >   `git update-index --no-assume-unchanged `

    查詢哪些文件被忽略了
    >   `git ls-files -v | grep '^h'`
    >   `git ls-files -v | grep '^h' | awk '{print $2}' | xargs git update-index --no-assume-unchanged `


## 檔案修改狀態 ##
雖然 git status 輸出內容相當全面，但也相當囉嗦；
Git 另外提供一個簡潔輸出的選項，因此你可以以一種較精簡的方式來檢視你的修改；
如果你執行 git status -s 或 git status --short，
你可以從該命令得到一個相當簡單的輸出內容

想瞭解尚未預存的修改，輸入不帶其它參數的
>   `git diff`

如果你想檢視你已經預存而接下來將會被提交的內容，可以使用
>   `git diff --staged` （--staged 和 --cached 是同義選項）

這個命令比對的對象是「預存區」和「最後一次提交」

## 暫存檔案 ##
暫存全部已修改檔案
>   `git add -A (--all)`

暫存全部已修改檔案，影響範圍僅限當下目錄以及子目錄
>   `git add .`

取消暫存檔案
>   `git reset [路徑]`

## commit ##
>   `git commit -m "字串"`:新增commit message
>   `git commit --amend`:將暫存變更推送到最後一個commit的node (不新增新的node)
>   `git commit --amend --reset-author --no-edit`:修改最後一個 commit 的作者與 email
>   `git commit --fixup=<commit>`: 修正該commit，跟--amend同，但不問你commit message(不新增新的node)

### git commit message guide:
    feat: a new feature
    fix: a bug fix
    docs: changes to documentation
    style: formatting, missing semi colons, etc; no code change
    refactor: refactoring production code
    test: adding tests, refactoring test; no production code change
    chore: updating build tasks, package manager configs, etc; no production code change

## log ##
>   `git log --pretty=oneline --graph`

format，讓你可以指定自訂的輸出格式； 當需要輸出給機器分析時特別有用——因為明確地指定了格式，即可確定它不會因為更新 Git 而被更動：
```bash
git log --pretty=format:"%h - %an, %ar : %s"
ca82a6d - Scott Chacon, 6 years ago : changed the version number
085bb3b - Scott Chacon, 6 years ago : removed unnecessary test
a11bef0 - Scott Chacon, 6 years ago : first commit
```
>   `git reflog --pretty=oneline --graph`

可以查閱到所有的log紀錄

### 常用選項 ###
    只顯示最後 n 筆提交。
    -(n)

    列出特定日期後的提交。
    --since, --after
    `git log --pretty=oneline --since="9am" --until="12am" --after="2017-01"`

    列出特定日期前的提交。
    --until, --before

    列出作者名字符合指定字串的提交。
    --author

    列出提交者名字符合指定字串的提交。
    --committer

    列出提交訊息中符合指定字串的提交。
    --grep

    列出修改檔案中有加入或移除指定字串的提交。
    -S[字串]

## 指標(移動指標) ##
>   `git checkout <commit id>`
>   `git switch master`

## 復原動作 ##
確認之前的 git 操作紀錄
>   `git reflog`

將檔案移出預存區
>   `git reset HEAD <file>`

將檔案復原(已被修改的)，放棄儲存至最初commit的樣子
>   `git checkout -- <file or path>`
將檔案復原(已被修改的)，還原到特定的 commit
>   `git checkout <commit hash> -- <file or path>`

將檔案復原(新增的檔案)，放棄儲存至最初commit的樣子
>   `git clean -f <path>`

## commit 的相對指標 ##
- `HEAD~` 代表
  -
- `HEAD^` 代表
  -

## 暫存目前工作進度 ##
暫存目前的工作，建立暫時的commit
>   `git stash`
>   `git stash --include-untracked`
>   `git stash push -- ./pa/info/doc_company/`
查看目前的暫存清單
>   `git stash list`
將之前暫存的工作復原，但不會刪除在暫存清單內的stash
>   `git stash apply`
將之前暫存的工作復原，會刪除在暫存清單內的stash
>   `git stash pop`
將之前暫存的工作刪除
>   `git stash drop`
將之前暫存的工作清單全部清空(包含已損毀 stash@{xx} is not a stash-like commit)
>   `git stash clear`

## branch 分支 ##
主分支追蹤
>   `git branch --set-upstream-to=<remote>/master master`

瀏覽分支
>   `git branch -a`

創建分支
>   `git branch <branch_name>`
  - 與上述語句相同
    >   `git checkout -b <branch_name>`
  - 大寫B會將已存在同樣名稱的分支merge目前遷出分支的狀態，這樣就不需要再delete重新checkout
    >   `git checkout -B <branch_name>`

將分支上傳至線上
>   `git push <remote-name> <branch-name>`
上傳分支到線上，但建立另外的名稱
>   `git push <remote-name> <branch-name>:<the-new-branch-name-you-want>`

刪除分支
>   `git branch -d <branch_name>`

刪除遠端分支 (對，push 空的分支到線上=刪除)
>   `git push <remote-name> :<the-new-branch-name-you-want-delete>`

刪除分支依據 regular exp
>   `git branch --merged | grep -v \* | xargs git branch -d `

刪除所有已經merge的分支 (注意，一定要在master上面)\
-v 代表反向搜尋，\* 代表找目前所在的 branch 以外的
>   `git branch --merged | grep -v \* | xargs git branch -d `

## 合併分支 ##
- merge

    在 merge 的時候，合併後變成一個 commit 紀錄
    >   `git merge --squash <branch_name>`

- rebase
    - rebase 是將目前的 commit 重新經過計算 sha 值再成立 commits 到current branch 之上
    - 合併 commits，可以依據內容選擇合併 commit 或是保留
    >   `git rebase -i <commit hash>`
    - 若要復原 rebase 可以使用 ORIG_HEAD 回到 rebase 之前的狀態
    >   `git reset ORIG_HEAD --hard`

- 合併特定分支的某一檔案

    >   `git checkout -p <branch> <file>`

## 衝突 ##
- conflict

## 移除版控 ##
- 使用 .gitignore 檔案管理，若將規則加入後仍有追蹤檔案，代表該檔案已被追蹤，需要先將其從git移除
- .gitignore必須放在控管主目錄之下
- 使用 `git rm` 移除git版控檔案
    | flag             | description                                                                    |
    | ---------------- | ------------------------------------------------------------------------------ |
    | -r               | 遞迴刪除，資料夾下所有檔案都刪掉                                               |
    | --cached         | 僅刪除git的版控追蹤，並不會刪除到實體檔案 (通常必加，不然檔案就會真的被刪掉了) |
    | --ignore-unmatch | 如果沒有符合的檔案，將會忽略錯誤訊息                                           |
- 永久從版控中移除
1. 可利用`git filter-branch`
    >   `git filter-branch -f --index-filter 'git rm --cached --ignore-unmatch [檔案路徑]' --prune-empty --tag-name-filter cat -- --all`

    清除紀錄，並且將 reflog 設為逾期，並將垃圾清掉
    >   `git reflog expire --expire=now --all && git gc --prune=now`

2. 使用 `git filter-repo` **第三方套件，比git filter-branch快**

    安裝
    >    `pip3 install git-filter-repo`

    | flag           | description                                          |
    | -------------- | ---------------------------------------------------- |
    | --force (-f)   | 強制執行，請不要將此註記常駐，以避免造成不可逆之錯誤 |
    | --path         | 過濾指定的確切路徑，多個相同選項將會取聯集結果       |
    | --path-regex   | 使用正規表示式來規範路徑，多個相同選項將會取聯集結果 |
    | --invert-paths | 反向選取，原本為選取保留路徑                         |

    清除某資料夾之下檔案
    >   `git filter-repo --path-regex '^file/[\.\w-]+\.exe$' --invert-paths`

## add將本地已有檔案 與線上commit歷程合併
>   `git reset --hard <remote>/<branch_name>`

## 拆分子專案 ##
先把要拆的專案變成一個新 branch (subProjectPath 不要用 ./ 開頭)
>   `git subtree split -P <subProjectPath> -b <subProjectBranch>`
>   `cd <subProjectPath> && git init`
>   `git pull <subProjectPath> <subProjectBranch>`

## tags 標籤 ##
一個輕量級的標籤就像是一個不會移動的分支——這個標籤只會指向一個特定的提交。

然而，有註解的標籤，會在 Git 的資料庫中儲存成完整的物件。
它們將被計算校驗碼；包含貼標籤那個人的名字、電子郵件和日期；能夠紀錄一個標籤訊息；並且可以簽署及透過 GNU Privacy Guard (GPG) 驗證。
通常建議你可以建立一個有註解的標籤，以便你可以保留跟這個標籤有關的所有資訊；
但是你如果只想要一個暫時的標籤，或是因為某些原因不想保留額外的資訊，你也可以只用輕量級標籤。

建立一個有註解的標籤很簡單。 最簡單的方法是在你建立標籤時，同時指定 -a 的選項如下：

>   `git tag -a v1.4 -m "my version 1.4"`

另外一種能標記提交的標籤是輕量級標籤。 這基本上是把該提交的校驗碼存在一個檔案中，不包含其他資訊。 如果想要建立一個輕量級的標籤，不要指定 -a、-s 或 -m 的選項如下：

>   `git tag v1.4-lw`

如果想要一次推送很多標籤，也可以在使用 git push 指令的時候加上 --tags 選項。 這將會把你所有不在伺服器上面的標籤傳送給遠端伺服器。

>   `git push origin --tags`

假設你忘記在專案的「updated rakefile」提交貼 v1.2 的標籤。 你可以在後來再補貼標籤。要在那個提交上面貼標籤，你需要在指令後面指定那個提交的校驗碼（可以省略後半段）：

>   `git tag -a v1.2 9fceb02`

檢出標籤
在 Git 中你不能真的檢出一個標籤，因為它們並不能像分支一樣四處移動。
如果你希望工作目錄和版本庫中特定的標籤版本完全一樣，
你可以使用 git checkout -b [branchname] [tagname] 在該標籤上建立一個新分支：

>   `git checkout -b version2 v2.0.0`

當然，如果在建立新分支以後又進行了一次提交，version2 分支將會和 v2.0.0 標籤有所差異，因為這個分支已經因為你的提交而改變了，請特別小心。

## 增強功能 Git Aliases (別名) ##

如果你只打了某個指令的一部份，Git 並不會自動推測出你想要的指令。
如果你懶得輸入完整的 Git 指令，你可以輕易的使用 git config 來替指令設定別名。
下面有一些你可能會想要設定別名的範例：

>   `git config --global alias.co checkout`
>   `git config --global alias.br branch`
>   `git config --global alias.ci commit`
>   `git config --global alias.st status`

舉其中一個例子來說，這樣的設定意味著你可以只打 git ci 而不需要打 git commit。
隨著你深入使用 Git，你將會發現某些指令用的很頻繁，不要猶豫，馬上建立新的指令別名。

這個非常有用的技術還能用來創造一些你覺得應該存在的指令。 舉例來說，為了提高 unstage 檔案的方便性，你可以加入你自己的 unstage 別名：

>   `git config --global alias.unstage 'reset HEAD --'`

而且這個 unstage 別名會讓以下兩個指令有相同的功用：

>   `git unstage fileA`
>   `git reset HEAD -- fileA`

這樣看起來更加簡單明瞭了。 此外，大家通常還會新增一個 last 指令如下：

>   `git config --global alias.last 'log -1 HEAD'`

如此一來，你可以更簡易的看到最後的提交訊息：

>   `git last`

    commit 66938dae3329c7aebe598c2246a8e6af90d04646
    Author: Josh Goebel <dreamer3@example.com>
    Date:   Tue Aug 26 19:48:51 2008 +0800

        test for current head

        Signed-off-by: Scott Chacon <schacon@example.com>

如你所見，Git 會將別名直接取代成你別名內設定的指令。
然而，你可能會想要執行一個外部指令，而非 git 下的子指令。 在這個情況下，你需要在指令的開頭加個 ! 字元。
這個技巧在你如果想為 Git 倉儲撰寫自製工具時很有用。 我們可以用以下的範例設定 git visual 執行 gitk：

>   `git config --global alias.visual '!gitk'`
