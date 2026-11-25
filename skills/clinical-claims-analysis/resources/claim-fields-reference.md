# Claim Fields Reference

This document explains each field in the clinical claims data structure used by the clinical-claims-analysis skill.

---

## Claim Fields

### Core Identification

#### `claim_id` (Required)
**Type:** String  
**Example:** `"CLM-2026-001"`, `"claim_12345"`

**Purpose:** Unique identifier for the claim. Used to trace the claim through the system and in audit logs.

**Rules:**
- Must be unique across all claims
- Cannot be a patient identifier
- Safe to log and audit

---

#### `clinic_id` (Required)
**Type:** String  
**Example:** `"clinic_austin_01"`, `"clinic_london_01"`

**Purpose:** Which clinic submitted the claim. Used for clinic-level aggregation and analysis.

**Rules:**
- Must correspond to actual HealthCore clinic
- Same clinics may appear multiple times across different claims
- Used in aggregated reporting

---

#### `provider_id` (Required)
**Type:** String  
**Example:** `"prov_smith"`, `"prov_dr_johnson"`

**Purpose:** Which healthcare provider coded and submitted the claim. Used for provider performance analysis.

**Rules:**
- Must correspond to actual HealthCore provider
- Used to identify coding patterns by individual provider
- Safe to use for performance tracking

---

### Clinical Codes

#### `diagnosis_code` (Required)
**Type:** String (ICD-10 format)  
**Example:** `"M79.3"`, `"E11.9"`, `"I10"`

**Purpose:** Medical diagnosis code from the ICD-10 standard (International Classification of Diseases).

**Rules:**
- Must be valid ICD-10 code (3-7 characters)
- Cannot be blank
- Used to identify diagnosis-related rejection patterns
- Multiple diagnoses on single claim should use separate claim records (or array in future)

**Reference:** See `diagnosis-codes.txt` for valid codes.

---

#### `procedure_code` (Required)
**Type:** String (CPT format)  
**Example:** `"99213"`, `"90837"`, `"99214"`

**Purpose:** Medical procedure code from the CPT (Current Procedural Terminology) standard.

**Rules:**
- Must be valid CPT code (5 digits)
- Cannot be blank
- Used to identify procedure-related rejection patterns
- Multiple procedures on single claim should use separate claim records (or array in future)

**Reference:** See `procedure-codes.txt` for valid codes.

---

### Rejection Information

#### `rejection_reason` (Optional, Conditional)
**Type:** String or null  
**Example:** `"Invalid diagnosis code for procedure"`, `"Missing required modifier"`, `null`

**Purpose:** Why the claim was rejected. If the claim was rejected, this field contains the rejection reason.

**Rules:**
- If claim was rejected: must contain the reason
- If claim was NOT rejected: must be `null`
- Used as the primary grouping key for pattern detection
- Must be exact text from the payer (don't paraphrase)

**Common Values:**
- `"Invalid diagnosis code for procedure"`
- `"Missing required modifier"`
- `"Duplicate claim"`
- `"Non-covered service"`
- `"Authorization required"`
- `"Patient liability limitation exceeded"`

---

#### `rejection_date` (Optional, Conditional)
**Type:** Date (ISO 8601 format) or null  
**Example:** `"2026-11-20"`, `null`

**Purpose:** When the claim was rejected.

**Rules:**
- If claim was rejected: must contain the date
- If claim was NOT rejected: must be `null`
- Used for trend analysis (is this problem getting worse or better?)
- Used to calculate days from submission to rejection

**Format:** `YYYY-MM-DD` (ISO 8601)

---

### Insurance & Billing

#### `insurance_payer` (Required)
**Type:** String  
**Example:** `"aetna"`, `"bcbs"`, `"bupa"`, `"medicare"`, `"medicaid"`

**Purpose:** Which insurance company the claim was submitted to.

**Rules:**
- Must match payer abbreviations in system
- Used to identify payer-specific rejection patterns
- Some payers have different rules (Medicare vs commercial vs Medicaid)

**Common Payers:**
- US: aetna, bcbs, cigna, humana, medicare, medicaid
- UK: bupa, axa, aviva

---

#### `claim_amount` (Required)
**Type:** Number (currency)  
**Example:** `150.00`, `200.50`, `1250.75`

**Purpose:** Amount billed to the insurance company for this claim.

**Rules:**
- Must be positive number
- In USD (US claims) or GBP (UK claims)
- Used to calculate total financial impact of rejections
- Used to prioritize which rejection patterns to fix first

---

### Processing Metadata

#### `processing_date` (Optional)
**Type:** Date (ISO 8601 format)  
**Example:** `"2026-11-15"`, `"2026-11-20"`

**Purpose:** When the claim was initially processed/submitted.

**Rules:**
- Used to calculate time-to-rejection
- Used for trend analysis (which week had most rejections?)
- Used to filter analysis by time period

**Format:** `YYYY-MM-DD` (ISO 8601)

---

#### `rework_attempts` (Optional)
**Type:** Integer  
**Example:** `0`, `1`, `3`

**Purpose:** How many times the claim was resubmitted after rejection.

**Rules:**
- 0 = never resubmitted (rejected and given up)
- 1+ = resubmitted one or more times
- Higher numbers indicate complex issues
- Used to identify which rejection patterns require multiple rework cycles

---

#### `days_to_rejection` (Optional)
**Type:** Integer (days)  
**Example:** `5`, `15`, `30`

**Purpose:** How many days elapsed between submission and rejection.

**Rules:**
- Useful for identifying slow-responding payers
- Used for cash flow analysis
- Pre-calculated for convenience

---

## Example Complete Record

```json
{
  "claim_id": "CLM-2026-001",
  "diagnosis_code": "M79.3",
  "procedure_code": "99213",
  "rejection_reason": "Invalid diagnosis code for procedure",
  "rejection_date": "2026-11-20",
  "clinic_id": "clinic_austin_01",
  "provider_id": "prov_smith",
  "insurance_payer": "aetna",
  "claim_amount": 150.00,
  "processing_date": "2026-11-15",
  "rework_attempts": 2,
  "days_to_rejection": 5
}
```

---

## Data Quality Rules

### Required vs Optional

| Field | Required? | Nullable? | Notes |
|-------|-----------|-----------|-------|
| claim_id | ✅ Yes | ❌ No | Must have |
| diagnosis_code | ✅ Yes | ❌ No | Must have |
| procedure_code | ✅ Yes | ❌ No | Must have |
| rejection_reason | ⚠️ Conditional | ✅ Yes | Null if accepted, string if rejected |
| rejection_date | ⚠️ Conditional | ✅ Yes | Null if accepted, date if rejected |
| clinic_id | ✅ Yes | ❌ No | Must have |
| provider_id | ✅ Yes | ❌ No | Must have |
| insurance_payer | ✅ Yes | ❌ No | Must have |
| claim_amount | ✅ Yes | ❌ No | Must be > 0 |
| processing_date | ⚠️ Optional | ✅ Yes | Nice to have for trending |
| rework_attempts | ⚠️ Optional | ✅ Yes | Nice to have for complexity analysis |
| days_to_rejection | ⚠️ Optional | ✅ Yes | Can be calculated from dates |

---

## Validation Rules

Before submitting claims to the analysis skill, verify:

- ✅ All required fields are present and non-null
- ✅ diagnosis_code is a valid ICD-10 code
- ✅ procedure_code is a valid CPT code
- ✅ claim_amount is > 0
- ✅ rejection_reason and rejection_date must both be present or both be null
- ✅ clinic_id matches a known HealthCore clinic
- ✅ provider_id matches a known HealthCore provider
- ✅ insurance_payer matches a known payer
- ✅ Dates are in YYYY-MM-DD format
- ✅ No patient names, SSNs, DOBs, or other PHI

---

## Privacy & Compliance

### Safe to Include
- Diagnosis codes (medical categories, not sensitive on their own)
- Procedure codes (medical categories, not sensitive on their own)
- Rejection reasons (operational data)
- Clinic ID and Provider ID (anonymized identifiers)
- Financial amounts (operational data)

### NEVER Include
- Patient names
- Patient SSNs or medical record numbers
- Patient dates of birth
- Patient home addresses or phone numbers
- Raw clinical notes (use codes instead)

---

**Reference updated:** 2026-11-25
