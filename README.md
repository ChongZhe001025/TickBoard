# TaskTrek

TaskTrek is a project that consists of a backend API built with Go and a frontend application built with React. This project aims to provide a task management system where users can manage their tasks efficiently.

## Project Structure

The project is organized into two main directories:

- **gin-api**: This directory contains the backend API built using the Gin framework in Go.
  - **.env**: Environment variables configuration file for the backend.
  - **Dockerfile**: Dockerfile for building the backend application.
  - **go.mod**: Go module file defining the project's dependencies.
  - **go.sum**: Go module checksum file for dependency consistency.
  - **main.go**: Entry point for the application, starting the Gin server and setting up routes.
  - **controllers/**: Contains controllers for handling various requests.
  - **docs/**: Contains API documentation files.
  - **internal/**: Contains internal logic for database interactions and security.
  - **models/**: Contains data models and request models.

- **frontend**: This directory contains the frontend application built with React.
  - **.env**: Environment variables configuration file for the frontend.
  - **Dockerfile**: Dockerfile for building the frontend application.
  - **package.json**: npm configuration file listing project dependencies and scripts.
  - **README.md**: Documentation for the frontend application.
  - **tsconfig.json**: TypeScript configuration file.
  - **public/**: Contains static files for the frontend application.
  - **src/**: Contains the source code for the frontend application, including components, pages, and styles.

## Getting Started

To get started with the project, follow these steps:

1. Clone the repository:
   ```
   git clone <repository-url>
   cd tasktrek
   ```

2. Set up the backend:
   - Navigate to the `gin-api` directory.
   - Create a `.env` file with the necessary environment variables.
   - Build and run the Docker container:
     ```
     docker build -t gin-api .
  docker run -p 8080:8080 gin-api
     ```

3. Set up the frontend:
   - Navigate to the `frontend` directory.
   - Create a `.env` file with the necessary environment variables.
   - Install dependencies:
     ```
     npm install
     ```
   - Start the development server:
     ```
    npm start

## Deploy to EC2 with HTTPS (Expose only Frontend & Mongo-Express)

Production architecture: Nginx terminates TLS on EC2 and reverse-proxies to local containers. Only the following are exposed via Nginx:

- `/` → frontend (127.0.0.1:3000)
- `/api/` → gin-api (127.0.0.1:8082)
- `/db/` → mongo-express (127.0.0.1:8081)

Docker services bind to 127.0.0.1 using `docker-compose.prod.yml` so they are not directly exposed to the internet.

### Steps

1. DNS: point `tasktrek.czhuang.dev` to the EC2 public IP. Ensure Security Group allows 80/443.
2. Start containers on EC2:
  ```
  sudo docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d --build
  ```
3. Nginx:
  - Copy `nginx/tickboard.conf` to `/etc/nginx/conf.d/tickboard.conf`
  - `sudo nginx -t && sudo systemctl reload nginx`
4. TLS certificate (Let’s Encrypt):
  ```
  sudo certbot --nginx -d tasktrek.czhuang.dev --agree-tos -m you@example.com
  ```
5. Frontend env (build-time): ensure `frontend/.env.production` includes
  ```
  REACT_APP_GIN_API_BASE=https://tasktrek.czhuang.dev
  # TickBoard

  全端任務管理應用：
  - 後端：Go Gin（JWT 登入、任務 CRUD、使用者 CRUD、Dashboard 聚合、Swagger）
  - 前端：React + CRA（以 Axios 串接 API，支援 Cookie 驗證）
  - 資料庫：MongoDB + mongo-express（管理介面）
  - 佈署：Docker Compose + Nginx 反向代理（HTTPS via Let’s Encrypt）

  ## 功能總覽
  - 帳號註冊/登入/登出（JWT，HttpOnly Cookie）
  - 使用者查詢/更新/刪除（/api/users/:id）
  - 任務新增/查詢/更新/刪除（/api/tasks）
  - Dashboard 聚合（/api/dashboard）與使用者資訊（/api/me）
  - 健康檢查（/api/health）
  - Swagger 文件（/api/swagger/index.html）

  ## 專案結構

  ```
  docker-compose.yml
  nginx/
    tickboard.conf        # Nginx 反代 + TLS 設定
  frontend/
    Dockerfile
    .env                  # 前端環境變數（開發）
    .env.production       # 前端環境變數（正式，若使用）
    src/
      lib/api.ts          # Axios 客戶端（withCredentials）
  gin-api/
    Dockerfile
    .env                  # 後端環境變數（PORT/MONGO_URI/JWT_SECRET）
    main.go               # 路由註冊，含 / 與 /api 前綴兩組路徑
    controllers/          # auth、users、task 控制器
    internal/             # db 連線、JWT、CORS 等
    docs/                 # Swagger 文件
  ```

  ## 環境變數

  - `gin-api/.env`
    ```
    PORT=8082
    MONGO_URI=mongodb://root:pass@mongo:27017
    DB_NAME=TickBoard
    JWT_SECRET=<請改為隨機長字串>
    ```
    - 正式環境建議同時提供 `NODE_ENV=production`（影響 Cookie Secure 屬性）。

  - `frontend/.env`（本地與容器開發）
    ```
    REACT_APP_GIN_API_BASE=/
    ```
    - 使用相對路徑，讓 CRA dev server 代理 `/api` 到後端容器。

  - `frontend/.env.production`（正式可選）
    ```
    REACT_APP_GIN_API_BASE=https://<你的網域>
    ```

  安全注意：JWT_SECRET 只放在後端，切勿放在前端或提交到版本庫。

  ## 本機快速啟動（Docker Compose）

  1. 準備 `.env`：請確認 `gin-api/.env` 與 `frontend/.env` 已就緒。
  2. 啟動：
     ```bash
     docker-compose up -d --build
     ```
  3. 存取：
     - 前端：http://localhost:3000
     - 後端健康檢查（透過 dev server 代理）：http://localhost:3000/api/health
     - 或直接打後端：http://localhost:8082/api/health
     - mongo-express：http://localhost:8081

  說明：
  - Compose 讓各容器在同一網路，後端用 `mongo:27017` 連 Mongo，無需對外開 27017。
  - 前端以 CRA proxy 將 `/api` 代理到後端容器，`REACT_APP_GIN_API_BASE=/` 即可。

  ## 正式佈署（EC2 + Nginx + HTTPS）

  架構：Nginx 對外提供 80/443，反向代理到本機容器（僅綁 127.0.0.1）。對外只暴露：
  - `/` → 前端（127.0.0.1:3000）
  - `/api/` → Gin API（127.0.0.1:8082）
  - `/db/` → mongo-express（127.0.0.1:8081）

  1) DNS 與安全群組
  - A 記錄指到 EC2 公網 IP。
  - SG 開放 80/443（及 22/SSH）。

  2) 安裝相依（在 EC2）
  ```bash
  sudo apt update
  sudo apt install -y docker.io docker-compose-plugin nginx certbot python3-certbot-nginx
  sudo systemctl enable --now docker nginx
  ```

  3) 啟動容器（在專案根目錄）
  ```bash
  sudo docker compose up -d --build
  ```

  4) 佈署 Nginx 設定
  - 將 `nginx/tickboard.conf` 放到 `/etc/nginx/conf.d/tickboard.conf`
  - 注意 `/api/` 的 `proxy_pass` 不要尾斜線：
    ```nginx
    location /api/ {
        proxy_pass http://127.0.0.1:8082;  # ← 無尾斜線，保留 /api 前綴
    }
    ```
  - 測試並重新載入：
  ```bash
  sudo nginx -t && sudo systemctl reload nginx
  ```

  5) 申請憑證（Let’s Encrypt）
  ```bash
  sudo certbot --nginx -d <你的網域> --agree-tos -m <你的Email>
  ```

  6) 驗證
  - https://<你的網域>/ 應能開啟前端
  - https://<你的網域>/api/health 應回 `{"ok": true}`
  - https://<你的網域>/db/ 可開啟 mongo-express

  Cookie 與 CORS：
  - 後端已開啟 AllowCredentials；在正式環境請設 `NODE_ENV=production` 以啟用 Secure Cookie。

  ## API 一覽（節選）
  - Auth：
    - POST `/api/auth/register`
    - POST `/api/auth/login`
    - POST `/api/auth/logout`
    - GET  `/api/me`
  - Tasks：
    - GET `/api/tasks`
    - POST `/api/tasks`
    - GET `/api/tasks/:id`
    - PATCH `/api/tasks/:id`
    - DELETE `/api/tasks/:id`
  - Users：
    - GET `/api/users/:id`
    - PATCH `/api/users/:id`
    - DELETE `/api/users/:id`
  - 雜項：
    - GET `/api/health`
    - Swagger：`/api/swagger/index.html`

  ## 疑難排解
  - 前端打不到 API：
    - 檢查 Nginx `/api/` 的 `proxy_pass` 是否無尾斜線。
    - 前端 `.env` 是否為 `REACT_APP_GIN_API_BASE=/` 並已重啟容器。
  - 502/Bad Gateway：
    - 後端容器是否啟動；`curl http://127.0.0.1:8082/api/health` 是否 200。
  - Mongo 無法連線：
    - `MONGO_URI` 是否用 `mongo:27017`；無需對外開 27017。
  - 需要外部連 Mongo：
    - 建議用 SSH Tunnel；不建議對外開埠。

  ## 授權
  MIT