# ============================================================
# Application Configuration Setup Guide
# ============================================================
# This document explains how to configure the application
# for different environments.

## File Structure

```
src/main/resources/
├── application.yaml                 # Active profile (usually dev)
├── application.yaml.example         # Template for production-like config
├── application-dev.yaml             # Development config (local, gitignored)
├── application-dev.yaml.example     # Template for development setup
├── application-prod.yaml.example    # Template for production setup
└── ... other configs
```

## Quick Start for New Developers

### 1. Initial Setup

```bash
# Navigate to workspace directory
cd apps/qa-vibe/fluent-apps/workspace

# Copy development configuration template
cp src/main/resources/application-dev.yaml.example \
   src/main/resources/application-dev.yaml

# Copy environment variables template
cp .env.example .env
```

### 2. Configure Local Development

Edit `src/main/resources/application-dev.yaml`:
```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/workspace
    username: postgres
    password: your_local_password
  jpa:
    show-sql: true
    generate-ddl: true
```

Edit `.env`:
```bash
DB_URL=jdbc:postgresql://localhost:5432/workspace
DB_USERNAME=postgres
DB_PASSWORD=your_local_password
UPLOAD_PATH=/tmp/upload
```

### 3. Build and Run

```bash
# Using Maven
mvn clean install
mvn spring-boot:run

# Using Gradle (if configured)
gradle build
gradle bootRun
```

## Environment Profiles

### Development (`application-dev.yaml`)
- **Database**: Local PostgreSQL
- **SQL Logging**: Enabled for debugging
- **Error Details**: Full stack traces
- **API Documentation**: Enabled (Knife4j)
- **Security**: CSRF enabled, basic validation

**Use When**: Local development and testing

### Production (`application-prod.yaml`)
- **Database**: Remote managed database with connection pooling
- **SQL Logging**: Disabled for performance
- **Error Details**: Minimal exposure for security
- **SSL**: Enabled for secure connections
- **Logging**: File-based with rotation
- **Security**: Full security checks enabled

**Use When**: Production deployments

### Default (`application.yaml`)
```yaml
spring:
  profiles:
    active: dev
```

**Note**: This determines which profile is active. Change to `prod` for production.

## Configuration Variables

### Required Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `DB_URL` | PostgreSQL connection string | `jdbc:postgresql://localhost:5432/workspace` |
| `DB_USERNAME` | Database user | `postgres` |
| `DB_PASSWORD` | Database password | `secure_password` |
| `UPLOAD_PATH` | File upload directory | `/tmp/upload` |

### Optional Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `SERVER_PORT` | Application port | `9090` |
| `SSL_KEYSTORE_PATH` | SSL certificate path | N/A |
| `SSL_KEYSTORE_PASSWORD` | SSL certificate password | N/A |
| `LOG_LEVEL` | Root logging level | `WARN` |

## Database Setup

### PostgreSQL Local Setup

```bash
# Using Docker (recommended)
docker run --name workspace-db \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=postgres \
  -e POSTGRES_DB=workspace \
  -p 5432:5432 \
  -d postgres:15

# Using Homebrew (macOS)
brew install postgresql@15
brew services start postgresql@15
createdb workspace
```

## Security Considerations

⚠️ **Important**: Never commit actual configuration files with credentials!

✅ **Best Practices**:
- Use `.example` files for templates
- Store actual credentials in `.env` (gitignored)
- Use environment variables in production
- Rotate credentials regularly
- Review `.gitignore` to ensure sensitive files are excluded

See `SECURITY.md` for detailed security guidelines.

## Troubleshooting

### Connection Refused
- Verify PostgreSQL is running
- Check database URL and credentials in configuration
- Ensure port 5432 is accessible

### Class Not Found Errors
- Run `mvn clean install` to rebuild
- Clear Maven cache: `rm -rf ~/.m2/repository`

### Permission Denied on Upload Path
- Create upload directory: `mkdir -p /tmp/upload`
- Ensure write permissions: `chmod 755 /tmp/upload`

### Port Already in Use
- Change port in configuration: `server.port: 9091`
- Or kill process using port 9090

## Support

For more information, see:
- Spring Boot Documentation: https://spring.io/projects/spring-boot
- PostgreSQL Documentation: https://www.postgresql.org/docs/
- Project README: `../../README.md`
