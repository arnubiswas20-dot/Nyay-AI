[27-05-2026 01:29] Arnesha Biswas: code .bob/rules/basic_rules.md
[27-05-2026 01:42] Arnesha Biswas: # Nyay AI Project Architecture and Safety Rules

## 1. Absolute Loophole Fix: Secure Gateway Landing
- Whenever a user enters the application, they MUST face a "Secure Workspace Gateway" (Login/Access Control Screen).
- Users can ONLY access dashboards by entering their unique role ID:
  * Investigating Officer -> OFF-211013
  * Medical Officer (Hospital) -> MED-786
  * Lab Technician -> LAB-404
  * Judge -> JUDGE-001
- Use strict JavaScript conditional unmounting. When one dashboard is active, ALL other dashboard components/sections must be completely unmounted from the DOM tree so they cannot be inspected or bypassed.
- Save the session state in localStorage so refresh preserves the logged-in role.

## 2. Role Refinement & Clean-Up
- Investigating Officer: REMOVE 'Generate Court Report' option and REMOVE 'Verify Integrity' feature from this view.
- Judge: This is the ONLY section allowed to have 'Generate Court Report'.
- Medical Officer: Add a dedicated "Hospital Profile Portal" with form entries for Biological Sample Registration (DNA/Swab) with auto-timestamps and geolocation.

## 3. Lab Technician & Cryptographic Verification Logic
- Exclusive Right: 'Verify Integrity' belongs ONLY to Lab Technician (LAB-404).
- The Forensic Flow:
  1. Officer registers evidence -> App generates an initial SHA-256 hash.
  2. Lab Technician receives physical evidence -> Uploads the arrival photo.
  3. The system calculates the SHA-256 hash of the uploaded photo.
  4. If (Original Hash == Arrival Photo Hash) -> Display "🟢 Verification Successful: Integrity Intact".
  5. If (Original Hash != Arrival Photo Hash) -> Instantly trigger an un-dismissable global red alert banner: "🚨 CRITICAL: Cryptographic Hash Mismatch - Chain of Custody Broken!"
- Lab Findings Backend: Implement JavaScript logic for the "Add Lab Finding" form. When submitted, append findings to the specific case record and generate a unique Forensic Lab Report Hash.

## 4. Forensic Custody Audit Trail
- For every single critical action (Login, Evidence Registration, Medical Entry, Lab Verification), Bob must write an interaction summary log inside a dedicated directory named forensic-custody-logs/.
- File naming format: 2026-05-27_[RoleID]_[Action].md.