# Security Guidelines

## Sensitive Information Protection

This project handles sensitive data such as database credentials, API keys, and configuration secrets. Please follow these guidelines to prevent accidental exposure.

### ⚠️ Never Commit

The following should **never** be committed to version control:
- `application*.yaml` - Spring Boot configuration files with credentials
- `application*.properties` - Property files with secrets
- `.env` - Environment variable files
- Any files containing passwords, API keys, or tokens
- Private keys, certificates, or authentication tokens

### ✅ Use Instead

1. **Environment Variables**: Use `${VARIABLE_NAME:default_value}` syntax in example configuration files
   ```yaml
   spring:
     datasource:
       password: ${DB_PASSWORD}
       url: ${DB_URL}
   ```

2. **Example Files**: Commit `.example` files showing the structure and required variables
   - `application-dev.yaml.example`
   - `.env.example`

3. **Local Configuration**: Create local configuration files that are ignored by Git
   - `application-dev.yaml` (ignored)
   - `.env` (ignored)

### 🔧 Setup Instructions

1. Copy example files to create local versions:
   ```bash
   cp .env.example .env
   cp apps/qa-vibe/fluent-apps/workspace/src/main/resources/application-dev.yaml.example \
      apps/qa-vibe/fluent-apps/workspace/src/main/resources/application-dev.yaml
   ```

2. Edit the local files with your actual credentials:
   ```bash
   # Edit .env
   export DB_PASSWORD=your_actual_password
   export DB_URL=jdbc:postgresql://your-host:5432/database
   ```

3. **Keep local files secure** - Never share or commit them

### 🔐 CI/CD Secrets

For automated deployments, use your CI/CD platform's secret management:
- GitHub Secrets (GitHub Actions)
- GitLab CI/CD Variables (GitLab)
- Jenkins Credentials Plugin
- Azure Key Vault (Azure DevOps)

### 📋 Checklist Before Committing

- [ ] No `.env` files included
- [ ] No `application*.yaml` files with actual credentials
- [ ] No API keys or passwords in code
- [ ] Only `.example` template files are committed
- [ ] `.gitignore` includes all sensitive patterns

### 🚨 If Credentials are Accidentally Committed

1. **Immediately rotate** the exposed credentials
2. **Remove from history** using `git filter-branch` or similar tools
3. **Notify** team members and security team
4. **Update** all systems using the old credentials

---

For more information, see [OWASP: Secrets Management](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html)
