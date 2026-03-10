# Nova Shop

一个开源的外贸电商网站，提供 GraphQL API 接口。

## 仓库结构

| 目录 | 说明 |
|------|------|
| `/`（根目录） | 后端 API 服务（基于 Saleor） |
| `dashboard/` | 网站管理后台 WebUI |
| `storefront/` | 商店页面 WebUI |

## 部署

### 前置要求

- Docker
- Docker Compose
- 外部 PostgreSQL 服务器（建议版本 ≥ 13）
- 外部 Redis 服务器（建议版本 ≥ 6）

---

### 第一步：初始化 PostgreSQL

在 PostgreSQL 服务器上创建专用用户和数据库：

```sql
-- 使用管理员账户登录 psql 后执行
CREATE USER nova_shop WITH PASSWORD 'your_password';
CREATE DATABASE nova_shop OWNER nova_shop;
GRANT ALL PRIVILEGES ON DATABASE nova_shop TO nova_shop;

-- PostgreSQL 15+ 需额外授权 schema
\c nova_shop
GRANT ALL ON SCHEMA public TO nova_shop;
```

验证连接：

```bash
psql -h <服务器IP> -U nova_shop -d nova_shop -c "SELECT version();"
```

---

### 第二步：初始化 Redis

Redis 无需建库，只需确保服务可访问。如果 Redis 开启了认证，在 `docker-compose.yaml` 中的连接地址加上密码参数：

```
# 无密码
redis://192.168.1.100:6379/0

# 有密码
redis://:your_redis_password@192.168.1.100:6379/0
```

验证连接：

```bash
redis-cli -h <服务器IP> ping
# 应返回：PONG
```

---

### 第三步：配置并启动

1. 克隆代码并进入项目目录

```bash
git clone https://github.com/gogoshine/nova-shop.git
cd nova-shop
```

2. 修改 `docker-compose.yaml` 中的以下配置项：

| 配置项 | 说明 |
|------|------|
| `SECRET_KEY` | 替换为随机字符串 |
| `DATABASE_URL` | 填入实际 PostgreSQL 地址、用户名、密码、库名 |
| `CACHE_URL` | 填入实际 Redis 地址 |
| `CELERY_BROKER_URL` | 填入实际 Redis 地址（可与 CACHE_URL 使用不同 db） |
| `ALLOWED_HOSTS` / `ALLOWED_CLIENT_HOSTS` | 改为实际域名 |

3. 启动服务（首次启动会自动执行数据库迁移）

```bash
docker compose up -d
```

服务启动后，API 默认监听 `http://localhost:8000/graphql/`。

---

### 服务说明

| 服务 | 说明 |
|------|------|
| `nova-shop` | Web API，启动时自动执行数据库迁移 |
| `celery` | 异步任务 Worker（邮件、Webhook 等） |
| `celery-beat` | 定时任务调度器 |

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
