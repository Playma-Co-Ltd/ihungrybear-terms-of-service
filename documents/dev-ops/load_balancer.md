# Load Balancer

1. 選擇 Application Load Balancer 並建立
2. 修改 load balancer 名稱

> 命名規則：{project-name}-{type}-load-balancer
udb-crm 的 load balancer 名稱：udb-crm-dev-load-balancer

3. 建立 load balancer 的安全群組

> 入站規則增加類型：HTTPS
{project-name}-{service}-security-group
udb-crm 的 load balancer 安全群組：udb-crm-load-balancer-security-group

4. 建立目標群組

> 目標群組設定
Target type：Instances
命名規則：{project-name}-{type}-target-group
udb-crm 的 target group 名稱：udb-crm-dev-target-group
Success codes：200,302

5. 註冊 EC2 到新建立的 target group，按下 Include as pending below

6.最後按下 Create Target Group 完成

7. 在 load balancer 裡將剛建立好的 target group 關聯到 HTTP 80 port

8. 按下 Create load balancer 建立

9. 到 listener 裡新增 listener，HTTPS  443 port， forward 到上面建立的 target group

10. 修改原本 HTTP:80 的 listener，移除原本的 forward，新增 redirect 到 HTTPS 443 port
