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
     ```

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for any improvements or bug fixes.

## License

This project is licensed under the MIT License. See the LICENSE file for more details.