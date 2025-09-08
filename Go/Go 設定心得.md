𐄍 檢查更新
# 檢查所有可用更新
go list -u -m all

# 檢查特定模組的更新
go list -u -m github.com/gin-gonic/gin

# 更新所有直接依賴到最新版本
go get -u ./...

𐄄 更新依賴
# 更新所有依賴（包括間接依賴）到最新版本
go get -u all

# 更新特定套件
go get -u github.com/gin-gonic/gin

# 更新到特定版本
go get github.com/gin-gonic/gin@v1.10.0

# 更新到最新的 patch 版本（安全更新）
go get -u=patch ./...

𐇹 清理依賴
# 清理未使用的依賴，並確保 go.mod 和 go.sum 一致
go mod tidy

# 下載缺失的依賴到本地快取
go mod download

# 驗證依賴完整性
go mod verify
