# AGENTS.md

This file provides guidance to agents when working with code in this repository.

## Project Context
Nyay AI - Digital Forensic Chain Ledger for Indian judicial system. Prevents evidence tampering through cryptographic hashing and immutable custody tracking.

## Non-Obvious Technical Details

### localStorage Schema (Critical)
- Evidence IDs use specific prefixes: `EV-YYYY-XXXXXX` (crime scene), `MLC-YYYY-XXXXXX` (medical)
- Storage keys follow pattern: `nyai_evidence_{id}`, `nyai_medical_{id}`, `nyai_custody_{id}`
- Session stored as: `nyai_user_session` with role and credential validation
- Tamper alerts tracked separately: `nyai_tamper_alerts` array
- Lab findings: `nyai_lab_findings_{id}` (append-only)

### Role-Based Access Control (Zero-Trust)
**Critical**: Permission checks happen at TWO levels:
1. Before opening modal/form (UI level)
2. Before executing action (function level)

Role ID formats are MANDATORY:
- Officer: `OFF-XXXXX`
- Lab Tech: `LAB-XXXXX`
- Judge: `JDG-XXXXX`
- Victim: `VIC-XXXXX`

**Non-obvious restrictions**:
- Victims CANNOT see SHA-256 hashes (privacy protected)
- Victims CANNOT see internal police notes
- Victims MUST provide valid Case ID to view anything
- Lab findings form ONLY unlocks after successful hash verification
- Report generation restricted to Judge role ONLY (not in other dashboards)

### Hash Verification Logic (Critical State Management)
```javascript
// MATCH: Green UI + unlock lab findings form
evidence.verificationStatus = "VERIFIED";
evidence.isTampered = false;

// MISMATCH: Red alert + lock all forms
evidence.isTampered = true;
evidence.verificationStatus = "TAMPERED";
```

**Non-obvious**: Tampered evidence CANNOT be transferred (immutability rule enforced in transfer function).

### QR Code Format (Deep-Link URLs)
NOT plain text - must be full URLs:
- Crime: `https://nyay-ai.app/verify?id=EV-XXX&hash=XXX&type=crime`
- Medical: `https://nyay-ai.app/verify?id=MLC-XXX&hash=XXX&type=medical`

### DOM Isolation Strategy
When rendering dashboards, MUST remove unauthorized dashboard components from DOM entirely:
```javascript
document.getElementById('officer-dashboard')?.remove();
document.getElementById('medical-dashboard')?.remove();
// etc. - then render only authorized dashboard
```

### Immutability Rules (Append-Only)
- Evidence data CANNOT be edited (only appended)
- Chain of custody is append-only
- Lab findings are append-only
- Tampered evidence blocks all modifications

## Code Style (From .cursorrules)

### Security Principles
1. Least Privilege: Each role has minimum necessary permissions
2. Separation of Duties: No single role can perform all actions
3. Privacy by Design: Victim data protected by default
4. Immutable Records: Evidence integrity maintained through cryptographic hashing

### Function Naming
- Use `checkPermission('action_name')` before every action
- Use `blockUnauthorizedAction('Action Name')` for access denial
- Hash generation: `generateHash(data)` returns SHA-256
- Session validation: Check `nyai_user_session` from localStorage

### Error Messages Format
```
🚫 ACCESS DENIED

Role: [Role Name]
Action: [Action Attempted]

You do not have permission to perform this action.
```

## Testing Notes
- No build system - pure HTML/JS/CSS
- Test by opening HTML files directly in browser
- localStorage persists between sessions (clear manually if needed)
- Role switching requires clearing session: `localStorage.removeItem('nyai_user_session')`