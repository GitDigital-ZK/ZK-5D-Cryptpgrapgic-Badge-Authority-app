I'll audit and refine the entire codebase. Here are the files that need changes to fix issues, improve consistency, and ensure proper wiring.

===== FILE UPDATED: src/config/appConfig.ts =====
export const appConfig = {
name: 'ZK-5D Cryptographic Badge Authority',
version: '1.0.0',

badge: {
maxPerUser: 100,
issuanceCooldownHours: 24,
revocationBufferDays: 30
},

zk: {
circuitSize: Math.pow(2, 20),
proofType: 'groth16' as const,
curve: 'bn128' as const
},

github: {
apiVersion: '2022-11-28',
userAgent: 'ZK-Badge-Authority/1.0'
},

solana: {
commitment: 'confirmed' as const,
maxRetries: 3,
confirmTransactionTimeout: 60000
},

governance: {
minProposalStake: 1000,
votingPeriodBlocks: 10080, // ~7 days
executionDelayBlocks: 4320 // ~3 days
}
};

===== FILE UPDATED: src/auth/githubAppAuth.ts =====
import { createAppAuth } from '@octokit/auth-app';
import { Octokit } from '@octokit/rest';
import { SecretManager } from '../config/secrets';
import crypto from 'crypto';

export class GitHubAppAuth {
private appId: string;
private privateKey: string;
private appAuth: any;

constructor() {
this.appId = process.env.GITHUB_APP_ID!;
this.privateKey = SecretManager.getInstance().getSecret('GITHUB_PRIVATE_KEY');
this.appAuth = createAppAuth({
appId: this.appId,
privateKey: this.privateKey
});
}

async getAppToken(): Promise<string> {
const authentication = await this.appAuth({ type: 'app' });
return authentication.token;
}

async getInstallationToken(installationId: number): Promise<string> {
const authentication = await this.appAuth({
type: 'installation',
installationId
});
return authentication.token;
}

async createOctokit(installationId: number): Promise<Octokit> {
const token = await this.getInstallationToken(installationId);
return new Octokit({ auth: token });
}

async verifyWebhookSignature(payload: string, signature: string): Promise<boolean> {
const secret = process.env.GITHUB_WEBHOOK_SECRET;
if (!secret) {
throw new Error('GITHUB_WEBHOOK_SECRET not configured');
}

}
}

===== FILE UPDATED: src/badges/badgeEngine.ts =====
import { BadgeSchema, BadgeLevel } from './badgeSchemas';
import { Badge5DModel, create5DBadge } from './badge5DModel';
import { logger } from '../utils/logger';

export interface ContributionData {
userId: string;
repository: string;
mergedPRs: number;
reviews: number;
comments: number;
bugFixes: number;
securityReports: number;
firstContributionDate: Date;
lastContributionDate: Date;
}

export class BadgeEngine {
private schemas: Map<string, BadgeSchema> = new Map();

constructor(schemas: BadgeSchema[]) {
for (const schema of schemas) {
this.schemas.set(schema.name, schema);
}
}

async evaluateBadges(data: ContributionData): Promise<Badge5DModel[]> {
const eligibleBadges: Badge5DModel[] = [];

}

private checkEligibility(schema: BadgeSchema, data: ContributionData): BadgeLevel | null {
switch (schema.name) {
case 'First Contributor':
return data.mergedPRs >= 1 ? 'bronze' : null;

}

async revokeBadge(badgeId: string, reason: string): Promise<void> {
logger.warn(Revoking badge ${badgeId}: ${reason});
// In production, this would update on-chain state
}

getSchema(name: string): BadgeSchema | undefined {
return this.schemas.get(name);
}
}

===== FILE UPDATED: src/badges/badgeIssuer.ts =====
import { Badge5DModel, create5DBadge } from './badge5DModel';
import { generateProof } from '../zk/prover/generateProof';
import { BadgeEngine, ContributionData } from './badgeEngine';
import { logger } from '../utils/logger';
import { SolanaClient } from '../zk/solana/client/solanaClient';
import { BadgeAccount } from '../zk/solana/client/badgeAccount';

export class BadgeIssuer {
private engine: BadgeEngine;
private solanaClient: SolanaClient;
private badgeAccount: BadgeAccount;

constructor(engine: BadgeEngine, solanaClient: SolanaClient) {
this.engine = engine;
this.solanaClient = solanaClient;
this.badgeAccount = new BadgeAccount(solanaClient);
}

async issueBadgesForUser(data: ContributionData): Promise<Badge5DModel[]> {
const eligibleBadges = await this.engine.evaluateBadges(data);
const issuedBadges: Badge5DModel[] = [];

}

async revokeBadge(badgeId: string, reason: string): Promise<void> {
await this.badgeAccount.revokeBadge(badgeId, reason);
logger.info(Revoked badge ${badgeId}: ${reason});
}
}

===== FILE UPDATED: src/badges/badgeVerifier.ts =====
import { verifyProof } from '../zk/prover/verifyProof';
import { isValidBadge, Badge5DModel } from './badge5DModel';
import { SolanaClient } from '../zk/solana/client/solanaClient';
import { BadgeAccount } from '../zk/solana/client/badgeAccount';

export class BadgeVerifier {
private badgeAccount: BadgeAccount;

constructor(solanaClient: SolanaClient) {
this.badgeAccount = new BadgeAccount(solanaClient);
}

async verifyBadge(badgeId: string): Promise<{
valid: boolean;
badge?: Badge5DModel;
reason?: string;
}> {
try {
const badge = await this.badgeAccount.getBadge(badgeId);

}
}

===== FILE UPDATED: src/zk/prover/generateProof.ts =====
import { groth16 } from 'snarkjs';
import { logger } from '../../utils/logger';
import { createHash } from 'crypto';
import { ContributionData } from '../../badges/badgeEngine';

export interface ProofInput {
badgeId: string;
userId: string;
contributions: ContributionData;
}

export interface GeneratedProof {
proof: any;
publicSignals: any;
hash: string;
}

export async function generateProof(input: ProofInput): Promise<GeneratedProof> {
try {
// Load circuit and keys
const provingKey = process.env.ZK_PROVING_KEY_PATH || './src/zk/circuits/keys/proving.key';

} catch (error) {
logger.error('Proof generation failed:', error);
throw new Error('Failed to generate ZK proof');
}

private static getThresholdForBadge(badgeId: string): number {
// Parse badge type from ID and return appropriate threshold
if (badgeId.includes('First-Contributor')) return 1;
if (badgeId.includes('Regular-Contributor')) return 10;
if (badgeId.includes('Core-Contributor')) return 50;
return 1;
}
}

function hashToField(str: string): bigint {
const hash = createHash('sha256').update(str).digest('hex');
return BigInt(0x${hash}) % BigInt(2 ** 253);
}

===== FILE UPDATED: src/zk/prover/verifyProof.ts =====
import { groth16 } from 'snarkjs';
import { logger } from '../../utils/logger';

export async function verifyProof(
proofHash: string,
publicSignals: any
): Promise<boolean> {
try {
const verificationKey = process.env.ZK_VERIFICATION_KEY_PATH || 
'./src/zk/circuits/keys/verification.key';

} catch (error) {
logger.error('Proof verification failed:', error);
return false;
}
}

export async function verifyFullProof(
proof: any,
publicSignals: any
): Promise<boolean> {
try {
const verificationKey = process.env.ZK_VERIFICATION_KEY_PATH || 
'./src/zk/circuits/keys/verification.key';

} catch (error) {
logger.error('Full proof verification failed:', error);
return false;
}
}

===== FILE UPDATED: src/zk/solana/client/badgeAccount.ts =====
import { PublicKey, Transaction, SystemProgram } from '@solana/web3.js';
import { SolanaClient } from './solanaClient';
import { Badge5DModel } from '../../../badges/badge5DModel';
import { ZKInstructionBuilder } from './zkInstructionBuilder';
import { logger } from '../../../utils/logger';

export class BadgeAccount {
private client: SolanaClient;
private instructionBuilder: ZKInstructionBuilder;

constructor(client: SolanaClient) {
this.client = client;
this.instructionBuilder = new ZKInstructionBuilder(client['programId']);
}

async storeBadge(badge: Badge5DModel): Promise<string> {
try {
const badgePubkey = this.getBadgeAddress(badge.id);

}

async getBadge(badgeId: string): Promise<Badge5DModel | null> {
try {
const badgePubkey = this.getBadgeAddress(badgeId);
const accountInfo = await this.client.getAccountInfo(badgePubkey);

}

async revokeBadge(badgeId: string, reason: string): Promise<string> {
try {
const badgePubkey = this.getBadgeAddress(badgeId);
const tx = new Transaction().add(
this.instructionBuilder.buildRevokeBadgeInstruction(
badgePubkey,
this.client['payer'].publicKey,
reason
)
);

}

private getBadgeAddress(badgeId: string): PublicKey {
// Derive PDA for badge account
return PublicKey.createWithSeed(
this.client['payer'].publicKey,
badgeId,
this.client['programId']
);
}
}

===== FILE UPDATED: src/zk/solana/client/zkInstructionBuilder.ts =====
import { TransactionInstruction, PublicKey } from '@solana/web3.js';
import { Badge5DModel } from '../../../badges/badge5DModel';
import { serialize } from 'borsh';

export class ZKInstructionBuilder {
private programId: PublicKey;

constructor(programId: PublicKey) {
this.programId = programId;
}

buildIssueBadgeInstruction(
badgeAccount: PublicKey,
authority: PublicKey,
badgeData: Badge5DModel
): TransactionInstruction {
// Serialize badge data for on-chain storage
const serializedData = this.serializeBadgeData(badgeData);
const data = Buffer.concat([
Buffer.from([0]), // Instruction tag for IssueBadge
Buffer.from(serializedData)
]);

}

buildRevokeBadgeInstruction(
badgeAccount: PublicKey,
authority: PublicKey,
reason: string
): TransactionInstruction {
const reasonBuffer = Buffer.from(reason, 'utf8');
const data = Buffer.concat([
Buffer.from([1]), // Instruction tag for RevokeBadge
Buffer.from(Uint32Array.from([reasonBuffer.length])),
reasonBuffer
]);

}

buildVerifyBadgeInstruction(
badgeAccount: PublicKey,
badgeId: string
): TransactionInstruction {
const idBuffer = Buffer.from(badgeId, 'utf8');
const data = Buffer.concat([
Buffer.from([2]), // Instruction tag for VerifyBadge
Buffer.from(Uint32Array.from([idBuffer.length])),
idBuffer
]);

}

private serializeBadgeData(badge: Badge5DModel): Buffer {
// Simple JSON serialization for now
// In production, use Borsh or similar binary format
return Buffer.from(JSON.stringify({
id: badge.id,
type: badge.type,
level: badge.level,
issuedTo: badge.issuedTo,
issuedAt: badge.issuedAt,
expiresAt: badge.expiresAt,
repository: badge.repository,
proofHash: badge.proofHash,
metadata: badge.metadata
}));
}
}

===== FILE UPDATED: src/github/eventHandlers/onPullRequestMerged.ts =====
import { logger } from '../../utils/logger';
import { BadgeEngine } from '../../badges/badgeEngine';
import { BadgeIssuer } from '../../badges/badgeIssuer';
import { SolanaClient } from '../../zk/solana/client/solanaClient';
import { defaultBadgeSchemas } from '../../badges/badgeSchemas';
import { ContributionData } from '../../badges/badgeEngine';

export async function onPullRequestMerged(payload: any): Promise<void> {
try {
const { repository, pull_request, sender } = payload;
const userId = pull_request.user.login;

} catch (error) {
logger.error('Error processing PR merged event:', error);
}
}

===== FILE UPDATED: src/github/webhookRouter.ts =====
import { Router, Request, Response } from 'express';
import { GitHubAppAuth } from '../auth/githubAppAuth';
import { onIssueOpened } from './eventHandlers/onIssueOpened';
import { onPullRequestMerged } from './eventHandlers/onPullRequestMerged';
import { onPush } from './eventHandlers/onPush';
import { onRelease } from './eventHandlers/onRelease';
import { onWorkflowRun } from './eventHandlers/onWorkflowRun';
import { handleInstallation } from './installation/installHandler';
import { handleUninstallation } from './installation/uninstallHandler';
import { logger } from '../utils/logger';

export const webhookRouter = Router();
const githubAuth = new GitHubAppAuth();

webhookRouter.post('/', async (req: Request, res: Response) => {
try {
const signature = req.headers['x-hub-signature-256'] as string;
const event = req.headers['x-github-event'] as string;
const payload = req.body;

} catch (error) {
logger.error('Webhook processing error:', error);
res.status(500).json({ error: 'Internal server error' });
}
});

===== FILE UPDATED: src/github/installation/installHandler.ts =====
import { logger } from '../../utils/logger';
import { GitHubAppAuth } from '../../auth/githubAppAuth';
import { checkInstallationPermissions } from './permissionsCheck';

export async function handleInstallation(installation: any, repositories: any[]): Promise<void> {
try {
logger.info(GitHub App installed on account: ${installation.account.login});
logger.info(Installation ID: ${installation.id});
logger.info(Repositories: ${repositories?.map(r => r.full_name).join(', ') || 'none'});

} catch (error) {
logger.error('Error handling installation:', error);
throw error;
}
}

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
// Initialize dependencies
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
const result = await this.verifier.verifyBadge(badgeId);

};

getUserBadges = async (req: Request, res: Response): Promise<void> => {
try {
const { userId } = req.params;
// In production, fetch from database or on-chain
res.json({ badges: [] });
} catch (error) {
logger.error('Error getting user badges:', error);
res.status(500).json({ error: 'Internal server error' });
}
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
"test": "jest",
"test:unit": "jest tests/unit",
"test:integration": "jest tests/integration",
"test:e2e": "jest tests/e2e",
"lint": "eslint src//*.ts",
"format": "prettier --write \"src//*.ts\"",
"circuits:compile": "circom src/zk/circuits/identity.circom --r1cs --wasm --sym -o src/zk/circuits/compiled && circom src/zk/circuits/badgeProof.circom --r1cs --wasm --sym -o src/zk/circuits/compiled && circom src/zk/circuits/revocation.circom --r1cs --wasm --sym -o src/zk/circuits/compiled",
"solana:build": "cd src/zk/solana/program && cargo build-bpf",
"solana:deploy": "solana program deploy src/zk/solana/program/target/deploy/zkbadge_program.so",
"badge:issue": "ts-node scripts/issue-badges.ts"
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

===== FILE ADDED: scripts/issue-badges.ts =====
#!/usr/bin/env ts-node
import dotenv from 'dotenv';
import { BadgeEngine } from '../src/badges/badgeEngine';
import { BadgeIssuer } from '../src/badges/badgeIssuer';
import { defaultBadgeSchemas } from '../src/badges/badgeSchemas';
import { SolanaClient } from '../src/zk/solana/client/solanaClient';
import { logger } from '../src/utils/logger';

dotenv.config();

async function issueBadges() {
try {
logger.info('Starting badge issuance cron job');

} catch (error) {
logger.error('Badge issuance cron job failed:', error);
process.exit(1);
}
}

issueBadges();

===== FILE ADDED: scripts/build-circuits.sh =====
#!/bin/bash

set -e

echo "Building ZK circuits..."

Create directories

mkdir -p src/zk/circuits/compiled
mkdir -p src/zk/circuits/keys

Compile identity circuit

echo "Compiling identity.circom..."
circom src/zk/circuits/identity.circom --r1cs --wasm --sym -o src/zk/circuits/compiled

Compile badgeProof circuit

echo "Compiling badgeProof.circom..."
circom src/zk/circuits/badgeProof.circom --r1cs --wasm --sym -o src/zk/circuits/compiled

Compile revocation circuit

echo "Compiling revocation.circom..."
circom src/zk/circuits/revocation.circom --r1cs --wasm --sym -o src/zk/circuits/compiled

echo "Circuit compilation complete"

===== FILE UPDATED: docs/ARCHITECTURE.md =====

Architecture Overview

System Architecture

The ZK-5D Cryptographic Badge Authority is built as a distributed system with the following components:

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│   GitHub App    │────▶│   Badge Engine   │────▶│   Solana Chain  │
│   (Webhooks)    │     │   (TypeScript)   │     │   (Program)     │
└─────────────────┘     └──────────────────┘     └─────────────────┘
        │                        │                        │
        ▼                        ▼                        ▼
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│   ZK Prover     │     │   Governance     │     │   Verification  │
│   (snarkjs)     │     │   (GitDigital)   │     │   (API)         │
└─────────────────┘     └──────────────────┘     └─────────────────┘
```

Component Breakdown

1. GitHub App Integration

· Listens to webhook events (PR merges, issues, pushes, installations)
· Authenticates via GitHub App installation
· Collects contribution data for badge evaluation
· Validates webhook signatures with HMAC-SHA256

2. Badge Engine

· Evaluates contributions against badge criteria defined in schemas
· Manages badge schemas and rules from .gitdigital-badges.yml
· Triggers ZK proof generation for eligible badges

3. ZK Circuit Layer

· Identity circuit: proves GitHub identity ownership
· Badge proof circuit: proves meeting criteria without revealing exact counts
· Revocation circuit: proves badge validity at a given timestamp
· Prover generates proofs using Groth16
· Verifier validates proofs off-chain or on-chain

4. Solana Program

· Stores badge state on-chain with Borsh serialization
· Handles badge issuance, revocation, and verification
· Account structure includes badge data, authority, timestamps, and revocation status
· Instructions: IssueBadge, RevokeBadge, VerifyBadge

5. Governance Layer

· GitDigital-based decision making
· Proposal system for badge schema changes
· Community voting mechanisms with quorum and approval thresholds

Data Flow

1. GitHub event triggers webhook (e.g., pull_request merged)
2. Webhook signature verified
3. Contribution data extracted and passed to Badge Engine
4. Badge Engine evaluates against schemas
5. If criteria met, ZK proof generated off-chain via snarkjs
6. Proof submitted to Solana program via client
7. Badge minted and stored on-chain in PDA account
8. Verification requests query on-chain state and verify proofs

Directory Structure

```
src/
├── api/            # REST API endpoints, controllers, middleware
├── auth/           # GitHub App auth, JWT, permissions
├── badges/         # Badge engine, schemas, 5D model, issuer, verifier
├── config/         # Environment config, secrets management
├── github/         # Webhook router, event handlers, installation handlers
├── utils/          # Logging, crypto, helpers
├── zk/             # ZK circuits, prover, verifier, Solana program
│   ├── circuits/   # Circom circuit definitions
│   ├── prover/     # Proof generation and verification
│   └── solana/     # Solana program and client
```

Security Considerations

· All ZK circuits audited before deployment
· Multi-sig for contract upgrades (3/5 signatures)
· Rate limiting on all endpoints (100 requests per 15 minutes)
· JWT-based API authentication with 24-hour expiration
· Environment secrets never committed, stored in SecretManager
· Webhook signatures verified with timing-safe comparison
· Solana private keys stored in secrets manager, never exposed

Deployment

Local Development

```bash
npm install
npm run build:circuits
npm run solana:build
npm run dev
```

Production

```bash
docker-compose up -d
```

The application runs on port 3000 with:

· /webhook - GitHub webhook endpoint
· /api/* - Authenticated API endpoints
· /health - Health check endpoint

===== FILE UPDATED: docs/API-REFERENCE.md =====

API Reference

Authentication

All API endpoints except /health and /webhook require Bearer token authentication:

```
Authorization: Bearer <JWT_TOKEN>
```

Obtaining a JWT

JWT tokens are generated internally for service-to-service communication. For external clients, integrate with the GitHub App OAuth flow.

Badge Endpoints

Get Badge

```
GET /api/badges/:badgeId
```

Response:

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

Get User Badges

```
GET /api/badges/user/:userId
```

Response:

```json
{
  "badges": [...]
}
```

Issue Badge

```
POST /api/badges/issue
```

Request:

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

Response:

```json
{
  "success": true,
  "badges": [...]
}
```

Revoke Badge

```
POST /api/badges/revoke
```

Request:

```json
{
  "badgeId": "First-Contributor-github:user-1234567890",
  "reason": "Fraudulent activity detected"
}
```

Response:

```json
{
  "success": true
}
```

Verify Badge

```
POST /api/badges/verify
```

Request:

```json
{
  "badgeId": "First-Contributor-github:user-1234567890"
}
```

Response:

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

Request:

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

Response:

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

Request:

```json
{
  "proofHash": "abc123...",
  "publicSignals": [...]
}
```

Response:

```json
{
  "valid": true
}
```

Get Proof Status

```
GET /api/proofs/:proofHash
```

Response:

```json
{
  "proofHash": "abc123...",
  "status": "verified"
}
```

Governance Endpoints

Create Proposal

```
POST /api/governance/proposals
```

Request:

```json
{
  "title": "Add New Badge: Documentation Hero",
  "description": "Proposal to add a badge for documentation contributors...",
  "options": ["Approve", "Reject"]
}
```

Response:

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

Response:

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

Response:

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

Request:

```json
{
  "vote": "Approve"
}
```

Response:

```json
{
  "success": true
}
```

Execute Proposal

```
POST /api/governance/proposals/:proposalId/execute
```

Response:

```json
{
  "success": true
}
```

Health Check

```
GET /health
```

Response:

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

Rate Limits

· IP-based: 100 requests per 15 minutes
· User-based: 1000 requests per hour (with valid JWT)

Rate limit headers are included in responses:

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
  "message": "Additional details (development only)"
}
```

===== FILE UPDATED: docs/GOVERNANCE-RULES.md =====

Governance Rules

Proposal Lifecycle

1. Submission - Any user with governance role can submit proposal via API
2. Discussion - 7-day community discussion period (minimum)
3. Voting - 7-day token-weighted voting period
4. Execution - Automatic execution if approved and quorum met
5. Implementation - Core team implements approved changes

Proposal Types

Badge Schema Changes

· Requires 60% approval
· 7-day voting period
· Must include updated .gitdigital-badges.yml
· Changes are applied after execution

Protocol Upgrades

· Requires 75% approval
· 14-day voting period
· Requires external audit before voting
· Multi-sig execution required

Emergency Actions

· Requires 50% approval
· 24-hour voting period
· Requires multi-sig execution
· Limited to security-critical changes

Voting Power Calculation

Voting power is calculated as:

```
VotingPower = (DIGITAL_Token_Balance * 0.6) + (Reputation_Score * 0.4)
```

Where:

· DIGITAL token balance is measured at proposal creation time
· Reputation score is sum of badge reputation values (10 per bronze, 25 per silver, 50 per gold, 100 per platinum)

Quorum Requirements

· Badge Schema Changes: 30% of total voting power must participate
· Protocol Upgrades: 40% of total voting power must participate
· Emergency Actions: 20% of total voting power must participate

Council

5-member community council:

· 12-month terms, elected annually
· Elected by token holders using quadratic voting
· Handles disputes and arbitration
· Can veto proposals with 4/5 majority within 48 hours of approval
· Council members cannot vote on proposals they authored

Dispute Resolution Process

1. Challenge - Any badge holder can challenge a badge via governance proposal
2. Review - Council reviews evidence within 7 days
3. Decision - Council votes (3/5 majority required)
4. Appeal - Token holders can appeal council decision to full vote with 10% quorum

Proposal Parameters

Parameter Value
Min Proposal Stake 1000 DIGITAL
Voting Period 7 days (10080 blocks)
Execution Delay 3 days (4320 blocks)
Discussion Period 7 days minimum

Implementation

Proposals are implemented via:

· Badge Schema Changes: Update .gitdigital-badges.yml and deploy
· Protocol Upgrades: Deploy new Solana program and update program ID
· Emergency Actions: Direct multi-sig execution with timelock
