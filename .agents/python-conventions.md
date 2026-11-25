# Python Conventions — Code Style & Organization

**Purpose:** Define Python coding conventions for the HealthCore monorepo.

**Applies to:** All `.py` files in `services/`, `data/`, `agents/`, and other Python code.

---

## 📋 Naming Conventions

### Functions & Variables
```python
# ✅ GOOD
def analyze_claims_data(claims: list) -> dict:
    rejection_patterns = []
    total_claims_processed = 0
    return {"patterns": rejection_patterns, "total": total_claims_processed}

# ❌ BAD
def AnalyzeClaimsData(Claims: list) -> dict:  # PascalCase for functions
    rejectionPatterns = []  # camelCase for variables
    TOTAL_CLAIMS = 0  # UPPER_SNAKE for non-constant
    return {}
```

**Rule:** `snake_case` for all functions and variables.

---

### Classes
```python
# ✅ GOOD
class ClaimsAnalyzer:
    def process_data(self):
        pass

# ❌ BAD
class claims_analyzer:  # lowercase for class
    def ProcessData(self):  # PascalCase for method
        pass
```

**Rule:** `PascalCase` for class names, `snake_case` for methods.

---

### Constants
```python
# ✅ GOOD
HIPAA_RETENTION_DAYS = 365
MAX_CLAIMS_BATCH_SIZE = 1000
DEFAULT_TIMEOUT_SECONDS = 30

# ❌ BAD
hipaa_retention_days = 365  # lowercase
MaxClaimsBatchSize = 1000  # PascalCase
```

**Rule:** `UPPER_SNAKE_CASE` for module-level constants only.

---

### Private Members
```python
# ✅ GOOD
class ClaimsProcessor:
    def __init__(self):
        self._internal_cache = {}  # Private, internal use only
        self.__very_private = None  # Very private (name mangling)
    
    def public_method(self):
        return self._get_cache_data()  # Can call private methods
    
    def _get_cache_data(self):
        return self._internal_cache

# ❌ BAD
class ClaimsProcessor:
    def __init__(self):
        self.internal_cache = {}  # Public name, but meant to be private
        self.get_cache = None  # Unclear intent
```

**Rule:** Prefix private members and methods with `_`. Use `__` only for name mangling.

---

## 📂 File & Folder Organization

### Module Structure
```
services/api/
├── routers/              # API endpoint definitions
│   ├── __init__.py
│   ├── clinical.py
│   ├── patient_access.py
│   └── revenue_cycle.py
├── models/               # Pydantic models, database schemas
│   ├── __init__.py
│   ├── clinical.py
│   ├── patient.py
│   └── claim.py
├── services/             # Business logic (data processing, algorithms)
│   ├── __init__.py
│   ├── claim_analyzer.py
│   └── patient_processor.py
├── utils/                # Shared utilities, helpers
│   ├── __init__.py
│   ├── validators.py
│   └── formatters.py
├── config.py             # Configuration (API keys, DB URLs, etc.)
├── main.py               # Application entry point
└── requirements.txt      # Python dependencies
```

**Rule:** Organize by responsibility, not by layer. Use folders when module gets > 5 files.

---

### File Naming
```python
# ✅ GOOD
claim_analyzer.py
patient_processor.py
hipaa_validator.py

# ❌ BAD
ClaimAnalyzer.py  # PascalCase for files
claim-analyzer.py  # Hyphens in filenames
ca.py  # Too abbreviated
```

**Rule:** `snake_case.py` for all Python files.

---

## 📥 Imports

### Order
```python
# ✅ GOOD
# 1. Standard library
import os
import json
from datetime import datetime
from typing import list, dict

# 2. Third-party libraries
import pandas as pd
from fastapi import APIRouter
from pydantic import BaseModel

# 3. Local imports
from .models import ClaimModel
from .services import claim_analyzer
from ..utils import validators

# ❌ BAD
# Mixed import order
from .models import ClaimModel
import pandas as pd
import os
from fastapi import APIRouter
from .services import claim_analyzer
```

**Rule:** Standard lib → Third-party → Local. Alphabetical within each group.

---

### Import Style
```python
# ✅ GOOD
import pandas as pd
from typing import Optional, List
from fastapi import APIRouter, HTTPException

# ⚠️ AVOID (unless necessary)
from module import *  # Wildcard imports hide what's used

# ❌ BAD
import pandas  # Then use pandas.DataFrame (verbose)
from fastapi import *  # Unclear what's imported
```

**Rule:** Be explicit. Avoid wildcard imports unless it's a known pattern (e.g., `from __future__ import annotations`).

---

## 📝 Docstrings

### Function Docstrings (Google style)
```python
def analyze_claims_for_rejection_patterns(claims_data: list[dict], 
                                         min_frequency: int = 5) -> dict:
    """
    Analyze clinical claims to identify rejection patterns.
    
    This function processes a list of claims and identifies patterns in
    rejections to help improve coding accuracy and reduce denials.
    
    Args:
        claims_data: List of claim dictionaries, each containing:
            - claim_id (str): Unique claim identifier
            - diagnosis_code (str): ICD-10 diagnosis code
            - procedure_code (str): CPT procedure code
            - rejection_reason (str or None): Reason for rejection if rejected
        min_frequency: Minimum number of occurrences to consider a pattern.
            Default is 5.
    
    Returns:
        Dictionary with the following structure:
        {
            "total_claims": int,
            "rejected_claims": int,
            "patterns": [
                {
                    "pattern": str,
                    "frequency": int,
                    "affected_codes": [str, ...]
                }
            ],
            "recommendations": [str, ...]
        }
    
    Raises:
        ValueError: If claims_data is empty or malformed
        TypeError: If claims_data is not a list
    
    Examples:
        >>> claims = [
        ...     {"claim_id": "C001", "diagnosis_code": "M79.3", ...},
        ...     {"claim_id": "C002", "diagnosis_code": "M79.3", ...}
        ... ]
        >>> result = analyze_claims_for_rejection_patterns(claims)
        >>> print(result["patterns"])
    """
    # Implementation
    pass
```

**Rule:** Always include Args, Returns, Raises, and Examples.

---

### Class Docstrings
```python
class ClaimsAnalyzer:
    """
    Analyze rejection patterns in medical claims.
    
    This class processes claim data and identifies common rejection patterns
    to help improve coding accuracy and revenue cycle performance.
    
    Attributes:
        min_frequency (int): Minimum pattern occurrences to report
        logger (Logger): Application logger
    
    Example:
        >>> analyzer = ClaimsAnalyzer(min_frequency=5)
        >>> result = analyzer.analyze(claims_data)
    """
```

---

### Module Docstrings
```python
# ✅ GOOD - at the top of claim_analyzer.py
"""
Claim analysis module for revenue cycle optimization.

This module provides tools to analyze rejection patterns in medical claims,
identify coding issues, and generate recommendations for improvement.

Classes:
    ClaimsAnalyzer: Main analysis class

Functions:
    analyze_claims_data(): Quick analysis without class instantiation
    
See Also:
    models.claim for data structures
    utils.validators for validation helpers
"""
```

---

## 🔄 Code Style

### Line Length
- Maximum: **100 characters** per line
- For long strings/URLs: use implicit line continuation or `\`

```python
# ✅ GOOD
result = analyze_claims(
    claims_data=large_dataset,
    min_frequency=5,
    output_format="json"
)

# ✅ GOOD - implicit continuation
long_string = (
    "This is a very long string that would exceed "
    "the 100 character limit if on one line"
)

# ❌ BAD - too long
result = analyze_claims(claims_data=large_dataset, min_frequency=5, output_format="json")
```

---

### Blank Lines
```python
# ✅ GOOD
# Two blank lines between top-level functions/classes
def function_one():
    pass


def function_two():
    pass


class MyClass:
    def method_one(self):
        pass
    
    def method_two(self):  # One blank line between methods
        pass
```

---

### Comments
```python
# ✅ GOOD - explain WHY, not WHAT
def process_claims(claims: list) -> list:
    # Filter out rejected claims first to avoid unnecessary processing
    # (improves performance for large datasets)
    active_claims = [c for c in claims if c["status"] != "rejected"]
    
    # Sort by date to process oldest claims first
    # This helps with audit trail consistency
    return sorted(active_claims, key=lambda x: x["date"])

# ❌ BAD - restates code
def process_claims(claims: list) -> list:
    active_claims = []  # Create a list
    for claim in claims:  # Iterate over claims
        if claim["status"] != "rejected":  # If status is not rejected
            active_claims.append(claim)  # Add to list
    return active_claims
```

**Rule:** Comment explains WHY, not WHAT. Code should be self-documenting.

---

## ⚠️ Data Sensitivity

### Never Do This With Patient Data
```python
# ❌ BAD - Logs patient names
def process_patient_claim(patient: dict, claim: dict):
    logger.info(f"Processing claim for {patient['name']}")  # HIPAA violation!
    return analyze_claim(claim)

# ❌ BAD - Exposes SSN in error
def validate_patient_id(ssn: str):
    if not is_valid(ssn):
        raise ValueError(f"Invalid SSN: {ssn}")  # SSN exposed in error!
    return ssn

# ✅ GOOD - Sanitize before logging
def process_patient_claim(patient: dict, claim: dict):
    logger.info(f"Processing claim for patient {patient['id']}")  # Only ID, anonymized
    return analyze_claim(claim)

# ✅ GOOD - Generic error messages
def validate_patient_id(ssn: str):
    if not is_valid(ssn):
        raise ValueError("Invalid patient identifier")  # No PII exposed
    return ssn
```

**Rule:** See [`data-handling.md`](./data-handling.md) for complete data sensitivity guidelines.

---

## 🧪 Testing Convention (When Tests Exist)

```python
# tests/test_claim_analyzer.py
import pytest
from services.claim_analyzer import analyze_claims_data

def test_analyze_claims_identifies_patterns():
    """Test that rejection patterns are correctly identified."""
    claims = [
        {"claim_id": "C1", "diagnosis_code": "M79.3", "rejection_reason": "Invalid code"},
        {"claim_id": "C2", "diagnosis_code": "M79.3", "rejection_reason": "Invalid code"},
    ]
    result = analyze_claims_data(claims)
    
    assert result["patterns"]
    assert result["patterns"][0]["pattern"] == "Invalid code"

def test_analyze_claims_empty_list_raises_error():
    """Test that empty claims list raises ValueError."""
    with pytest.raises(ValueError):
        analyze_claims_data([])
```

**Rule:** Test files in `tests/` folder, naming: `test_[module_name].py`

---

## 🔗 Related

- **Data Handling:** [`data-handling.md`](./data-handling.md)
- **Compliance:** [`compliance-rules.md`](./compliance-rules.md)
- **Tech Context:** [`../docs/TECHNICAL_CONTEXT.md`](../docs/TECHNICAL_CONTEXT.md)

---

**Last updated:** 2026-11-25  
**Review this:** Before writing Python code
