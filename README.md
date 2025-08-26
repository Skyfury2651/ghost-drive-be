# 🔐 Ghost Drive

A secure end-to-end encrypted file storage system built with **Elysia** and **Bun runtime**. Ghost Drive ensures that only users can decrypt their files - even the development team cannot access user data.

## 🚀 Features

- **End-to-End Encryption**: Files are encrypted on the client side using AES-256
- **PIN-Based Security**: User-defined PINs protect encryption keys
- **Zero-Knowledge Architecture**: Server cannot decrypt user files
- **MinIO Integration**: Scalable S3-compatible object storage
- **Modern Stack**: Built with Elysia.js and Bun runtime
- **RESTful API**: Complete file management endpoints
- **JWT Authentication**: Secure user authentication
- **Swagger Documentation**: Interactive API documentation

## 🔒 How Encryption Works

Ghost Drive implements a sophisticated encryption system that ensures complete privacy:

### Encryption Architecture

1. **PIN-Based Key Derivation**: Users provide a secret PIN (e.g., "123456")
2. **AES Key Generation**: Backend generates a random AES-256 key for file encryption
3. **Key Encryption**: The AES key is encrypted using the user's PIN
4. **Secure Storage**: Only the encrypted AES key is stored in the database
5. **Client-Side Processing**: All file encryption/decryption happens on the client

### Step-by-Step Process

#### File Upload (Encryption)

```
┌─────────────┐    PIN      ┌─────────────┐
│    User     │ ──────────► │   Backend   │
│             │             │             │
└─────────────┘             └─────────────┘
                                    │
                                    ▼
                            ┌─────────────┐
                            │ Generate    │
                            │ Random AES  │
                            │ Key         │
                            └─────────────┘
                                    │
                                    ▼
┌─────────────┐    Encrypted    ┌─────────────┐
│  Database   │ ◄────────────── │   Backend   │
│             │    AES Key      │             │
└─────────────┘                 └─────────────┘
                                    │
                                    ▼
                            ┌─────────────┐
                            │ Send        │
                            │ Encrypted   │
                            │ AES Key     │
                            └─────────────┘
                                    │
                                    ▼
┌─────────────┐    Encrypted    ┌─────────────┐
│    User     │ ◄────────────── │   Backend   │
│             │    AES Key      │             │
└─────────────┘                 └─────────────┘
        │
        ▼
┌─────────────┐
│ Decrypt AES │
│ Key with    │
│ PIN         │
└─────────────┘
        │
        ▼
┌─────────────┐
│ Encrypt     │
│ File with   │
│ Plain AES   │
│ Key         │
└─────────────┘
        │
        ▼
┌─────────────┐    Encrypted    ┌─────────────┐
│    User     │ ───────────────►│   MinIO     │
│             │      File       │  Storage    │
└─────────────┘                 └─────────────┘
```

**Step-by-step explanation:**

1. **User provides PIN**: User sends their secret PIN to the backend
2. **Backend generates AES key**: Server creates a random AES-256 encryption key
3. **Key encryption**: The AES key is encrypted using the user's PIN
4. **Store encrypted key**: Only the encrypted AES key is saved in the database
5. **Send to client**: Backend sends the encrypted AES key to the user
6. **Client decrypts key**: User decrypts the AES key using their PIN
7. **File encryption**: User encrypts their file with the plain AES key
8. **Upload encrypted file**: Encrypted file is uploaded to MinIO storage

#### File Download (Decryption)

```
┌─────────────┐    Request     ┌─────────────┐
│    User     │ ──────────────►    Backend   │ 
│             │   File + PIN   │             │
└─────────────┘                └─────────────┘
                                    │
                                    ▼
┌─────────────┐    Encrypted    ┌─────────────┐
│  Database   │ ───────────────►│   Backend   │
│             │    AES Key      │             │
└─────────────┘                 └─────────────┘
                                    │
                                    ▼
┌─────────────┐    Encrypted    ┌─────────────┐
│    User     │ ◄────────────── │   Backend   │
│             │    AES Key      │             │
└─────────────┘                 └─────────────┘
        │
        ▼
┌─────────────┐
│ Decrypt AES │
│ Key with    │
│ PIN         │
└─────────────┘
        │
        ▼
┌─────────────┐    Encrypted    ┌─────────────┐
│    User     │ ───────────────►│   MinIO     │
│             │   File Request  │  Storage    │
└─────────────┘                 └─────────────┘
                                    │
                                    ▼
┌─────────────┐    Encrypted    ┌─────────────┐
│    User     │ ◄────────────── │   MinIO     │
│             │      File       │  Storage    │
└─────────────┘                 └─────────────┘
        │
        ▼
┌─────────────┐
│ Decrypt     │
│ File with   │
│ Plain AES   │
│ Key         │
└─────────────┘
        │
        ▼
┌─────────────┐
│ Original    │
│ File        │
│ Restored    │
└─────────────┘
```

**Step-by-step explanation:**

1. **User requests file**: User asks for a file and provides their PIN
2. **Backend retrieves key**: Server gets the encrypted AES key from database
3. **Send encrypted key**: Backend sends the encrypted AES key to user
4. **Client decrypts key**: User decrypts the AES key using their PIN
5. **Request encrypted file**: User requests the encrypted file from MinIO
6. **Receive encrypted file**: MinIO sends the encrypted file to user
7. **File decryption**: User decrypts the file using the plain AES key
8. **Original file restored**: User now has access to the original file

## 🛠️ Technology Stack

- **Runtime**: [Bun](https://bun.sh/) - Fast JavaScript runtime
- **Framework**: [Elysia](https://elysiajs.com/) - Fast web framework
- **Database**: PostgreSQL with [MikroORM](https://mikro-orm.io/)
- **Storage**: [MinIO](https://min.io/) - S3-compatible object storage
- **Authentication**: JWT tokens
- **Documentation**: Swagger/OpenAPI

## 📋 Prerequisites

- [Bun](https://bun.sh/) (v1.0.0 or higher)
- [PostgreSQL](https://www.postgresql.org/) (v12 or higher)
- [MinIO](https://min.io/) server
- [Docker](https://www.docker.com/) (optional)

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone <repository-url>
cd ghost-drive-be
```

### 2. Install Dependencies

```bash
bun install
```

### 3. Environment Configuration

Create a `.env` file in the root directory:

```env
# Database Configuration
DATABASE_URL=postgresql://username:password@localhost:5432/ghost_drive

# JWT Configuration
JWT_SECRET=your-super-secret-jwt-key

# MinIO Configuration
MINIO_ENDPOINT=localhost
MINIO_PORT=9000
MINIO_ACCESS_KEY=your-access-key
MINIO_SECRET_KEY=your-secret-key
MINIO_BUCKET=ghost-drive-files
MINIO_USE_SSL=false

# Server Configuration
PORT=3000
NODE_ENV=development
```

### 4. Database Setup

```bash
# Create database
createdb ghost_drive

# Run migrations (auto-generated by MikroORM)
bun run dev
```

### 5. Start MinIO

```bash
# Using Docker
docker run -p 9000:9000 -p 9001:9001 \
  -e "MINIO_ROOT_USER=admin" \
  -e "MINIO_ROOT_PASSWORD=password123" \
  minio/minio server /data --console-address ":9001"

# Or download and run MinIO directly
# https://min.io/download
```

### 6. Run the Application

```bash
# Development mode with hot reload
bun dev

# Production mode
bun start
```

The application will be available at:

- **API**: http://localhost:3000
- **Swagger Documentation**: http://localhost:3000/swagger-ui
- **MinIO Console**: http://localhost:9001

## 🐳 Docker Deployment

### Build and Run with Docker

```bash
# Build the image
docker build -t ghost-drive .

# Run the container
docker run -p 3000:3000 \
  -e DATABASE_URL=postgresql://username:password@host:5432/ghost_drive \
  -e JWT_SECRET=your-secret \
  -e MINIO_ENDPOINT=minio-server \
  ghost-drive
```

### Docker Compose (Complete Stack)

Create a `docker-compose.yml` file:

```yaml
version: "3.8"

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgresql://postgres:password@postgres:5432/ghost_drive
      - JWT_SECRET=your-super-secret-jwt-key
      - MINIO_ENDPOINT=minio
      - MINIO_PORT=9000
      - MINIO_ACCESS_KEY=admin
      - MINIO_SECRET_KEY=password123
    depends_on:
      - postgres
      - minio

  postgres:
    image: postgres:15
    environment:
      - POSTGRES_DB=ghost_drive
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  minio:
    image: minio/minio
    ports:
      - "9000:9000"
      - "9001:9001"
    environment:
      - MINIO_ROOT_USER=admin
      - MINIO_ROOT_PASSWORD=password123
    command: server /data --console-address ":9001"
    volumes:
      - minio_data:/data

volumes:
  postgres_data:
  minio_data:
```

Run with:

```bash
docker-compose up -d
```

## 🔧 Development

### Project Structure

```
src/
├── controllers/     # API route handlers
├── crypto/         # Encryption utilities
├── entities/       # Database models
├── macros/         # Elysia macros (auth, etc.)
├── middlewares/    # Request/response middleware
├── services/       # Business logic
├── db.ts          # Database configuration
├── index.ts       # Application entry point
└── mikro-orm.config.ts
```

### Available Scripts

```bash
# Development with hot reload
bun dev

# Production mode
bun start

# Run tests
bun test

# Build for production
bun run build
```

### Adding New Features

1. **Create Entity**: Add new models in `src/entities/`
2. **Create Service**: Add business logic in `src/services/`
3. **Create Controller**: Add API endpoints in `src/controllers/`
4. **Update Documentation**: Modify Swagger docs in controller files

## 🔐 Security Considerations

- **PIN Security**: Users should use strong, unique PINs
- **Key Rotation**: Consider implementing key rotation mechanisms
- **Rate Limiting**: Implement rate limiting for authentication endpoints
- **HTTPS**: Always use HTTPS in production
- **Audit Logging**: Log access attempts and file operations
- **Backup Strategy**: Implement secure backup for encrypted keys

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Built with modern web technologies for maximum security and performance
- Special thanks to the Elysia.js and Bun communities

---

**⚠️ Important**: This is a security-critical application. Always review the encryption implementation and conduct security audits before deploying to production.

**🔐 Remember**: The strength of your encryption depends on the strength of your PIN. Choose wisely!
