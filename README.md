# MERN Todo App — Docker Desktop

Ứng dụng Todo MERN chạy hoàn toàn trên **Docker Desktop** (không cần VPS hay domain).

## Yêu cầu

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) đã cài và đang chạy
- Windows / macOS / Linux

## Chạy nhanh

```bash
# 1. Tạo file môi trường
cp .env.example .env

# 2. Build và khởi động toàn bộ stack
docker compose up -d --build

# 3. Xem log (tuỳ chọn)
docker compose logs -f
```

## Truy cập dịch vụ

| Dịch vụ | URL |
|---------|-----|
| Frontend (Todo App) | http://localhost:8080 |
| Backend API | http://localhost:8000/api |
| Health check | http://localhost:8000/api/health |
| Grafana | http://localhost:3001 (admin / mật khẩu trong `.env`) |
| Prometheus | http://localhost:9090 |
| MongoDB | localhost:27017 |

## Cấu trúc Docker

```
docker compose
├── mongodb          MongoDB 6
├── backend          Express API (port 8000)
├── frontend         React + Nginx (port 8080)
├── prometheus       Metrics collector
├── grafana          Dashboard monitoring
├── node-exporter    CPU / RAM / Disk
└── mongodb-exporter Trạng thái database
```

Frontend gọi API qua Nginx proxy `/api` → `backend:8000`, không cần cấu hình CORS khi truy cập qua http://localhost:8080.

## Biến môi trường

Chỉnh file `.env` (copy từ `.env.example`):

```env
MONGO_URI=mongodb://mongodb:27017/todo
PORT=8000
JWT_SECRET=change-me
GMAIL_USERNAME=          # tuỳ chọn — forgot password / email task
GMAIL_PASSWORD=          # tuỳ chọn
FRONTEND_URL=http://localhost:8080
REACT_APP_API_URL=/api
GRAFANA_ADMIN_PASSWORD=admin
```

## Lệnh thường dùng

```bash
# Dừng stack
docker compose down

# Dừng và xoá dữ liệu MongoDB
docker compose down -v

# Rebuild sau khi sửa code
docker compose up -d --build

# Chỉ rebuild một service
docker compose up -d --build backend
```

## Dev local không dùng Docker (tuỳ chọn)

```bash
# Terminal 1 — MongoDB (hoặc dùng container: docker run -d -p 27017:27017 mongo:6)
mongod

# Terminal 2 — Backend
cd backend
cp .env.example .env
npm install
npm start

# Terminal 3 — Frontend
cd frontend
cp .env.example .env
npm install
npm start
```

Frontend dev: http://localhost:3000 — API: http://localhost:8000/api

## Monitoring

Sau khi stack chạy, mở Grafana tại http://localhost:3001:

- Đăng nhập: `admin` / mật khẩu từ `GRAFANA_ADMIN_PASSWORD`
- Dashboards: **Node Exporter - Hardware**, **MongoDB Overview**

Prometheus scrape metrics từ `node-exporter` (phần cứng) và `mongodb-exporter` (database).

## Ghi chú

- Port **8080** dùng cho frontend để tránh xung đột port 80 trên Windows.
- Đổi `JWT_SECRET` trước khi dùng thật.
- Tính năng email (forgot password) cần `GMAIL_USERNAME` và `GMAIL_PASSWORD`.
