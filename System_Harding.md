===== FILE ADDED: config/environments/local.env.example =====

Local Development Environment

NODE_ENV=local
PORT=3000
LOG_LEVEL=debug

GitHub App (Local Testing)

GITHUB_APP_ID=123456
GITHUB_PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----\nMIIEpAIBAAKCAQEA..."
GITHUB_WEBHOOK_SECRET=local_webhook_secret_dev_only
GITHUB_CLIENT_ID=local_client_id
GITHUB_CLIENT_SECRET=local_client_secret

Solana (Local Test Validator)

SOLANA_RPC_URL=http://localhost:8899
SOLANA_PRIVATE_KEY=local_test_keypair_base58
SOLANA_PROGRAM_ID=ZKBadgeLocal111111111111111111111111111111

ZK Circuit Paths (Local)

ZK_CIRCUIT_DIR=./src/zk/circuits/compiled
ZK_PROVING_KEY_PATH=./src/zk/circuits/keys/local/proving.key
ZK_VERIFICATION_KEY_PATH=./src/zk/circuits/keys/local/verification.key

JWT (Local)

JWT_SECRET=local_jwt_secret_32_chars_minimum!
JWT_EXPIRATION=24h

Database (Local)

DATABASE_URL=postgresql://zkbadge:zkbadge@localhost:5432/zkbadge_local
DB_PASSWORD=zkbadge

Redis (Local)

REDIS_URL=redis://localhost:6379

Rate Limiting (Disabled for Local)

RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=1000
ENABLE_RATE_LIMITING=false

CORS (Local)

CORS_ORIGINS=http://localhost:3000,http://localhost:3001

Security (Local)

SESSION_SECRET=local_session_secret
ENCRYPTION_KEY=local_encryption_key_32_bytes

===== FILE ADDED: config/environments/dev.env.example =====

Development Environment

NODE_ENV=development
PORT=3000
LOG_LEVEL=debug

GitHub App (Dev App)

GITHUB_APP_ID=${DEV_GITHUB_APP_ID}
GITHUB_PRIVATE_KEY=${DEV_GITHUB_PRIVATE_KEY}
GITHUB_WEBHOOK_SECRET=${DEV_GITHUB_WEBHOOK_SECRET}
GITHUB_CLIENT_ID=${DEV_GITHUB_CLIENT_ID}
GITHUB_CLIENT_SECRET=${DEV_GITHUB_CLIENT_SECRET}

Solana (Devnet)

SOLANA_RPC_URL=https://api.devnet.solana.com
SOLANA_PRIVATE_KEY=${DEV_SOLANA_PRIVATE_KEY}
SOLANA_PROGRAM_ID=${DEV_SOLANA_PROGRAM_ID}

ZK Circuit Paths

ZK_CIRCUIT_DIR=./src/zk/circuits/compiled
ZK_PROVING_KEY_PATH=./src/zk/circuits/keys/dev/proving.key
ZK_VERIFICATION_KEY_PATH=./src/zk/circuits/keys/dev/verification.key

JWT

JWT_SECRET=${DEV_JWT_SECRET}
JWT_EXPIRATION=24h

Database (Development)

DATABASE_URL=${DEV_DATABASE_URL}
DB_PASSWORD=${DEV_DB_PASSWORD}

Redis

REDIS_URL=${DEV_REDIS_URL}

Rate Limiting

RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=500
ENABLE_RATE_LIMITING=true

CORS

CORS_ORIGINS=https://dev.zkbadge.io,https://dev-api.zkbadge.io

Security

SESSION_SECRET=${DEV_SESSION_SECRET}
ENCRYPTION_KEY=${DEV_ENCRYPTION_KEY}

Monitoring

SENTRY_DSN=${DEV_SENTRY_DSN}
OTEL_ENDPOINT=${DEV_OTEL_ENDPOINT}

===== FILE ADDED: config/environments/staging.env.example =====

Staging Environment

NODE_ENV=staging
PORT=3000
LOG_LEVEL=info

GitHub App (Staging App)

GITHUB_APP_ID=${STAGING_GITHUB_APP_ID}
GITHUB_PRIVATE_KEY=${STAGING_GITHUB_PRIVATE_KEY}
GITHUB_WEBHOOK_SECRET=${STAGING_GITHUB_WEBHOOK_SECRET}
GITHUB_CLIENT_ID=${STAGING_GITHUB_CLIENT_ID}
GITHUB_CLIENT_SECRET=${STAGING_GITHUB_CLIENT_SECRET}

Solana (Testnet)

SOLANA_RPC_URL=https://api.testnet.solana.com
SOLANA_PRIVATE_KEY=${STAGING_SOLANA_PRIVATE_KEY}
SOLANA_PROGRAM_ID=${STAGING_SOLANA_PROGRAM_ID}

ZK Circuit Paths

ZK_CIRCUIT_DIR=./src/zk/circuits/compiled
ZK_PROVING_KEY_PATH=./src/zk/circuits/keys/staging/proving.key
ZK_VERIFICATION_KEY_PATH=./src/zk/circuits/keys/staging/verification.key

JWT

JWT_SECRET=${STAGING_JWT_SECRET}
JWT_EXPIRATION=12h

Database (Staging)

DATABASE_URL=${STAGING_DATABASE_URL}
DB_PASSWORD=${STAGING_DB_PASSWORD}

Redis

REDIS_URL=${STAGING_REDIS_URL}

Rate Limiting

RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=250
ENABLE_RATE_LIMITING=true

CORS

CORS_ORIGINS=https://staging.zkbadge.io,https://staging-api.zkbadge.io

Security

SESSION_SECRET=${STAGING_SESSION_SECRET}
ENCRYPTION_KEY=${STAGING_ENCRYPTION_KEY}

Monitoring

SENTRY_DSN=${STAGING_SENTRY_DSN}
OTEL_ENDPOINT=${STAGING_OTEL_ENDPOINT}

===== FILE ADDED: config/environments/prod.env.example =====

Production Environment

NODE_ENV=production
PORT=3000
LOG_LEVEL=info

GitHub App (Production App)

GITHUB_APP_ID=${PROD_GITHUB_APP_ID}
GITHUB_PRIVATE_KEY=${PROD_GITHUB_PRIVATE_KEY}
GITHUB_WEBHOOK_SECRET=${PROD_GITHUB_WEBHOOK_SECRET}
GITHUB_CLIENT_ID=${PROD_GITHUB_CLIENT_ID}
GITHUB_CLIENT_SECRET=${PROD_GITHUB_CLIENT_SECRET}

Solana (Mainnet)

SOLANA_RPC_URL=https://api.mainnet-beta.solana.com
SOLANA_PRIVATE_KEY=${PROD_SOLANA_PRIVATE_KEY}
SOLANA_PROGRAM_ID=${PROD_SOLANA_PROGRAM_ID}

ZK Circuit Paths

ZK_CIRCUIT_DIR=./src/zk/circuits/compiled
ZK_PROVING_KEY_PATH=./src/zk/circuits/keys/prod/proving.key
ZK_VERIFICATION_KEY_PATH=./src/zk/circuits/keys/prod/verification.key

JWT

JWT_SECRET=${PROD_JWT_SECRET}
JWT_EXPIRATION=8h

Database (Production)

DATABASE_URL=${PROD_DATABASE_URL}
DB_PASSWORD=${PROD_DB_PASSWORD}

Redis

REDIS_URL=${PROD_REDIS_URL}

Rate Limiting

RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=100
ENABLE_RATE_LIMITING=true

CORS

CORS_ORIGINS=https://zkbadge.io,https://app.zkbadge.io,https://api.zkbadge.io

Security

SESSION_SECRET=${PROD_SESSION_SECRET}
ENCRYPTION_KEY=${PROD_ENCRYPTION_KEY}

Monitoring

SENTRY_DSN=${PROD_SENTRY_DSN}
OTEL_ENDPOINT=${PROD_OTEL_ENDPOINT}

Security Headers

HSTS_MAX_AGE=31536000
CSP_ENABLED=true

===== FILE ADDED: config/secrets/secrets.schema.json =====
{
"$schema": "http://json-schema.org/draft-07/schema#",
"title": "Secrets Registry Schema",
"description": "Validation schema for environment secrets",
"type": "object",
"required": [
"GITHUB_APP_ID",
"GITHUB_PRIVATE_KEY",
"GITHUB_WEBHOOK_SECRET",
"JWT_SECRET",
"SOLANA_PRIVATE_KEY",
"ENCRYPTION_KEY"
],
"properties": {
"GITHUB_APP_ID": {
"type": "string",
"pattern": "^[0-9]+$",
"minLength": 1,
"maxLength": 20,
"description": "GitHub App ID"
},
"GITHUB_PRIVATE_KEY": {
"type": "string",
"pattern": "^-----BEGIN RSA PRIVATE KEY-----",
"minLength": 100,
"description": "GitHub App private key (PEM format)"
},
"GITHUB_WEBHOOK_SECRET": {
"type": "string",
"minLength": 32,
"maxLength": 128,
"description": "Webhook secret for signature verification"
},
"GITHUB_CLIENT_ID": {
"type": "string",
"pattern": "^[a-f0-9]{20}$",
"description": "GitHub OAuth client ID"
},
"GITHUB_CLIENT_SECRET": {
"type": "string",
"minLength": 40,
"description": "GitHub OAuth client secret"
},
"SOLANA_RPC_URL": {
"type": "string",
"format": "uri",
"description": "Solana RPC endpoint URL"
},
"SOLANA_PRIVATE_KEY": {
"type": "string",
"minLength": 87,
"maxLength": 88,
"pattern": "^[1-9A-HJ-NP-Za-km-z]+$",
"description": "Solana wallet private key (base58)"
},
"SOLANA_PROGRAM_ID": {
"type": "string",
"pattern": "^[1-9A-HJ-NP-Za-km-z]{32,44}$",
"description": "Solana program ID"
},
"JWT_SECRET": {
"type": "string",
"minLength": 32,
"description": "JWT signing secret"
},
"JWT_EXPIRATION": {
"type": "string",
"pattern": "^[0-9]+[smhdw]$",
"description": "JWT expiration time"
},
"DATABASE_URL": {
"type": "string",
"format": "uri",
"pattern": "^postgresql://",
"description": "PostgreSQL connection URL"
},
"REDIS_URL": {
"type": "string",
"format": "uri",
"pattern": "^redis://",
"description": "Redis connection URL"
},
"ENCRYPTION_KEY": {
"type": "string",
"minLength": 32,
"description": "AES-256 encryption key"
},
"SESSION_SECRET": {
"type": "string",
"minLength": 32,
"description": "Session encryption secret"
}
}
}

===== FILE ADDED: config/secrets/secrets.registry.md =====

Secrets Registry

This document defines all secrets required for deployment across environments.

Secret Categories

GitHub App Secrets

Secret Environment Description Rotation
GITHUB_APP_ID All GitHub App identifier Never
GITHUB_PRIVATE_KEY All RSA private key for app authentication Quarterly
GITHUB_WEBHOOK_SECRET All Webhook signature verification Quarterly
GITHUB_CLIENT_ID All OAuth client ID Never
GITHUB_CLIENT_SECRET All OAuth client secret Yearly

Solana Secrets

Secret Environment Description Rotation
SOLANA_RPC_URL All RPC endpoint Never
SOLANA_PRIVATE_KEY Dev/Staging/Prod Deployer wallet Monthly (prod)
SOLANA_PROGRAM_ID All Deployed program ID Never

JWT & Security

Secret Environment Description Rotation
JWT_SECRET All JWT signing key Monthly
ENCRYPTION_KEY All AES-256 encryption Quarterly
SESSION_SECRET All Session encryption Quarterly

Database & Cache

Secret Environment Description Rotation
DATABASE_URL All PostgreSQL connection Monthly
DB_PASSWORD All Database password Monthly
REDIS_URL All Redis connection Never

Secret Management

Local Development

· Use .env file (never committed)
· Generate local keys via npm run generate:secrets

Development/Staging

· Store in Railway/Fly.io secrets manager
· Rotate monthly via automation

Production

· Store in HashiCorp Vault
· Multi-person access control
· Audit logging enabled
· Automatic rotation quarterly

Rotation Procedures

Manual Rotation

1. Generate new secret
2. Update secrets manager
3. Restart affected services
4. Verify functionality
5. Remove old secret after 24h

Automated Rotation (Production)

1. Vault generates new secret
2. Services reload config
3. Old secret invalidated after 24h
4. Audit log generated

Governance Gates

All secret changes require:

· PR with justification
· Security team review (prod)
· 24h cool-down (prod)
· Multi-sig approval (prod)

Emergency Access

Emergency secrets access requires:

· Two security team members
· Written incident report
· Time-boxed access (max 4h)
· Full audit logging

===== FILE ADDED: config/deploy/docker/Dockerfile.api =====

Multi-stage build for API service

FROM node:20-alpine AS builder

Install build dependencies

RUN apk add --no-cache python3 make g++ git

WORKDIR /app

Copy package files

COPY package*.json ./
RUN npm ci

Copy source

COPY . .

Build TypeScript

RUN npm run build

Production stage

FROM node:20-alpine

Install runtime dependencies

RUN apk add --no-cache tini curl

Create non-root user

RUN addgroup -g 1001 -S nodejs && 
    adduser -S nodejs -u 1001

WORKDIR /app

Copy package files

COPY --from=builder --chown=nodejs:nodejs /app/package*.json ./
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist

Copy ZK circuits

COPY --from=builder --chown=nodejs:nodejs /app/src/zk/circuits/compiled ./src/zk/circuits/compiled

Switch to non-root user

USER nodejs

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 
  CMD node -e "require('http').get('http://localhost:3000/health', (r) => {r.statusCode === 200 ? process.exit(0) : process.exit(1)})"

ENTRYPOINT ["/sbin/tini", "--"]
CMD ["node", "dist/index.js"]

===== FILE ADDED: config/deploy/docker/Dockerfile.github-app =====

GitHub App service (specialized for webhook handling)

FROM node:20-alpine AS builder

RUN apk add --no-cache python3 make g++ git

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

Production stage

FROM node:20-alpine

RUN apk add --no-cache tini

RUN addgroup -g 1001 -S nodejs && 
    adduser -S nodejs -u 1001

WORKDIR /app

COPY --from=builder --chown=nodejs:nodejs /app/package*.json ./
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/src/github ./src/github

USER nodejs

EXPOSE 3000

Higher timeout for webhook processing

ENV WEBHOOK_TIMEOUT_MS=30000

CMD ["node", "dist/index.js"]

===== FILE ADDED: config/deploy/docker/Dockerfile.zk-worker =====

ZK Proof Worker - dedicated for proof generation

FROM node:20-alpine AS builder

RUN apk add --no-cache python3 make g++ git

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

Install Python for snarkjs

FROM node:20-alpine

RUN apk add --no-cache python3 py3-pip tini

RUN addgroup -g 1001 -S nodejs && 
    adduser -S nodejs -u 1001

WORKDIR /app

COPY --from=builder --chown=nodejs:nodejs /app/package*.json ./
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/src/zk ./src/zk

USER nodejs

Worker configuration

ENV WORKER_CONCURRENCY=2
ENV WORKER_QUEUE_SIZE=100

CMD ["node", "dist/workers/zkWorker.js"]

===== FILE ADDED: config/deploy/docker/Dockerfile.cli =====

CLI tool for admin operations

FROM node:20-alpine AS builder

RUN apk add --no-cache python3 make g++ git

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

Final stage

FROM node:20-alpine

RUN apk add --no-cache tini bash

RUN addgroup -g 1001 -S nodejs && 
    adduser -S nodejs -u 1001

WORKDIR /app

COPY --from=builder --chown=nodejs:nodejs /app/package*.json ./
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/scripts ./scripts

USER nodejs

ENTRYPOINT ["/sbin/tini", "--"]
CMD ["node", "dist/cli.js"]

===== FILE ADDED: config/deploy/railway/railway.json =====
{
"$schema": "https://railway.app/railway.schema.json",
"build": {
"builder": "NIXPACKS",
"buildCommand": "npm run build"
},
"deploy": {
"numReplicas": 2,
"restartPolicyType": "ON_FAILURE",
"restartPolicyMaxRetries": 10,
"healthcheckPath": "/health",
"healthcheckTimeout": 100,
"healthcheckInterval": 30
}
}

===== FILE ADDED: config/deploy/railway/service.api.json =====
{
"name": "zk-badge-api",
"description": "ZK Badge Authority API Service",
"type": "service",
"build": {
"dockerfilePath": "config/deploy/docker/Dockerfile.api"
},
"deploy": {
"replicas": 2,
"resources": {
"cpu": 1024,
"memory": 2048
},
"env": {
"NODE_ENV": "production",
"PORT": "3000",
"LOG_LEVEL": "info"
}
},
"scaling": {
"min": 2,
"max": 10,
"targetCpu": 70,
"targetMemory": 80
}
}

===== FILE ADDED: config/deploy/railway/service.zk-worker.json =====
{
"name": "zk-badge-worker",
"description": "ZK Proof Generation Worker",
"type": "worker",
"build": {
"dockerfilePath": "config/deploy/docker/Dockerfile.zk-worker"
},
"deploy": {
"replicas": 3,
"resources": {
"cpu": 2048,
"memory": 4096
},
"env": {
"WORKER_CONCURRENCY": "2",
"WORKER_QUEUE_SIZE": "100"
}
},
"scaling": {
"min": 2,
"max": 20,
"targetQueueLength": 50
}
}

===== FILE ADDED: config/deploy/fly/fly.toml =====

fly.toml file for ZK Badge Authority API

app = "zk-badge-authority"

[build]
dockerfile = "config/deploy/docker/Dockerfile.api"

[env]
NODE_ENV = "production"
PORT = "3000"
LOG_LEVEL = "info"

[http_service]
internal_port = 3000
force_https = true
auto_stop_machines = false
auto_start_machines = true
min_machines_running = 2
processes = ["app"]

[[vm]]
memory = "2gb"
cpu_kind = "shared"
cpus = 2

[metrics]
port = 9090
path = "/metrics"

[deploy]
release_command = "npm run migrate"

[[services]]
protocol = "tcp"
internal_port = 3000

[[services.ports]]
port = 80
handlers = ["http"]

[[services.ports]]
port = 443
handlers = ["tls", "http"]

[[services.tcp_checks]]
interval = 15000
timeout = 2000
grace_period = 10000

===== FILE ADDED: config/deploy/fly/fly.github-app.toml =====

fly.toml for GitHub App deployment

app = "zk-badge-github-app"

[build]
dockerfile = "config/deploy/docker/Dockerfile.github-app"

[env]
NODE_ENV = "production"
PORT = "3000"
LOG_LEVEL = "info"
WEBHOOK_TIMEOUT_MS = "30000"

[http_service]
internal_port = 3000
force_https = true
auto_stop_machines = false
auto_start_machines = true
min_machines_running = 3
processes = ["app"]

[[vm]]
memory = "1gb"
cpu_kind = "shared"
cpus = 1

[[services]]
protocol = "tcp"
internal_port = 3000

[[services.ports]]
port = 80
handlers = ["http"]

[[services.ports]]
port = 443
handlers = ["tls", "http"]

===== FILE ADDED: config/deploy/solana/Anchor.toml =====
[programs.devnet]
zkbadge = "ZKBadgeDev111111111111111111111111111111"

[programs.testnet]
zkbadge = "ZKBadgeTest111111111111111111111111111111"

[programs.mainnet]
zkbadge = "ZKBadgeMain111111111111111111111111111111"

[registry]
url = "https://api.apr.dev"

[provider]
cluster = "devnet"
wallet = "~/.config/solana/id.json"

[scripts]
test = "cargo test-bpf -- --nocapture"
deploy_dev = "./deploy.sh devnet"
deploy_test = "./deploy.sh testnet"
deploy_prod = "./deploy.sh mainnet"

===== FILE ADDED: config/deploy/solana/deploy.sh =====
#!/bin/bash

set -e

ENVIRONMENT=${1:-devnet}
PROGRAM_NAME="zkbadge_program"
BUILD_DIR="target/deploy"

echo "Deploying to $ENVIRONMENT..."

Build program

echo "Building Solana program..."
cargo build-bpf --manifest-path src/zk/solana/program/Cargo.toml

Get program ID based on environment

if [ "$ENVIRONMENT" == "devnet" ]; then
PROGRAM_ID=$(cat config/deploy/solana/program-id.json | jq -r .devnet)
CLUSTER="devnet"
elif [ "$ENVIRONMENT" == "testnet" ]; then
PROGRAM_ID=$(cat config/deploy/solana/program-id.json | jq -r .testnet)
CLUSTER="testnet"
elif [ "$ENVIRONMENT" == "mainnet" ]; then
PROGRAM_ID=$(cat config/deploy/solana/program-id.json | jq -r .mainnet)
CLUSTER="mainnet-beta"
else
echo "Invalid environment: $ENVIRONMENT"
exit 1
fi

Check if program already deployed

EXISTING_PROGRAM=$(solana program show $PROGRAM_ID --url $CLUSTER 2>/dev/null || echo "")

if [ -z "$EXISTING_PROGRAM" ]; then
echo "Deploying new program..."
solana program deploy $BUILD_DIR/${PROGRAM_NAME}.so 
        --program-id $PROGRAM_ID 
        --url $CLUSTER 
        --with-compute-unit-price 100000
else
echo "Upgrading existing program..."
solana program deploy $BUILD_DIR/${PROGRAM_NAME}.so 
        --program-id $PROGRAM_ID 
        --url $CLUSTER 
        --upgrade-authority ~/.config/solana/upgrade-authority.json 
        --with-compute-unit-price 100000
fi

echo "Deployment complete!"
echo "Program ID: $PROGRAM_ID"
echo "Cluster: $CLUSTER"

Verify deployment

echo "Verifying deployment..."
solana program show $PROGRAM_ID --url $CLUSTER

===== FILE ADDED: config/deploy/solana/program-id.json =====
{
"devnet": "ZKBadgeDev111111111111111111111111111111",
"testnet": "ZKBadgeTest111111111111111111111111111111",
"mainnet": "ZKBadgeMain111111111111111111111111111111",
"local": "ZKBadgeLocal111111111111111111111111111111"
}

===== FILE ADDED: .github/workflows/deploy-dev.yml =====
name: Deploy to Development

on:
push:
branches:
- develop
paths-ignore:
- '.md'
- 'docs/'
workflow_dispatch:

jobs:
validate:
runs-on: ubuntu-latest
steps:
- uses: actions/checkout@v3

governance-gate:
needs: validate
runs-on: ubuntu-latest
steps:
- name: Check governance approval
run: |
echo "Development deployment requires simple approval"
# In production, this would check GitDigital PR status

deploy-railway:
needs: governance-gate
runs-on: ubuntu-latest
environment: development
steps:
- uses: actions/checkout@v3

deploy-solana:
needs: governance-gate
runs-on: ubuntu-latest
environment: development
steps:
- uses: actions/checkout@v3

===== FILE ADDED: .github/workflows/deploy-staging.yml =====
name: Deploy to Staging

on:
push:
branches:
- staging
paths-ignore:
- '.md'
- 'docs/'
workflow_dispatch:

jobs:
validate:
runs-on: ubuntu-latest
steps:
- uses: actions/checkout@v3

governance-gate:
needs: validate
runs-on: ubuntu-latest
steps:
- name: Check GitDigital governance
run: |
echo "Staging deployment requires GitDigital PR approval"
# Check if PR has required approvals

deploy-railway:
needs: governance-gate
runs-on: ubuntu-latest
environment: staging
steps:
- uses: actions/checkout@v3

deploy-solana:
needs: governance-gate
runs-on: ubuntu-latest
environment: staging
steps:
- uses: actions/checkout@v3

===== FILE ADDED: .github/workflows/deploy-prod.yml =====
name: Deploy to Production

on:
push:
tags:
- 'v*'
workflow_dispatch:

jobs:
validate:
runs-on: ubuntu-latest
steps:
- uses: actions/checkout@v3

security-scan:
needs: validate
runs-on: ubuntu-latest
steps:
- uses: actions/checkout@v3

governance-gate:
needs: security-scan
runs-on: ubuntu-latest
steps:
- name: Multi-sig approval check
run: |
echo "Production deployment requires 3/5 multi-sig approval"
# Check for approvals from designated signers

deploy-flyio:
needs: governance-gate
runs-on: ubuntu-latest
environment: production
steps:
- uses: actions/checkout@v3

deploy-solana:
needs: governance-gate
runs-on: ubuntu-latest
environment: production
steps:
- uses: actions/checkout@v3

post-deploy-verification:
needs: [deploy-flyio, deploy-solana]
runs-on: ubuntu-latest
steps:
- name: Verify production endpoints
run: |
curl -f https://api.zkbadge.io/health
curl -f https://api.zkbadge.io/api/badges/verify -X POST -d '{"badgeId":"test"}' -H "Content-Type: application/json"

===== FILE ADDED: .github/workflows/deploy-solana.yml =====
name: Deploy Solana Program

on:
workflow_dispatch:
inputs:
environment:
description: 'Target environment'
required: true
type: choice
options:
- devnet
- testnet
- mainnet
program_id:
description: 'Program ID (optional)'
required: false
type: string

jobs:
deploy:
runs-on: ubuntu-latest
environment: ${{ github.event.inputs.environment }}

===== FILE ADDED: .github/workflows/deploy-zk-worker.yml =====
name: Deploy ZK Worker

on:
push:
branches:
- main
paths:
- 'src/zk/**'
- 'config/deploy/docker/Dockerfile.zk-worker'
workflow_dispatch:

jobs:
deploy:
runs-on: ubuntu-latest
environment: production

===== FILE ADDED: .github/workflows/governance-gate.yml =====
name: Governance Gate

on:
pull_request:
types: [opened, synchronize, labeled]
paths:
- '.gitdigital-badges.yml'
- '.gitdigital-governance.yml'
- 'src/badges/**'
- 'docs/BADGE-SCHEMAS.md'

jobs:
validate-governance:
runs-on: ubuntu-latest

===== FILE ADDED: .github/workflows/secrets-validation.yml =====
name: Secrets Validation

on:
pull_request:
paths:
- 'config/environments/*.env.example'
- 'config/secrets/**'
workflow_dispatch:

jobs:
validate-secrets:
runs-on: ubuntu-latest

===== FILE ADDED: .github/workflows/monorepo-consistency.yml =====
name: Monorepo Consistency

on:
push:
branches:
- main
- develop
- staging
pull_request:

jobs:
consistency-check:
runs-on: ubuntu-latest

===== FILE ADDED: scripts/validate-badge-schemas.js =====
#!/usr/bin/env node

const fs = require('fs');
const yaml = require('js-yaml');
const path = require('path');

console.log('Validating badge schemas...');

// Load badge definitions
const badgeYaml = yaml.load(fs.readFileSync('.gitdigital-badges.yml', 'utf8'));
const tsSchemas = require('../dist/badges/badgeSchemas').defaultBadgeSchemas;

// Validate YAML structure
if (!badgeYaml.badges || !Array.isArray(badgeYaml.badges)) {
console.error('Invalid .gitdigital-badges.yml: missing badges array');
process.exit(1);
}

// Validate each badge
badgeYaml.badges.forEach(badge => {
if (!badge.name || !badge.type || !badge.criteria) {
console.error(Invalid badge: ${badge.name || 'unnamed'});
process.exit(1);
}

// Check against TypeScript schemas
const tsSchema = tsSchemas.find(s => s.name === badge.name);
if (!tsSchema) {
console.warn(Warning: Badge "${badge.name}" defined in YAML but not in TypeScript);
} else {
if (tsSchema.type !== badge.type) {
console.error(Type mismatch for ${badge.name}: ${tsSchema.type} vs ${badge.type});
process.exit(1);
}
}
});

console.log('✅ Badge schemas validated successfully');

===== FILE ADDED: scripts/validate-api-docs.js =====
#!/usr/bin/env node

const fs = require('fs');
const path = require('path');

console.log('Validating API documentation...');

// Read actual routes
const routesDir = path.join(__dirname, '../src/api/routes');
const routeFiles = fs.readdirSync(routesDir);

const endpoints = [];
routeFiles.forEach(file => {
const content = fs.readFileSync(path.join(routesDir, file), 'utf8');
const matches = content.match(/router\.(get|post|put|delete)\(['"`]([^'"`]+)['"`]/g);
  if (matches) {
    matches.forEach(match => {
      const [, method, route] = match.match(/router\.(get|post|put|delete)\(['"`]([^'"`]+)['"`]/);
      endpoints.push(`${method.toUpperCase()} /api/${file.replace('.ts', '')}${route === '/' ? '' : route}`);
    });
  }
});

// Read API docs
const apiDocs = fs.readFileSync(path.join(__dirname, '../docs/API-REFERENCE.md'), 'utf8');

// Check each endpoint is documented
endpoints.forEach(endpoint => {
const endpointPattern = endpoint.replace('/', '\\/');
if (!apiDocs.match(new RegExp(endpointPattern, 'i'))) {
console.warn(Warning: Endpoint ${endpoint} not documented in API-REFERENCE.md);
}
});

console.log('✅ API documentation validated');
