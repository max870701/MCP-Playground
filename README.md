# MCP GitHub 服務器配置

此項目包含 GitHub MCP 服務器的配置。

## 前置要求

### 1. 安裝 Docker

此 MCP 服務器需要 Docker 來運行。請確保已安裝並運行 Docker：

**macOS:**
- 安裝 [Docker Desktop](https://www.docker.com/products/docker-desktop/) 或 [OrbStack](https://orbstack.dev/)
- 啟動 Docker Desktop/OrbStack 應用程式
- 驗證安裝：在終端運行 `docker --version`

**Linux:**
```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install docker.io
sudo systemctl start docker
sudo systemctl enable docker

# 驗證安裝
docker --version
```

**Windows:**
- 安裝 [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/)
- 啟動 Docker Desktop
- 驗證安裝：在 PowerShell 運行 `docker --version`

### 2. 創建 GitHub Personal Access Token

1. 前往 [GitHub Settings > Developer settings > Personal access tokens](https://github.com/settings/tokens)
2. 點擊 "Generate new token" > "Generate new token (classic)"
3. 設置名稱和過期時間
4. 選擇所需權限（至少需要 `repo` 權限）
5. 生成並複製 token（只會顯示一次）

## 設置步驟

### 1. 設置環境變數

創建 `.env` 文件（如果還沒有）：

```bash
cp .env.example .env
```

編輯 `.env` 文件，填入你的 GitHub Personal Access Token：

```
GITHUB_PERSONAL_ACCESS_TOKEN=your_actual_token_here
```

### 2. 載入環境變數

**方法 1：自動載入（推薦）**

在 macOS/Linux 上，將以下內容添加到你的 shell 配置文件（`~/.zshrc` 或 `~/.bashrc`）：

```bash
# 載入 MCP 項目的環境變數
# 請將路徑替換為你的實際項目路徑
if [ -f /path/to/MCP/.env ]; then
    export $(cat /path/to/MCP/.env | grep -v '^#' | xargs)
fi
```

**方法 2：手動載入**

每次使用前手動載入：

```bash
cd /path/to/MCP
export $(cat .env | grep -v '^#' | xargs)
```

**方法 3：從終端啟動 Cursor（macOS）**

```bash
cd /path/to/MCP
export $(cat .env | grep -v '^#' | xargs)
open -a Cursor
```

這樣可以確保 Cursor 繼承環境變數。

### 3. 驗證 Docker 和配置

確保 Docker 正在運行：

```bash
docker ps
```

如果 Docker 未運行，請啟動 Docker Desktop 或 OrbStack。

### 4. 重啟 Cursor

確保環境變數已設置且 Docker 正在運行後，重啟 Cursor 以載入 MCP 配置。

### 5. 驗證 MCP 服務器連接

1. 打開 Cursor
2. 前往 **Settings** > **Tools & MCP**
3. 在 "Installed MCP Servers" 部分，應該看到 "github" 服務器
4. 檢查是否有錯誤訊息
5. 如果看到 "No MCP Tools"，這是正常的，工具會在需要時自動載入

## 使用方法

### 在 Cursor 中使用 GitHub MCP 功能

配置完成後，你可以在 Cursor 中使用以下 GitHub 相關功能：

- **查詢倉庫信息**：搜索、列出、獲取倉庫詳情
- **管理 Issues**：創建、更新、關閉、搜索 Issues
- **管理 Pull Requests**：創建、審查、合併 PR
- **查看提交歷史**：獲取提交信息、比較分支
- **管理標籤和發布**：創建、列出標籤和發布版本
- **搜索代碼**：在 GitHub 上搜索代碼、用戶、倉庫

MCP 工具會根據你的需求自動提供，無需手動配置。

## 故障排除

### 問題 1：MCP 服務器未連接

**症狀：** 在 Cursor 設置中看不到 MCP 服務器或顯示錯誤

**解決方法：**
1. 檢查 Docker 是否正在運行：`docker ps`
2. 檢查環境變數是否設置：`echo $GITHUB_PERSONAL_ACCESS_TOKEN`
3. 檢查 `.env` 文件是否存在且包含正確的 token
4. 重啟 Cursor
5. 檢查 Cursor 的錯誤日誌

### 問題 2：Docker daemon 未運行

**症狀：** `docker ps` 命令失敗

**解決方法：**
- macOS：啟動 Docker Desktop 或 OrbStack 應用程式
- Linux：運行 `sudo systemctl start docker`
- Windows：啟動 Docker Desktop

### 問題 3：環境變數未載入

**症狀：** MCP 服務器無法連接，提示 token 無效

**解決方法：**
1. 確認 `.env` 文件存在且格式正確
2. 手動載入環境變數：`export $(cat .env | grep -v '^#' | xargs)`
3. 從終端啟動 Cursor（確保環境變數被繼承）
4. 檢查 shell 配置文件中的自動載入設置

### 問題 4：GitHub Token 無效

**症狀：** MCP 服務器連接但無法執行操作

**解決方法：**
1. 驗證 token 是否有效：
   ```bash
   curl -H "Authorization: token YOUR_TOKEN" https://api.github.com/user
   ```
2. 檢查 token 是否過期
3. 確認 token 有足夠的權限（至少需要 `repo` 權限）
4. 重新生成 token 並更新 `.env` 文件

### 問題 5：Docker 映像拉取失敗

**症狀：** 無法拉取 `ghcr.io/github/github-mcp-server` 映像

**解決方法：**
1. 檢查網路連接
2. 手動拉取映像：`docker pull ghcr.io/github/github-mcp-server`
3. 檢查 Docker Hub 連接：`docker pull hello-world`

## 更新 MCP 服務器

當 GitHub 發布新版本的 MCP 服務器時，更新 Docker 映像：

```bash
docker pull ghcr.io/github/github-mcp-server:latest
```

Cursor 會在下次使用時自動使用最新版本。

## 項目結構

```
MCP/
├── .cursor/
│   └── mcp.json          # MCP 服務器配置文件
├── .env                  # 環境變數文件（包含敏感 token，不提交）
├── .env.example          # 環境變數範例文件
├── .gitignore           # Git 忽略文件
└── README.md            # 本文件
```

## 文件說明

- **`.cursor/mcp.json`** - MCP 服務器配置文件
  - 定義 Docker 命令和參數
  - 使用環境變數引用，不包含實際 token
  - 已提交到版本控制

- **`.env`** - 環境變數文件
  - 包含實際的 GitHub Personal Access Token
  - 已添加到 `.gitignore`，不會被提交
  - 需要手動創建（從 `.env.example` 複製）

- **`.env.example`** - 環境變數範例文件
  - 作為模板，不包含實際 token
  - 可以提交到版本控制

- **`.gitignore`** - Git 忽略文件
  - 確保敏感文件不會被提交

## 安全注意事項

- ✅ `.env` 文件已添加到 `.gitignore`，不會被提交
- ✅ `mcp.json` 使用環境變數引用，不包含實際 token
- ⚠️ **不要將 `.env` 文件提交到版本控制系統**
- ⚠️ **不要將包含實際 token 的配置文件提交到版本控制系統**
- ⚠️ 定期輪換 GitHub Personal Access Token
- ⚠️ 使用 Fine-grained token 時，只授予必要的權限
- ⚠️ 不要在公共場所或截圖中暴露 token

## 相關資源

- [GitHub MCP Server 官方文檔](https://github.com/github/mcp-server-github)
- [Model Context Protocol 規範](https://modelcontextprotocol.io/)
- [Cursor 官方文檔](https://cursor.sh/docs)
- [Docker 官方文檔](https://docs.docker.com/)

## 許可證

此配置項目僅包含配置文件，遵循 GitHub MCP Server 的許可證條款。