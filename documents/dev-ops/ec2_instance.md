# EC2 Instance

以專案 udb-crm 為例

1. 切換區域到 亞太區域(東京) ap-northeast-1（或依據專案切換到最合適的地點）
2. 按下啟動新實例
3. 輸入名稱

> 命名規則：{project-name}-{type}-ec2
udb-crm 的測試區：`udb-crm-dev-ec2`
udb-crm 的正式區：`udb-crm-production-ec2`

4. 實例類型選擇：t3.micro（如有特殊需求專案才開較高規格）
5. 創建新的密鑰，類型選擇 RSA，格式選擇 pem

> 命名規則：{project-name}-{type}
udb-crm 的測試區密鑰名稱：`udb-crm-dev`
udb-crm 的正式區密鑰名稱：`udb-crm-production`

6. 將下載的 pem 檔案放置到 .ssh 資料夾內
7. 到 google 該專案的內部資料夾內新建 AWS EC2 PEM 資料夾，並上傳該 PEM
8. 創建新的網路群組

> 命名規則：{project-name}-{service}-security-group
udb-crm 的 EC2 安全群組：`udb-crm-ec2-security-group`
udb-crm 的 RDS 安全群組：`udb-crm-rds-security-group`

9. 創建 EC2 實例
10. 到網路與安全中的彈性 ip 配置彈性 ip 地址
11. 修改新建的彈性 ip 地址名稱

> 命名規則：{ec2-name}-elastic-ip
udb-crm 的測試區彈性 IP 名稱：`udb-crm-dev-ec2-elastic-ip`

12. 按下操作 -> 關聯彈性 ip，將該 ip 配置到 對應的 ec2 上，勾選允許重新關聯此彈性 IP 地址
13. 在 .ssh 資料夾中，新增或修改 config 檔案，將 ip 與 pem 關聯的設定存入

> 在 config 的檔案中，依照以下格式輸入並儲存
```
Host {your-ssh-target-name}
     HostName {your-ssh-target-ip}
     User {your-ssh-user}
     IdentityFile {your-ssh-pem-location}

Host udb-crm-dev
     HostName 54.250.8.183
     User e2-user
     IdentityFile ~/.ssh/udb-crm/udb-crm-dev.pem
```

14. 修改 pem 檔案的權限設定
```
chmod 400 ~/.ssh/udb-crm-dev.pem
```

15.  ssh 連線至 EC2 instance `ssh {your-ssh-target-name}`

16. 安裝 git 
```
sudo yum install git
```

17. 產生 ssh key
```
ssh-keygen
```

18. 開啟產生的 public key 並複製
```
vim .ssh/id_rsa.pub
```

19. 登入 playma-ltd 的 giithub 帳號，並進入 Settings
20. 進入 SSH and GPG keys 分頁，並新增剛剛的 public ssh key

> SSH key 命名規則：{project-name} ssh key
udb-crm 的 SSH key 名稱：`udb-crm ssh key`

21. 回到 ssh 連線並 git clone 專案
```
git clone git@github.com:playma-ltd/your-project.git
```

22. 在本機進入專案資料夾並上傳 production 的 .env 檔案（cookiecutter-django 創建專案者持有）

> scp -r .envs/.production {ssh-user}@{ssh-config-host}:/home/ec2-user/{project-name}/.envs

```
cd your-project-name
scp -r .envs/.production  ec2-user@udb-crm-dev:/home/ec2-user/udb-crm-django/.envs
```

23.(Option) 根據[建立 RDS 步驟](https://github.com/playma-ltd/documents/issues/10#issuecomment-1305391859)後取得參數修改.envs/.production/.postgres 的設定指向對應資料庫

```
POSTGRES_HOST=RDS_END_POINT
POSTGRES_PORT=5432
POSTGRES_DB=DATABASE_NAME
POSTGRES_USER=USER
POSTGRES_PASSWORD=PASSWORD
```

24. 建立 sentry 並複製 dsn 以修改 .envs/.production/.django 的 SENTRY_DSN 參數

25. 根據[建立 IAM 步驟](https://github.com/playma-ltd/documents/issues/10#issuecomment-1306539437)步驟建立 Administrator 並取得 ACCESS_KEY_ID 與 SECRET_ACCESS_KEY 更新 .envs/.production/.django

26. 根據[建立 S3 步驟](https://github.com/playma-ltd/documents/issues/10#issuecomment-1306530420)步驟建立 S3 並取得 STORAGE_BUCKET_NAME 更新 .envs/.production/.django 

27. 根據 EC2 的 私有 ip、公有 ip、domain 添加入 .envs/.production/.django 的 DJANGO_ALLOWED_HOSTS

28. 根據「在 Amazon Linux 2 上安裝 Docker」步驟安裝並啟動 docker

> https://docs.aws.amazon.com/zh_tw/AmazonECS/latest/developerguide/create-container-image.html

29. 根據步驟安裝 docker-compose

> 進入網站複製 curl 下載安裝 docker compose
https://docs.docker.com/compose/install/other/

> 修改權限
```
sudo chmod +x /usr/local/bin/docker-compose
```

30. 執行 docker-compose 指令建立並啟動專案
```
docker-compose -f production.yml build
docker-compose -f production.yml up
```
