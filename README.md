# Financing Agent SDK (TypeScript)

Analyze SMB financing offers — MCA, term loans, PO financing, receivables purchase. Upload a PDF contract and get a plain English analysis with APR calculations, predatory term detection, and market benchmarks.

## Install

```bash
npm install @financing-agent/sdk
```

## Quick Start

```typescript
import { FinancingAgent } from '@financing-agent/sdk';

const agent = new FinancingAgent({ apiKey: 'fin_abc123...' });

const result = await agent.analyzePdf('./offer.pdf');
console.log(result.analysis);
```

Or set the API key as an environment variable:

```bash
export FINANCING_API_KEY=fin_abc123...
```

```typescript
const agent = new FinancingAgent(); // reads from env
```

## Get an API Key

```typescript
const apiKey = await FinancingAgent.register(
  'Jane Smith',
  'jane@acme.com',
  'Acme Corp'
);
console.log(`Your API key: ${apiKey}`);
// Save this — it's shown once.
```

## API Reference

### Initialize

```typescript
import { FinancingAgent } from '@financing-agent/sdk';

// From environment variable
const agent = new FinancingAgent();

// Or pass directly
const agent = new FinancingAgent({ apiKey: 'fin_abc123...' });

// Custom endpoint (self-hosted)
const agent = new FinancingAgent({
  apiKey: 'fin_abc123...',
  baseUrl: 'https://your-proxy.example.com',
});
```

### Analyze a PDF

```typescript
const result = await agent.analyzePdf('./offer.pdf');

console.log(result.analysis);     // Full plain English analysis
console.log(result.toolCalls);    // ["analyze_offer", "detect_predatory_terms", ...]
```

### Stream a PDF analysis

```typescript
for await (const chunk of agent.analyzePdfStream('./offer.pdf')) {
  process.stdout.write(chunk);
}
```

### Analyze from text

```typescript
const result = await agent.analyzeText(
  'I got an MCA offer: $50K advance, 1.35 factor rate, ' +
  '6 month term, daily ACH payments. Is this a good deal?'
);
console.log(result.analysis);
```

### Check usage

```typescript
const usage = await agent.getUsage();
console.log(`Total calls: ${usage.totalCalls}`);
console.log(`Last used: ${usage.lastCalledAt}`);
```

### Custom analysis prompt

```typescript
const result = await agent.analyzePdf('./offer.pdf', 'Is this predatory?');
```

## Types

```typescript
interface AnalysisResult {
  analysis: string;      // Full plain English analysis
  toolCalls: string[];   // MCP tools called during analysis
}

interface UsageInfo {
  name: string;
  company: string;
  createdAt: string;
  totalCalls: number;
  lastCalledAt: string | null;
}

interface FinancingAgentOptions {
  apiKey?: string;       // Falls back to FINANCING_API_KEY env var
  baseUrl?: string;      // Override for self-hosted deployments
  timeoutMs?: number;    // Default: 120000 (2 minutes)
}
```

## What You Get Back

The analysis includes:

- **Product identification** — MCA, term loan, PO financing, or receivables purchase
- **Key terms extracted** — advance amount, factor rate, repayment structure, fees
- **Effective APR** — annualized cost, comparable across product types
- **Cost per dollar** — how much each borrowed dollar costs
- **Predatory term detection** — red flags with severity and plain English explanations
- **Market benchmarks** — where the offer falls vs typical market rates
- **Plain English explanation** — tradeoffs, risks, and assessment

## Requirements

- Node.js 18+ (uses native `fetch`)
- An API key (get one via `FinancingAgent.register()`)
