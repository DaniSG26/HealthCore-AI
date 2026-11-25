# Data Handling — Classification, Privacy, and Compliance

**Purpose:** Define how to classify, handle, and protect sensitive data in the HealthCore monorepo.

**Critical:** This document prevents HIPAA/GDPR violations. Read before touching ANY patient or user data.

---

## 📊 Data Classification

All data in HealthCore falls into one of these categories:

### 🔴 **High Sensitivity (PHI/PII)**

**What it is:**
- Patient names, dates of birth, SSNs, medical record numbers
- Diagnosis codes, procedure codes, medication lists
- Appointment history, clinical notes, lab results
- Home addresses, phone numbers, email addresses
- Insurance information

**Where it comes from:**
- Electronic Health Records (EHR)
- Appointment systems
- Patient registration forms
- Billing systems

**Handling rules:**
- ✅ Encrypt at rest (database encryption)
- ✅ Encrypt in transit (HTTPS, TLS)
- ✅ Audit log all access (who, when, what)
- ✅ Never log values (log keys only)
- ✅ Remove from error messages
- ✅ Restrict access by role (clinical staff only)
- ✅ Support data deletion (GDPR right to be forgotten)
- ❌ Never expose in URLs, query params, or unencrypted storage
- ❌ Never include in error messages sent to users
- ❌ Never store in plaintext logs

**Example code:**
```python
# ❌ BAD - Exposes PHI
def process_patient(patient_dict):
    logger.info(f"Processing patient: {patient_dict['name']}")  # Name exposed!
    logger.debug(f"SSN: {patient_dict['ssn']}")  # SSN exposed!
    return patient_dict

# ✅ GOOD - Sanitized logging
def process_patient(patient_dict):
    logger.info(f"Processing patient ID: {patient_dict['id']}")  # Only ID
    # SSN not logged at all
    return sanitize_before_response(patient_dict)

# ✅ GOOD - Error handling
def validate_ssn(ssn: str) -> bool:
    if not is_valid_ssn_format(ssn):
        raise ValueError("Invalid patient identifier")  # Generic message
    return True
```

---

### 🟠 **Medium Sensitivity (Operational Data)**

**What it is:**
- Appointment times, clinic locations, staff schedules
- Billing amounts, insurance coverage details (without PII)
- Department KPIs, operational metrics
- Anonymized patient counts

**Handling rules:**
- ✅ Store in regular database
- ✅ Normal database access control
- ✅ Audit log access (optional for high-volume)
- ✅ Can appear in operational logs
- ✅ Can be used for dashboards/analytics
- ❌ Don't combine with 🔴 data without re-encryption

**Example code:**
```python
# ✅ GOOD - Medium sensitivity
def get_clinic_schedule():
    return {
        "clinic_id": "clinic_001",
        "date": "2026-11-25",
        "appointments_scheduled": 24,
        "staff_on_duty": 8
    }

# ✅ GOOD - Anonymized from PHI
def get_rejection_metrics():
    return {
        "clinic_id": "clinic_001",
        "total_claims": 1250,
        "rejection_rate": 0.14,
        "top_rejection_reasons": ["Invalid code", "Missing documentation"]
    }
    # No patient names, no specific diagnoses
```

---

### 🟡 **Low Sensitivity (Public/Aggregated)**

**What it is:**
- Company information, general statistics
- Aggregated metrics (e.g., "we process 10,000 claims/month")
- Public documentation, error codes
- Non-identifiable research data

**Handling rules:**
- ✅ Can be freely logged
- ✅ Can appear in error messages
- ✅ Can be shared publicly
- ✅ No encryption required

**Example code:**
```python
# ✅ GOOD - Public information
logger.info("API request to /metrics received")
logger.info("Processing queue contains 1,250 items")

raise ValueError("Invalid JSON in request body")  # Generic error OK
```

---

## 🔐 Practical Examples

### Scenario 1: Patient Registration
```python
# ✅ CORRECT HANDLING
def register_patient(form_data: dict) -> Patient:
    """
    Register a new patient (high PHI data).
    """
    # 1. Validate (but don't log values)
    if not validate_email(form_data['email']):
        raise ValueError("Invalid email format")  # No value exposed
    
    # 2. Encrypt before storage
    encrypted_patient = {
        'name_encrypted': encrypt(form_data['name']),  # Encrypt
        'email_encrypted': encrypt(form_data['email']),
        'ssn_encrypted': encrypt(form_data['ssn']),
        'created_at': datetime.now(),
        'created_by': current_user.id
    }
    
    # 3. Store in database
    db.patients.insert(encrypted_patient)
    
    # 4. Log anonymously
    logger.info(f"New patient registered with ID: {encrypted_patient.id}")  # ID only
    
    # 5. Audit trail (always for PHI)
    audit_log('patient_created', {
        'patient_id': encrypted_patient.id,
        'timestamp': datetime.now(),
        'user_id': current_user.id,
        'ip_address': request.remote_addr
    })
    
    return encrypted_patient
```

---

### Scenario 2: Claim Analysis
```python
# ✅ CORRECT HANDLING
def analyze_claims(claims: list[dict]) -> dict:
    """
    Analyze rejection patterns (medium sensitivity).
    Claims contain some PHI (patient ID) but analysis is on codes.
    """
    # 1. Sanitize: keep only non-PII columns
    sanitized_claims = []
    for claim in claims:
        sanitized_claims.append({
            'claim_id': claim['claim_id'],  # Safe to keep
            'diagnosis_code': claim['diagnosis_code'],  # Safe (code, not diagnosis name)
            'procedure_code': claim['procedure_code'],  # Safe
            'rejection_reason': claim.get('rejection_reason'),  # Safe (operational)
            # Exclude: patient_name, ssn, dob, medical_history
        })
    
    # 2. Analyze on sanitized data only
    patterns = identify_patterns(sanitized_claims)
    
    # 3. Log results (no PII exposed)
    logger.info(f"Analyzed {len(sanitized_claims)} claims")
    logger.info(f"Patterns found: {len(patterns)}")
    
    # 4. Return sanitized results
    return {
        'patterns': patterns,
        'total_analyzed': len(sanitized_claims),
        'timestamp': datetime.now()
    }
```

---

### Scenario 3: Error Handling
```python
# ❌ BAD - Exposes PHI in error
def get_patient_record(patient_id: str):
    try:
        return db.patients.find_by_id(patient_id)
    except DatabaseError as e:
        logger.error(f"Failed to load patient {patient_id}: {e}")  # What if patient_id is a name?
        raise HTTPException(status_code=500, detail=str(e))  # May expose system details

# ✅ GOOD - Sanitized error handling
def get_patient_record(patient_id: str):
    try:
        return db.patients.find_by_id(patient_id)
    except DatabaseError as e:
        # Log with just ID, no details
        logger.error(f"Database error retrieving record ID: {patient_id}")
        # Generic error to user
        raise HTTPException(status_code=500, detail="Unable to retrieve record")
    except Exception as e:
        logger.error(f"Unexpected error: {type(e).__name__}")  # Type only, no message
        raise HTTPException(status_code=500, detail="An unexpected error occurred")
```

---

## 🔍 Audit & Access Control

### Audit Logging (Required for All 🔴 Data Access)

**What to log:**
- Who accessed the data (user ID)
- When they accessed it (timestamp)
- What data they accessed (record ID, not values)
- What action they performed (view, edit, delete)
- From where (IP address, system)

**Code example:**
```python
def view_patient_record(patient_id: str, user: User) -> dict:
    """
    Retrieve a patient record (high PHI sensitivity).
    """
    # Verify access permissions
    if not user.can_access_patient(patient_id):
        logger.warning(f"Unauthorized access attempt by user {user.id}")
        raise PermissionError("Not authorized")
    
    # Retrieve record
    record = db.patients.find_by_id(patient_id)
    
    # Mandatory audit log
    audit_log({
        'action': 'patient_record_viewed',
        'patient_id': patient_id,  # ID, not name
        'user_id': user.id,
        'timestamp': datetime.now(),
        'ip_address': request.remote_addr,
        'purpose': 'clinical_review'  # Optional: why was it accessed?
    })
    
    logger.info(f"Patient record accessed by user {user.id}")  # Log ID, not PII
    
    return record
```

---

### Access Control Rules

| Data Class | Who Can Access | Conditions |
|-----------|---------------|-------------|
| 🔴 PHI (Patient data) | Clinical staff only | Only own clinic's patients |
| 🔴 PHI (Billing) | Billing staff + admin | Only own jurisdiction |
| 🟠 Operational | Department heads | Own department |
| 🟡 Public | Everyone | Unrestricted |

---

## 🗑️ Data Deletion & Right to be Forgotten (GDPR)

**Requirement:** When a patient requests deletion, all 🔴 data must be removed.

```python
# ✅ CORRECT - Implements GDPR right to be forgotten
def delete_patient_data(patient_id: str, requesting_user: User) -> bool:
    """
    Delete all patient data (GDPR compliance).
    """
    # Verify access (only admin or the patient)
    if requesting_user.id != patient_id and not requesting_user.is_admin:
        raise PermissionError("Not authorized")
    
    # Find all records
    records_to_delete = {
        'patient_record': db.patients.find_by_id(patient_id),
        'appointments': db.appointments.find_by_patient(patient_id),
        'claims': db.claims.find_by_patient(patient_id),
        'clinical_notes': db.notes.find_by_patient(patient_id)
    }
    
    # Soft delete (mark as deleted, keep for audit)
    for record_type, records in records_to_delete.items():
        for record in records:
            db[record_type].update(record.id, {'deleted_at': datetime.now()})
    
    # Mandatory audit trail
    audit_log({
        'action': 'patient_data_deleted',
        'patient_id': patient_id,
        'requested_by': requesting_user.id,
        'timestamp': datetime.now(),
        'records_deleted': len(records_to_delete)
    })
    
    logger.info(f"Patient data deletion completed for ID: {patient_id}")
    
    return True
```

---

## 🚫 Never Do This

| ❌ Action | Why | What to Do Instead |
|----------|-----|-------------------|
| Log patient names | HIPAA violation | Log patient ID only |
| Store SSN in plaintext | Security breach | Encrypt with encryption key |
| Send PHI via email | Unencrypted transmission | Use secure API + TLS |
| Expose error details to user | May leak system info | Generic error message |
| Copy production data to dev | Unnecessary exposure | Use sanitized datasets |
| Hardcode credentials with PHI | Data exposure in source | Use environment variables |
| Share patient data across borders | GDPR violation | Implement regional access control |

---

## ✅ Checklist: Data Handling

Before writing code that touches patient data:

- ✅ Classified the data (🔴 / 🟠 / 🟡)
- ✅ Determined access control (who can see it)
- ✅ Decided on encryption (at rest, in transit)
- ✅ Planned audit logging (what to track)
- ✅ Considered error handling (no PII in errors)
- ✅ Reviewed for GDPR compliance (deletion, access)
- ✅ Sanitized any logging (no values, only IDs)
- ✅ Tested with fake data first (not production)

---

## 🔗 Related

- **Compliance Rules:** [`compliance-rules.md`](./compliance-rules.md)
- **Python Conventions:** [`python-conventions.md`](./python-conventions.md#-data-sensitivity)
- **TypeScript Conventions:** [`typescript-conventions.md`](./typescript-conventions.md#-data-sensitivity-in-frontend)
- **Tech Context:** [`../docs/TECHNICAL_CONTEXT.md`](../docs/TECHNICAL_CONTEXT.md#-data-handling--compliance)

---

**Last updated:** 2026-11-25  
**Critical Rule:** Never expose PHI in logs, errors, or non-audit contexts
