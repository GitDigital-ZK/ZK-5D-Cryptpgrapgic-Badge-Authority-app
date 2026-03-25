===== FILE: README.md =====

ZK-5D-Cryptographic-Badge-Authority-app

Zero-Knowledge Cryptographic Badge Authority for GitHub

A decentralized, privacy-preserving credentialing system that issues verifiable badges based on GitHub contributions using zk-SNARKs, Solana, and GitDigital governance.

Overview

This application serves as a cryptographic authority that issues, verifies, and revokes badges representing developer contributions. Badges are represented as 5-dimensional cryptographic assets combining:

· Identity – Cryptographic identity binding
· Proof – Zero-knowledge proof of contribution
· Reputation – Weighted contribution scoring
· Temporal – Timestamp-constrained validity
· Spatial – Repository/organization scope

Quick Start

```bash
# Install dependencies
npm install

# Set up environment
cp .env.example .env
# Edit .env with your configuration

# Start services
docker-compose up -d

# Run development server
npm run dev
```

System Requirements

· Node.js 20+
· Rust 1.70+ (for Solana program)
· Circom 2.0+ (for ZK circuits)
· Solana CLI tools
· Docker (optional)

Core Components

1. ZK Circuits – Generate and verify zero-knowledge proofs of contributions
2. Solana Program – On-chain badge state management
3. GitHub App – Webhook listener for contribution events
4. Badge Engine – Rule-based badge issuance logic
5. Governance Layer – GitDigital-based decision making

Documentation

· Architecture Overview
· ZK Proofs
· Badge Schemas
· 5D Badge Specification
· API Reference

Security

See SECURITY.md for security policies and reporting vulnerabilities.

License

MIT License – see LICENSE

===== FILE: GOVERNANCE.md =====

Governance Model

This project uses GitDigital for decentralized governance and decision-making.

Governance Structure

· Core Team – Technical steering and security oversight
· Community Council – Elected representatives for badge schema changes
· Token Holders – $DIGITAL token holders vote on major proposals

Decision Making

Decision Type Voting Method Threshold
Badge Schema Changes GitDigital PR + Community Vote 60% approval
Security Parameters Core Team + External Audit Unanimous
Protocol Upgrades GitDigital Proposal 75% approval
Emergency Actions Multi-sig + Timelock 3/5 signatures

Badge Issuance Rules

Badges are governed by a set of rules defined in .gitdigital-badges.yml:

· Rules are versioned and can be updated via governance proposals
· Each badge type requires specific contribution patterns
· Proofs are validated on-chain before badge issuance

Dispute Resolution

1. Contested Badge – Any badge can be challenged via governance PR
2. Arbitration – Community council reviews and decides
3. Appeal – Token holders can override council decisions

Amendment Process

This governance document can be amended through:

1. Draft PR with proposed changes
2. 7-day discussion period
3. Community vote with 60% approval
4. Implementation by core team

===== FILE: BADGES.md =====

Badge Types & Criteria

Badge Categories

1. Contribution Badges

· First Contributor – First merged PR
· Regular Contributor – 10+ merged PRs in 90 days
· Core Contributor – 50+ merged PRs + code review participation
· Bug Hunter – 5+ bug fixes merged

2. Quality Badges

· Review Master – 20+ PR reviews with substantive comments
· Docs Champion – Significant documentation contributions
· Test Guardian – 10+ test contributions or 90% coverage improvement

3. Security Badges

· Security Researcher – Valid security report accepted
· Cryptography Expert – ZK circuit contributions or audits

4. Community Badges

· Community Builder – 50+ helpful comments/issues triaged
· Mentor – Guided 3+ new contributors to first PR

Badge Levels

Each badge has 4 levels:

· Bronze – Entry level (minimum criteria)
· Silver – Significant contributions (3x minimum)
· Gold – Exceptional contributions (10x minimum)
· Platinum – Elite, invitation-only

Expiration

· Temporary Badges – 90 days (e.g., seasonal contributor)
· Annual Badges – 1 year (renewable)
· Lifetime Badges – Permanent (core team, security researchers)

Revocation Conditions

· False claims in proof generation
· Malicious contributions
· Governance vote for revocation
· Account compromise

===== FILE: SECURITY.md =====

Security Policy

Supported Versions

Version Supported
1.x ✅
< 1.0 ❌

Reporting a Vulnerability

DO NOT report security vulnerabilities through public GitHub issues.

Please report via:

1. Email: security@zkbadge.io
2. PGP Key: [link to key]
3. Encrypted message with full details

You should receive acknowledgment within 24 hours and a detailed response within 72 hours.

Security Measures

· All ZK circuits audited before deployment
· Multi-sig for contract upgrades (3/5 signatures)
· Rate limiting on badge issuance endpoints
· Automatic revocation on suspicious activity
· Regular security scans (daily)
· Bug bounty program for critical vulnerabilities

Disclosure Policy

· Vulnerabilities fixed in private
· Public disclosure after 90 days or with reporter's consent
· Credits in security acknowledgments

Security Hardening

· Use environment variables for secrets (never commit)
· Rotate keys quarterly
· Monitor for anomalous badge issuance patterns
· Circuit parameters stored in secure HSM

===== FILE: CODE_OF_CONDUCT.md =====

Code of Conduct

Our Pledge

We as members, contributors, and leaders pledge to make participation in our community a harassment-free experience for everyone.

Our Standards

Examples of behavior that contributes to a positive environment:

· Using welcoming and inclusive language
· Being respectful of differing viewpoints
· Gracefully accepting constructive criticism

Examples of unacceptable behavior:

· Trolling, insulting comments, personal attacks
· Public or private harassment
· Publishing others' private information

Enforcement Responsibilities

Community leaders are responsible for clarifying standards of acceptable behavior and will take appropriate corrective action.

Scope

This Code of Conduct applies within all community spaces and when an individual is representing the community.

Enforcement

Instances of abusive behavior may be reported to the community leaders at conduct@zkbadge.io. All complaints will be reviewed and investigated promptly.

Attribution

This Code of Conduct is adapted from the Contributor Covenant, version 2.0.

===== FILE: CONTRIBUTING.md =====

Contributing to ZK-5D Cryptographic Badge Authority

Thank you for your interest in contributing!

Development Setup

```bash
# Clone the repository
git clone https://github.com/your-org/ZK-5D-Cryptographic-Badge-Authority-app.git
cd ZK-5D-Cryptographic-Badge-Authority-app

# Install dependencies
npm install

# Build ZK circuits
npm run build:circuits

# Build Solana program
cd src/zk/solana/program && cargo build-bpf

# Run tests
npm test
```

Contribution Workflow

1. Fork the repository
2. Create a branch (git checkout -b feature/amazing-feature)
3. Commit changes with conventional commits
4. Push to your fork
5. Open a Pull Request

Code Style

· TypeScript: ESLint + Prettier
· Rust: rustfmt
· Circuits: Standard Circom style

Testing Requirements

· Unit tests for all new functions
· Integration tests for new badge types
· E2E tests for critical paths
· ZK circuit tests with sample proofs

Pull Request Process

1. Update documentation for any API changes
2. Add tests for new functionality
3. Ensure all tests pass (npm test)
4. Get review from at least one maintainer
5. Squash commits before merge

Badge Development

To propose a new badge type:

1. Add schema in badgeSchemas.ts
2. Implement issuance logic in badgeEngine.ts
3. Update .gitdigital-badges.yml
4. Create governance PR for approval

Questions?

Join our Discord or open a discussion!

===== FILE: LICENSE =====
MIT License

Copyright (c) 2025 ZK-5D Cryptographic Badge Authority

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

===== FILE: .gitignore =====

Dependencies

node_modules/
target/
**/target/
.pnpm-store/

Build outputs

dist/
build/
*.wasm
*.r1cs
*.zkey
*.sym
*.wtns

Environment

.env
.env.local
.env.*.local
.env.production

Logs

logs/
.log
npm-debug.log
yarn-debug.log*
yarn-error.log*

IDE

.vscode/
.idea/
*.swp
*.swo
*~
.DS_Store

Testing

coverage/
.nyc_output/
test-results/

Solana

**/program/target/
**/program/deps/
test-ledger/

ZK proofs

proofs/*.json
!proofs/example.json
circuits/compiled/
circuits/inputs/
circuits/outputs/

Docker

docker-compose.override.yml

Temporary files

*.tmp
*.temp

===== FILE: .env.example =====

Server Configuration

PORT=3000
NODE_ENV=development
LOG_LEVEL=info

GitHub App

GITHUB_APP_ID=123456
GITHUB_PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----\n..."
GITHUB_WEBHOOK_SECRET=your_webhook_secret
GITHUB_CLIENT_ID=your_client_id
GITHUB_CLIENT_SECRET=your_client_secret

Solana

SOLANA_RPC_URL=https://api.devnet.solana.com
SOLANA_PRIVATE_KEY=your_private_key_base58
SOLANA_PROGRAM_ID=YourProgramId1111111111111111111111111111111111

ZK Circuit Paths

ZK_CIRCUIT_DIR=./src/zk/circuits/compiled
ZK_PROVING_KEY_PATH=./src/zk/circuits/keys/proving.key
ZK_VERIFICATION_KEY_PATH=./src/zk/circuits/keys/verification.key

JWT

JWT_SECRET=your_jwt_secret_key_min_32_characters
JWT_EXPIRATION=24h

Database (optional)

DATABASE_URL=postgresql://user:password@localhost:5432/zkbadge

Redis (optional)

REDIS_URL=redis://localhost:6379

Rate Limiting

RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=100

Security

ENABLE_RATE_LIMITING=true
CORS_ORIGINS=http://localhost:3000,https://app.zkbadge.io

===== FILE: .nvmrc =====
20.11.0

===== FILE: package.json =====
{
"name": "zk-5d-cryptographic-badge-authority",
"version": "1.0.0",
"description": "Zero-Knowledge Cryptographic Badge Authority for GitHub",
"main": "dist/index.js",
"scripts": {
"build": "tsc",
"build:circuits": "bash scripts/build-circuits.sh",
"dev": "nodemon --exec ts-node src/index.ts",
"start": "node dist/index.js",
"test": "jest",
"test:unit": "jest tests/unit",
"test:integration": "jest tests/integration",
"test:e2e": "jest tests/e2e",
"lint": "eslint src//*.ts",
"format": "prettier --write \"src//*.ts\"",
"circuits:compile": "circom src/zk/circuits/identity.circom --r1cs --wasm --sym -o src/zk/circuits/compiled",
"solana:build": "cd src/zk/solana/program && cargo build-bpf",
"solana:deploy": "solana program deploy src/zk/solana/program/target/deploy/zkbadge_program.so"
},
"keywords": ["zk-snarks", "solana", "github", "badges", "cryptography"],
"author": "ZK-5D Badge Authority",
"license": "MIT",
"dependencies": {
"@solana/web3.js": "^1.87.0",
"@solana/spl-token": "^0.3.8",
"axios": "^1.6.2",
"circomlibjs": "^0.1.7",
"cors": "^2.8.5",
"dotenv": "^16.3.1",
"express": "^4.18.2",
"helmet": "^7.1.0",
"jsonwebtoken": "^9.0.2",
"snarkjs": "^0.7.0",
"winston": "^3.11.0"
},
"devDependencies": {
"@types/cors": "^2.8.17",
"@types/express": "^4.17.21",
"@types/jest": "^29.5.10",
"@types/jsonwebtoken": "^9.0.5",
"@types/node": "^20.10.4",
"@typescript-eslint/eslint-plugin": "^6.13.2",
"@typescript-eslint/parser": "^6.13.2",
"eslint": "^8.55.0",
"jest": "^29.7.0",
"nodemon": "^3.0.2",
"prettier": "^3.1.0",
"ts-jest": "^29.1.1",
"ts-node": "^10.9.2",
"typescript": "^5.3.2"
}
}

===== FILE: tsconfig.json =====
{
"compilerOptions": {
"target": "ES2022",
"module": "commonjs",
"lib": ["ES2022"],
"outDir": "./dist",
"rootDir": "./src",
"strict": true,
"esModuleInterop": true,
"skipLibCheck": true,
"forceConsistentCasingInFileNames": true,
"resolveJsonModule": true,
"declaration": true,
"declarationMap": true,
"sourceMap": true,
"noUnusedLocals": true,
"noUnusedParameters": true,
"noImplicitReturns": true,
"noFallthroughCasesInSwitch": true,
"moduleResolution": "node",
"allowSyntheticDefaultImports": true
},
"include": ["src/**/*"],
"exclude": ["node_modules", "dist", "tests"]
}

===== FILE: app.manifest.json =====
{
"name": "ZK-5D Cryptographic Badge Authority",
"short_name": "ZK Badge Auth",
"description": "Decentralized badge issuance with zero-knowledge proofs",
"version": "1.0.0",
"author": "ZK-5D Badge Authority",
"repository": "https://github.com/your-org/ZK-5D-Cryptographic-Badge-Authority-app",
"license": "MIT",
"github_app": {
"id": "zk-badge-authority",
"name": "ZK Badge Authority",
"webhook_url": "https://api.zkbadge.io/webhook",
"permissions": {
"metadata": "read",
"pull_requests": "write",
"issues": "write",
"contents": "read",
"actions": "read"
},
"events": [
"pull_request",
"issues",
"push",
"release",
"workflow_run"
]
},
"solana": {
"program_id": "ZKBadge1111111111111111111111111111111111",
"network": "devnet"
}
}

===== FILE: .gitdigital-badges.yml =====
version: "1.0"
badges:

· name: "First Contributor"
  type: "contribution"
  criteria:
  min_merged_prs: 1
  timeframe_days: null
  level: "bronze"
  lifetime: "permanent"
  requires_proof: true
· name: "Regular Contributor"
  type: "contribution"
  criteria:
  min_merged_prs: 10
  timeframe_days: 90
  level: "silver"
  lifetime: "annual"
  requires_proof: true
· name: "Core Contributor"
  type: "contribution"
  criteria:
  min_merged_prs: 50
  min_reviews: 20
  timeframe_days: 365
  level: "gold"
  lifetime: "annual"
  requires_proof: true
· name: "Bug Hunter"
  type: "security"
  criteria:
  min_bug_fixes: 5
  labels: ["bug", "fix"]
  level: "bronze"
  lifetime: "permanent"
  requires_proof: true
· name: "Review Master"
  type: "quality"
  criteria:
  min_reviews: 20
  min_comments_per_review: 2
  level: "silver"
  lifetime: "annual"
  requires_proof: true
· name: "Security Researcher"
  type: "security"
  criteria:
  min_valid_reports: 1
  severity: ["high", "critical"]
  level: "gold"
  lifetime: "permanent"
  requires_proof: true

governance:
proposal_threshold: 1000
voting_period_days: 7
approval_threshold: 0.6

===== FILE: .gitdigital-governance.yml =====
version: "1.0"
council:
members:
- "github:alice"
- "github:bob"
- "github:charlie"
term_length_months: 12
election_frequency_months: 12

voting:
quorum_percentage: 0.3
approval_threshold: 0.6
veto_power:
- "core_team"
- "security_team"

proposal_types:

· name: "badge_schema"
  required_approvals: 0.6
  discussion_days: 7
  voting_days: 7
· name: "protocol_upgrade"
  required_approvals: 0.75
  discussion_days: 14
  voting_days: 14
  requires_audit: true
· name: "emergency"
  required_approvals: 0.5
  discussion_days: 0
  voting_days: 1
  requires_multisig: true

disputes:
appeal_period_days: 14
arbitration_multisig:
- "0x1234..."
- "0x5678..."
- "0x9abc..."

===== FILE: docker-compose.yml =====
version: '3.8'

services:
app:
build: .
ports:
- "3000:3000"
environment:
- NODE_ENV=production
- PORT=3000
env_file:
- .env
depends_on:
- redis
- postgres
restart: unless-stopped

redis:
image: redis:7-alpine
ports:
- "6379:6379"
volumes:
- redis-data:/data
restart: unless-stopped

postgres:
image: postgres:15-alpine
environment:
POSTGRES_DB: zkbadge
POSTGRES_USER: zkbadge
POSTGRES_PASSWORD: ${DB_PASSWORD:-changeme}
ports:
- "5432:5432"
volumes:
- postgres-data:/var/lib/postgresql/data
restart: unless-stopped

solana:
image: solanalabs/solana:latest
command: solana-test-validator
ports:
- "8899:8899"
- "8900:8900"
volumes:
- solana-data:/opt/solana/config
restart: unless-stopped

volumes:
redis-data:
postgres-data:
solana-data:

===== FILE: Dockerfile =====
FROM node:20-alpine AS builder

WORKDIR /app

Install dependencies

COPY package*.json ./
RUN npm ci

Copy source and build

COPY . .
RUN npm run build

Production stage

FROM node:20-alpine

WORKDIR /app

Install runtime dependencies

COPY package*.json ./
RUN npm ci --only=production

Copy built files

COPY --from=builder /app/dist ./dist
COPY --from=builder /app/src/zk/circuits/compiled ./src/zk/circuits/compiled

Copy configuration

COPY .env.example .env

EXPOSE 3000

USER node

CMD ["node", "dist/index.js"]

===== FILE: src/index.ts =====
import dotenv from 'dotenv';
import { createServer } from './server';
import { logger } from './utils/logger';
import { validateConfig } from './config/env';

dotenv.config();

async function main() {
try {
validateConfig();

} catch (error) {
logger.error('Failed to start server:', error);
process.exit(1);
}
}

main();

===== FILE: src/server.ts =====
import express from 'express';
import helmet from 'helmet';
import cors from 'cors';
import { errorHandler } from './api/middleware/errorHandler';
import { rateLimiter } from './api/middleware/rateLimit';
import { authMiddleware } from './api/middleware/auth';
import badgesRouter from './api/routes/badges';
import proofsRouter from './api/routes/proofs';
import governanceRouter from './api/routes/governance';
import healthRouter from './api/routes/health';
import { webhookRouter } from './github/webhookRouter';

export function createServer() {
const app = express();

// Security middleware
app.use(helmet());
app.use(cors({
origin: process.env.CORS_ORIGINS?.split(',') || '*'
}));

// Body parsing
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true }));

// Rate limiting
if (process.env.ENABLE_RATE_LIMITING === 'true') {
app.use(rateLimiter);
}

// Routes
app.use('/webhook', webhookRouter);
app.use('/api/badges', authMiddleware, badgesRouter);
app.use('/api/proofs', authMiddleware, proofsRouter);
app.use('/api/governance', authMiddleware, governanceRouter);
app.use('/health', healthRouter);

// Error handling
app.use(errorHandler);

return app;
}

===== FILE: src/config/env.ts =====
export interface Config {
port: number;
nodeEnv: string;
githubAppId: string;
githubPrivateKey: string;
githubWebhookSecret: string;
solanaRpcUrl: string;
solanaPrivateKey: string;
solanaProgramId: string;
jwtSecret: string;
jwtExpiration: string;
zkCircuitDir: string;
zkProvingKeyPath: string;
zkVerificationKeyPath: string;
rateLimitWindowMs: number;
rateLimitMaxRequests: number;
}

export function validateConfig(): Config {
const required = [
'GITHUB_APP_ID',
'GITHUB_PRIVATE_KEY',
'GITHUB_WEBHOOK_SECRET',
'SOLANA_RPC_URL',
'SOLANA_PRIVATE_KEY',
'SOLANA_PROGRAM_ID',
'JWT_SECRET'
];

for (const key of required) {
if (!process.env[key]) {
throw new Error(Missing required environment variable: ${key});
}
}

return {
port: parseInt(process.env.PORT || '3000'),
nodeEnv: process.env.NODE_ENV || 'development',
githubAppId: process.env.GITHUB_APP_ID,
githubPrivateKey: process.env.GITHUB_PRIVATE_KEY,
githubWebhookSecret: process.env.GITHUB_WEBHOOK_SECRET,
solanaRpcUrl: process.env.SOLANA_RPC_URL,
solanaPrivateKey: process.env.SOLANA_PRIVATE_KEY,
solanaProgramId: process.env.SOLANA_PROGRAM_ID,
jwtSecret: process.env.JWT_SECRET,
jwtExpiration: process.env.JWT_EXPIRATION || '24h',
zkCircuitDir: process.env.ZK_CIRCUIT_DIR || './src/zk/circuits/compiled',
zkProvingKeyPath: process.env.ZK_PROVING_KEY_PATH || './src/zk/circuits/keys/proving.key',
zkVerificationKeyPath: process.env.ZK_VERIFICATION_KEY_PATH || './src/zk/circuits/keys/verification.key',
rateLimitWindowMs: parseInt(process.env.RATE_LIMIT_WINDOW_MS || '900000'),
rateLimitMaxRequests: parseInt(process.env.RATE_LIMIT_MAX_REQUESTS || '100')
};
}

=
