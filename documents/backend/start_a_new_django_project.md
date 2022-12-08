# Start a new django project

1. 建立新 github repository

> Repository name 採用全小寫， dash（-）分隔，最後再加上使用的語言。Ex：pet-passport-django

> Repository 設為 private

> 別忘記要先討論過專案名稱是否合適

2. 在新 repository 的 settings 中，選擇 Collaborators 並邀請相關成員

3. 執行 cookiecutter-django 建立新專案
```
cookiecutter https://github.com/cookiecutter/cookiecutter-django

project_name：首字母大寫、空格間隔的專案名稱，Ex：Pet Passport
project_slug：snake_case 格式的名稱，Ex：pet_passport
description：專案的描述，Ex：Pet Passport Django
author_name：playma-ltd
domain_name：根據網站 domain 設定
email：預設即可，以後根據專案需求決定
version：預設即可
Select open_source_license：5
timezone [UTC]: Asia/Taipei
windows [n]：n
use_pycharm [n]：n
use_docker [n]：y
Select postgresql_version：1
Select cloud_provider：視專案是否使用雲端服務決定
Select mail_service：視專案是否有選擇指定 mail service 決定
use_async：y
use_drf：y
Select frontend_pipeline：1
use_celery：視專案是否有需要非同步處理需求決定
use_mailhog：n
use_sentry：y
use_whitenoise：n
use_heroku：n
Select ci_tool：1
keep_local_envs_in_vcs：y
debug：n
```
> 在上傳前最好先討論確認過該設定是否有問題

> 建立完成後，建議將資料夾名稱改成 github 專案名稱
```
mv local-folder-name github-repository-name
```

4. 將以上設定放到 md 檔裡
```
cd your-project-path
vi cookiecutter_django_init_settings.md
```
貼上步驟 3. 的設定並儲存

> 記得在最前面跟最後面新增一行 ``` 包住設定

5. commit and push 專案到 github repository
```
git init
git add .
git commit -m "first commit"
git branch -M master
git remote add origin your-project-github-ssh-url(your-project-github-https-url)
git push -u origin master
```

6. 建立 virtual environment 並進入
```
python3 -m venv .venv
source .venv/bin/activate
```

7. 在 requirement/local.txt 中，加上 django-queryinspect 的 package
```
django-queryinspect==1.1.0  #  https://github.com/dobarkod/django-queryinspect
```

8. 在 config/settings/local.py 中，加上相關 settings
```
MIDDLEWARE += [
    ...,
    'qinspect.middleware.QueryInspectMiddleware',
]

LOGGING['loggers'] = {
    'qinspect': {
        'handlers': ['console'],
        'level': 'DEBUG',
        'propagate': True,
    },
}

# django-queryinspect
# ------------------------------------------------------------------------------
# https://github.com/dobarkod/django-queryinspect
QUERY_INSPECT_ENABLED = True

```

9. 安裝 local requirements
```
pip install -r requirements/local.txt
```
> 如果出現版本衝突，可以試著將 sphinx 的版本指定刪除後再執行一次

10. 在 .pre-commit-config.yaml 中的 pre-commit-hooks 的 hooks 加上雙引號修正單引號的設定
```
- id: double-quote-string-fixer
```

11. 在 .pre-commit-config.yaml 中的 black 的 id 加上參數的設定避免單引被改回雙引號
```
hooks:
  - id: black
    args: ["--skip-string-normalization"]
```

12. add 並 commit 完設定的變更後執行 pre-commit 整理 code
```
git add .pre-commit-config.yaml
git commit -m "feat: change pre-commit settings"
pre-commit install
pre-commit run --all-files
```

13. 將整理過的 code 上傳至 github
```
git add .
git commit -m "feat: pre-commit all files"
git push
```

14.  在專案 root 下進入 app folder 並新建 common app 與 api folder 及其相關檔案
```
cd project-module-name
python ../manage.py startapp common
mkdir ./common/api
touch ./common/api/serializers.py
touch ./common/api/views.py
```

15. 修改新建的 common 下，apps.py 檔案中 name 的值
```
name = 'project-module-name.common'
```

16. 新增 config/settings/base.py 的 LOCAL_APPS
```
LOCAL_APPS = [
    ...,
    'project-module-name.common',
]
```

17. 將 common 下新建的檔案中，有 import 但未使用的部分，句尾增加  # noqa: F401 避免 lint error

> 例如在 admin.py 中

```
from django.contrib import admin  # noqa: F401

# Register your models here.

```

> 開發中若是需要新增 app 便依照 步驟 12 ~ 步驟 15 操作，並在操作完後 commit push 

18. 在 common 下新增 models 資料夾，並刪除原本 common 的 models.py 檔案
19. 在 common/models/ 資料夾裡新增 \_\_init\_\_.py 檔案與 base_model.py 檔案
20. 在 base_model.py 裡加上 BaseModel 的 code，提供以後所有 model 繼承使用
```
import uuid

from django.db import models


class BaseModel(models.Model):
    id = models.UUIDField(
        primary_key=True,
        default=uuid.uuid4,
        editable=False,
    )

    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True

```

21. 在 \_\_init\_\_.py 裡引用剛建立好的 base_model
```
from .base_model import BaseModel

__all__ = [
    'BaseModel',
]

```

22. 完成後結構大致如下
![截圖 2022-10-21 上午10 08 02](https://user-images.githubusercontent.com/45349930/197094634-016c5090-2a48-4064-9c47-7d732c42d73b.png)

23. 在 /your-project/users/models.py 裡引用剛建立好的 BaseModel，並讓 user model 繼承
```
from your_project.common.models import BaseModel


class User(AbstractUser, BaseModel):
```

24. 新增 config/settings/base.py 中，django-cors-header 設定
以 https://crm-dev.udb.com.tw/ 為例
```
CORS_ALLOWED_ORIGIN_REGEXES = [
    r'^https:\/\/\S+\.udb\.com\.tw$',
    r'^http:\/\/localhost(:[0-9]+)?$',
    r'^http:\/\/127\.0\.0\.1(:[0-9]+)?$',
]
REST_FRAMEWORK= {
    ...
    'DATETIME_FORMAT': '%s000',
}
```

25.(Optional) 如果有使用 Sentry，則修改 config/settings/production.py 中，sentry 設定 ignore_logger
```
from sentry_sdk.integrations.logging import ignore_logger
....
sentry_sdk.init(
    ....
)
ignore_logger('django.security.DisallowedHost')
```

26. 調整 production.yml 設定，移除 volumes 的 production_traefik 與 services 內的 traefik，並為 django 加上 port
```
django:
    ports:
        - "80:5000"
    env_file:
    ...
celeryworker:
    ports: []
    ...
celerybeat:
    ports: []
    ...
flower:
    ports:
        - "5555:5555"
```

27.(Optional) 如果有選 amazon ses 的設定，則在 config/settings/production.py 中增加以下 code
```
ANYMAIL = {
    'AMAZON_SES_CLIENT_PARAMS': {
        'aws_access_key_id': env('AWS_ACCESS_KEY_FOR_ANYMAIL_SES'),
        'aws_secret_access_key': env('AWS_SECRET_KEY_FOR_ANYMAIL_SES'),
        'region_name': env('AWS_REGION_NAME_FOR_ANYMAIL_SES'),
    },
}
```

28.(Optional)如果不是 ssr 的設計方式，則將 專案下的 templates/base.html 清空

29. 將整理過的 code 上傳至 github
```
git add .
git commit -m "feat: create common app"
git push
```

30. 全部完成後可以提交專案 review
