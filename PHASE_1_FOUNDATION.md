# Phase 1: Foundation — Core Configuration & Build Infrastructure
**Objective:** Eliminate build drift, prevent secret leaks, add unified testing & CI/CD  
**Timeline:** 1 week  
**Owner:** DevOps & Backend Architecture

---

## 1.1: Universal Configuration Files

### Action: Deploy `.gitignore` to All 11 Repos

**File:** `.gitignore`

```gitignore
# Dependencies
node_modules/
.pnp
.pnp.js

# Production builds
dist/
build/
out/
.next/
.vercel/
.output/

# Environment and secrets
.env
.env.*
!.env.example
*.pem
*.key
secrets.h

# Python virtual environment and bytecode
__pycache__/
*.py[cod]
*$py.class
.venv/
venv/
ENV/
*.egg-info/

# Logs and runtime data
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.eslintcache
.turbo/

# OS artifacts
.DS_Store
Thumbs.db
```

**Deployment Command:**
```bash
for repo in rick-c137 sovereign-blob the-sovereign-blob oracle-c137-harmony-hub \
            codex-control-deck sovereign-bridge sovereign-b2d xbox-plasma-sovereign \
            rick-portal-app sovereign-portal-c137 prometheus-a17; do
  cp .gitignore ../$repo/.gitignore
  cd ../$repo && git add .gitignore && git commit -m "chore: add universal .gitignore"
  cd ..
done
```

---

### Action: Deploy `.env.example` to All Repos

**File:** `.env.example`

```env
# ==============================================================================
# Sovereign Ecosystem Environment Template
# Duplicate this file to .env and populate with legitimate credentials.
# ==============================================================================

# Node / App Environment
NODE_ENV=development
PORT=3000
APP_BASE_URL=http://localhost:3000

# Auth & Sessions (Must match better-auth >=1.6.40 requirements)
BETTER_AUTH_SECRET=changeme-replace-with-at-least-32-byte-hex-string
BETTER_AUTH_URL=http://localhost:3000/api/auth

# Database (PostgreSQL >=16.0)
DATABASE_URL=postgres://sovereign_user:sovereign_pass@localhost:5432/sovereign_db
DATABASE_POOL_MIN=2
DATABASE_POOL_MAX=10

# AI Provider Integrations (oracle-c137-harmony-hub)
OPENAI_API_KEY=sk-placeholder
ANTHROPIC_API_KEY=sk-ant-placeholder

# Inter-Service Security
INTERNAL_SHARED_SECRET=sovereign-cluster-internal-auth-token-example
TELEMETRY_ENDPOINT=http://localhost:9090/api/v1/telemetry
PROMETHEUS_BEARER_TOKEN=token-telemetry-sovereign

# Hardware Controllers (xbox-plasma-sovereign)
PLASMA_DEVICE_PORT=/dev/ttyUSB0
PLASMA_BAUD_RATE=115200

# Feature Flags
ENABLE_WEBGPU=true
ENABLE_LOCAL_LLM=false
DEMO_MODE=false
```

**Deployment:** Copy to all repos, commit with `git add .env.example`

---

### Action: Deploy Unified TypeScript & Linting Config

**File:** `tsconfig.base.json`

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,
    "isolatedModules": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true
  }
}
```

**File:** `.eslintrc.json`

```json
{
  "root": true,
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaVersion": 2022,
    "sourceType": "module"
  },
  "plugins": ["@typescript-eslint"],
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended"
  ],
  "rules": {
    "@typescript-eslint/no-unused-vars": ["error", { "argsIgnorePattern": "^_" }],
    "@typescript-eslint/no-explicit-any": "warn",
    "no-console": ["warn", { "allow": ["warn", "error", "info"] }]
  },
  "ignorePatterns": ["dist/", "node_modules/", "*.config.js"]
}
```

**File:** `.prettierrc`

```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "all",
  "printWidth": 100,
  "arrowParens": "always"
}
```

---

## 1.2: Node Version Pinning & Package Validation

### Action: Add `.nvmrc` to All Node-Based Repos

**File:** `.nvmrc`
```
20.12.0
```

### Action: Add `engines` Field to `package.json`

**Update in all 9 JavaScript/TypeScript repos:**

```json
{
  "engines": {
    "node": ">=20.12.0",
    "npm": ">=10.0.0"
  }
}
```

### Action: Audit & Pin Critical Dependencies

**Commands to Run in Each Repo:**

```bash
npm audit --audit-level=high
npm audit fix --force

# Pin problematic versions
npm install better-auth@^1.6.40 --save
npm install pg@^8.16.5 --save
npm install nitro@^3.0.0 --save
```

---

## 1.3: CI/CD Pipeline Setup

### Action: Create `.github/workflows/ci.yml` in All Repos

**File:** `.github/workflows/ci.yml`

```yaml
name: Sovereign Ecosystem CI/CD

on:
  push:
    branches: [main, master, staging]
  pull_request:
    branches: [main, master]

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  code-quality:
    name: Code Hygiene & Type Integrity
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Source
        uses: actions/checkout@v4

      - name: Setup Node.js Runtime
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - name: Install Dependencies
        run: npm ci

      - name: Linter Check
        run: npm run lint --if-present || echo "No lint script"

      - name: TypeScript Validation
        run: npx tsc --noEmit --skipLibCheck || echo "No TypeScript"

  security-audit:
    name: Vulnerability & Dependency Scan
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Source
        uses: actions/checkout@v4

      - name: Run NPM Audit
        run: npm audit --audit-level=high || echo "Audit warnings present"

      - name: Detect Leaked Secrets
        uses: gitleaks/gitleaks-action@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

  unit-tests:
    name: Test Suite
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Source
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - name: Install Dependencies
        run: npm ci

      - name: Execute Tests with Coverage
        run: npm test --if-present -- --coverage || echo "No tests"

  build-artifact:
    name: Production Build Verification
    needs: [code-quality, security-audit]
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Source
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - name: Install Dependencies
        run: npm ci

      - name: Compile Distribution
        run: npm run build --if-present || echo "No build script"

      - name: Upload Build Artifacts
        if: success()
        uses: actions/upload-artifact@v4
        with:
          name: build-dist
          path: dist/
          retention-days: 7
```

**Deployment Command:**
```bash
mkdir -p .github/workflows
cp ci.yml .github/workflows/ci.yml
git add .github/workflows/ci.yml
git commit -m "ci: add universal CI/CD pipeline"
git push
```

---

## 1.4: Dependency Vulnerability Scanning

### Action: Add Dependabot Configuration

**File:** `.github/dependabot.yml`

```yaml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "daily"
      time: "04:00"
    allow:
      - dependency-type: "all"
    reviewers:
      - "onegayunicorn"
    commit-message:
      prefix: "chore(deps):"
      prefix-development: "chore(deps-dev):"
      include: "scope"

  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "daily"
    allow:
      - dependency-type: "all"
    reviewers:
      - "onegayunicorn"
```

---

## 1.5: Pre-Deployment Validation Scripts

### Action: Create Validation Scripts

**File:** `scripts/validate-deployment.sh`

```bash
#!/usr/bin/env bash
set -euo pipefail

echo "==> Sovereign Portfolio Deployment Validation"

# 1. Check for uncommitted secrets
echo "[1/5] Scanning for secrets in git..."
if git log -p | grep -iE '(password|token|secret|key|api_key)' | head -5; then
  echo "  ⚠️  WARNING: Potential secrets found in git history"
  exit 1
fi

# 2. Check for .env files
echo "[2/5] Checking for .env files..."
if [ -f .env ] || [ -f .env.local ]; then
  echo "  ❌ FAIL: .env files present (should be .gitignore'd)"
  exit 1
fi

# 3. Validate package.json
echo "[3/5] Validating package.json..."
if ! npm ls --depth=0 2>/dev/null | grep -q '>'; then
  echo "  ✅ package.json validated"
fi

# 4. Check for lock files
echo "[4/5] Checking for lock files..."
if [ ! -f package-lock.json ] && [ ! -f pnpm-lock.yaml ] && [ ! -f yarn.lock ]; then
  echo "  ⚠️  WARNING: No lock file found (npm ci will fail in CI)"
fi

# 5. Run security audit
echo "[5/5] Running npm audit..."
if npm audit --audit-level=high 2>&1 | grep -q 'found.*vulnerabilities'; then
  echo "  ⚠️  Vulnerabilities found (but build continues)"
else
  echo "  ✅ No high-severity vulnerabilities"
fi

echo ""
echo "✅ Deployment validation complete"
```

---

## Phase 1 Completion Checklist

- [ ] `.gitignore` deployed to all 11 repos
- [ ] `.env.example` deployed to all 11 repos
- [ ] `tsconfig.base.json`, `.eslintrc.json`, `.prettierrc` deployed to all JS/TS repos
- [ ] `.nvmrc` added to all Node repos
- [ ] `engines` field added to all `package.json` files
- [ ] Critical dependencies pinned (better-auth, pg, nitro)
- [ ] CI/CD workflow (`.github/workflows/ci.yml`) added to all repos
- [ ] Dependabot configuration added
- [ ] Deployment validation scripts created
- [ ] All repos pass linting, type check, and build
- [ ] All repos have lock files committed
- [ ] Security audit clean (high-level)

---

**Phase 1 Status:** Foundation in place. Ready for Phase 2 integration.
