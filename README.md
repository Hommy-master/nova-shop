# Nova Shop

一个开源的外贸电商网站，提供 GraphQL API 接口。

## 部署

### 前置要求

- Docker
- Docker Compose

### 一键启动

1. 克隆代码并进入项目目录

```bash
git clone https://github.com/gogoshine/nova-shop.git
cd nova-shop
```

2. 按需修改 `docker-compose.yaml` 中的配置项（至少修改以下内容）：

   - `SECRET_KEY`：替换为随机字符串
   - `ALLOWED_HOSTS` / `ALLOWED_CLIENT_HOSTS`：改为实际域名
   - `POSTGRES_PASSWORD`：改为安全密码
   - `DATABASE_URL` 中的密码与 `POSTGRES_PASSWORD` 保持一致

3. 启动所有服务

```bash
docker compose up -d
```

服务启动后，API 默认监听 `http://localhost:8000/graphql/`。

### 服务说明

| 服务 | 说明 |
|------|------|
| `nova-shop` | Web API，启动时自动执行数据库迁移 |
| `celery` | 异步任务 Worker（邮件、Webhook 等） |
| `celery-beat` | 定时任务调度器 |
| `postgres` | PostgreSQL 15 数据库 |
| `redis` | 缓存 & 消息队列 |

### 常用命令

```bash
# 查看运行状态
docker compose ps

# 查看日志
docker compose logs -f nova-shop

# 停止所有服务
docker compose down

# 更新镜像并重启
docker compose pull && docker compose up -d
```
