# Compliance Rules — HIPAA, UK GDPR, Audit Requirements

**Purpose:** Define non-negotiable compliance rules for HealthCore.

**Status:** CRITICAL — Breaking these rules can result in legal liability, fines, and loss of patient trust.

---

## 🚨 Golden Rules (Never Break These)

### Rule 1: Patient Data is Always Sensitive
Every piece of patient data is Protected Health Information (PHI) unless explicitly determined otherwise.

**What this means:**
- Assume PII/PHI until proven otherwise
- When in doubt, treat it as sensitive
- Default to encryption, not openness

**Code check:**
```python
# Always ask: "Is this patient-related data?"
# If YES → apply Rule 1 protections

data_type = "patient appointment time"  # Related to patient
# → Assume sensitive until proven otherwise
```

---

### Rule 2: Audit Logs Are Mandatory for PHI
Any access to patient data must be logged with: who, when, what, why.

**Non-negotiable fields:**
- `user_id` — who accessed
- `timestamp` — when
- `patient_id` or `record_id` — what (ID, not values)
- `action` — view/edit/delete
- `ip_address` — from where

**Code check:**
```python
# Every PHI access must include audit logging
def access_patient_data(patient_id, user_id):
    # ... retrieve data ...
    
    # Audit log is MANDATORY
    audit_log({
        'user_id': user_id,
        'timestamp': now(),
        'patient_id': patient_id,
        'action': 'viewed',
        'ip_address': request.ip
    })
    # Without this → non-compliant code
```

---

### Rule 3: No Patient Data in Error Messages
Error messages visible to users or in public logs must NOT contain PHI.

**Examples:**

```python
# ❌ NOT ALLOWED
except DatabaseError as e:
    return {"error": f"Failed to load patient {patient_name}: {e}"}  # Name exposed!

# ✅ ALLOWED
except DatabaseError as e:
    logger.error(f"DB error loading patient {patient_id}")  # ID, not name
    return {"error": "Unable to load record"}  # Generic to user
```

---

### Rule 4: Encryption for Data in Transit
All PHI transmission must use HTTPS/TLS. No exceptions.

**Code check:**
```python
# ❌ WRONG
requests.get("http://old-system.local/patients/123")  # HTTP!

# ✅ CORRECT
requests.get("https://secure-api.healthcore.com/patients/123")  # HTTPS
```

---

### Rule 5: Access Control by Role
Not everyone can see all patient data.

**Rule:**
- Clinical staff → own clinic's patients only
- Billing staff → own clinic's billing data only
- Admin → cross-clinic data only if authorized
- Other roles → no patient data

**Code check:**
```python
# Every PHI endpoint must check permissions
@app.get("/patients/{patient_id}")
def get_patient(patient_id: str, user: User):
    # ✅ Check if user can access
    if not user.can_access_patient(patient_id):
        raise PermissionError("Not authorized")  # Deny access
    
    # ✅ Then proceed
    return db.patients.find(patient_id)
```

---

### Rule 6: Support Data Deletion (GDPR)
Patients have the right to request deletion of their data. System must support it.

**Code check:**
```python
# Must implement delete endpoint
@app.delete("/patients/{patient_id}")
def delete_patient_data(patient_id: str, requesting_user: User):
    # Verify request is from patient or admin
    # Delete all associated records
    # Log deletion in audit trail
    # Return confirmation
```

---

## 📋 HIPAA Compliance (United States)

**Applies to:** All US operations (Texas, Florida, Georgia)

**Key requirements:**

### Encryption
- ✅ Encrypt PHI at rest (database encryption)
- ✅ Encrypt PHI in transit (HTTPS/TLS)
- ✅ Use strong encryption standards (AES-256)
- ❌ No plaintext storage of PHI

---

### Access Control
- ✅ User authentication (login required)
- ✅ Role-based access (not everyone sees everything)
- ✅ Activity audit logs (who accessed what, when)
- ✅ Session timeout (auto-logout after inactivity)
- ❌ No shared credentials

---

### Data Breach Notification
- ✅ Report breaches within 60 days
- ✅ Notify affected patients
- ✅ Document incident in audit trail
- ❌ Conceal breaches

---

### Minimum Necessary
- ✅ Only request the data you need
- ✅ Share only what's necessary
- ✅ Discard when no longer needed
- ❌ Over-collect data

---

### Documentation
- ✅ Document who can access what (create a matrix)
- ✅ Document encryption methods
- ✅ Document audit procedures
- ✅ Document incident response plan

---

## 🇬🇧 UK GDPR Compliance (United Kingdom)

**Applies to:** All UK operations (London, Manchester)

**Key requirements (beyond HIPAA):**

### Lawful Basis
- ✅ Patient consent (must be explicit)
- ✅ Performance of contract (appointment management)
- ✅ Legal obligation (healthcare law)
- ❌ No processing without basis

---

### Right to Access
- ✅ Patients can request their data (data subject access request)
- ✅ Provide within 30 days
- ✅ Compile from all systems
- ✅ Free of charge

**Code check:**
```python
# Must implement DSAR (Data Subject Access Request) endpoint
@app.post("/gdpr/data-access-request")
def request_data_access(patient_id: str):
    """
    Compile all patient data for GDPR access request.
    Must include: appointments, medical records, billing, communication logs.
    """
    all_patient_data = compile_from_all_systems(patient_id)
    return all_patient_data  # Downloadable format
```

---

### Right to Deletion
- ✅ Patients can request deletion ("right to be forgotten")
- ✅ Delete within 30 days (some exceptions allowed)
- ✅ Notify other parties if shared
- ✅ Keep audit trail of deletion

---

### Data Processing Agreement (DPA)
- ✅ All third-party services must have a DPA
- ✅ Document data flows
- ✅ Define data retention periods
- ❌ No processing without written agreement

---

## 🏥 Healthcare-Specific Compliance

### Clinical Accuracy
- ✅ System records must be accurate
- ✅ Clinical staff responsible for accuracy
- ✅ Audit trail shows who changed what, when
- ✅ No retroactive deletion (medical legal records)

**Rule:** Clinical notes are immutable. If error found, create an amendment note.

---

### Informed Consent
- ✅ Patients must consent to data use
- ✅ Document consent in audit trail
- ✅ Respect opt-out choices
- ✅ Clear communication about data use

---

### Mandatory Reporting
- ✅ Some patient data may be required to report to authorities
- ✅ Document mandatory disclosures
- ✅ Notify patient if legally required
- ❌ Over-disclose data

---

## ⚠️ Prohibited Actions

| Action | Consequence | Alternative |
|--------|-----------|-------------|
| Store passwords in plaintext | HIPAA/GDPR violation | Use bcrypt/argon2 hashing |
| Log patient names | HIPAA violation | Log patient ID only |
| Share patient data across borders without agreement | GDPR violation | Use data transfer agreements |
| Retain data longer than needed | GDPR violation | Implement data retention schedule |
| Process data without consent | GDPR violation | Obtain explicit consent first |
| Use data for different purpose than stated | GDPR violation | Only use as consented |
| Hardcode API keys in code | Security breach | Use environment variables |

---

## ✅ Compliance Checklist

Before code is production-ready:

**HIPAA/GDPR:**
- ✅ All PHI is encrypted (at rest and in transit)
- ✅ Audit logs record all PHI access
- ✅ Access control is enforced by role
- ✅ Error messages don't expose PHI
- ✅ Deletion is supported

**Data Quality:**
- ✅ Patient data is accurate
- ✅ Audit trail shows who changed what
- ✅ No retroactive deletion of clinical records

**Documentation:**
- ✅ Data flow documented (where data goes)
- ✅ Access matrix defined (who can see what)
- ✅ Retention schedule defined (how long kept)
- ✅ Incident response plan exists

**Third-Party:**
- ✅ All vendor services have DPA/BAA
- ✅ Data flows to vendors are documented
- ✅ Vendors are HIPAA/GDPR compliant

---

## 🔍 Audit & Evidence

### Maintaining Audit Trail
```python
# Example audit log structure
audit_record = {
    'id': unique_id(),
    'timestamp': datetime.now(),
    'user_id': current_user.id,
    'user_role': current_user.role,  # clinical, billing, admin
    'action': 'patient_record_viewed',  # viewed/edited/deleted
    'resource_id': patient_id,  # What was accessed (ID, not values)
    'details': {
        'ip_address': request.remote_addr,
        'session_id': session.id,
        'changes_made': [...]  # What was modified
    },
    'status': 'success'  # or 'failure' (failed access attempt)
}

db.audit_logs.insert(audit_record)
```

### Retention Period
- ✅ Keep audit logs for **minimum 6 years** (HIPAA requirement)
- ✅ Longer if required by law
- ✅ Make them immutable (can't be edited/deleted)

---

## 🆘 If You Find a Violation

**Step 1:** Stop the operation immediately (don't proceed)

**Step 2:** Log it internally (document what happened)

**Step 3:** Escalate to compliance officer (Claire Whitfield in CONTEXT.md)

**Step 4:** Don't delete evidence (audit logs are needed)

**Step 5:** Assess scope (how many records affected)

**Example:**
```
Violation found: Patient name logged in error message

Steps taken:
1. Removed error message from production
2. Reviewed logs for instances
3. Notified compliance officer
4. Planned code fix
5. Reviewed access logs to see who was affected
```

---

## 🔗 Related

- **Data Handling:** [`data-handling.md`](./data-handling.md)
- **Tech Context:** [`../docs/TECHNICAL_CONTEXT.md`](../docs/TECHNICAL_CONTEXT.md#-data-handling--compliance)
- **Business Context:** [`../CONTEXT.md`](../CONTEXT.md)

---

**Last updated:** 2026-11-25  
**Review this:** Before writing ANY code that handles patient data  
**Questions:** Ask compliance officer (Claire Whitfield) if unsure
