# TypeScript/JavaScript Conventions — Code Style & Organization

**Purpose:** Define TypeScript/JavaScript coding conventions for the HealthCore monorepo.

**Applies to:** All `.ts`, `.tsx`, `.js`, `.jsx` files in `uis/`, `services/` (if Node-based), and other frontend/Node code.

---

## 📋 Naming Conventions

### Variables & Functions
```typescript
// ✅ GOOD
let totalClaimsProcessed: number = 0;
const maxBatchSize: number = 1000;

function analyzeClaimsData(claims: Claim[]): AnalysisResult {
  const rejectionPatterns: Pattern[] = [];
  return { patterns: rejectionPatterns };
}

// ❌ BAD
let TotalClaimsProcessed = 0;  // PascalCase for variable
const max_batch_size = 1000;  // snake_case
function AnalyzeClaimsData() {}  // PascalCase for function
```

**Rule:** `camelCase` for variables, functions, and properties.

---

### Classes & Interfaces
```typescript
// ✅ GOOD
class ClaimsAnalyzer {
  private cachedData: Map<string, Claim>;
  
  constructor(minFrequency: number) {}
  
  public analyze(claims: Claim[]): AnalysisResult {}
  
  private validateClaim(claim: Claim): boolean {}
}

interface IAnalysisResult {
  patterns: Pattern[];
  totalClaims: number;
}

// ❌ BAD
class claims_analyzer {}  // snake_case
interface analysisResult {}  // lowercase, no I prefix
```

**Rule:** 
- Classes: `PascalCase`
- Interfaces: `PascalCase` (optional `I` prefix, e.g., `IAnalysisResult`)
- Private methods: prefix with `private` keyword

---

### Constants
```typescript
// ✅ GOOD
export const HIPAA_RETENTION_DAYS = 365;
export const MAX_CLAIMS_BATCH_SIZE = 1000;

// ❌ BAD
export const hipaa_retention_days = 365;  // lowercase
export const maxClaimsBatchSize = 1000;  // camelCase for constant
```

**Rule:** `UPPER_SNAKE_CASE` for module-level constants.

---

### File Names
```
// ✅ GOOD
components/
  ClaimsAnalyzer.tsx
  PatientForm.tsx
  
services/
  claimsService.ts
  authService.ts
  
types/
  claim.types.ts
  analysis.types.ts

// ❌ BAD
components/
  claims-analyzer.tsx  # Hyphens
  patient_form.tsx  # snake_case

services/
  ClaimsService.ts  # PascalCase
```

**Rule:** 
- Components: `PascalCase.tsx`
- Functions/services: `camelCase.ts`
- Types: `camelCase.types.ts` or `camelCase.ts`

---

## 📂 File & Folder Organization

### React/Frontend Structure
```
uis/website/  or  uis/backoffice/
├── src/
│   ├── components/           # Reusable React components
│   │   ├── ClaimsAnalyzer/
│   │   │   ├── ClaimsAnalyzer.tsx
│   │   │   ├── ClaimsAnalyzer.module.css
│   │   │   └── ClaimsAnalyzer.test.tsx
│   │   └── PatientForm/
│   │       ├── PatientForm.tsx
│   │       └── PatientForm.module.css
│   ├── pages/                # Page components (routes)
│   │   ├── Dashboard.tsx
│   │   └── PatientProfile.tsx
│   ├── services/             # API calls, business logic
│   │   ├── claimsService.ts
│   │   └── authService.ts
│   ├── types/                # TypeScript interfaces
│   │   ├── claim.types.ts
│   │   └── patient.types.ts
│   ├── utils/                # Shared utilities
│   │   ├── formatters.ts
│   │   └── validators.ts
│   ├── styles/               # Global styles
│   │   └── globals.css
│   └── App.tsx               # Main app component
├── public/                   # Static assets
├── package.json
└── tsconfig.json
```

---

### Express/Backend (Node) Structure
```
services/api/  (if Node-based)
├── src/
│   ├── routes/               # API endpoints
│   │   ├── claims.routes.ts
│   │   └── patients.routes.ts
│   ├── controllers/          # Request handlers
│   │   ├── claimsController.ts
│   │   └── patientsController.ts
│   ├── services/             # Business logic
│   │   ├── claimsService.ts
│   │   └── patientService.ts
│   ├── middleware/           # Express middleware
│   │   ├── auth.middleware.ts
│   │   └── errorHandler.ts
│   ├── types/                # Interfaces
│   │   ├── claim.types.ts
│   │   └── patient.types.ts
│   ├── utils/                # Helpers
│   │   └── validators.ts
│   └── app.ts                # Express app setup
├── package.json
└── tsconfig.json
```

---

## 📝 Types & Interfaces

### Naming Types
```typescript
// ✅ GOOD
interface Patient {
  id: string;
  name: string;
  dateOfBirth: Date;
  medicalHistory?: string[];
}

type AnalysisResult = {
  patterns: Pattern[];
  totalClaims: number;
};

// ❌ BAD
interface IPatient {}  // redundant I prefix
interface patient {}  # lowercase
type AnalysisResult {}  # (interface vs type is a team choice)
```

**Rule:** `PascalCase` for both `interface` and `type`.

---

### Explicit Types
```typescript
// ✅ GOOD
let totalClaims: number = 0;
const claims: Claim[] = [];
function analyze(data: Claim[]): AnalysisResult {
  return { patterns: [], totalClaims: 0 };
}

// ❌ BAD
let totalClaims = 0;  // Type inference (less explicit)
const claims = [];  # Empty array type
function analyze(data) {}  # No parameter type
function analyze(data: any) {}  # 'any' is too broad
```

**Rule:** Always provide explicit types. Avoid `any`.

---

## 📥 Imports

### Order
```typescript
// ✅ GOOD
// 1. React/third-party libraries
import React, { useState } from 'react';
import axios from 'axios';
import { Button } from '@mui/material';

// 2. Types
import type { Claim, Patient } from '../types';

// 3. Local components/services
import { ClaimsTable } from './ClaimsTable';
import { claimsService } from '../services/claimsService';

// 4. Styles
import styles from './Dashboard.module.css';

// ❌ BAD
import styles from './Dashboard.module.css';
import axios from 'axios';
import { claimsService } from '../services/claimsService';
import React from 'react';
```

**Rule:** External → Types → Local → Styles. Alphabetical within each group.

---

## 🎯 React Component Conventions

### Functional Components
```typescript
// ✅ GOOD
interface ClaimsAnalyzerProps {
  claims: Claim[];
  onAnalysisComplete: (result: AnalysisResult) => void;
}

export const ClaimsAnalyzer: React.FC<ClaimsAnalyzerProps> = ({
  claims,
  onAnalysisComplete,
}) => {
  const [isLoading, setIsLoading] = useState(false);
  
  const handleAnalyze = async () => {
    setIsLoading(true);
    try {
      const result = await claimsService.analyze(claims);
      onAnalysisComplete(result);
    } catch (error) {
      console.error('Analysis failed:', error);
    } finally {
      setIsLoading(false);
    }
  };
  
  return (
    <div>
      <button onClick={handleAnalyze} disabled={isLoading}>
        {isLoading ? 'Analyzing...' : 'Analyze'}
      </button>
    </div>
  );
};

// ❌ BAD
export function claimsAnalyzer(claims) {  # lowercase name, no types
  const [isLoading, setIsLoading] = useState(false);
  // Missing prop interface
  return <div>...</div>;
}
```

---

### Props Pattern
```typescript
// ✅ GOOD
interface ButtonProps {
  label: string;
  onClick: (event: React.MouseEvent) => void;
  disabled?: boolean;
  variant?: 'primary' | 'secondary';
}

export const Button: React.FC<ButtonProps> = ({
  label,
  onClick,
  disabled = false,
  variant = 'primary',
}) => (
  <button className={`btn btn-${variant}`} onClick={onClick} disabled={disabled}>
    {label}
  </button>
);

// ✅ GOOD - with spread props
interface CardProps extends React.HTMLAttributes<HTMLDivElement> {
  title: string;
  children: React.ReactNode;
}

export const Card: React.FC<CardProps> = ({ title, children, ...rest }) => (
  <div {...rest}>
    <h2>{title}</h2>
    {children}
  </div>
);
```

---

## ⚠️ Data Sensitivity in Frontend

### Never Expose Patient Data
```typescript
// ❌ BAD - Logs patient name
const handlePatientLoad = (patient: Patient) => {
  console.log(`Loaded patient: ${patient.name}`);  // PII in console!
};

// ❌ BAD - Shows SSN in UI
<div>SSN: {patient.ssn}</div>

// ✅ GOOD - Anonymized logging
const handlePatientLoad = (patient: Patient) => {
  console.log(`Loaded patient: ${patient.id}`);
};

// ✅ GOOD - Mask sensitive data
<div>SSN: ***-**-{patient.ssn.slice(-4)}</div>
```

**Rule:** See [`data-handling.md`](./data-handling.md) for complete guidelines.

---

## 🧪 Testing Convention (When Tests Exist)

```typescript
// components/ClaimsAnalyzer.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { ClaimsAnalyzer } from './ClaimsAnalyzer';

describe('ClaimsAnalyzer', () => {
  it('should render analyze button', () => {
    const mockOnComplete = jest.fn();
    render(
      <ClaimsAnalyzer claims={[]} onAnalysisComplete={mockOnComplete} />
    );
    
    expect(screen.getByText('Analyze')).toBeInTheDocument();
  });
  
  it('should call onAnalysisComplete when analysis succeeds', async () => {
    // Test implementation
  });
});
```

**Rule:** Test files in `__tests__/` or `.test.ts(x)` files.

---

## 🔄 Code Style

### Line Length
- Maximum: **100 characters** per line (same as Python)

```typescript
// ✅ GOOD
const result = analyzeClaimsData(
  claims,
  options.minFrequency,
  options.outputFormat
);

// ❌ BAD - Too long
const result = analyzeClaimsData(claims, options.minFrequency, options.outputFormat);
```

---

### Semicolons
```typescript
// ✅ GOOD - Use semicolons consistently
const claims: Claim[] = [];
function analyze() {}
export const MyComponent = () => <div />;

// ❌ INCONSISTENT
const claims: Claim[] = []  # No semicolon
function analyze() {}
export const MyComponent = () => <div />  # Inconsistent
```

**Rule:** Use semicolons consistently (Prettier can enforce this).

---

## 🔗 Related

- **Data Handling:** [`data-handling.md`](./data-handling.md)
- **Compliance:** [`compliance-rules.md`](./compliance-rules.md)
- **Tech Context:** [`../docs/TECHNICAL_CONTEXT.md`](../docs/TECHNICAL_CONTEXT.md)

---

**Last updated:** 2026-11-25  
**Review this:** Before writing TypeScript/JavaScript code
