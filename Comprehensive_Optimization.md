I'll perform a comprehensive optimization and hardening pass on the codebase. Here are the critical updates needed for production readiness.

===== FILE UPDATED: src/auth/jwt.ts =====
import jwt from 'jsonwebtoken';
import { SecretManager } from '../config/secrets';

export interface TokenPayload {
userId: string;
installationId?: number;
permissions: string[];
exp?: number;
iat?: number;
}

export class JWTService {
private secret: string;
private expiresIn: string;
private issuer: string;
private audience: string;

constructor() {
this.secret = SecretManager.getInstance().getSecret('JWT_SECRET');
this.expiresIn = process.env.JWT_EXPIRATION || '24h';
this.issuer = 'zk-badge-authority';
this.audience = 'zk-badge-api';
}

generateToken(payload: Omit<TokenPayload, 'exp' | 'iat'>): string {
return jwt.sign(
payload, 
this.secret, 
{ 
expiresIn: this.expiresIn,
issuer: this.issuer,
audience: this.audience,
algorithm: 'HS256'
}
);
}

verifyToken(token: string): TokenPayload {
try {
const decoded = jwt.verify(token, this.secret, {
issuer: this.issuer,
audience: this.audience,
algorithms: ['HS256']
}) as TokenPayload;
return decoded;
} catch (error) {
if (error instanceof jwt.TokenExpiredError) {
throw new Error('Token expired');
}
if (error instanceof jwt.JsonWebTokenError) {
throw new Error('Invalid token');
}
throw new Error('Token verification failed');
}
}

decodeToken(token: string): TokenPayload | null {
try {
return jwt.decode(token) as TokenPayload;
} catch {
return null;
}
}

refreshToken(token: string): string {
const payload = this.verifyToken(token);
const { exp, iat, ...rest } = payload;
return this.generateToken(rest);
}

async validateTokenStrength(token: string): Promise<boolean> {
try {
const decoded = this.decodeToken(token);
if (!decoded) return false;

}
}

===== FILE UPDATED: src/api/middleware/auth.ts =====
import { Request, Response, NextFunction } from 'express';
import { JWTService, TokenPayload } from '../../auth/jwt';
import { PermissionChecker, Permission } from '../../auth/permissions';

declare global {
namespace Express {
interface Request {
user?: TokenPayload;
}
}
}

export const authMiddleware = async (
req: Request,
res: Response,
next: NextFunction
): Promise<void> => {
try {
const authHeader = req.headers.authorization;

} catch (error) {
const message = error instanceof Error ? error.message : 'Invalid or expired token';
res.status(401).json({ error: message });
}
};

export const requirePermission = (permission: Permission) => {
return (req: Request, res: Response, next: NextFunction): void => {
if (!req.user) {
res.status(401).json({ error: 'Authentication required' });
return;
}

};
};

export const optionalAuthMiddleware = async (
req: Request,
res: Response,
next: NextFunction
): Promise<void> => {
try {
const authHeader = req.headers.authorization;

} catch {
// Silently fail, user remains unauthenticated
next();
}
};

===== FILE UPDATED: src/api/middleware/rateLimit.ts =====
import { Request, Response, NextFunction } from 'express';
import rateLimit from 'express-rate-limit';
import RedisStore from 'rate-limit-redis';
import Redis from 'ioredis';
import { logger } from '../../utils/logger';

let redisClient: Redis | null = null;

// Initialize Redis if configured
if (process.env.REDIS_URL) {
try {
redisClient = new Redis(process.env.REDIS_URL);
logger.info('Redis connected for rate limiting');
} catch (error) {
logger.warn('Redis connection failed, using memory store for rate limiting');
}
}

const store = redisClient 
? new RedisStore({
sendCommand: (...args: string[]) => redisClient!.call(...args),
})
: undefined;

export const rateLimiter = rateLimit({
windowMs: parseInt(process.env.RATE_LIMIT_WINDOW_MS || '900000'),
max: parseInt(process.env.RATE_LIMIT_MAX_REQUESTS || '100'),
message: { error: 'Too many requests, please try again later.' },
standardHeaders: true,
legacyHeaders: false,
skip: (req: Request) => {
// Skip rate limiting for health checks
return req.path === '/health';
},
keyGenerator: (req: Request) => {
// Use user ID if authenticated, otherwise IP
const userId = (req as any).user?.userId;
if (userId) return user:${userId};
return req.ip || req.socket.remoteAddress || 'unknown';
},
handler: (req: Request, res: Response) => {
logger.warn(Rate limit exceeded for ${(req as any).user?.userId || req.ip});
res.status(429).json({ 
error: 'Rate limit exceeded',
retryAfter: Math.ceil(parseInt(process.env.RATE_LIMIT_WINDOW_MS || '900000') / 1000)
});
}
});

// Stricter rate limit for sensitive endpoints
export const strictRateLimiter = rateLimit({
windowMs: 60 * 60 * 1000, // 1 hour
max: 10, // 10 requests per hour
message: { error: 'Too many requests to this endpoint' },
standardHeaders: true,
legacyHeaders: false,
keyGenerator: (req: Request) => {
return (req as any).user?.userId || req.ip || 'unknown';
}
});

===== FILE UPDATED: src/zk/solana/program/instructions.rs =====
use solana_program::{
account_info::{AccountInfo, next_account_info},
entrypoint::ProgramResult,
pubkey::Pubkey,
msg,
program_error::ProgramError,
program_pack::IsInitialized,
};
use borsh::BorshSerialize;
use crate::state::*;
use crate::errors::BadgeError;

pub enum Instruction {
IssueBadge { badge_data: BadgeData },
RevokeBadge { reason: String },
VerifyBadge { badge_id: String },
}

impl Instruction {
pub fn unpack(input: &[u8]) -> Result<Self, ProgramError> {
let (tag, rest) = input.split_first().ok_or(ProgramError::InvalidInstructionData)?;

}

pub fn issue_badge(
program_id: &Pubkey,
accounts: &[AccountInfo],
badge_data: BadgeData,
) -> ProgramResult {
let account_info_iter = &mut accounts.iter();
let badge_account = next_account_info(account_info_iter)?;
let authority = next_account_info(account_info_iter)?;
let system_program = next_account_info(account_info_iter)?;

}

pub fn revoke_badge(
program_id: &Pubkey,
accounts: &[AccountInfo],
reason: String,
) -> ProgramResult {
let account_info_iter = &mut accounts.iter();
let badge_account = next_account_info(account_info_iter)?;
let authority = next_account_info(account_info_iter)?;

}

pub fn verify_badge(
program_id: &Pubkey,
accounts: &[AccountInfo],
badge_id: String,
) -> ProgramResult {
let account_info_iter = &mut accounts.iter();
let badge_account = next_account_info(account_info_iter)?;

}

===== FILE UPDATED: src/zk/solana/program/state.rs =====
use borsh::{BorshDeserialize, BorshSerialize};
use solana_program::pubkey::Pubkey;
use solana_program::program_pack::IsInitialized;

#[derive(BorshSerialize, BorshDeserialize, Debug, Clone, PartialEq)]
pub struct BadgeData {
pub id: String,
pub badge_type: String,
pub level: String,
pub issued_to: String,
pub proof_hash: String,
pub expires_at: Option<i64>,
pub metadata: Vec<u8>,
}

#[derive(BorshSerialize, BorshDeserialize, Debug)]
pub struct BadgeAccount {
pub badge: Option<BadgeData>,
pub authority: Pubkey,
pub issued_at: i64,
pub revoked: bool,
pub revoked_at: Option<i64>,
pub revocation_reason: Option<String>,
pub is_initialized_flag: bool,
}

impl BadgeAccount {
pub const LEN: usize = 2048; // Increased size for safety

}

impl IsInitialized for BadgeAccount {
fn is_initialized(&self) -> bool {
self.is_initialized_flag
}
}

#[derive(BorshSerialize, BorshDeserialize, Debug)]
pub struct GovernanceAccount {
pub proposals: Vec<Proposal>,
pub council_members: Vec<Pubkey>,
pub voting_period: i64,
pub is_initialized: bool,
}

#[derive(BorshSerialize, BorshDeserialize, Debug)]
pub struct Proposal {
pub id: u64,
pub proposer: Pubkey,
pub description: String,
pub votes_for: u64,
pub votes_against: u64,
pub start_time: i64,
pub end_time: i64,
pub executed: bool,
}

impl GovernanceAccount {
pub const LEN: usize = 4096;
}

===== FILE UPDATED: src/zk/solana/program/errors.rs =====
use solana_program::program_error::ProgramError;
use thiserror::Error;

#[derive(Debug, Error, Clone, Copy, PartialEq)]
pub enum BadgeError {
#[error("Unauthorized")]
Unauthorized,

}

impl From<BadgeError> for ProgramError {
fn from(e: BadgeError) -> Self {
ProgramError::Custom(e as u32)
}
}

===== FILE UPDATED: package.json =====
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
"test": "jest --coverage",
"test:unit": "jest tests/unit --coverage",
"test:integration": "jest tests/integration --coverage",
"test:e2e": "jest tests/e2e --coverage",
"lint": "eslint src//*.ts",
"format": "prettier --write \"src//*.ts\"",
"circuits:compile": "circom src/zk/circuits/identity.circom --r1cs --wasm --sym -o src/zk/circuits/compiled && circom src/zk/circuits/badgeProof.circom --r1cs --wasm --sym -o src/zk/circuits/compiled && circom src/zk/circuits/revocation.circom --r1cs --wasm --sym -o src/zk/circuits/compiled",
"circuits:verify": "snarkjs r1cs info src/zk/circuits/compiled/identity.r1cs",
"solana:build": "cd src/zk/solana/program && cargo build-bpf",
"solana:test": "cd src/zk/solana/program && cargo test-bpf",
"solana:deploy": "solana program deploy src/zk/solana/program/target/deploy/zkbadge_program.so",
"badge:issue": "ts-node scripts/issue-badges.ts",
"docker:build": "docker build -t zk-badge-authority .",
"docker:up": "docker-compose up -d",
"docker:down": "docker-compose down"
},
"keywords": ["zk-snarks", "solana", "github", "badges", "cryptography"],
"author": "ZK-5D Badge Authority",
"license": "MIT",
"dependencies": {
"@octokit/auth-app": "^6.0.0",
"@octokit/rest": "^20.0.0",
"@solana/web3.js": "^1.87.0",
"@solana/spl-token": "^0.3.8",
"axios": "^1.6.2",
"borsh": "^0.10.0",
"circomlibjs": "^0.1.7",
"cors": "^2.8.5",
"dotenv": "^16.3.1",
"express": "^4.18.2",
"express-rate-limit": "^7.0.0",
"helmet": "^7.1.0",
"ioredis": "^5.3.2",
"jsonwebtoken": "^9.0.2",
"rate-limit-redis": "^4.0.0",
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
},
"engines": {
"node": ">=20.0.0",
"npm": ">=10.0.0"
}
}

===== FILE UPDATED: tsconfig.json =====
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
"removeComments": false,
"noUnusedLocals": true,
"noUnusedParameters": true,
"noImplicitReturns": true,
"noFallthroughCasesInSwitch": true,
"moduleResolution": "node",
"allowSyntheticDefaultImports": true,
"incremental": true,
"tsBuildInfoFile": ".tsbuildinfo"
},
"include": ["src//*"],
"exclude": ["node_modules", "dist", "tests", "/*.test.ts"]
}

===== FILE UPDATED: Dockerfile =====
FROM node:20-alpine AS builder

Install build dependencies

RUN apk add --no-cache python3 make g++ git

WORKDIR /app

Copy package files

COPY package*.json ./
RUN npm ci --only=production

Copy source and build

COPY . .
RUN npm run build

Production stage

FROM node:20-alpine

Install runtime dependencies for ZK circuits

RUN apk add --no-cache python3

Create non-root user

RUN addgroup -g 1001 -S nodejs && 
    adduser -S nodejs -u 1001

WORKDIR /app

Copy package files

COPY --from=builder --chown=nodejs:nodejs /app/package*.json ./
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/src/zk/circuits/compiled ./src/zk/circuits/compiled

Copy configuration

COPY --chown=nodejs:nodejs .env.example .env

Switch to non-root user

USER nodejs

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 
  CMD node -e "require('http').get('http://localhost:3000/health', (r) => {r.statusCode === 200 ? process.exit(0) : process.exit(1)})"

CMD ["node", "dist/index.js"]

===== FILE UPDATED: .env.example =====

Server Configuration

PORT=3000
NODE_ENV=production
LOG_LEVEL=info

GitHub App

GITHUB_APP_ID=
GITHUB_PRIVATE_KEY=
GITHUB_WEBHOOK_SECRET=
GITHUB_CLIENT_ID=
GITHUB_CLIENT_SECRET=

Solana

SOLANA_RPC_URL=https://api.mainnet-beta.solana.com
SOLANA_PRIVATE_KEY=
SOLANA_PROGRAM_ID=

ZK Circuit Paths

ZK_CIRCUIT_DIR=./src/zk/circuits/compiled
ZK_PROVING_KEY_PATH=./src/zk/circuits/keys/proving.key
ZK_VERIFICATION_KEY_PATH=./src/zk/circuits/keys/verification.key

JWT

JWT_SECRET=
JWT_EXPIRATION=24h

Database (PostgreSQL)

DATABASE_URL=postgresql://user:password@postgres:5432/zkbadge
DB_PASSWORD=

Redis

REDIS_URL=redis://redis:6379

Rate Limiting

RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=100
ENABLE_RATE_LIMITING=true

CORS

CORS_ORIGINS=https://app.zkbadge.io,https://admin.zkbadge.io

Security

SESSION_SECRET=
ENCRYPTION_KEY=

===== FILE UPDATED: src/server.ts =====
import express from 'express';
import helmet from 'helmet';
import cors from 'cors';
import { errorHandler } from './api/middleware/errorHandler';
import { rateLimiter, strictRateLimiter } from './api/middleware/rateLimit';
import { authMiddleware } from './api/middleware/auth';
import badgesRouter from './api/routes/badges';
import proofsRouter from './api/routes/proofs';
import governanceRouter from './api/routes/governance';
import healthRouter from './api/routes/health';
import { webhookRouter } from './github/webhookRouter';

export function createServer() {
const app = express();

// Security middleware - order matters
app.use(helmet({
contentSecurityPolicy: {
directives: {
defaultSrc: ["'self'"],
styleSrc: ["'self'", "'unsafe-inline'"],
scriptSrc: ["'self'"],
imgSrc: ["'self'", "data:", "https:"],
},
},
hsts: {
maxAge: 31536000,
includeSubDomains: true,
preload: true
}
}));

app.use(cors({
origin: process.env.CORS_ORIGINS?.split(',') || [],
credentials: true,
optionsSuccessStatus: 200
}));

// Body parsing with size limits
app.use(express.json({ limit: '1mb' }));
app.use(express.urlencoded({ extended: true, limit: '1mb' }));

// Rate limiting - apply to all routes
if (process.env.ENABLE_RATE_LIMITING === 'true') {
app.use(rateLimiter);
}

// Routes
app.use('/webhook', webhookRouter);
app.use('/api/badges', authMiddleware, badgesRouter);
app.use('/api/proofs', authMiddleware, proofsRouter);
app.use('/api/governance', authMiddleware, strictRateLimiter, governanceRouter);
app.use('/health', healthRouter);

// Error handling - must be last
app.use(errorHandler);

return app;
}

===== FILE UPDATED: src/utils/logger.ts =====
import winston from 'winston';
import path from 'path';
import fs from 'fs';

// Ensure log directory exists
const logDir = 'logs';
if (!fs.existsSync(logDir)) {
fs.mkdirSync(logDir, { recursive: true });
}

const levels = {
error: 0,
warn: 1,
info: 2,
http: 3,
debug: 4
};

const colors = {
error: 'red',
warn: 'yellow',
info: 'green',
http: 'magenta',
debug: 'blue'
};

winston.addColors(colors);

const format = winston.format.combine(
winston.format.timestamp({ format: 'YYYY-MM-DD HH:mm:ss:ms' }),
winston.format.colorize({ all: true }),
winston.format.printf(
(info) => ${info.timestamp} [${info.level}]: ${info.message} ${
      Object.keys(info.metadata || {}).length ? JSON.stringify(info.metadata) : ''
    }
)
);

const transports = [
new winston.transports.Console({
level: process.env.LOG_LEVEL || 'info',
format
}),
new winston.transports.File({
filename: path.join(logDir, 'error.log'),
level: 'error',
maxsize: 5242880, // 5MB
maxFiles: 5,
format: winston.format.uncolorize()
}),
new winston.transports.File({
filename: path.join(logDir, 'combined.log'),
maxsize: 5242880, // 5MB
maxFiles: 5,
format: winston.format.uncolorize()
})
];

export const logger = winston.createLogger({
level: process.env.LOG_LEVEL || 'info',
levels,
format: winston.format.combine(
winston.format.metadata(),
winston.format.timestamp()
),
transports,
exitOnError: false
});

export const httpLogger = {
log: (message: string, meta?: any) => {
logger.http(message, meta);
}
};

===== FILE ADDED: src/api/middleware/validation.ts =====
import { Request, Response, NextFunction } from 'express';
import Joi from 'joi';

export const validateBody = (schema: Joi.ObjectSchema) => {
return (req: Request, res: Response, next: NextFunction): void => {
const { error, value } = schema.validate(req.body, {
abortEarly: false,
stripUnknown: true
});

};
};

export const validateParams = (schema: Joi.ObjectSchema) => {
return (req: Request, res: Response, next: NextFunction): void => {
const { error, value } = schema.validate(req.params, {
abortEarly: false
});

};
};

// Validation schemas
export const badgeSchemas = {
issueBadge: Joi.object({
userId: Joi.string().required().pattern(/^[a-z0-9-]+$/),
badgeType: Joi.string().required(),
contributionData: Joi.object({
mergedPRs: Joi.number().integer().min(0),
reviews: Joi.number().integer().min(0),
comments: Joi.number().integer().min(0),
bugFixes: Joi.number().integer().min(0),
repository: Joi.string().pattern(/^[a-z0-9-]+\/[a-z0-9-]+$/)
})
}),

revokeBadge: Joi.object({
badgeId: Joi.string().required(),
reason: Joi.string().max(500)
}),

verifyBadge: Joi.object({
badgeId: Joi.string().required()
}),

generateProof: Joi.object({
badgeId: Joi.string().required(),
userId: Joi.string().required(),
contributions: Joi.object({
mergedPRs: Joi.number().integer().min(0).required(),
reviews: Joi.number().integer().min(0),
comments: Joi.number().integer().min(0),
bugFixes: Joi.number().integer().min(0),
securityReports: Joi.number().integer().min(0)
}).required()
}),

createProposal: Joi.object({
title: Joi.string().min(5).max(200).required(),
description: Joi.string().min(20).max(5000).required(),
options: Joi.array().items(Joi.string()).min(2).required()
}),

castVote: Joi.object({
vote: Joi.string().required()
})
};

===== FILE UPDATED: src/api/controllers/badgeController.ts =====
import { Request, Response } from 'express';
import { BadgeVerifier } from '../../badges/badgeVerifier';
import { BadgeIssuer } from '../../badges/badgeIssuer';
import { BadgeEngine } from '../../badges/badgeEngine';
import { defaultBadgeSchemas } from '../../badges/badgeSchemas';
import { SolanaClient } from '../../zk/solana/client/solanaClient';
import { logger } from '../../utils/logger';

export class BadgeController {
private verifier: BadgeVerifier;
private issuer: BadgeIssuer;
private engine: BadgeEngine;

constructor() {
const solanaClient = new SolanaClient(
process.env.SOLANA_RPC_URL!,
process.env.SOLANA_PRIVATE_KEY!,
process.env.SOLANA_PROGRAM_ID!
);
this.engine = new BadgeEngine(defaultBadgeSchemas);
this.issuer = new BadgeIssuer(this.engine, solanaClient);
this.verifier = new BadgeVerifier(solanaClient);
}

getBadge = async (req: Request, res: Response): Promise<void> => {
try {
const { badgeId } = req.params;

};

getUserBadges = async (req: Request, res: Response): Promise<void> => {
try {
const { userId } = req.params;

};

issueBadge = async (req: Request, res: Response): Promise<void> => {
try {
const { userId, badgeType, contributionData } = req.body;

};

revokeBadge = async (req: Request, res: Response): Promise<void> => {
try {
const { badgeId, reason } = req.body;

};

verifyBadge = async (req: Request, res: Response): Promise<void> => {
try {
const { badgeId } = req.body;

};
}

===== FILE UPDATED: src/api/routes/badges.ts =====
import { Router } from 'express';
import { BadgeController } from '../controllers/badgeController';
import { validateBody, validateParams, badgeSchemas } from '../middleware/validation';
import { requirePermission } from '../middleware/auth';
import { permissions } from '../../auth/permissions';

const router = Router();
const badgeController = new BadgeController();

router.get('/:badgeId', 
validateParams(badgeSchemas.verifyBadge),
badgeController.getBadge
);

router.get('/user/:userId', 
validateParams(badgeSchemas.verifyBadge),
badgeController.getUserBadges
);

router.post('/issue', 
requirePermission(permissions.BADGE_ISSUE),
validateBody(badgeSchemas.issueBadge),
badgeController.issueBadge
);

router.post('/revoke', 
requirePermission(permissions.BADGE_REVOKE),
validateBody(badgeSchemas.revokeBadge),
badgeController.revokeBadge
);

router.post('/verify', 
validateBody(badgeSchemas.verifyBadge),
badgeController.verifyBadge
);

export default router;

===== FILE UPDATED: docs/API-REFERENCE.md =====

API Reference

Authentication

All API endpoints except /health and /webhook require Bearer token authentication:

```
Authorization: Bearer <JWT_TOKEN>
```

JWT tokens:

· Issued with HS256 algorithm
· Valid for 24 hours
· Include issuer (zk-badge-authority) and audience (zk-badge-api)
· Can be refreshed before expiration

Badge Endpoints

Get Badge

```
GET /api/badges/:badgeId
```

Parameters:

· badgeId (path): Badge identifier

Response (200 OK):

```json
{
  "id": "First-Contributor-github:user-1234567890",
  "type": "contribution",
  "level": "bronze",
  "issuedTo": "github:user",
  "issuedAt": 1234567890,
  "expiresAt": null,
  "repository": "owner/repo",
  "proofHash": "abc123...",
  "metadata": {
    "contributions": {
      "mergedPRs": 1,
      "reviews": 0,
      "comments": 0
    },
    "schema": "First Contributor"
  },
  "identity": "did:github:github:user",
  "proof": "abc123...",
  "reputation": 10,
  "temporal": {
    "issued": 1234567890,
    "duration": 0
  },
  "spatial": {
    "scope": "github",
    "contexts": ["owner/repo"]
  }
}
```

Error Responses:

· 400 - Invalid badge ID
· 404 - Badge not found
· 500 - Internal server error

Get User Badges

```
GET /api/badges/user/:userId
```

Parameters:

· userId (path): GitHub username

Response (200 OK):

```json
{
  "badges": [...]
}
```

Issue Badge (Admin/Issuer only)

```
POST /api/badges/issue
```

Headers:

· Authorization: Bearer <JWT_TOKEN>

Request Body:

```json
{
  "userId": "github:username",
  "badgeType": "First Contributor",
  "contributionData": {
    "mergedPRs": 1,
    "repository": "owner/repo"
  }
}
```

Response (200 OK):

```json
{
  "success": true,
  "badges": [...]
}
```

Error Responses:

· 400 - Missing or invalid fields
· 401 - No token provided
· 403 - Insufficient permissions
· 500 - Internal server error

Revoke Badge (Admin/Issuer only)

```
POST /api/badges/revoke
```

Headers:

· Authorization: Bearer <JWT_TOKEN>

Request Body:

```json
{
  "badgeId": "First-Contributor-github:user-1234567890",
  "reason": "Fraudulent activity detected"
}
```

Response (200 OK):

```json
{
  "success": true
}
```

Verify Badge

```
POST /api/badges/verify
```

Request Body:

```json
{
  "badgeId": "First-Contributor-github:user-1234567890"
}
```

Response (200 OK):

```json
{
  "valid": true,
  "badge": {...}
}
```

Proof Endpoints

Generate Proof

```
POST /api/proofs/generate
```

Request Body:

```json
{
  "badgeId": "badge_123",
  "userId": "github:user",
  "contributions": {
    "mergedPRs": 5,
    "reviews": 10,
    "comments": 20,
    "bugFixes": 2,
    "securityReports": 0
  }
}
```

Response (200 OK):

```json
{
  "proof": {...},
  "publicSignals": [...],
  "hash": "abc123..."
}
```

Verify Proof

```
POST /api/proofs/verify
```

Request Body:

```json
{
  "proofHash": "abc123...",
  "publicSignals": [...]
}
```

Response (200 OK):

```json
{
  "valid": true
}
```

Get Proof Status

```
GET /api/proofs/:proofHash
```

Parameters:

· proofHash (path): Proof hash

Response (200 OK):

```json
{
  "proofHash": "abc123...",
  "status": "verified"
}
```

Governance Endpoints

All governance endpoints require governance role.

Create Proposal

```
POST /api/governance/proposals
```

Request Body:

```json
{
  "title": "Add New Badge: Documentation Hero",
  "description": "Proposal to add a badge for documentation contributors...",
  "options": ["Approve", "Reject"]
}
```

Response (200 OK):

```json
{
  "proposalId": "prop_123",
  "status": "created"
}
```

List Proposals

```
GET /api/governance/proposals
```

Response (200 OK):

```json
{
  "proposals": [
    {
      "id": "prop_123",
      "title": "Add New Badge",
      "status": "active",
      "votesFor": 150,
      "votesAgainst": 20
    }
  ]
}
```

Get Proposal

```
GET /api/governance/proposals/:proposalId
```

Response (200 OK):

```json
{
  "id": "prop_123",
  "title": "Add New Badge",
  "description": "...",
  "status": "active",
  "startTime": 1234567890,
  "endTime": 1235172690,
  "votesFor": 150,
  "votesAgainst": 20
}
```

Cast Vote

```
POST /api/governance/proposals/:proposalId/vote
```

Request Body:

```json
{
  "vote": "Approve"
}
```

Response (200 OK):

```json
{
  "success": true
}
```

Execute Proposal

```
POST /api/governance/proposals/:proposalId/execute
```

Response (200 OK):

```json
{
  "success": true
}
```

Health Check

```
GET /health
```

Response (200 OK):

```json
{
  "status": "healthy",
  "timestamp": 1234567890,
  "version": "1.0.0",
  "services": {
    "solana": "connected",
    "github": "operational",
    "zk": "ready"
  }
}
```

Webhook Endpoint

GitHub Webhook

```
POST /webhook
```

This endpoint receives GitHub webhook events. No authentication required; signature verification is performed using the webhook secret.

Supported events:

· pull_request (closed + merged)
· issues (opened)
· push
· release (published)
· workflow_run (completed)
· installation (created/deleted)

Response (200 OK):

```json
{
  "received": true
}
```

Rate Limits

· IP-based: 100 requests per 15 minutes
· Authenticated users: 1000 requests per hour
· Governance endpoints: 10 requests per hour

Rate limit headers included in responses:

· X-RateLimit-Limit
· X-RateLimit-Remaining
· X-RateLimit-Reset

Error Codes

Code Description
400 Bad request - missing or invalid parameters
401 Unauthorized - missing or invalid JWT
403 Forbidden - insufficient permissions
404 Not found - badge or resource doesn't exist
429 Rate limit exceeded - too many requests
500 Internal server error - something went wrong

Error Response Format

```json
{
  "error": "Error description",
  "details": ["Additional validation details (if applicable)"]
}
```
