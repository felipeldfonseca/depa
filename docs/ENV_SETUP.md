# Environment Setup Guide
## AI Departments Platform

**Version:** 1.0  
**Date:** 2025-09-13  
**Owner:** Felipe PM + Claude  
**Status:** DEVELOPMENT READY  

---

## Overview

Complete guide to setting up the development environment for the AI Departments Platform. This guide ensures consistent setup across all development machines and supports our meta-strategy of transparent, reproducible development.

**Setup Philosophy:** One-command setup with comprehensive documentation for our transparent development approach.

---

## 1. Prerequisites & System Requirements

### 1.1 System Requirements
```yaml
minimum_system_requirements:
  os:
    supported: ["macOS 12+", "Ubuntu 20.04+", "Windows 11 with WSL2"]
    recommended: "macOS 13+ or Ubuntu 22.04+"
  
  hardware:
    cpu:
      minimum: "4 cores, 2.4 GHz"
      recommended: "8 cores, 3.0 GHz (M1/M2 Mac or equivalent)"
    memory:
      minimum: "8 GB RAM"
      recommended: "16 GB RAM or more"
    storage:
      minimum: "20 GB free space"
      recommended: "50 GB free space (SSD preferred)"
  
  network:
    internet: "Stable broadband connection for AI model downloads"
    bandwidth: "Minimum 10 Mbps for development"
```

### 1.2 Required Software Installations
```bash
# Package managers
# macOS: Install Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Ubuntu: Update package lists
sudo apt update && sudo apt upgrade -y

# Windows: Install WSL2 + Ubuntu
wsl --install -d Ubuntu
```

---

## 2. Core Development Tools Setup

### 2.1 Node.js & Package Manager Setup
```bash
#!/bin/bash
# install_node.sh - Node.js setup script

echo "🚀 Setting up Node.js environment..."

# Install Node Version Manager (nvm)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# Reload shell configuration
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"

# Install and use Node.js 20 LTS
nvm install 20
nvm use 20
nvm alias default 20

# Verify installation
node --version  # Should show v20.x.x
npm --version   # Should show 10.x.x

# Install pnpm (faster package manager)
npm install -g pnpm

# Verify pnpm
pnpm --version

echo "✅ Node.js environment setup complete!"
```

### 2.2 Python & FastAPI Setup
```bash
#!/bin/bash
# install_python.sh - Python setup script

echo "🐍 Setting up Python environment..."

# Install Python 3.11 (macOS with Homebrew)
if [[ "$OSTYPE" == "darwin"* ]]; then
  brew install python@3.11
  brew install pyenv
fi

# Install Python 3.11 (Ubuntu)
if [[ "$OSTYPE" == "linux-gnu"* ]]; then
  sudo apt install -y software-properties-common
  sudo add-apt-repository ppa:deadsnakes/ppa
  sudo apt update
  sudo apt install -y python3.11 python3.11-venv python3.11-pip
fi

# Install pyenv for Python version management
curl https://pyenv.run | bash

# Add pyenv to shell configuration
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc

# Reload shell
exec $SHELL

# Install Python 3.11 via pyenv
pyenv install 3.11.0
pyenv global 3.11.0

# Install poetry for Python dependency management
curl -sSL https://install.python-poetry.org | python3 -

# Add poetry to PATH
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
exec $SHELL

# Verify installations
python --version  # Should show 3.11.x
pip --version
poetry --version

echo "✅ Python environment setup complete!"
```

### 2.3 Database Setup (PostgreSQL + Redis)
```bash
#!/bin/bash
# install_databases.sh - Database setup script

echo "🗄️ Setting up databases..."

# Install PostgreSQL (macOS)
if [[ "$OSTYPE" == "darwin"* ]]; then
  brew install postgresql@15
  brew services start postgresql@15
  
  # Create development database and user
  createdb ai_departments_dev
  psql ai_departments_dev -c "CREATE USER ai_departments WITH PASSWORD 'dev_password';"
  psql ai_departments_dev -c "ALTER USER ai_departments CREATEDB;"
  psql ai_departments_dev -c "GRANT ALL PRIVILEGES ON DATABASE ai_departments_dev TO ai_departments;"
fi

# Install PostgreSQL (Ubuntu)
if [[ "$OSTYPE" == "linux-gnu"* ]]; then
  sudo apt install -y postgresql postgresql-contrib
  sudo systemctl start postgresql
  sudo systemctl enable postgresql
  
  # Create development database and user
  sudo -u postgres createdb ai_departments_dev
  sudo -u postgres psql -c "CREATE USER ai_departments WITH PASSWORD 'dev_password';"
  sudo -u postgres psql -c "ALTER USER ai_departments CREATEDB;"
  sudo -u postgres psql -c "GRANT ALL PRIVILEGES ON DATABASE ai_departments_dev TO ai_departments;"
fi

# Install Redis (macOS)
if [[ "$OSTYPE" == "darwin"* ]]; then
  brew install redis
  brew services start redis
fi

# Install Redis (Ubuntu)
if [[ "$OSTYPE" == "linux-gnu"* ]]; then
  sudo apt install -y redis-server
  sudo systemctl start redis-server
  sudo systemctl enable redis-server
fi

# Verify installations
psql --version
redis-cli --version

# Test database connections
psql postgresql://ai_departments:dev_password@localhost:5432/ai_departments_dev -c "SELECT version();"
redis-cli ping  # Should return PONG

echo "✅ Database setup complete!"
```

---

## 3. AI Development Environment

### 3.1 AI Model & API Setup
```bash
#!/bin/bash
# setup_ai_environment.sh - AI development setup

echo "🤖 Setting up AI development environment..."

# Create .env file with API keys template
cat > .env.example << 'EOF'
# AI Model API Keys
OPENAI_API_KEY=your_openai_api_key_here
ANTHROPIC_API_KEY=your_anthropic_api_key_here
GOOGLE_AI_API_KEY=your_google_ai_api_key_here

# Primary model configuration (cost optimization)
PRIMARY_MODEL_PROVIDER=google
PRIMARY_MODEL_NAME=gemini-2.5-flash
FALLBACK_MODEL_PROVIDER=openai
FALLBACK_MODEL_NAME=gpt-4o-mini

# Model routing configuration
COST_OPTIMIZATION_MODE=true
HIGH_QUALITY_MODEL_RATIO=0.2  # 20% high-quality, 80% cost-efficient
EOF

# Install AI development libraries (Python)
pip install -r requirements-ai.txt

# Create requirements-ai.txt if it doesn't exist
cat > requirements-ai.txt << 'EOF'
# AI & ML Libraries
openai==1.12.0
anthropic==0.18.0
google-generativeai==0.5.0
langchain==0.1.9
langchain-community==0.0.25
crewai==0.28.0

# Vector databases & embeddings
chromadb==0.4.24
sentence-transformers==2.3.1
tiktoken==0.6.0

# Data processing
pandas==2.1.4
numpy==1.26.3
pydantic==2.6.1

# Monitoring & observability
langsmith==0.0.87
wandb==0.16.2
EOF

# Install AI libraries
pip install -r requirements-ai.txt

echo "✅ AI development environment setup complete!"
echo "⚠️  Remember to add your actual API keys to .env file"
```

### 3.2 Development Tools & IDE Setup
```bash
#!/bin/bash
# setup_dev_tools.sh - Development tools setup

echo "🛠️ Setting up development tools..."

# Install Git (if not already installed)
git --version || {
  if [[ "$OSTYPE" == "darwin"* ]]; then
    brew install git
  elif [[ "$OSTYPE" == "linux-gnu"* ]]; then
    sudo apt install -y git
  fi
}

# Configure Git with Brazilian defaults
git config --global init.defaultBranch main
git config --global pull.rebase false
git config --global core.autocrlf input
git config --global core.editor "code --wait"

# Install VS Code extensions for the project
code_extensions=(
  "ms-python.python"
  "ms-python.black-formatter"
  "ms-python.isort"
  "ms-toolsai.jupyter"
  "bradlc.vscode-tailwindcss"
  "esbenp.prettier-vscode"
  "ms-vscode.vscode-typescript-next"
  "GraphQL.vscode-graphql"
  "ms-vscode.remote-containers"
  "GitHub.copilot"
  "GitHub.copilot-chat"
  "redhat.vscode-yaml"
  "ms-vscode-remote.remote-ssh"
)

# Install VS Code extensions
for extension in "${code_extensions[@]}"; do
  code --install-extension $extension
done

# Create VS Code workspace settings
mkdir -p .vscode

cat > .vscode/settings.json << 'EOF'
{
  "python.defaultInterpreterPath": "./.venv/bin/python",
  "python.terminal.activateEnvironment": true,
  "python.formatting.provider": "black",
  "python.linting.enabled": true,
  "python.linting.pylintEnabled": false,
  "python.linting.flake8Enabled": true,
  "typescript.preferences.importModuleSpecifier": "relative",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.organizeImports": true,
    "source.fixAll.eslint": true
  },
  "tailwindCSS.experimental.classRegex": [
    ["cva\\(([^)]*)\\)", "[\"'`]([^\"'`]*).*?[\"'`]"],
    ["cx\\(([^)]*)\\)", "(?:'|\"|`)([^']*)(?:'|\"|`)"]
  ],
  "files.exclude": {
    "**/__pycache__": true,
    "**/node_modules": true,
    "**/.next": true,
    "**/dist": true,
    "**/.venv": true
  },
  "search.exclude": {
    "**/node_modules": true,
    "**/.next": true,
    "**/dist": true,
    "**/.venv": true,
    "**/logs": true
  }
}
EOF

cat > .vscode/launch.json << 'EOF'
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "FastAPI Development Server",
      "type": "python",
      "request": "launch",
      "program": "${workspaceFolder}/backend/main.py",
      "console": "integratedTerminal",
      "env": {
        "PYTHONPATH": "${workspaceFolder}/backend",
        "ENVIRONMENT": "development"
      }
    },
    {
      "name": "Next.js Development Server",
      "type": "node",
      "request": "launch",
      "cwd": "${workspaceFolder}/frontend",
      "runtimeExecutable": "pnpm",
      "runtimeArgs": ["dev"]
    }
  ]
}
EOF

echo "✅ Development tools setup complete!"
```

---

## 4. Project Initialization

### 4.1 Repository Setup
```bash
#!/bin/bash
# init_project.sh - Initialize project repository

echo "📁 Initializing project repository..."

# Clone repository (replace with actual repo URL)
# git clone https://github.com/felipe/ai-departments-platform.git
# cd ai-departments-platform

# Or initialize new repository
git init
git add .
git commit -m "Initial commit: AI Departments Platform setup

🤖 Generated with [Claude Code](https://claude.ai/code)

Co-Authored-By: Claude <noreply@anthropic.com>"

# Create main development branches
git checkout -b development
git checkout -b staging
git checkout main

# Set up branch protection and workflows would be done via GitHub UI

echo "✅ Repository initialization complete!"
```

### 4.2 Frontend Setup (Next.js + React)
```bash
#!/bin/bash
# setup_frontend.sh - Frontend development setup

echo "⚛️ Setting up Frontend development environment..."

# Navigate to frontend directory
cd frontend

# Install dependencies using pnpm
pnpm install

# Create development environment file
cp .env.example .env.local

# Default environment variables for development
cat >> .env.local << 'EOF'
# Frontend Environment Variables
NEXT_PUBLIC_API_BASE_URL=http://localhost:8000/api/v1
NEXT_PUBLIC_APP_ENV=development
NEXT_PUBLIC_SENTRY_DSN=your_sentry_dsn_here

# Authentication
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your_nextauth_secret_here

# External Services
NEXT_PUBLIC_GOOGLE_ANALYTICS_ID=your_ga_id_here
NEXT_PUBLIC_HOTJAR_ID=your_hotjar_id_here
EOF

# Install Tailwind CSS and configure
pnpm add -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# Run development server
echo "Starting Frontend development server..."
pnpm dev &

echo "✅ Frontend setup complete!"
echo "🌐 Frontend running at http://localhost:3000"
```

### 4.3 Backend Setup (FastAPI + Python)
```bash
#!/bin/bash
# setup_backend.sh - Backend development setup

echo "🚀 Setting up Backend development environment..."

# Navigate to backend directory
cd backend

# Create Python virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Create development environment file
cp .env.example .env

# Default environment variables for development
cat >> .env << 'EOF'
# Backend Environment Variables
DATABASE_URL=postgresql://ai_departments:dev_password@localhost:5432/ai_departments_dev
REDIS_URL=redis://localhost:6379/0
ENVIRONMENT=development
DEBUG=true
LOG_LEVEL=DEBUG

# Security
SECRET_KEY=your_secret_key_here_at_least_32_characters
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
REFRESH_TOKEN_EXPIRE_DAYS=7

# AI Configuration
OPENAI_API_KEY=your_openai_api_key
ANTHROPIC_API_KEY=your_anthropic_api_key
GOOGLE_AI_API_KEY=your_google_ai_api_key

# WhatsApp Business API
WHATSAPP_ACCESS_TOKEN=your_whatsapp_token
WHATSAPP_PHONE_NUMBER_ID=your_phone_number_id
WHATSAPP_WEBHOOK_VERIFY_TOKEN=your_webhook_verify_token

# Email Configuration (Development)
EMAIL_BACKEND=console
SMTP_HOST=localhost
SMTP_PORT=1025
SMTP_USERNAME=
SMTP_PASSWORD=
EOF

# Run database migrations
python -m alembic upgrade head

# Create initial admin user (development only)
python scripts/create_admin_user.py

# Run development server
echo "Starting Backend development server..."
uvicorn main:app --host 0.0.0.0 --port 8000 --reload &

echo "✅ Backend setup complete!"
echo "🚀 Backend running at http://localhost:8000"
echo "📚 API docs available at http://localhost:8000/docs"
```

---

## 5. Development Workflow Setup

### 5.1 Pre-commit Hooks Setup
```bash
#!/bin/bash
# setup_precommit.sh - Pre-commit hooks setup

echo "🔧 Setting up pre-commit hooks..."

# Install pre-commit
pip install pre-commit

# Create .pre-commit-config.yaml
cat > .pre-commit-config.yaml << 'EOF'
repos:
  # Python formatting and linting
  - repo: https://github.com/psf/black
    rev: 24.1.1
    hooks:
      - id: black
        language_version: python3.11
        
  - repo: https://github.com/pycqa/isort
    rev: 5.13.2
    hooks:
      - id: isort
        
  - repo: https://github.com/pycqa/flake8
    rev: 7.0.0
    hooks:
      - id: flake8
        additional_dependencies: [flake8-docstrings]
        
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.8.0
    hooks:
      - id: mypy
        additional_dependencies: [types-all]
        
  # JavaScript/TypeScript formatting and linting
  - repo: https://github.com/pre-commit/mirrors-prettier
    rev: v4.0.0-alpha.8
    hooks:
      - id: prettier
        types_or: [javascript, jsx, ts, tsx, json, yaml, css, scss, markdown]
        
  - repo: https://github.com/pre-commit/mirrors-eslint
    rev: v8.56.0
    hooks:
      - id: eslint
        files: \.(js|jsx|ts|tsx)$
        types: [file]
        
  # General hooks
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files
      - id: check-merge-conflict
      
  # Security scanning
  - repo: https://github.com/PyCQA/bandit
    rev: 1.7.7
    hooks:
      - id: bandit
        args: ["-c", "pyproject.toml"]
        
  # Secrets detection
  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.4.0
    hooks:
      - id: detect-secrets
        args: ['--baseline', '.secrets.baseline']
EOF

# Install pre-commit hooks
pre-commit install

# Create secrets baseline
detect-secrets scan --baseline .secrets.baseline

echo "✅ Pre-commit hooks setup complete!"
```

### 5.2 Testing Environment Setup
```bash
#!/bin/bash
# setup_testing.sh - Testing environment setup

echo "🧪 Setting up testing environment..."

# Frontend testing setup
cd frontend
pnpm add -D jest @testing-library/react @testing-library/jest-dom @testing-library/user-event
pnpm add -D @types/jest jest-environment-jsdom

# Create jest.config.js
cat > jest.config.js << 'EOF'
const nextJest = require('next/jest')

const createJestConfig = nextJest({
  // Provide the path to your Next.js app to load next.config.js and .env files
  dir: './',
})

// Add any custom config to be passed to Jest
const customJestConfig = {
  setupFilesAfterEnv: ['<rootDir>/jest.setup.js'],
  moduleNameMapping: {
    // Handle module aliases (this will be automatically configured for you based on your tsconfig.json paths)
    '^@/components/(.*)$': '<rootDir>/components/$1',
    '^@/pages/(.*)$': '<rootDir>/pages/$1',
    '^@/lib/(.*)$': '<rootDir>/lib/$1',
  },
  testEnvironment: 'jest-environment-jsdom',
}

// createJestConfig is exported this way to ensure that next/jest can load the Next.js config which is async
module.exports = createJestConfig(customJestConfig)
EOF

# Create jest.setup.js
cat > jest.setup.js << 'EOF'
import '@testing-library/jest-dom'
EOF

# Backend testing setup
cd ../backend
pip install pytest pytest-asyncio pytest-cov httpx

# Create pytest.ini
cat > pytest.ini << 'EOF'
[tool:pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
addopts = 
    -v
    --tb=short
    --cov=.
    --cov-report=term-missing
    --cov-report=html:htmlcov
    --cov-fail-under=80
asyncio_mode = auto
EOF

# Create test database
createdb ai_departments_test
psql ai_departments_test -c "CREATE USER ai_departments_test WITH PASSWORD 'test_password';"
psql ai_departments_test -c "GRANT ALL PRIVILEGES ON DATABASE ai_departments_test TO ai_departments_test;"

echo "✅ Testing environment setup complete!"
```

---

## 6. Development Server Management

### 6.1 Process Management Scripts
```bash
#!/bin/bash
# scripts/dev-start.sh - Start all development servers

echo "🚀 Starting AI Departments Platform development environment..."

# Start PostgreSQL and Redis
if [[ "$OSTYPE" == "darwin"* ]]; then
  brew services start postgresql@15
  brew services start redis
elif [[ "$OSTYPE" == "linux-gnu"* ]]; then
  sudo systemctl start postgresql
  sudo systemctl start redis-server
fi

# Start backend server
echo "Starting Backend server..."
cd backend
source .venv/bin/activate
uvicorn main:app --host 0.0.0.0 --port 8000 --reload &
BACKEND_PID=$!

# Start frontend server
echo "Starting Frontend server..."
cd ../frontend
pnpm dev &
FRONTEND_PID=$!

# Start AI model services (if needed)
echo "Starting AI services..."
cd ../ai-services
python -m uvicorn ai_orchestrator:app --host 0.0.0.0 --port 8001 --reload &
AI_PID=$!

# Save PIDs for cleanup
echo $BACKEND_PID > .backend.pid
echo $FRONTEND_PID > .frontend.pid
echo $AI_PID > .ai.pid

echo "✅ Development servers started!"
echo "🌐 Frontend: http://localhost:3000"
echo "🚀 Backend: http://localhost:8000"
echo "📚 API Docs: http://localhost:8000/docs"
echo "🤖 AI Services: http://localhost:8001"

# Wait for Ctrl+C
trap 'kill $BACKEND_PID $FRONTEND_PID $AI_PID; exit' INT
wait
```

```bash
#!/bin/bash
# scripts/dev-stop.sh - Stop all development servers

echo "🛑 Stopping AI Departments Platform development servers..."

# Kill development servers
if [ -f .backend.pid ]; then
  kill $(cat .backend.pid) 2>/dev/null
  rm .backend.pid
fi

if [ -f .frontend.pid ]; then
  kill $(cat .frontend.pid) 2>/dev/null
  rm .frontend.pid
fi

if [ -f .ai.pid ]; then
  kill $(cat .ai.pid) 2>/dev/null
  rm .ai.pid
fi

# Stop databases (optional)
if [[ "$OSTYPE" == "darwin"* ]]; then
  brew services stop postgresql@15
  brew services stop redis
elif [[ "$OSTYPE" == "linux-gnu"* ]]; then
  sudo systemctl stop postgresql
  sudo systemctl stop redis-server
fi

echo "✅ Development servers stopped!"
```

### 6.2 Health Check Scripts
```bash
#!/bin/bash
# scripts/health-check.sh - Check development environment health

echo "🏥 Checking development environment health..."

# Check Node.js
echo "Checking Node.js..."
node --version || echo "❌ Node.js not found"

# Check Python
echo "Checking Python..."
python --version || echo "❌ Python not found"

# Check PostgreSQL
echo "Checking PostgreSQL..."
pg_isready -h localhost -p 5432 || echo "❌ PostgreSQL not running"

# Check Redis
echo "Checking Redis..."
redis-cli ping | grep -q "PONG" || echo "❌ Redis not running"

# Check frontend dependencies
echo "Checking Frontend dependencies..."
cd frontend && pnpm list --depth=0 | grep -q "next" || echo "❌ Frontend dependencies missing"

# Check backend dependencies
echo "Checking Backend dependencies..."
cd ../backend && source .venv/bin/activate && pip list | grep -q "fastapi" || echo "❌ Backend dependencies missing"

# Check API endpoints
echo "Checking API endpoints..."
curl -s http://localhost:8000/health | grep -q "ok" || echo "❌ Backend API not responding"
curl -s http://localhost:3000 | grep -q "AI Departments" || echo "❌ Frontend not responding"

echo "✅ Health check complete!"
```

---

## 7. Environment Variables & Secrets

### 7.1 Environment Configuration
```bash
# .env.example - Template for environment variables

# ===========================================
# 🚨 NEVER COMMIT ACTUAL SECRETS TO GIT 🚨
# ===========================================

# Application Configuration
APP_NAME=AI Departments Platform
APP_VERSION=1.0.0
ENVIRONMENT=development
DEBUG=true
LOG_LEVEL=DEBUG

# Database Configuration
DATABASE_URL=postgresql://ai_departments:dev_password@localhost:5432/ai_departments_dev
REDIS_URL=redis://localhost:6379/0

# Security Configuration
SECRET_KEY=your-secret-key-here-at-least-32-characters-long
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
REFRESH_TOKEN_EXPIRE_DAYS=7

# AI Model Configuration
PRIMARY_MODEL_PROVIDER=google
PRIMARY_MODEL_NAME=gemini-2.5-flash
FALLBACK_MODEL_PROVIDER=openai
FALLBACK_MODEL_NAME=gpt-4o-mini
COST_OPTIMIZATION_MODE=true
HIGH_QUALITY_MODEL_RATIO=0.2

# AI API Keys
OPENAI_API_KEY=sk-proj-your-openai-api-key-here
ANTHROPIC_API_KEY=sk-ant-your-anthropic-api-key-here
GOOGLE_AI_API_KEY=your-google-ai-api-key-here

# WhatsApp Business API
WHATSAPP_ACCESS_TOKEN=your-whatsapp-access-token
WHATSAPP_PHONE_NUMBER_ID=your-whatsapp-phone-number-id
WHATSAPP_WEBHOOK_VERIFY_TOKEN=your-webhook-verify-token

# Email Configuration
EMAIL_BACKEND=console
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USERNAME=your-email@gmail.com
SMTP_PASSWORD=your-app-password
FROM_EMAIL=noreply@aidepartments.com

# Google Cloud Platform
GCP_PROJECT_ID=ai-departments-platform
GCP_REGION=us-central1
GOOGLE_APPLICATION_CREDENTIALS=./gcp-service-account.json

# Frontend Configuration
NEXT_PUBLIC_API_BASE_URL=http://localhost:8000/api/v1
NEXT_PUBLIC_APP_ENV=development
NEXTAUTH_URL=http://localhost:3000
NEXTAUTH_SECRET=your-nextauth-secret-here

# Analytics & Monitoring
NEXT_PUBLIC_GOOGLE_ANALYTICS_ID=G-XXXXXXXXXX
NEXT_PUBLIC_HOTJAR_ID=your-hotjar-id
SENTRY_DSN=your-sentry-dsn-here

# Payment Processing
STRIPE_PUBLIC_KEY=pk_test_your-stripe-public-key
STRIPE_SECRET_KEY=sk_test_your-stripe-secret-key
STRIPE_WEBHOOK_SECRET=whsec_your-webhook-secret

# Brazilian Payment Processing
PIX_API_KEY=your-pix-api-key
PIX_CLIENT_ID=your-pix-client-id
PIX_CLIENT_SECRET=your-pix-client-secret

# Social Media Integration
INSTAGRAM_ACCESS_TOKEN=your-instagram-access-token
FACEBOOK_ACCESS_TOKEN=your-facebook-access-token
LINKEDIN_CLIENT_ID=your-linkedin-client-id
LINKEDIN_CLIENT_SECRET=your-linkedin-client-secret

# File Storage
AWS_ACCESS_KEY_ID=your-aws-access-key
AWS_SECRET_ACCESS_KEY=your-aws-secret-key
AWS_S3_BUCKET=ai-departments-dev
AWS_S3_REGION=us-east-1

# Development Tools
JUPYTER_TOKEN=your-jupyter-token
WANDB_API_KEY=your-wandb-api-key
```

### 7.2 Secrets Management
```bash
#!/bin/bash
# scripts/setup-secrets.sh - Secure secrets management setup

echo "🔐 Setting up secrets management..."

# Create secrets directory (git-ignored)
mkdir -p .secrets
echo ".secrets/" >> .gitignore

# Install git-crypt for repository encryption (optional)
if [[ "$OSTYPE" == "darwin"* ]]; then
  brew install git-crypt
elif [[ "$OSTYPE" == "linux-gnu"* ]]; then
  sudo apt install -y git-crypt
fi

# Setup git-crypt
git-crypt init

# Create .gitattributes for encrypted files
cat > .gitattributes << 'EOF'
.secrets/** filter=git-crypt diff=git-crypt
.env.production filter=git-crypt diff=git-crypt
**/*secret* filter=git-crypt diff=git-crypt
**/*key* filter=git-crypt diff=git-crypt
EOF

# Create secrets validation script
cat > scripts/validate-secrets.sh << 'EOF'
#!/bin/bash
# Validate required secrets are present

required_vars=(
  "SECRET_KEY"
  "DATABASE_URL"
  "OPENAI_API_KEY"
  "WHATSAPP_ACCESS_TOKEN"
)

echo "🔍 Validating required secrets..."

missing_vars=()
for var in "${required_vars[@]}"; do
  if [ -z "${!var}" ]; then
    missing_vars+=("$var")
  fi
done

if [ ${#missing_vars[@]} -ne 0 ]; then
  echo "❌ Missing required environment variables:"
  printf '%s\n' "${missing_vars[@]}"
  exit 1
fi

echo "✅ All required secrets are present!"
EOF

chmod +x scripts/validate-secrets.sh

echo "✅ Secrets management setup complete!"
echo "⚠️  Remember to:"
echo "   1. Copy .env.example to .env and fill in real values"
echo "   2. Never commit .env files to git"
echo "   3. Use git-crypt for production secrets"
```

---

## 8. Troubleshooting Guide

### 8.1 Common Setup Issues
```yaml
# troubleshooting_guide.yaml
common_issues:
  node_version_conflicts:
    problem: "Multiple Node.js versions causing conflicts"
    symptoms: ["Module not found errors", "Build failures"]
    solution: "Use nvm to manage versions: nvm use 20"
    
  python_virtual_environment:
    problem: "Python packages not found or version conflicts"
    symptoms: ["ModuleNotFoundError", "Import errors"]
    solution: "Activate virtual environment: source .venv/bin/activate"
    
  database_connection_failed:
    problem: "Cannot connect to PostgreSQL"
    symptoms: ["Connection refused", "Database does not exist"]
    solutions:
      - "Check PostgreSQL is running: pg_isready"
      - "Create database: createdb ai_departments_dev"
      - "Check credentials in .env file"
      
  port_already_in_use:
    problem: "Development server ports are occupied"
    symptoms: ["Port 3000/8000 already in use"]
    solution: "Kill processes: lsof -ti:3000 | xargs kill -9"
    
  memory_issues:
    problem: "Out of memory during development"
    symptoms: ["JavaScript heap out of memory", "Build failures"]
    solutions:
      - "Increase Node.js memory: export NODE_OPTIONS='--max-old-space-size=4096'"
      - "Close unnecessary applications"
      - "Restart development servers"
```

### 8.2 Performance Optimization
```bash
#!/bin/bash
# scripts/optimize-dev-environment.sh - Optimize development performance

echo "⚡ Optimizing development environment performance..."

# Node.js optimizations
export NODE_OPTIONS="--max-old-space-size=4096"
echo 'export NODE_OPTIONS="--max-old-space-size=4096"' >> ~/.bashrc

# pnpm configuration for faster installs
pnpm config set store-dir ~/.pnpm-store
pnpm config set cache-dir ~/.pnpm-cache
pnpm config set fetch-retries 3
pnpm config set fetch-retry-factor 2

# Python optimizations
export PYTHONDONTWRITEBYTECODE=1
export PYTHONUNBUFFERED=1
echo 'export PYTHONDONTWRITEBYTECODE=1' >> ~/.bashrc
echo 'export PYTHONUNBUFFERED=1' >> ~/.bashrc

# Git optimizations
git config --global core.preloadindex true
git config --global core.fscache true
git config --global gc.auto 256

# PostgreSQL development optimizations
cat >> postgresql.dev.conf << 'EOF'
# Development-only optimizations (not for production)
fsync = off
synchronous_commit = off
checkpoint_segments = 32
checkpoint_completion_target = 0.9
wal_buffers = 16MB
shared_buffers = 256MB
EOF

echo "✅ Development environment optimized!"
echo "💡 Restart your terminal to apply all changes"
```

---

## 9. Quick Start Commands

### 9.1 One-Command Setup
```bash
#!/bin/bash
# quick-start.sh - Complete environment setup in one command

echo "🚀 AI Departments Platform - Quick Start Setup"
echo "=============================================="

# Check if running on supported OS
case "$OSTYPE" in
  darwin*)
    echo "🍎 macOS detected"
    OS="macos"
    ;;
  linux-gnu*)
    echo "🐧 Linux detected"
    OS="linux"
    ;;
  msys*|cygwin*|win32*)
    echo "🪟 Windows detected - please use WSL2"
    exit 1
    ;;
  *)
    echo "❌ Unsupported OS: $OSTYPE"
    exit 1
    ;;
esac

# Run all setup scripts
echo "📦 Installing prerequisites..."
./scripts/install_prerequisites.sh

echo "🔧 Setting up Node.js..."
./scripts/install_node.sh

echo "🐍 Setting up Python..."
./scripts/install_python.sh

echo "🗄️ Setting up databases..."
./scripts/install_databases.sh

echo "🤖 Setting up AI environment..."
./scripts/setup_ai_environment.sh

echo "🛠️ Setting up development tools..."
./scripts/setup_dev_tools.sh

echo "📁 Initializing project..."
./scripts/setup_frontend.sh
./scripts/setup_backend.sh

echo "🔧 Setting up pre-commit hooks..."
./scripts/setup_precommit.sh

echo "🧪 Setting up testing environment..."
./scripts/setup_testing.sh

echo "🔐 Setting up secrets management..."
./scripts/setup-secrets.sh

echo "⚡ Optimizing development environment..."
./scripts/optimize-dev-environment.sh

# Validate setup
echo "🏥 Validating setup..."
./scripts/health-check.sh

echo ""
echo "✅ Setup complete! Next steps:"
echo "1. Copy .env.example to .env and fill in your API keys"
echo "2. Run: ./scripts/dev-start.sh to start development servers"
echo "3. Open http://localhost:3000 in your browser"
echo ""
echo "📚 Documentation: http://localhost:3000/docs"
echo "🚀 API: http://localhost:8000/docs"
echo ""
echo "Happy coding! 🎉"
```

### 9.2 Daily Development Commands
```bash
# ~/.bashrc or ~/.zshrc aliases for daily development

# AI Departments Platform aliases
alias ai-start='cd ~/Dev/ai-departments-platform && ./scripts/dev-start.sh'
alias ai-stop='cd ~/Dev/ai-departments-platform && ./scripts/dev-stop.sh'
alias ai-test='cd ~/Dev/ai-departments-platform && ./scripts/run-tests.sh'
alias ai-health='cd ~/Dev/ai-departments-platform && ./scripts/health-check.sh'
alias ai-logs='cd ~/Dev/ai-departments-platform && ./scripts/view-logs.sh'
alias ai-reset='cd ~/Dev/ai-departments-platform && ./scripts/reset-dev-environment.sh'

# Quick navigation
alias ai-frontend='cd ~/Dev/ai-departments-platform/frontend'
alias ai-backend='cd ~/Dev/ai-departments-platform/backend'
alias ai-docs='cd ~/Dev/ai-departments-platform/docs'

# Common development tasks
alias ai-install='cd ~/Dev/ai-departments-platform && pnpm install && cd backend && pip install -r requirements.txt'
alias ai-build='cd ~/Dev/ai-departments-platform/frontend && pnpm build'
alias ai-lint='cd ~/Dev/ai-departments-platform && pnpm lint && cd backend && black . && isort .'
```

---

**Document Status:** Complete environment setup guide ready for development team onboarding and consistent development environment establishment.

**Meta-Strategy Alignment:** This setup guide enables transparent development by ensuring all team members (Felipe + Claude) can quickly establish identical development environments, supporting our approach of building in public and demonstrating AI capabilities through our development process.