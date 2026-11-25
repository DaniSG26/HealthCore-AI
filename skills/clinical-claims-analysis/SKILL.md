# Clinical Claims Analysis Skill

**Skill ID:** `clinical-claims-analysis`  
**Version:** 1.0  
**Status:** Active  
**Last updated:** 2026-11-25

---

## 📋 Purpose & Overview

This skill analyzes patterns in medical claim rejections to identify coding errors, operational inefficiencies, and opportunities for improvement.

**Primary goal:** Reduce the 14% claim rejection rate (current) toward the industry standard of 5-8%.

**Use when:**
- You need to identify why claims are being rejected
- You want to find patterns across multiple claims
- You need to recommend coding corrections
- You want to predict which claims are at high risk
- You need to generate compliance/audit reports

---

## 🎯 Skill Inputs

### Required Input
**Format:** CSV or JSON  
**Location:** Any accessible data source (database, file, API)

**Required columns:**
```
claim_id              # Unique claim identifier (string)
diagnosis_code        # ICD-10 code (string, e.g., "M79.3")
procedure_code        # CPT code (string, e.g., "99213")
rejection_reason      # Reason for rejection if rejected (string or null)
rejection_date        # Date of rejection if rejected (date or null)
clinic_id             # Which clinic submitted claim (string)
provider_id           # Which provider coded it (string)
insurance_payer       # Insurance company (string)
claim_amount          # Billed amount (number)
```

**Example:**
```json
{
  "claim_id": "CLM-2026-001",
  "diagnosis_code": "M79.3",
  "procedure_code": "99213",
  "rejection_reason": "Invalid diagnosis code for procedure",
  "rejection_date": "2026-11-20",
  "clinic_id": "clinic_austin_01",
  "provider_id": "prov_dr_reid",
  "insurance_payer": "aetna",
  "claim_amount": 150.00
}
```

### Optional Input
```
processing_date       # When claim was processed (helps with trends)
rework_attempts       # How many times reworked (number)
days_to_rejection     # How long before rejection (number)
```

---

## 📊 Skill Outputs

### Standard Output (Always Provided)

```json
{
  "analysis_id": "ana_20261125_001",
  "timestamp": "2026-11-25T14:30:00Z",
  "metadata": {
    "total_claims_analyzed": 1250,
    "rejected_claims": 175,
    "rejection_rate": 0.14,
    "date_range": {
      "start": "2026-11-01",
      "end": "2026-11-25"
    },
    "clinics_included": ["clinic_austin_01", "clinic_houston_01"],
    "analysis_confidence": 0.95
  },
  
  "rejection_patterns": [
    {
      "pattern_id": "PAT_001",
      "pattern_name": "Invalid diagnosis code for procedure",
      "frequency": 45,
      "percentage_of_rejections": 25.7,
      "affected_diagnosis_codes": ["M79.3", "M25.5"],
      "affected_procedure_codes": ["99213", "99214"],
      "affected_clinics": ["clinic_austin_01"],
      "affected_providers": ["prov_dr_reid", "prov_dr_smith"],
      "average_claim_amount": 150.00,
      "financial_impact": 6750.00,
      "trend": "stable"
    },
    {
      "pattern_id": "PAT_002",
      "pattern_name": "Missing required modifier",
      "frequency": 32,
      "percentage_of_rejections": 18.3,
      "affected_diagnosis_codes": ["E11.9"],
      "affected_procedure_codes": ["90837"],
      "affected_clinics": ["clinic_houston_01"],
      "affected_providers": ["prov_dr_johnson"],
      "average_claim_amount": 120.00,
      "financial_impact": 3840.00,
      "trend": "increasing"
    }
  ],
  
  "high_risk_coding_combinations": [
    {
      "diagnosis_code": "M79.3",
      "procedure_code": "99213",
      "historical_rejection_rate": 0.32,
      "risk_level": "high",
      "recommendation": "Verify modifier -25 is included; consider requiring pre-auth"
    }
  ],
  
  "provider_performance": [
    {
      "provider_id": "prov_dr_reid",
      "total_claims": 320,
      "rejected_claims": 48,
      "rejection_rate": 0.15,
      "top_rejection_patterns": ["Invalid diagnosis code for procedure"],
      "improvement_opportunity": "Coding training on diagnosis-procedure validation"
    }
  ],
  
  "clinic_performance": [
    {
      "clinic_id": "clinic_austin_01",
      "total_claims": 650,
      "rejected_claims": 91,
      "rejection_rate": 0.14,
      "trend": "stable",
      "comparison_to_network": "at_average"
    }
  ],
  
  "recommendations": [
    {
      "recommendation_id": "REC_001",
      "priority": "high",
      "action": "Implement pre-submission validation for diagnosis-procedure pairs",
      "expected_impact": "Reduce rejections by 25-45",
      "affected_patterns": ["PAT_001"],
      "implementation_effort": "2-3 days",
      "estimated_roi": 7500.00
    },
    {
      "recommendation_id": "REC_002",
      "priority": "medium",
      "action": "Provider training on modifier usage",
      "expected_impact": "Reduce rejections by 15-20",
      "affected_providers": ["prov_dr_johnson"],
      "implementation_effort": "1 day",
      "estimated_roi": 2400.00
    }
  ],
  
  "compliance_notes": {
    "hipaa_compliant": true,
    "phi_handling": "No patient names or identifiers included. Aggregated by clinic and provider ID only.",
    "data_sources": ["claims_database", "rejection_logs"],
    "audit_trail": "Available upon request"
  }
}
```

### Export Formats

The skill can generate output in:
- ✅ JSON (for API integration)
- ✅ CSV (for spreadsheets)
- ✅ PDF Report (for executive summary)
- ✅ Dashboard queries (for Tableau/Grafana)

---

## ✅ Acceptance Criteria (Verification)

The skill is considered **successful** when ALL of these criteria are met:

### Criteria 1: Input Validation ✓
- **Test:** Provide 100+ claims with valid structure
- **Expected:** Skill accepts data without errors
- **Failure:** "Input validation failed" or partial processing
- **Verification:** No warnings in audit log

### Criteria 2: Pattern Detection ✓
- **Test:** Provide claims with 3+ distinct rejection reasons
- **Expected:** Skill identifies all 3 patterns
- **Failure:** Misses patterns or creates false patterns
- **Verification:** Each pattern has ≥5 occurrences OR ≥10% of rejections

### Criteria 3: Accuracy ✓
- **Test:** Manually verify 10 random pattern instances
- **Expected:** 100% of instances match the pattern definition
- **Failure:** False positives (pattern misclassified)
- **Verification:** Spot-check results

### Criteria 4: Performance ✓
- **Test:** Analyze 1,000+ claims on standard server
- **Expected:** Complete in < 30 seconds
- **Failure:** Timeout or excessive memory usage
- **Verification:** Monitor processing time

### Criteria 5: HIPAA Compliance ✓
- **Test:** Scan output for patient names, dates of birth, SSNs, MRNs
- **Expected:** Zero instances of PHI
- **Failure:** Any patient identifiers found
- **Verification:** Manual or automated scanning

### Criteria 6: Practical Value ✓
- **Test:** Compare recommendations with known coding issues
- **Expected:** Recommendations match real problems or suggest new ones
- **Failure:** Generic or useless recommendations
- **Verification:** Review with coding department

### Criteria 7: Auditability ✓
- **Test:** Request audit trail of analysis
- **Expected:** Can identify all data used, transformations applied, conclusions reached
- **Failure:** No way to trace results
- **Verification:** Audit log includes: input data SHA, transformations, output results

---

## 🧪 How to Test This Skill

### Test Dataset (Sanitized Example)

See [`examples/example-claim-analysis.md`](./examples/example-claim-analysis.md) for a complete worked example with:
- 20 sample claims (all fictitious data)
- Expected pattern detection
- Validation results

### Quick Test (5 minutes)
```python
from skills.clinical_claims_analysis import analyze_claims

# Use example data
test_claims = load_example_data()  # From examples/

# Run analysis
result = analyze_claims(test_claims)

# Verify output
assert result['metadata']['total_claims_analyzed'] == 20
assert len(result['rejection_patterns']) >= 2
assert all(p['frequency'] >= 5 for p in result['rejection_patterns'])
print("✅ Quick test passed")
```

### Full Validation (Requires Real Data)
1. ✅ Verify on 100+ real claims from one clinic
2. ✅ Validate patterns with coding department
3. ✅ Check HIPAA compliance (no PII)
4. ✅ Measure performance (< 30 sec for 1000 claims)
5. ✅ Confirm recommendations are actionable

---

## 📚 Resources

**Reference materials included in `resources/` folder:**

- [`claim-fields-reference.md`](./resources/claim-fields-reference.md) — Explanation of each field
- `diagnosis-codes.txt` — Valid ICD-10 codes
- `procedure-codes.txt` — Valid CPT codes
- `rejection-reasons.txt` — Standard rejection codes

---

## 🔐 Compliance & Data Handling

### HIPAA Compliance
- ✅ No patient names, SSNs, DOB, MRN in output
- ✅ Aggregated by clinic/provider ID (anonymized)
- ✅ Audit trail maintained for all access
- ✅ Results can be shared with operations staff

### Data Security
- ✅ Input data must be encrypted in transit
- ✅ Temporary processing files deleted after analysis
- ✅ Results stored in encrypted database
- ✅ Access restricted to authorized staff

### Data Retention
- ✅ Analysis results kept for 2 years minimum
- ✅ Audit logs kept for 7 years (HIPAA requirement)
- ✅ Anonymized claims data for trending

---

## 🔧 Integration Examples

### Python
```python
from skills.clinical_claims_analysis import analyze_claims

# Load from database
claims = db.claims.find({"clinic_id": "clinic_austin_01"})

# Analyze
result = analyze_claims(claims)

# View patterns
for pattern in result['rejection_patterns']:
    print(f"{pattern['pattern_name']}: {pattern['frequency']} instances")
```

### API
```bash
curl -X POST https://api.healthcore.com/skills/analyze-claims \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d @claims.json
```

### Dashboard Query
```sql
SELECT 
  clinic_id,
  rejection_rate,
  top_patterns,
  recommendations
FROM skills_analysis.clinical_claims_latest
WHERE clinic_id = 'clinic_austin_01'
ORDER BY rejection_rate DESC
```

---

## 🚀 When to Use (Decision Tree)

```
Do you need to analyze claims?
├─ Yes → Continue
└─ No → This skill not needed

Do you have:
├─ ✅ Diagnosis codes? 
├─ ✅ Procedure codes?
├─ ✅ Rejection reasons?
└─ All three?
   ├─ YES → Use this skill ✓
   └─ NO → Skill will produce limited output

Do you need:
├─ Identify rejection patterns?       → Use skill
├─ Predict which claims will reject?  → Use different ML model
├─ Forecast future rejection trends?  → Use this skill (trending analysis)
└─ Real-time claim validation?        → Use different validation skill
```

---

## ⚠️ Limitations

**What this skill DOES:**
- ✅ Identify historical rejection patterns
- ✅ Aggregate by clinic/provider/payer
- ✅ Recommend coding improvements
- ✅ Calculate financial impact

**What this skill DOES NOT:**
- ❌ Diagnose medical errors (not a clinical tool)
- ❌ Make real-time predictions (historical analysis only)
- ❌ Override clinical decision-making
- ❌ Replace human coding review
- ❌ Handle unstructured notes (only structured codes)

---

## 📞 Support & Questions

**Question:** "Will this identify my specific patient's rejected claims?"  
**Answer:** No. Output is aggregated by clinic/provider. No individual patient data.

**Question:** "Can I see which exact diagnosis codes are rejected?"  
**Answer:** Yes. The skill provides specific diagnosis/procedure pairs with rejection rates.

**Question:** "How often should I run this?"  
**Answer:** Weekly for trend detection, or whenever you want to investigate a problem.

**Question:** "Who can see the results?"  
**Answer:** Billing staff, revenue cycle managers, clinic administrators. Not clinical staff or patients.

---

## 🔗 Related

- **Data Handling:** [`../../.agents/data-handling.md`](../../.agents/data-handling.md)
- **Compliance:** [`../../.agents/compliance-rules.md`](../../.agents/compliance-rules.md)
- **Business Context:** [`../../CONTEXT.md`](../../CONTEXT.md#-ciclo-de-ingresos-y-facturación)

---

## 📋 Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-11-25 | Initial skill definition, criteria, examples |

---

**Last updated:** 2026-11-25  
**Verification:** All 7 acceptance criteria must pass before use in production  
**Review:** Monthly for relevance and accuracy
