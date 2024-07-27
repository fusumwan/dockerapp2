# cicd-pipeline
=================================

linux command:
chmod 400 cicd-pipeline.pem
Widnows command:
在 Windows 11 中，你可以使用 PowerShell 來更改文件的權限。雖然 Windows 沒有直接等同於 `chmod 400` 的命令，但你可以使用 PowerShell 來設置文件的訪問控制列表 (ACL) 來達到類似的效果。

`chmod 400` 的含義是在 Unix 系統上設置文件權限，使文件只有擁有者可以讀取，而不能寫入或執行。在 Windows 中，這意味著你需要設置文件的訪問權限，僅允許擁有者讀取文件。

以下是如何使用 PowerShell 在 Windows 11 中實現類似 `chmod 400 cicd-pipeline.pem` 的效果：

1. 打開 PowerShell（以管理員身份運行）。

2. 執行以下命令以設置文件的權限：

```powershell
# 獲取當前文件的 ACL
$acl = Get-Acl "cicd-pipeline.pem"

# 創建一個新的訪問規則，允許當前用戶讀取文件
$permission = "YOUR_USERNAME", "Read", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule $permission

# 清除所有現有的訪問規則，然後添加新的訪問規則
$acl.SetAccessRuleProtection($true, $false)
$acl.ResetAccessRule($accessRule)

# 設置新的 ACL 到文件
Set-Acl "cicd-pipeline.pem" $acl
```

請將 `YOUR_USERNAME` 替換為你的實際用戶名。這將設置文件 `cicd-pipeline.pem` 的權限，使其只能由指定用戶讀取。

### 逐步說明：
1. **獲取文件的 ACL**：`$acl = Get-Acl "cicd-pipeline.pem"`
2. **創建新的訪問規則**：`$permission = "YOUR_USERNAME", "Read", "Allow"` 和 `$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule $permission`
3. **清除所有現有的訪問規則並添加新的規則**：`$acl.SetAccessRuleProtection($true, $false)` 和 `$acl.ResetAccessRule($accessRule)`
4. **設置新的 ACL 到文件**：`Set-Acl "cicd-pipeline.pem" $acl`

這些步驟將確保只有指定的用戶可以讀取該文件，而無法寫入或執行該文件。


是的，你可以在 Windows 11 中通過文件屬性界面來修改文件權限，而不使用命令行。以下是具體步驟：

1. **找到文件**：
   - 打開文件資源管理器，找到 `cicd-pipeline.pem` 文件。

2. **打開屬性窗口**：
   - 右鍵點擊 `cicd-pipeline.pem` 文件，選擇 "屬性"。

3. **進入安全選項卡**：
   - 在屬性窗口中，切換到 "安全" 選項卡。

4. **編輯權限**：
   - 點擊 "編輯" 按鈕，這將打開一個新的窗口，允許你編輯文件的權限。

5. **設置權限**：
   - 在 "組或用戶名" 列表中，選擇你的用戶名。如果你的用戶名不在列表中，點擊 "添加" 按鈕，並輸入你的用戶名。
   - 選中你的用戶名後，在下面的 "權限" 部分，確保只有 "讀取" 和 "讀取和執行" 被勾選。取消勾選所有其他權限，例如 "修改" 和 "寫入"。
   - 點擊 "確定" 保存更改。

6. **確認更改**：
   - 確保所有更改已應用，並且文件權限已更新。

### 圖示指南
1. **文件屬性窗口**：
   ![文件屬性窗口](https://docs.microsoft.com/en-us/windows/security/images/properties.png)

2. **安全選項卡**：
   ![安全選項卡](https://docs.microsoft.com/en-us/windows/security/images/security_tab.png)

3. **編輯權限**：
   ![編輯權限](https://docs.microsoft.com/en-us/windows/security/images/edit_permissions.png)

通過這些步驟，你可以在 Windows 11 中圖形化界面上修改文件權限，而不需要使用命令行。這樣可以達到類似 `chmod 400` 的效果，即文件只有你可以讀取，而不能被修改或執行。

==============================

ssh -i "cicd-pipeline.pem" ubuntu@ec2-3-27-148-241.ap-southeast-2.compute.amazonaws.com
===============================
build image
docker build -t cicd-pipeline:latest .

=============================
Install Docker Engine on Ubuntu

https://docs.docker.com/engine/install/ubuntu/


=============================


當你在執行 `docker build -t cicd-pipeline:latest .` 命令時如果中途殺死了進程，Docker 可能會留下未完成的映像和中間層。你可以使用以下步驟來清理這些未完成的映像和中間層：

### 刪除未完成的映像
1. **列出所有 Docker 映像**：
   - 打開 PowerShell 或命令提示符，然後運行以下命令：
     ```sh
     docker images -a
     ```
   - 這將列出所有映像，包括未標記的中間層映像。

2. **查找並刪除未完成的映像**：
   - 根據輸出結果，找到你要刪除的未完成映像的 ID 或名稱，然後運行以下命令刪除它：
     ```sh
     docker rmi <image_id_or_name>
     ```
   - 例如，如果未完成的映像 ID 是 `abc123def456`，則運行：
     ```sh
     docker rmi abc123def456
     ```

### 清理所有未使用的 Docker 資源
Docker 提供了清理未使用的資源（包括未標記的中間層映像）的簡單命令。

1. **使用 `docker system prune` 清理未使用的資源**：
   - 運行以下命令來刪除所有未使用的映像、容器、網絡和卷：
     ```sh
     docker system prune -a
     ```
   - 注意：這將刪除所有未被標記為最新版本的映像和所有停止的容器。

2. **選擇性清理中間層**：
   - 如果你只想刪除未標記的中間層映像，可以運行以下命令：
     ```sh
     docker image prune -f
     ```

### 清理具體未完成的映像步驟
假設你中途殺死了構建過程，以下是具體的步驟來清理：

1. **列出所有映像**：
   ```sh
   docker images -a
   ```

2. **找到未完成的映像 ID**：在列出的映像中找到標記為 `cicd-pipeline:latest` 或者沒有標記（<none>）的映像。

3. **刪除未完成的映像**：
   ```sh
   docker rmi <image_id>
   ```

### 示例
假設未完成的映像 ID 是 `abc123def456`，你可以運行以下命令來刪除它：
```sh
docker rmi abc123def456
```

如果你不確定哪個映像是未完成的，可以運行以下命令來刪除所有未標記的映像和中間層：
```sh
docker image prune -a -f
```

這將確保清理所有未使用的資源和未完成的構建映像。
============================

當你嘗試刪除一個 Docker 映像但遇到錯誤，提示該映像正被某個容器使用時，你需要首先刪除使用該映像的容器。以下是詳細步驟：

### 步驟 1: 找到使用該映像的容器
1. **列出所有容器**：
   打開 PowerShell 或命令提示符，運行以下命令來列出所有容器（包括運行中的和停止的）：
   ```sh
   docker ps -a
   ```

2. **查找使用該映像的容器**：
   找到列出結果中使用 `timothyfudocker/privatedocker:latest` 映像的容器，記下它們的容器 ID。

### 步驟 2: 停止並刪除使用該映像的容器
1. **停止容器**（如果容器正在運行）：
   使用以下命令停止容器，將 `<container_id>` 替換為實際的容器 ID：
   ```sh
   docker stop <container_id>
   ```

2. **刪除容器**：
   使用以下命令刪除容器，將 `<container_id>` 替換為實際的容器 ID：
   ```sh
   docker rm <container_id>
   ```

### 步驟 3: 刪除 Docker 映像
在停止並刪除使用該映像的所有容器後，你應該能夠成功刪除該映像。運行以下命令來刪除映像：
```sh
docker rmi timothyfudocker/privatedocker:latest
```

### 完整步驟示例
假設你找到一個使用該映像的容器 ID 是 `abc123def456`：

1. **停止容器**：
   ```sh
   docker stop abc123def456
   ```

2. **刪除容器**：
   ```sh
   docker rm abc123def456
   ```

3. **刪除映像**：
   ```sh
   docker rmi timothyfudocker/privatedocker:latest
   ```

### 一次性停止並刪除所有使用該映像的容器
如果有多個容器在使用該映像，你可以使用以下腳本來一次性停止並刪除所有這些容器：

1. **找到並停止所有使用該映像的容器**：
   ```sh
   docker ps -a --filter "ancestor=timothyfudocker/privatedocker:latest" --format "{{.ID}}" | xargs -I {} docker stop {}
   ```

2. **刪除這些容器**：
   ```sh
   docker ps -a --filter "ancestor=timothyfudocker/privatedocker:latest" --format "{{.ID}}" | xargs -I {} docker rm {}
   ```

3. **刪除映像**：
   ```sh
   docker rmi timothyfudocker/privatedocker:latest
   ```

這些步驟將幫助你停止並刪除所有使用該映像的容器，從而能夠成功刪除該映像。

============================

Download
We recommend configuring the runner under "\actions-runner". This will help avoid issues related to service identity folder permissions and long path restrictions on Windows.

# Create a folder under the drive root
$ mkdir actions-runner; cd actions-runner# Download the latest runner package
$ Invoke-WebRequest -Uri https://github.com/actions/runner/releases/download/v2.317.0/actions-runner-win-x64-2.317.0.zip -OutFile actions-runner-win-x64-2.317.0.zip# Optional: Validate the hash
$ if((Get-FileHash -Path actions-runner-win-x64-2.317.0.zip -Algorithm SHA256).Hash.ToUpper() -ne 'a74dcd1612476eaf4b11c15b3db5a43a4f459c1d3c1807f8148aeb9530d69826'.ToUpper()){ throw 'Computed checksum did not match' }# Extract the installer
$ Add-Type -AssemblyName System.IO.Compression.FileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("$PWD/actions-runner-win-x64-2.317.0.zip", "$PWD")
Configure
# Create the runner and start the configuration experience
$ ./config.cmd --url https://github.com/fusumwan/cicd-pipeline --token AY37PMQLXHWNDKDL3IHGIS3GUOMVY# Run it!
$ ./run.cmd

=============================
…or create a new repository on the command line
echo "# cicd-pipeline" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:fusumwan/dockerapp2.git
git push -u origin main
…or push an existing repository from the command line
git remote add origin git@github.com:fusumwan/dockerapp2.git
git branch -M main
git push -u origin main
===================
git pull
git add .
git commit -m "Updating"
git branch -M main
git push -u origin main