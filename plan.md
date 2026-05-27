# Nyay AI - Digital Forensic Chain Ledger (DFCL) MVP Plan

## Project Overview
**Purpose**: Prevent evidence tampering in Indian judicial system using blockchain-inspired chain of custody tracking.

**Problem Statement**: Cases like Hathras and Unnao demonstrate how evidence tampering leads to injustice. This system ensures evidence integrity through cryptographic hashing and immutable custody tracking.

**Tech Stack**: HTML5, JavaScript (ES6+), Tailwind CSS, localStorage, Web Crypto API

---

## System Architecture (UPGRADED)

```
┌─────────────────────────────────────────────────────────────────────┐
│                     Nyay AI DFCL System (UPGRADED)                   │
├─────────────────────────────────────────────────────────────────────┤
│  Layer 1: DUAL REGISTRATION ENTRY PORTALS                            │
│  ├─ Portal A: Investigating Officer                                 │
│  │   ├─ Crime Scene Evidence Registration                           │
│  │   ├─ Photo Upload (File API)                                     │
│  │   ├─ GPS Auto-capture (Geolocation API)                          │
│  │   ├─ Timestamp (ISO 8601)                                        │
│  │   ├─ SHA-256 Hash Generation                                     │
│  │   └─ QR Code with Deep-Link URL                                  │
│  └─ Portal B: Medical Officer/Hospital                              │
│      ├─ MLC Trauma Report Registration                              │
│      ├─ Swab Logs & Medical Evidence                                │
│      ├─ Photo Upload (File API)                                     │
│      ├─ Timestamp (ISO 8601)                                        │
│      ├─ Separate SHA-256 Hash Generation                            │
│      └─ Separate QR Code with Deep-Link URL                         │
├─────────────────────────────────────────────────────────────────────┤
│  Layer 2: Chain of Custody                                           │
│  ├─ Transfer Tracking (Officer → Lab → Court)                       │
│  ├─ Custody Log with Timestamps                                     │
│  └─ Digital Signatures                                              │
├─────────────────────────────────────────────────────────────────────┤
│  Layer 3: STRICT TAMPER DETECTION (UPGRADED)                         │
│  ├─ Hash Verification (Lab Technician & Judge ONLY)                 │
│  ├─ Registered Hash vs Present Scan Hash Comparison                 │
│  ├─ Conditional Logic:                                              │
│  │   ├─ MATCH → Green Status UI + Unlock Lab Findings Form          │
│  │   └─ MISMATCH → isTampered: true + Backend Red Alert UI          │
│  └─ Audit Log (All verification attempts)                           │
├─────────────────────────────────────────────────────────────────────┤
│  Layer 4: SECURE WORKSPACE GATEWAY (UPGRADED)                        │
│  ├─ Mock Authentication Splash Screen                               │
│  ├─ Role-specific Credential Input:                                 │
│  │   ├─ Investigating Officer (Badge ID)                            │
│  │   ├─ Medical Officer (Doctor ID)                                 │
│  │   ├─ Lab Technician (Lab Tech ID)                                │
│  │   └─ Judge (Judge ID)                                            │
│  ├─ Conditional Dashboard Rendering (DOM-level isolation)           │
│  └─ NO cross-role access dropdown                                   │
├─────────────────────────────────────────────────────────────────────┤
│  Layer 5: REPORT GENERATOR (RESTRICTED ACCESS)                       │
│  ├─ Timeline Visualization                                          │
│  ├─ Evidence Summary                                                │
│  ├─ Chain of Custody Report                                         │
│  ├─ Integrity Verification Status                                   │
│  ├─ PDF Export (jsPDF)                                              │
│  └─ RESTRICTED: Judge Dashboard ONLY                                │
└─────────────────────────────────────────────────────────────────────┘
                                  │
                                  ▼
                        localStorage Database
```

---

## Data Structure Design (UPGRADED)

### Evidence Record - Investigating Officer Portal
```javascript
{
  id: "EV-2026-001234",
  type: "CRIME_SCENE_EVIDENCE",
  caseNumber: "CR-2026-MH-5678",
  description: "Physical evidence description",
  category: "Physical/Digital/Documentary/Biological",
  photo: "base64_encoded_image",
  photoHash: "sha256_hash_of_photo",
  location: {
    latitude: 19.0760,
    longitude: 72.8777,
    address: "Auto-resolved or manual"
  },
  timestamp: "2026-05-01T19:24:00.000Z",
  registeredBy: "Officer Name",
  officerId: "OFF-12345",
  evidenceHash: "sha256_combined_hash",
  qrCodeDeepLink: "https://nyay-ai.app/verify?id=EV-2026-001234&hash=abc123...",
  status: "Registered/In-Transit/At-Lab/In-Court",
  isTampered: false,
  chainOfCustody: []
}
```

### Medical Evidence Record - Medical Officer Portal
```javascript
{
  id: "MLC-2026-005678",
  type: "MEDICAL_LEGAL_CASE",
  caseNumber: "CR-2026-MH-5678",
  patientName: "Victim Name (Anonymized)",
  mlcNumber: "MLC/2026/5678",
  traumaDescription: "Detailed trauma report",
  swabLogs: [
    {
      swabId: "SWAB-001",
      bodyPart: "Right hand",
      timestamp: "2026-05-01T20:15:00.000Z",
      collectedBy: "Dr. Name"
    }
  ],
  photo: "base64_encoded_image",
  photoHash: "sha256_hash_of_photo",
  timestamp: "2026-05-01T20:00:00.000Z",
  registeredBy: "Dr. Medical Officer",
  doctorId: "DOC-67890",
  evidenceHash: "sha256_combined_hash",
  qrCodeDeepLink: "https://nyay-ai.app/verify?id=MLC-2026-005678&hash=def456...",
  status: "Registered/In-Transit/At-Lab/In-Court",
  isTampered: false,
  chainOfCustody: []
}
```

### Chain of Custody Entry
```javascript
{
  transferId: "TR-2026-001",
  evidenceId: "EV-2026-001234",
  fromPerson: "Officer A",
  fromRole: "Police Officer",
  toPerson: "Lab Technician B",
  toRole: "Forensic Lab",
  timestamp: "2026-05-02T10:30:00.000Z",
  location: "Forensic Lab, Mumbai",
  purpose: "DNA Analysis",
  signature: "digital_signature_hash",
  notes: "Sealed evidence bag intact"
}
```

---

## Implementation Phases

### Phase 1: Layer 1 - DUAL REGISTRATION PORTALS (UPGRADED)

#### Portal A: Investigating Officer Registration
**Files**: [`officer-registration.html`](officer-registration.html)

**Features**:
- Crime scene evidence registration form
- Responsive design with Tailwind CSS
- Photo upload with preview
- GPS auto-capture with fallback
- Auto-timestamp generation
- SHA-256 hash calculation
- QR code with deep-link URL format
- localStorage persistence
- Form validation

**User Flow**:
1. Investigating Officer opens registration portal
2. Enters case number and evidence details
3. Uploads crime scene photo (auto-hashed)
4. GPS auto-captured (or manual entry)
5. Clicks "Register Evidence"
6. System generates unique ID (EV-YYYY-XXXXXX)
7. Creates SHA-256 hash of combined evidence data
8. Generates QR code with deep-link: `https://nyay-ai.app/verify?id=EV-XXX&hash=XXX&type=crime`
9. Evidence saved to localStorage with `type: "CRIME_SCENE_EVIDENCE"`
10. Success modal displays QR code and evidence details

#### Portal B: Medical Officer Registration
**Files**: [`medical-registration.html`](medical-registration.html)

**Features**:
- MLC (Medico-Legal Case) trauma report registration
- Swab log entry system
- Medical evidence photo upload
- Auto-timestamp generation
- Separate SHA-256 hash calculation
- Separate QR code with deep-link URL
- localStorage persistence
- Medical-specific form validation

**User Flow**:
1. Medical Officer opens MLC registration portal
2. Enters patient details (anonymized) and MLC number
3. Documents trauma description and swab logs
4. Uploads medical evidence photo (auto-hashed)
5. Timestamp auto-generated
6. Clicks "Register MLC Evidence"
7. System generates unique ID (MLC-YYYY-XXXXXX)
8. Creates separate SHA-256 hash for medical evidence
9. Generates QR code with deep-link: `https://nyay-ai.app/verify?id=MLC-XXX&hash=XXX&type=medical`
10. Evidence saved to localStorage with `type: "MEDICAL_LEGAL_CASE"`
11. Success modal displays QR code and MLC details

**Libraries** (Both Portals):
- Tailwind CSS (CDN)
- QRCode.js (CDN)
- Web Crypto API (native)
- Geolocation API (native - Officer portal only)

---

### Phase 2: Layer 2 - Chain of Custody
**Files**: [`custody.html`](custody.html), [`custody.js`](custody.js)

**Features**:
- Evidence search by ID/QR scan
- Transfer form (from/to person, role, location)
- Custody log display
- Transfer history timeline
- Digital signature capture

**User Flow**:
1. Search evidence by ID
2. View current custody holder
3. Initiate transfer
4. Fill transfer details
5. Confirm transfer
6. Update custody chain
7. Generate transfer receipt

---

### Phase 3: Layer 3 - STRICT TAMPER DETECTION (UPGRADED)
**Files**: [`verify.html`](verify.html), [`verify.js`](verify.js)

**CRITICAL SECURITY UPGRADE**:
- **REMOVED**: "Verify Integrity" button from Investigating Officer dashboard
- **REMOVED**: "Verify Integrity" button from Medical Officer dashboard
- **RESTRICTED ACCESS**: Verification ONLY available to Lab Technician and Judge roles

**Features**:
- Hash verification interface (Lab Technician & Judge ONLY)
- Registered Hash vs Present Scan Hash comparison
- Conditional integrity logic with state management
- Tamper alert system with backend red alert UI
- Comprehensive audit log

**Verification Logic**:
```javascript
// Pseudo-code for verification logic
async function verifyEvidence(evidenceId) {
  const evidence = getEvidenceFromStorage(evidenceId);
  const registeredHash = evidence.evidenceHash;
  
  // Re-calculate hash from current evidence data
  const presentHash = await generateHash(evidence);
  
  if (registeredHash === presentHash) {
    // MATCH: Evidence is intact
    evidence.verificationStatus = "VERIFIED";
    evidence.isTampered = false;
    
    // Render GREEN status UI
    displayGreenStatusUI();
    
    // UNLOCK "Add Lab Findings" form (Lab Technician only)
    if (currentUserRole === "LAB_TECHNICIAN") {
      unlockLabFindingsForm();
    }
  } else {
    // MISMATCH: Evidence has been tampered
    evidence.isTampered = true;
    evidence.verificationStatus = "TAMPERED";
    
    // Trigger BACKEND RED ALERT UI
    triggerRedAlertUI();
    
    // Log tampering incident
    logTamperingIncident(evidenceId, registeredHash, presentHash);
    
    // LOCK all forms and notify authorities
    lockAllForms();
    notifyAuthorities(evidenceId);
  }
  
  // Save verification attempt to audit log
  saveVerificationAudit(evidenceId, currentUserRole, timestamp);
}
```

**User Flow - Lab Technician**:
1. Lab Technician logs into secure workspace
2. Selects evidence for verification
3. Clicks "Verify Integrity" button
4. System re-calculates evidence hash
5. Compares registered hash vs present hash
6. **IF MATCH**:
   - Display green status UI
   - Unlock "Add Lab Findings" submission form
   - Allow lab technician to proceed with analysis
7. **IF MISMATCH**:
   - Set `isTampered: true`
   - Display backend red alert UI
   - Lock all forms
   - Log tampering incident with timestamp
   - Notify system administrators

**User Flow - Judge**:
1. Judge logs into secure workspace
2. Reviews case evidence
3. Clicks "Verify Integrity" button
4. System performs hash comparison
5. **IF MATCH**: Display green verification status
6. **IF MISMATCH**: Display red alert with tampering details
7. Judge reviews audit log of all verification attempts

**Access Control Matrix**:
| Role | Can Verify Integrity? | Can View Verification Status? |
|------|----------------------|------------------------------|
| Investigating Officer | ❌ NO | ✅ YES (read-only) |
| Medical Officer | ❌ NO | ✅ YES (read-only) |
| Lab Technician | ✅ YES | ✅ YES |
| Judge | ✅ YES | ✅ YES |

---

### Phase 4: Layer 4 - SECURE WORKSPACE GATEWAY (UPGRADED)
**Files**: [`auth.html`](auth.html), [`auth.js`](auth.js), [`dashboard.html`](dashboard.html)

**CRITICAL SECURITY UPGRADE**:
- **REMOVED**: Open dropdown selector for cross-role access
- **IMPLEMENTED**: Secure Workspace Gateway with mock authentication
- **ENFORCED**: DOM-level isolation between role dashboards

**Features**:
- Mock authentication splash screen
- Role-specific credential input system
- Conditional dashboard rendering with DOM isolation
- Session management with localStorage
- Automatic logout on role mismatch

**Authentication Flow**:
```javascript
// Mock credentials for demonstration
const MOCK_CREDENTIALS = {
  INVESTIGATING_OFFICER: {
    badgeId: "OFF-12345",
    name: "Officer Rajesh Kumar"
  },
  MEDICAL_OFFICER: {
    doctorId: "DOC-67890",
    name: "Dr. Priya Sharma"
  },
  LAB_TECHNICIAN: {
    labTechId: "LAB-11223",
    name: "Lab Tech Amit Patel"
  },
  JUDGE: {
    judgeId: "JDG-99887",
    name: "Hon. Justice Meera Singh"
  }
};

function authenticateUser(role, credentialId) {
  // Validate credentials
  if (validateCredentials(role, credentialId)) {
    // Create session
    const session = {
      role: role,
      credentialId: credentialId,
      loginTime: new Date().toISOString(),
      sessionId: generateSessionId()
    };
    
    // Save to localStorage
    localStorage.setItem('nyai_user_session', JSON.stringify(session));
    
    // Render ONLY the authorized dashboard
    renderDashboard(role);
    
    // Remove all other dashboard components from DOM
    removeUnauthorizedDashboards(role);
    
    return true;
  }
  return false;
}
```

**Secure Workspace Gateway UI**:
1. User lands on authentication splash screen
2. Selects role from secure dropdown:
   - Investigating Officer
   - Medical Officer
   - Lab Technician
   - Judge
3. Enters role-specific credential:
   - Officer Badge ID (e.g., OFF-12345)
   - Doctor ID (e.g., DOC-67890)
   - Lab Tech ID (e.g., LAB-11223)
   - Judge ID (e.g., JDG-99887)
4. System validates credentials
5. On success: Render ONLY authorized dashboard
6. On failure: Display error and retry

**Dashboard Isolation Strategy**:
```javascript
function renderDashboard(role) {
  // Remove ALL dashboard components first
  document.getElementById('officer-dashboard')?.remove();
  document.getElementById('medical-dashboard')?.remove();
  document.getElementById('lab-dashboard')?.remove();
  document.getElementById('judge-dashboard')?.remove();
  
  // Render ONLY the authorized dashboard
  switch(role) {
    case 'INVESTIGATING_OFFICER':
      renderOfficerDashboard();
      break;
    case 'MEDICAL_OFFICER':
      renderMedicalDashboard();
      break;
    case 'LAB_TECHNICIAN':
      renderLabDashboard();
      break;
    case 'JUDGE':
      renderJudgeDashboard();
      break;
  }
}
```

**Role Permissions (UPDATED)**:
| Role | Register Evidence | Transfer Custody | Verify Integrity | Generate Report |
|------|------------------|------------------|------------------|-----------------|
| Investigating Officer | ✅ Crime Scene | ✅ YES | ❌ NO | ❌ NO |
| Medical Officer | ✅ MLC/Medical | ✅ YES | ❌ NO | ❌ NO |
| Lab Technician | ❌ NO | ✅ YES | ✅ YES | ❌ NO |
| Judge | ❌ NO | ❌ NO | ✅ YES | ✅ YES |

---

### Phase 5: Layer 5 - REPORT GENERATOR (RESTRICTED ACCESS)
**Files**: [`report.html`](report.html), [`report.js`](report.js)

**CRITICAL SECURITY UPGRADE**:
- **REMOVED**: "Generate Court Report" button from Investigating Officer dashboard
- **REMOVED**: "Generate Court Report" button from Medical Officer dashboard
- **REMOVED**: "Generate Court Report" button from Lab Technician dashboard
- **RESTRICTED ACCESS**: Report generation ONLY available to Judge role

**Features**:
- Timeline visualization (Judge dashboard only)
- Comprehensive evidence summary
- Complete chain of custody report
- Integrity verification status with tamper alerts
- PDF export functionality (using jsPDF)
- Court-ready formatting with legal headers
- Digital signature verification summary

**Report Sections**:
1. **Case Information**
   - Case number and filing date
   - Parties involved
   - Court jurisdiction
   
2. **Evidence Registry**
   - Crime scene evidence (from Investigating Officer)
   - Medical evidence (from Medical Officer)
   - Photo evidence with hashes
   - GPS location data
   
3. **Chain of Custody Timeline**
   - Complete transfer history
   - Timestamps and locations
   - Officer/technician signatures
   - Custody duration at each stage
   
4. **Integrity Verification Status**
   - Hash verification results
   - Tamper detection alerts (if any)
   - Verification audit trail
   - Lab technician findings
   
5. **Lab Analysis Results**
   - Forensic findings
   - DNA/fingerprint analysis
   - Expert opinions
   
6. **Digital Signatures & QR Codes**
   - All registered QR codes
   - Deep-link verification URLs
   - Cryptographic hash summary

**Access Control**:
```javascript
function generateCourtReport(caseNumber) {
  // Check if current user is Judge
  const session = JSON.parse(localStorage.getItem('nyai_user_session'));
  
  if (session.role !== 'JUDGE') {
    alert('Access Denied: Only Judges can generate court reports');
    return;
  }
  
  // Proceed with report generation
  const reportData = compileReportData(caseNumber);
  const pdfDocument = generatePDF(reportData);
  downloadPDF(pdfDocument, `Court_Report_${caseNumber}.pdf`);
}
```

**User Flow - Judge Only**:
1. Judge logs into secure workspace
2. Navigates to "Generate Court Report" section
3. Selects case number from dropdown
4. Reviews evidence summary preview
5. Clicks "Generate PDF Report"
6. System compiles all evidence, custody chain, and verification data
7. Generates court-ready PDF with legal formatting
8. Judge downloads PDF for court proceedings
9. System logs report generation in audit trail

---

## Technical Implementation Details

### SHA-256 Hashing Strategy
```javascript
async function generateHash(data) {
  const encoder = new TextEncoder();
  const dataBuffer = encoder.encode(data);
  const hashBuffer = await crypto.subtle.digest('SHA-256', dataBuffer);
  const hashArray = Array.from(new Uint8Array(hashBuffer));
  return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
}

// Combined evidence hash
const evidenceData = JSON.stringify({
  photo: photoBase64,
  location: gpsData,
  timestamp: isoTimestamp,
  description: description
});
const evidenceHash = await generateHash(evidenceData);
```

### localStorage Schema (UPGRADED)
```javascript
// Keys
- 'nyai_evidence_list': Array of all evidence IDs (both crime scene and medical)
- 'nyai_crime_evidence_list': Array of crime scene evidence IDs (EV-YYYY-XXXXXX)
- 'nyai_medical_evidence_list': Array of medical evidence IDs (MLC-YYYY-XXXXXX)
- 'nyai_evidence_{id}': Individual evidence record (crime scene)
- 'nyai_medical_{id}': Individual medical evidence record (MLC)
- 'nyai_custody_{id}': Custody chain for evidence
- 'nyai_user_session': Current user session with role and credentials
- 'nyai_audit_log': System audit trail
- 'nyai_verification_log_{id}': Verification attempts for specific evidence
- 'nyai_tamper_alerts': Array of tampered evidence IDs
- 'nyai_lab_findings_{id}': Lab analysis results for evidence
```

**Session Object Structure**:
```javascript
{
  role: "INVESTIGATING_OFFICER" | "MEDICAL_OFFICER" | "LAB_TECHNICIAN" | "JUDGE",
  credentialId: "OFF-12345" | "DOC-67890" | "LAB-11223" | "JDG-99887",
  name: "User Full Name",
  loginTime: "2026-05-26T17:00:00.000Z",
  sessionId: "unique_session_id",
  permissions: {
    canRegisterEvidence: boolean,
    canTransferCustody: boolean,
    canVerifyIntegrity: boolean,
    canGenerateReport: boolean
  }
}
```

### QR Code Content (UPGRADED - Deep-Link Format)
```javascript
// Crime Scene Evidence QR
"https://nyay-ai.app/verify?id=EV-2026-001234&hash=abc123def456&type=crime"

// Medical Evidence QR
"https://nyay-ai.app/verify?id=MLC-2026-005678&hash=def456abc789&type=medical"
```

**Deep-Link Benefits**:
- Direct navigation to case verification page
- Embedded evidence ID and hash for instant validation
- Type parameter for conditional rendering
- Mobile-friendly scanning experience

---

## Security Considerations (UPGRADED)

1. **Hash Integrity**: SHA-256 ensures any modification is detectable
2. **Immutable Chain**: Custody transfers append-only (no deletion)
3. **Timestamp Verification**: ISO 8601 format with timezone
4. **Photo Integrity**: Separate hash for photo data
5. **Audit Trail**: All actions logged with timestamp
6. **Role-based Access Control**: DOM-level isolation prevents unauthorized access
7. **Tamper Detection**: Automatic flagging with `isTampered: true` state
8. **Verification Restrictions**: Only Lab Technician and Judge can verify integrity
9. **Report Generation Restrictions**: Only Judge can generate court reports
10. **Deep-Link QR Codes**: Embedded verification URLs for instant validation
11. **Session Management**: Secure workspace gateway with credential validation
12. **Dual Registration Portals**: Separate evidence types with independent hashing

---

## MVP Limitations & Future Enhancements

### Current MVP Limitations
- localStorage (not suitable for production)
- No backend authentication
- No blockchain integration
- No real digital signatures
- Single-device access

### Future Enhancements
1. **Backend Integration**: Node.js + MongoDB/PostgreSQL
2. **Blockchain**: Ethereum/Hyperledger for immutability
3. **Mobile App**: React Native for field officers
4. **Biometric Auth**: Fingerprint/Face ID
5. **Cloud Storage**: AWS S3 for photos
6. **Real-time Sync**: WebSocket for multi-user
7. **Advanced Analytics**: ML for pattern detection
8. **Integration**: Court management systems

---

## Testing Strategy (UPGRADED)

### Unit Tests
- Hash generation accuracy (SHA-256)
- QR code generation with deep-link URLs
- GPS capture (Investigating Officer portal)
- Form validation (both registration portals)
- Role-based permission checks
- Tamper detection logic
- Session management

### Integration Tests
- **Dual Registration Flow**:
  - Crime scene evidence registration (Investigating Officer)
  - Medical evidence registration (Medical Officer)
- **Chain of Custody Flow**:
  - Evidence transfer between roles
  - Custody log updates
- **Verification Flow**:
  - Lab Technician verification with conditional logic
  - Judge verification and review
  - Tamper alert triggering
- **Report Generation Flow**:
  - Judge-only access validation
  - PDF generation with all evidence types

### User Acceptance Tests
- **Investigating Officer Workflow**:
  - Login with badge ID
  - Register crime scene evidence
  - Transfer custody to lab
  - View verification status (read-only)
- **Medical Officer Workflow**:
  - Login with doctor ID
  - Register MLC trauma report
  - Document swab logs
  - Transfer custody to lab
- **Lab Technician Workflow**:
  - Login with lab tech ID
  - Receive evidence from officers
  - Verify integrity (unlock lab findings form on match)
  - Add lab analysis results
  - Transfer to court
- **Judge Workflow**:
  - Login with judge ID
  - Review all evidence (crime + medical)
  - Verify integrity status
  - Generate comprehensive court report
  - Download PDF for proceedings

### Security Tests
- **Access Control**:
  - Verify DOM isolation between roles
  - Test unauthorized access attempts
  - Validate credential requirements
- **Tamper Detection**:
  - Simulate hash mismatch scenarios
  - Verify red alert UI triggering
  - Test form locking on tampering
- **Permission Enforcement**:
  - Verify integrity button hidden for officers
  - Report generation button hidden for non-judges
  - Cross-role action blocking

---

## Deployment Plan

### MVP Deployment (Static Hosting)
1. GitHub Pages (free)
2. Netlify (free tier)
3. Vercel (free tier)

### Production Deployment (Future)
1. Frontend: Vercel/Netlify
2. Backend: AWS EC2/Heroku
3. Database: MongoDB Atlas/AWS RDS
4. Storage: AWS S3
5. CDN: CloudFlare

---

## File Structure (UPGRADED)
```
nyay-ai-project/
├── index.html                      # Landing page / Secure Workspace Gateway
├── auth.html                       # Layer 4: Authentication Splash Screen
├── officer-registration.html       # Layer 1A: Investigating Officer Portal
├── medical-registration.html       # Layer 1B: Medical Officer Portal
├── officer-dashboard.html          # Investigating Officer Dashboard
├── medical-dashboard.html          # Medical Officer Dashboard
├── lab-dashboard.html              # Lab Technician Dashboard
├── judge-dashboard.html            # Judge Dashboard
├── custody.html                    # Layer 2: Chain of Custody
├── verify.html                     # Layer 3: Tamper Detection (Lab & Judge ONLY)
├── report.html                     # Layer 5: Report Generator (Judge ONLY)
├── plan.md                         # This file (Architecture Blueprint)
├── README.md                       # Project documentation
├── assets/
│   ├── css/
│   │   ├── custom.css              # Additional styles
│   │   ├── officer.css             # Officer-specific styles
│   │   ├── medical.css             # Medical-specific styles
│   │   ├── lab.css                 # Lab-specific styles
│   │   └── judge.css               # Judge-specific styles
│   └── js/
│       ├── auth.js                 # Authentication & Session Management
│       ├── evidence.js             # Evidence management (crime scene)
│       ├── medical-evidence.js     # Medical evidence management
│       ├── custody.js              # Custody tracking
│       ├── verify.js               # Verification logic (restricted)
│       ├── report.js               # Report generation (Judge only)
│       ├── hash-utils.js           # SHA-256 hashing utilities
│       ├── qr-generator.js         # QR code with deep-link generation
│       └── permissions.js          # Role-based permission enforcement
└── docs/
    ├── user-guide.md               # User documentation
    ├── officer-guide.md            # Investigating Officer guide
    ├── medical-guide.md            # Medical Officer guide
    ├── lab-guide.md                # Lab Technician guide
    └── judge-guide.md              # Judge guide
```

**Key File Changes**:
- **Dual Registration Portals**: Separate HTML files for crime scene and medical evidence
- **Role-specific Dashboards**: Individual dashboard files with DOM isolation
- **Restricted Access Files**: `verify.html` and `report.html` with role checks
- **Modular JavaScript**: Separate files for different evidence types and permissions
- **Role-specific CSS**: Custom styling for each user role

---

## Success Metrics

1. **Evidence Registration**: < 2 minutes per evidence
2. **Hash Generation**: < 1 second
3. **QR Code Generation**: < 1 second
4. **Custody Transfer**: < 1 minute
5. **Verification**: < 5 seconds
6. **Report Generation**: < 10 seconds

---

## Next Steps (UPDATED)

1. ✅ Create upgraded plan.md with all architectural changes
2. ⏳ Build Secure Workspace Gateway (auth.html)
3. ⏳ Build Layer 1A: Investigating Officer Registration Portal
4. ⏳ Build Layer 1B: Medical Officer Registration Portal
5. ⏳ Implement role-specific dashboards with DOM isolation
6. ⏳ Build Layer 2: Chain of Custody tracking
7. ⏳ Build Layer 3: Strict Tamper Detection (Lab & Judge only)
8. ⏳ Build Layer 5: Report Generator (Judge only)
9. ⏳ Implement permission enforcement across all layers
10. ⏳ Conduct comprehensive security testing
11. ⏳ Deploy MVP
12. ⏳ Gather feedback for improvements

---

## UPGRADE SUMMARY

### What Changed in This Architecture Upgrade?

#### 🔴 LAYER 1 - DUAL REGISTRATION ENTRY
**Before**: Single evidence registration portal
**After**: TWO independent portals
- **Portal A**: Investigating Officer (Crime Scene Evidence)
- **Portal B**: Medical Officer (MLC Trauma Reports, Swab Logs)
- **QR Code Fix**: Deep-link URL format instead of plain text
  - Old: `EV-2026-001234|hash123`
  - New: `https://nyay-ai.app/verify?id=EV-2026-001234&hash=abc123&type=crime`

#### 🔴 LAYER 3 - STRICT TAMPER DETECTION
**Before**: All roles could verify integrity
**After**: RESTRICTED to Lab Technician and Judge ONLY
- **REMOVED**: "Verify Integrity" button from Officer dashboards
- **ADDED**: Conditional logic with state management
  - **MATCH**: Green UI + Unlock Lab Findings Form
  - **MISMATCH**: `isTampered: true` + Backend Red Alert UI

#### 🔴 LAYER 4 - SECURE WORKSPACE GATEWAY
**Before**: Open dropdown for role selection
**After**: Mock authentication with credential validation
- **REMOVED**: Cross-role access dropdown
- **ADDED**: Secure splash screen with role-specific credentials
  - Investigating Officer: Badge ID
  - Medical Officer: Doctor ID
  - Lab Technician: Lab Tech ID
  - Judge: Judge ID
- **ENFORCED**: DOM-level isolation (unauthorized dashboards removed from DOM)

#### 🔴 LAYER 5 - REPORT GENERATION CLEANUP
**Before**: All roles could generate reports
**After**: RESTRICTED to Judge ONLY
- **REMOVED**: "Generate Court Report" button from Officer and Lab dashboards
- **ENFORCED**: Judge-only access with session validation

### Security Enhancements
1. ✅ DOM-level isolation prevents unauthorized access
2. ✅ Role-based permission enforcement at function level
3. ✅ Tamper detection with automatic flagging
4. ✅ Deep-link QR codes for instant verification
5. ✅ Session management with credential validation
6. ✅ Separate evidence types with independent hashing
7. ✅ Audit trail for all verification attempts
8. ✅ Form locking on tampering detection

### Breaking Changes
- Old `index.html` needs to be split into `officer-registration.html` and `medical-registration.html`
- Authentication flow now requires credential input
- Verification and report generation require role checks
- Dashboard components must be conditionally rendered based on role

---

## Contact & Support
**Project**: Nyay AI - Digital Forensic Chain Ledger (UPGRADED)
**Purpose**: Hackathon MVP with Enhanced Security
**Focus**: Evidence integrity and chain of custody for Indian judicial system
**Version**: 2.0 (Upgraded Architecture)
**Last Updated**: May 26, 2026