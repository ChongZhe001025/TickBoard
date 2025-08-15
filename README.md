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
  ```
6. Validate:
  - `https://tasktrek.czhuang.dev/` loads UI
  - `https://tasktrek.czhuang.dev/api/health` returns `{ ok: true }`
  - `https://tasktrek.czhuang.dev/db/` opens mongo-express
     ```

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for any improvements or bug fixes.

## License

This project is licensed under the MIT License. See the LICENSE file for more details.