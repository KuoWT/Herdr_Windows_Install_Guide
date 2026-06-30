# Herdr Windows 安裝指南（企業 Proxy 環境）

> 更新日期：2026-06-30
>
> 適用環境：
>
> - Windows 10 / Windows 11
> - 公司 Proxy（需驗證）
> - 一般使用者權限（非 Administrator）
> - PowerShell

---

# 一、環境資訊

|項目|內容|
|----|----|
|OS|Windows 10 / 11 x64|
|Shell|PowerShell|
|網路|企業 Proxy|
|權限|一般使用者|

---

# 二、官方安裝方式

官方提供：

```powershell
powershell -ExecutionPolicy Bypass -c "irm https://herdr.dev/install.ps1 | iex"
```

官方安裝流程：

```
下載 install.ps1
        │
        ▼
下載 Manifest
        │
        ▼
下載 Herdr Binary
        │
        ▼
更新 PATH
        │
        ▼
完成安裝
```

---

# 三、企業 Proxy 常見問題

直接執行官方指令可能出現：

```
Access Denied
authentication_failed

Credentials are missing.
```

原因：

- Proxy 需要登入
- Invoke-RestMethod 沒有帶 Proxy 認證

---

# 四、下載 install.ps1

設定 Proxy

```powershell
$proxy = "http://proxy.tn.test.com.tw:80"
```

取得 Proxy 認證

```powershell
$cred = Get-Credential
```

下載 installer

```powershell
Invoke-WebRequest `
    -Uri "https://herdr.dev/install.ps1" `
    -Proxy $proxy `
    -ProxyCredential $cred `
    -OutFile "$env:TEMP\install-herdr.ps1"
```

成功後：

```
C:\Users\<User>\AppData\Local\Temp\install-herdr.ps1
```

---

# 五、執行 Installer

直接執行：

```powershell
& "$env:TEMP\install-herdr.ps1"
```

成功畫面：

```
==> Fetching Herdr preview manifest

==> Installing Herdr 0.7.1-preview...

==> Downloading Herdr

==> PATH updated

Herdr installed successfully.
```

---

# 六、Execution Policy

若出現：

```
running scripts is disabled
```

執行：

```powershell
Set-ExecutionPolicy Bypass -Scope Process
```

再重新執行：

```powershell
& "$env:TEMP\install-herdr.ps1"
```

---

# 七、安裝完成

確認版本：

```powershell
herdr --version
```

啟動：

```powershell
herdr
```

若目前 PowerShell 找不到：

重新開 PowerShell。

---

# 八、安裝位置

程式：

```
%LOCALAPPDATA%\Programs\Herdr\
```

例如：

```
C:\Users\Kevin\AppData\Local\Programs\Herdr\
```

---

設定檔：

```
%APPDATA%\herdr\
```

例如：

```
C:\Users\Kevin\AppData\Roaming\herdr\
```

內容：

```
config.toml
```

---

# 九、PATH

Installer 會自動加入 PATH。

重新開 PowerShell 即可。

確認：

```powershell
echo $env:PATH
```

---

# 十、安裝流程圖

```
                官方網站
                     │
                     ▼
          install-herdr.ps1
                     │
                     ▼
         Download Manifest
                     │
                     ▼
          Download Binary
                     │
                     ▼
      %LOCALAPPDATA%\Programs
                     │
                     ▼
          更新 PATH
                     │
                     ▼
         完成安裝
```

---

# 十一、Proxy 安裝流程

```
               Proxy
                 │
                 ▼
      Get-Credential
                 │
                 ▼
 Invoke-WebRequest
                 │
                 ▼
 download install.ps1
                 │
                 ▼
     執行 install.ps1
                 │
                 ▼
      下載 Manifest
                 │
                 ▼
      下載 Binary
                 │
                 ▼
        安裝完成
```

---

# 十二、常見錯誤

## 1. authentication_failed

```
Access Denied

Credentials are missing.
```

原因：

Proxy 驗證失敗

解法：

使用：

```powershell
Invoke-WebRequest `
    -Proxy `
    -ProxyCredential
```

---

## 2. ExecutionPolicy

```
running scripts is disabled
```

解法：

```powershell
Set-ExecutionPolicy Bypass -Scope Process
```

---

## 3. 找不到 herdr

重新開 PowerShell

或確認 PATH：

```powershell
echo $env:PATH
```

---

# 十三、版本資訊

安裝成功：

```
Herdr 0.7.1-preview.2026-06-25-7811d84f15f1
```

Channel：

```
preview
```

---

# 十四、驗證

```powershell
herdr --version
```

```powershell
herdr
```

```powershell
where herdr
```

---

# 十五、常用指令

查看版本

```powershell
herdr --version
```

查看說明

```powershell
herdr --help
```

啟動

```powershell
herdr
```

查看 PATH

```powershell
echo $env:PATH
```

查看設定

```powershell
type "$env:APPDATA\herdr\config.toml"
```

---

# 十六、安裝摘要

|項目|結果|
|----|----|
|版本|0.7.1-preview|
|安裝方式|Official Installer|
|Proxy|需驗證|
|PATH|Installer 自動加入|
|設定檔|%APPDATA%\herdr|
|執行檔|%LOCALAPPDATA%\Programs\Herdr|

---

# 十七、建議

企業環境建議：

- 建立公司內部 Herdr Repository
- 建立離線安裝包
- 建立 Proxy 設定範本
- 建立 config.toml 標準模板
- 搭配 Claude Code、Hermes、RTK 使用
