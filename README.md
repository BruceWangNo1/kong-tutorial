## 注意
请提前安装好Kong所需要的PostgreSQL数据库。具体操作请参考 https://www.cnblogs.com/zhoujie/p/kong5.html

如果无法在centos上使用yum安装PostgreSQL数据库，那么请离线下载好安装包，手动上传然后安装。

请安装httpie。

如有在配置Kong时有任何疑问或者困难，欢迎联系IEG研发效能部王勇同学。企业微信：bruceywang。电话：88892。

## 正文

参考文档：https://docs.konghq.com/1.0.x/admin-api/

### 查看信息

```bash
http :8001/
http :8001/services
http :8001/routes
http :8001/plugins
http :8001/consumers
```

### 添加一个service

```bash
curl -i -X POST \
  --url http://localhost:8001/services/ \
  --data 'name=serverinfo' \
  --data 'url=http://serverinfo.com' \
  --data 'tags=serverinfo'
```

### 添加一个routes

```bash
 curl -i -X POST \
  --url http://localhost:8001/services/serverinfo/routes \
  --data 'hosts[]=serverinfo.com'
  --data 'name=serverinfo-route'
```

### 添加一个consumer

```bash
curl -d "username=frankunderwood&custom_id=123" http://kong:8001/consumers/
```

### 添加一个HTTP-LOG插件

参考文档：https://docs.konghq.com/hub/kong-inc/http-log/

```bash
curl -X POST http://kong:8001/services/serverinfo/plugins     --data "name=http-log"      --data "config.http_endpoint=http://localhost:60666/hello/log"     --data "config.method=POST"     --data "config.timeout=1000"     --data "config.keepalive=1000"
```

### 添加一个JWT鉴权插件

参考文档：https://docs.konghq.com/hub/kong-inc/jwt/

```bash
curl -X POST http://kong:8001/services/hello/plugins --data "name=jwt"
```

在一个route上使JWT鉴权插件生效

```bash
curl -X POST http://kong:8001/routes/routeName/plugins \
    --data "name=jwt" 
```

生成JWT Credentials。必须先得有一个consumer，一个consumer可以有很多JWT credentials。

```bash
curl -X POST http://kong:8001/consumers/{consumer}/jwt -H "Content-Type: application/x-www-form-urlencoded"
HTTP/1.1 201 Created
```

查看Credentials

```bash
curl -X GET http://kong:8001/consumers/consumerName/jwt
```

利用Secret生成JWT：
https://docs.konghq.com/hub/kong-inc/jwt/#craft-a-jwt-with-a-secret-hs256

发送带JWT的请求：
https://docs.konghq.com/hub/kong-inc/jwt/#send-a-request-with-the-jwt
