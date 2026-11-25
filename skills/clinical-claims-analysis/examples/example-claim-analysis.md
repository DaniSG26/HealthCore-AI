# Example: Clinical Claims Analysis

**Purpose:** Demonstrate the clinical-claims-analysis skill with sanitized sample data.

**Data Type:** All data is fictitious and sanitized (no real patient information).

---

## 📊 Sample Dataset

**20 sample claims with intentional rejection patterns:**

```json
[
  {
    "claim_id": "CLM-2026-001",
    "diagnosis_code": "M79.3",
    "procedure_code": "99213",
    "rejection_reason": "Invalid diagnosis code for procedure",
    "rejection_date": "2026-11-20",
    "clinic_id": "clinic_austin_01",
    "provider_id": "prov_smith",
    "insurance_payer": "aetna",
    "claim_amount": 150.00
  },
  {
    "claim_id": "CLM-2026-002",
    "diagnosis_code": "M25.5",
    "procedure_code": "99214",
    "rejection_reason": "Invalid diagnosis code for procedure",
    "rejection_date": "2026-11-19",
    "clinic_id": "clinic_austin_01",
    "provider_id": "prov_smith",
    "insurance_payer": "aetna",
    "claim_amount": 200.00
  },
  {
    "claim_id": "CLM-2026-003",
    "diagnosis_code": "E11.9",
    "procedure_code": "90837",
    "rejection_reason": "Missing required modifier",
    "rejection_date": "2026-11-18",
    "clinic_id": "clinic_houston_01",
    "provider_id": "prov_johnson",
    "insurance_payer": "bcbs",
    "claim_amount": 120.00
  },
  {
    "claim_id": "CLM-2026-004",
    "diagnosis_code": "I10",
    "procedure_code": "99213",
    "rejection_reason": null,
    "rejection_date": null,
    "clinic_id": "clinic_austin_01",
    "provider_id": "prov_brown",
    "insurance_payer": "aetna",
    "claim_amount": 150.00
  },
  {
    "claim_id": "CLM-2026-005",
    "diagnosis_code": "M79.3",
    "procedure_code": "99213",
    "rejection_reason": "Invalid diagnosis code for procedure",
    "rejection_date": "2026-11-17",
    "clinic_id": "clinic_austin_01",
    "provider_id": "prov_smith",
    "insurance_payer": "aetna",
    "claim_amount": 150.00
  },
  {
    "claim_id": "CLM-2026-006",
    "diagnosis_code": "E11.9",
    "procedure_code": "90837",
    "rejection_reason": "Missing required modifier",
    "rejection_date": "2026-11-16",
    "clinic_id": "clinic_houston_01",
    "provider_id": "prov_johnson",
    "insurance_payer": "bcbs",
    "claim_amount": 120.00
  },
  {
    "claim_id": "CLM-2026-007",
    "diagnosis_code": "Z00.00",
    "procedure_code": "99213",
    "rejection_reason": null,
    "rejection_date": null,
    "clinic_id": "clinic_houston_01",
    "provider_id": "prov_davis",
    "insurance_payer": "bcbs",
    "claim_amount": 150.00
  },
  {
    "claim_id": "CLM-2026-008",
    "diagnosis_code": "M79.3",
    "procedure_code": "99214",
    "rejection_reason": "Invalid diagnosis code for procedure",
    "rejection_date": "2026-11-15",
    "clinic_id": "clinic_austin_01",
    "provider_id": "prov_smith",
    "insurance_payer": "aetna",
    "claim_amount": 200.00
  },
  {
    "claim_id": "CLM-2026-009",
    "diagnosis_code": "K21.0",
    "procedure_code": "99213",
    "rejection_reason": null,
    "rejection_date": null,
    "clinic_id": "clinic_london_01",
    "provider_id": "prov_wilson",
    "insurance_payer": "bupa",
    "claim_amount": 180.00
  },
  {
    "claim_id": "CLM-2026-010",
    "diagnosis_code": "E11.9",
    "procedure_code": "90837",
    "rejection_reason": "Missing required modifier",
    "rejection_date": "2026-11-14",
    "clinic_id": "clinic_houston_01",
    "provider_id": "prov_johnson",
    "insurance_payer": "bcbs",
    "claim_amount": 120.00
  },
  {
    "claim_id": "CLM-2026-011",
    "diagnosis_code": "M25.5",
    "procedure_code": "99213",
    "rejection_reason": "Invalid diagnosis code for procedure",
    "rejection_date": "2026-11-13",
    "clinic_id": "clinic_austin_01",
    "provider_id": "prov_smith",
    "insurance_payer": "aetna",
    "claim_amount": 150.00
  },
  {
    "claim_id": "CLM-2026-012",
    "diagnosis_code": "I10",
    "procedure_code": "99214",
    "rejection_reason": null,
    "rejection_date": null,
    "clinic_id": "clinic_london_01",
    "provider_id": "prov_wilson",
    "insurance_payer": "bupa",
    "claim_amount": 200.00
  },
  {
    "claim_id": "CLM-2026-013",
    "diagnosis_code": "E11.9",
    "procedure_code": "90837",
    "rejection_reason": "Missing required modifier",
    "rejection_date": "2026-11-12",
    "clinic_id": "clinic_houston_01",
    "provider_id": "prov_johnson",
    "insurance_payer": "bcbs",
    "claim_amount": 120.00
  },
  {
    "claim_id": "CLM-2026-014",
    "diagnosis_code": "Z00.00",
    "procedure_code": "99214",
    "rejection_reason": null,
    "rejection_date": null,
    "clinic_id": "clinic_austin_01",
    "provider_id": "prov_brown",
    "insurance_payer": "aetna",
    "claim_amount": 200.00
  },
  {
    "claim_id": "CLM-2026-015",
    "diagnosis_code": "M79.3",
    "procedure_code": "99213",
    "rejection_reason": "Invalid diagnosis code for procedure",
    "rejection_date": "2026-11-11",
    "clinic_id": "clinic_austin_01",
    "provider_id": "prov_smith",
    "insurance_payer": "aetna",
    "claim_amount": 150.00
  },
  {
    "claim_id": "CLM-2026-016",
    "diagnosis_code": "K21.0",
    "procedure_code": "99214",
    "rejection_reason": null,
    "rejection_date": null,
    "clinic_id": "clinic_london_01",
    "provider_id": "prov_wilson",
    "insurance_payer": "bupa",
    "claim_amount": 200.00
  },
  {
    "claim_id": "CLM-2026-017",
    "diagnosis_code": "E11.9",
    "procedure_code": "90837",
    "rejection_reason": "Missing required modifier",
    "rejection_date": "2026-11-10",
    "clinic_id": "clinic_houston_01",
    "provider_id": "prov_johnson",
    "insurance_payer": "bcbs",
    "claim_amount": 120.00
  },
  {
    "claim_id": "CLM-2026-018",
    "diagnosis_code": "I10",
    "procedure_code": "99213",
    "rejection_reason": null,
    "rejection_date": null,
    "clinic_id": "clinic_austin_01",
    "provider_id": "prov_brown",
    "insurance_payer": "aetna",
    "claim_amount": 150.00
  },
  {
    "claim_id": "CLM-2026-019",
    "diagnosis_code": "M25.5",
    "procedure_code": "99214",
    "rejection_reason": "Invalid diagnosis code for procedure",
    "rejection_date": "2026-11-09",
    "clinic_id": "clinic_austin_01",
    "provider_id": "prov_smith",
    "insurance_payer": "aetna",
    "claim_amount": 200.00
  },
  {
    "claim_id": "CLM-2026-020",
    "diagnosis_code": "Z00.00",
    "procedure_code": "99213",
    "rejection_reason": null,
    "rejection_date": null,
    "clinic_id": "clinic_london_01",
    "provider_id": "prov_wilson",
    "insurance_payer": "bupa",
    "claim_amount": 150.00
  }
]
```

---

## 📊 Expected Output

When analyzed with the clinical-claims-analysis skill, this dataset should produce:

### Summary Statistics
- **Total claims:** 20
- **Rejected claims:** 10
- **Rejection rate:** 50% (note: this is artificially high for demonstration)
- **Total financial impact:** $1,650 (rejected claims only)

### Expected Patterns

#### Pattern 1: "Invalid diagnosis code for procedure"
- **Frequency:** 6 occurrences
- **Percentage of rejections:** 60%
- **Affected diagnosis codes:** M79.3, M25.5
- **Affected procedure codes:** 99213, 99214
- **Affected clinics:** clinic_austin_01
- **Affected providers:** prov_smith
- **Financial impact:** $1,050

#### Pattern 2: "Missing required modifier"
- **Frequency:** 4 occurrences
- **Percentage of rejections:** 40%
- **Affected diagnosis code:** E11.9
- **Affected procedure code:** 90837
- **Affected clinics:** clinic_houston_01
- **Affected providers:** prov_johnson
- **Financial impact:** $480

---

## ✅ Validation Checks

### Quick Validation (What to Verify)

1. ✅ **Pattern count:** Exactly 2 patterns identified
2. ✅ **Frequencies:** Pattern 1 has 6, Pattern 2 has 4
3. ✅ **Provider attribution:** smith → Pattern 1, johnson → Pattern 2
4. ✅ **Financial impact:** Total matches rejected amount ($1,530)
5. ✅ **No PII exposed:** Output has no patient names, DOBs, or SSNs
6. ✅ **Anonymized:** Only clinic_id and provider_id used (not names)
7. ✅ **Completion time:** < 5 seconds for 20 claims

---

## 🧪 How to Run This Test

### Method 1: Python
```python
import json
from skills.clinical_claims_analysis import analyze_claims

# Load example data
with open('examples/example-claim-analysis.md') as f:
    # Parse JSON from markdown...
    test_data = json.loads(claims_json)

# Run analysis
result = analyze_claims(test_data)

# Verify results
assert result['metadata']['total_claims_analyzed'] == 20
assert result['metadata']['rejected_claims'] == 10
assert result['metadata']['rejection_rate'] == 0.50
assert len(result['rejection_patterns']) == 2

print("✅ All checks passed!")
```

### Method 2: API
```bash
# Save sample claims to file
curl -X POST https://api.healthcore.com/skills/analyze-claims \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d @sample-claims.json \
  > result.json

# Verify output
cat result.json | jq '.metadata'
```

---

## 📝 Notes for Testers

- **Data is fictitious:** All diagnosis/procedure code combinations may not be medically valid
- **Patterns are deliberate:** Rejections are intentionally repeated for pattern detection
- **Anonymized:** No real patient names, real clinics, or real providers
- **Safe to share:** This example can be shared for training/documentation

---

**Example updated:** 2026-11-25
