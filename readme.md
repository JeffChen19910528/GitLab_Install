# Linux 安裝 GitLab（Omnibus 版本）完整教學

本文件將說明如何在 **Linux（以 Ubuntu / Debian 為例）** 上安裝 **GitLab Omnibus** 版本，適合用於公司內部 Git Server 或個人專案管理。

---

## 一、環境需求

在開始之前，請確認你的系統符合以下條件：

* 作業系統：Ubuntu 20.04 / 22.04（Debian 類似）
* CPU：至少 2 Core（建議 4 Core）
* RAM：**最低 4GB（建議 8GB 以上）**
* Disk：至少 10GB（依專案數量調整）
* Root 或 sudo 權限
* 一個可用的網域或 IP（例如：`gitlab.example.com`）

> ⚠️ GitLab 非常吃記憶體，4GB 以下容易不穩定。

---

## 二、系統前置設定

### 1️⃣ 更新系統套件

```bash
sudo apt update && sudo apt upgrade -y
```

### 2️⃣ 安裝必要套件

```bash
sudo apt install -y curl ca-certificates tzdata openssh-server
```

### 3️⃣ 啟動 SSH 服務（若尚未啟動）

```bash
sudo systemctl enable ssh
sudo systemctl start ssh
```

---

## 三、安裝 GitLab Omnibus

### 1️⃣ 新增 GitLab 官方套件來源

```bash
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash
```

> 若你想使用 **社群版（CE）**，可將 `gitlab-ee` 改成 `gitlab-ce`

---

### 2️⃣ 安裝 GitLab

請先設定 GitLab 對外網址（非常重要）：

```bash
sudo EXTERNAL_URL="http://gitlab.example.com" apt install -y gitlab-ee
```

* 若使用 IP：

```bash
sudo EXTERNAL_URL="http://192.168.1.10" apt install -y gitlab-ee
```

安裝過程約需 **5～10 分鐘**。

---

## 四、GitLab 初始化與啟動

安裝完成後，GitLab 會自動執行設定。

### 重新套用設定（必要時）

```bash
sudo gitlab-ctl reconfigure
```

### 查看 GitLab 狀態

```bash
sudo gitlab-ctl status
```

---

## 五、首次登入 GitLab

1. 使用瀏覽器開啟：

```
http://gitlab.example.com
```

2. 預設管理者帳號：

* Username：`root`

3. 取得初始密碼：

```bash
sudo cat /etc/gitlab/initial_root_password
```

> ⚠️ 此密碼只保留 **24 小時**，請務必登入後立刻修改。

---

## 六、常用設定調整

### 1️⃣ 修改 GitLab 設定檔

```bash
sudo vi /etc/gitlab/gitlab.rb
```

常見設定：

```ruby
external_url 'http://gitlab.example.com'

gitlab_rails['time_zone'] = 'Asia/Taipei'
```

修改後套用設定：

```bash
sudo gitlab-ctl reconfigure
```

---

### 2️⃣ 設定防火牆（UFW）

```bash
sudo ufw allow http
sudo ufw allow https
sudo ufw allow ssh
sudo ufw reload
```

---

## 七、設定 HTTPS（Let’s Encrypt）

GitLab 內建 Let’s Encrypt，可直接啟用：

```ruby
external_url 'https://gitlab.example.com'
letsencrypt['enable'] = true
letsencrypt['contact_emails'] = ['admin@example.com']
```

套用設定：

```bash
sudo gitlab-ctl reconfigure
```

---

## 八、常用管理指令

| 功能        | 指令                        |
| --------- | ------------------------- |
| 啟動 GitLab | `sudo gitlab-ctl start`   |
| 停止 GitLab | `sudo gitlab-ctl stop`    |
| 重新啟動      | `sudo gitlab-ctl restart` |
| 查看狀態      | `sudo gitlab-ctl status`  |
| 查看 Log    | `sudo gitlab-ctl tail`    |

---

## 九、備份與還原（重要）

### 建立備份

```bash
sudo gitlab-backup create
```

### 備份檔位置

```
/var/opt/gitlab/backups/
```

---

## 十、常見問題（FAQ）

### Q1：記憶體不足怎麼辦？

* 關閉不必要服務（Prometheus、Grafana）
* 增加 Swap

### Q2：安裝卡住？

```bash
sudo gitlab-ctl reconfigure
```

### Q3：無法連線？

* 確認防火牆
* 確認 `external_url`

---

## 十一、適合誰使用？

* 公司內部原始碼管理
* 自架 CI/CD 平台
* 需控管權限的專案團隊

---

## 十二、參考文件

* GitLab 官方文件：[https://docs.gitlab.com](https://docs.gitlab.com)

---

✍️ **作者建議**：

* 正式環境建議 8GB RAM 以上
* 定期設定備份與監控

---
