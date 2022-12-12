1. 總數變數的表達：

> 若是不可數名詞的命名，使用 amount
例如：
```
fiat_amount
crypto_amount
```

> 多個 amount 的加總則在後面加上 total
例如：
```
fiat_amount_total
crypto_amount_total
``` 

> 若是可數名詞的命名，使用 count
例如：
```
bedrooms_count
cats_count
```

> 多個 count 的加總則在後面加上 total
例如：
```
bedrooms_count_total
cats_count_total
``` 

2. 複數陣列的命名：

> 不採用 _list 或是 _dict，使用 _items 或是 _item

3. 檔案上傳路徑：

> 以 model name 為初始
例如：
```
def get_upload_file_path(instance, filename):
    now = timezone.now()
    return f'{convertCamelCaseToKebabCase(instance.__class__.__name__)}/{now.year}/{now.month}/{now.day}/{filename}'
```

> 如果該路徑上傳的檔案總數不多（少於一百張），則可考慮省略年月日

4. customize api router 命名：

如果在 ViewSet 中有客製化 api，如：
在 UserViewSet 有 update_line_uid
則 router view 命名為 {function_name}_view：
```
path('users/<str:pk>/update-line-uid/', update_line_uid_view),
```

如果在 APIView 中有客製化 api，則直接將 APIView import 到 router 使用：
```
path('upload-signed-urls/', UploadSignedUrlAPIView.as_view()),
```