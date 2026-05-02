# Nyay AI - Digital Forensic Chain Ledger (DFCL) MVP Plan

## Project Overview
**Purpose**: Prevent evidence tampering in Indian judicial system using blockchain-inspired chain of custody tracking.

**Problem Statement**: Cases like Hathras and Unnao demonstrate how evidence tampering leads to injustice. This system ensures evidence integrity through cryptographic hashing and immutable custody tracking.

**Tech Stack**: HTML5, JavaScript (ES6+), Tailwind CSS, localStorage, Web Crypto API

---

## System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Nyay AI DFCL System                      │
├─────────────────────────────────────────────────────────────┤
│  Layer 1: Evidence Registration                              │
│  ├─ Photo Upload (File API)                                 │
│  ├─ GPS Auto-capture (Geolocation API)                      │
│  ├─ Timestamp (ISO 8601)                                    │
│  ├─ SHA-256 Hash Generation                                 │
│  └─ QR Code Generation (evidence ID)                        │
├─────────────────────────────────────────────────────────────┤
│  Layer 2: Chain of Custody                                   │
│  ├─ Transfer Tracking (Officer → Lab → Court)               │
│  ├─ Custody Log with Timestamps                             │
│  └─ Digital Signatures                                      │
├─────────────────────────────────────────────────────────────┤
│  Layer 3: Tamper Detection                                   │
│  ├─ Hash Verification                                       │
│  ├─ Integrity Check                                         │
│  └─ Alert System                                            │
├─────────────────────────────────────────────────────────────┤
│  Layer 4: Role-based Access                                  │
│  ├─ Judge View (Full Access)                                │
│  ├─ Officer View (Register + Transfer)                      │
│  └─ Victim View (Read-only)                                 │
├─────────────────────────────────────────────────────────────┤
│  Layer 5: Report Generator                                   │
│  ├─ Timeline Visualization                                  │
│  ├─ PDF Export                                              │
│  └─ Court-ready Format                                      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
                  localStorage Database
```

---

## Data Structure Design

### Evidence Record
```javascript
{
  id: "EV-2026-001234",
  caseNumber: "CR-2026-MH-5678",
  description: "Physical evidence description",
  category: "Physical/Digital/Documentary",
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
  qrCode: "data:image/png;base64...",
  status: "Registered/In-Transit/At-Lab/In-Court",
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

### Phase 1: Layer 1 - Evidence Registration ✓ (Current Focus)
**Files**: [`index.html`](index.html)

**Features**:
- Responsive form with Tailwind CSS
- Photo upload with preview
- GPS auto-capture with fallback
- Auto-timestamp generation
- SHA-256 hash calculation
- QR code generation with evidence ID
- localStorage persistence
- Form validation

**Libraries**:
- Tailwind CSS (CDN)
- QRCode.js (CDN)
- Web Crypto API (native)
- Geolocation API (native)

**User Flow**:
1. Officer opens registration form
2. Fills case details and evidence description
3. Uploads photo (auto-hashed)
4. GPS auto-captured (or manual entry)
5. Clicks "Register Evidence"
6. System generates unique ID, hash, QR code
7. Evidence saved to localStorage
8. Success confirmation with QR display

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

### Phase 3: Layer 3 - Tamper Detection
**Files**: [`verify.html`](verify.html), [`verify.js`](verify.js)

**Features**:
- Hash verification interface
- Photo re-hash comparison
- Integrity status display
- Tamper alert system
- Audit log

**User Flow**:
1. Select evidence to verify
2. System re-calculates photo hash
3. Compares with stored hash
4. Displays integrity status
5. Logs verification attempt

---

### Phase 4: Layer 4 - Role-based Access
**Files**: [`auth.html`](auth.html), [`auth.js`](auth.js), [`dashboard.html`](dashboard.html)

**Features**:
- Simple login system
- Role selection (Judge/Officer/Victim)
- Permission-based UI rendering
- Dashboard customization per role

**Permissions**:
- **Judge**: Full access (view, verify, generate reports)
- **Officer**: Register, transfer, view own cases
- **Victim**: Read-only access to their case evidence

---

### Phase 5: Layer 5 - Report Generator
**Files**: [`report.html`](report.html), [`report.js`](report.js)

**Features**:
- Timeline visualization
- Evidence summary
- Chain of custody report
- Integrity verification status
- PDF export (using jsPDF)
- Court-ready formatting

**Report Sections**:
1. Case Information
2. Evidence Details
3. Chain of Custody Timeline
4. Integrity Verification
5. Digital Signatures
6. QR Code for Verification

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

### localStorage Schema
```javascript
// Keys
- 'nyai_evidence_list': Array of evidence IDs
- 'nyai_evidence_{id}': Individual evidence record
- 'nyai_custody_{id}': Custody chain for evidence
- 'nyai_user_session': Current user session
- 'nyai_audit_log': System audit trail
```

### QR Code Content
```
EV-2026-001234|sha256_hash_first_16_chars
```

---

## Security Considerations

1. **Hash Integrity**: SHA-256 ensures any modification is detectable
2. **Immutable Chain**: Custody transfers append-only (no deletion)
3. **Timestamp Verification**: ISO 8601 format with timezone
4. **Photo Integrity**: Separate hash for photo data
5. **Audit Trail**: All actions logged with timestamp

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

## Testing Strategy

### Unit Tests
- Hash generation accuracy
- QR code generation
- GPS capture
- Form validation

### Integration Tests
- Evidence registration flow
- Custody transfer flow
- Verification flow
- Report generation

### User Acceptance Tests
- Officer workflow
- Judge workflow
- Victim workflow
- Cross-role interactions

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

## File Structure
```
nyay-ai-project/
├── index.html              # Layer 1: Evidence Registration
├── custody.html            # Layer 2: Chain of Custody
├── verify.html             # Layer 3: Tamper Detection
├── auth.html               # Layer 4: Authentication
├── dashboard.html          # Layer 4: Role-based Dashboard
├── report.html             # Layer 5: Report Generator
├── plan.md                 # This file
├── README.md               # Project documentation
├── assets/
│   ├── css/
│   │   └── custom.css      # Additional styles
│   └── js/
│       ├── evidence.js     # Evidence management
│       ├── custody.js      # Custody tracking
│       ├── verify.js       # Verification logic
│       ├── auth.js         # Authentication
│       └── report.js       # Report generation
└── docs/
    └── user-guide.md       # User documentation
```

---

## Success Metrics

1. **Evidence Registration**: < 2 minutes per evidence
2. **Hash Generation**: < 1 second
3. **QR Code Generation**: < 1 second
4. **Custody Transfer**: < 1 minute
5. **Verification**: < 5 seconds
6. **Report Generation**: < 10 seconds

---

## Next Steps

1. ✅ Create this plan.md
2. ⏳ Build Layer 1 (index.html) - **CURRENT TASK**
3. ⏳ Test evidence registration workflow
4. ⏳ Build remaining layers sequentially
5. ⏳ Integrate all layers
6. ⏳ Conduct end-to-end testing
7. ⏳ Deploy MVP
8. ⏳ Gather feedback for improvements

---

## Contact & Support
**Project**: Nyay AI - Digital Forensic Chain Ledger  
**Purpose**: Hackathon MVP  
**Focus**: Evidence integrity and chain of custody for Indian judicial system