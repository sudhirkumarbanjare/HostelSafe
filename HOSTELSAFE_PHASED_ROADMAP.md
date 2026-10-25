# HostelSafe: Phased Architecture & SaaS Commercial Strategy

**HostelSafe** is a cloud-native, multi-tenant Student Living & Hostel Safety Governance Platform. It is engineered to support everything from boutique independent PGs to multi-city university campus chains with thousands of residents.

To provide a frictionless path to market and accommodate diverse hostel operating budgets, HostelSafe is structured into **two distinct deployment phases**:

- **Phase 1: Pure Software / Dual-Surface SaaS (Zero Hardware & Zero QR Code Dependency)**:
  - **Multi-Bed Room Architecture**: Complete support for shared rooms (Single, Double, Triple sharing, and dorms) with bed-level resident assignment (`Bed 204-A`, `Bed 204-B`), vacancy tracking, and bed-level rent collection.
  - **Non-QR Gate Clearance**: Elimination of QR codes and camera scanning dependencies. Verification is powered by **Dynamic 4-Digit Digital Gate PINs** and **Instant Roll Number / Bed ID lookup**.
  - **SMS OTP Authentication & Strictly In-App Notifications**: Passwordless security and zero WhatsApp dependency.
  - **Dual-Surface Coverage**: Dedicated **Website Dashboards AND Mobile Apps** for both **Admin** and **Warden**, plus **Resident** and **Parent** Mobile Apps.
- **Phase 2: IoT Hardware & Biometric Turnstile Security Suite**: Physical access barriers, USB OTG biometric scanners, AI face kiosks, motorized flap barrier relays, RTSP surveillance integration.

---

```mermaid
graph TD
    subgraph Phase 1: Software SaaS (Zero Hardware, Multi-Bed Rooms, Non-QR Gate Pass)
        subgraph Admin Interfaces
            P1_AdminWeb[Admin Web Dashboard: Multi-Hostel, Bed Matrix, Rent Ledger, Policy]
            P1_AdminApp[Admin Mobile App: Executive KPI Summary, Bed Vacancy, 1-Tap Curfew Alert]
        end
        subgraph Warden Interfaces
            P1_WardenWeb[Warden Web Dashboard: Front-Desk Gate PC, Roll/Bed Search, PIN Clearance]
            P1_WardenApp[Warden Mobile App: Bed-by-Bed Night Floor Headcount, Roaming Gate Check]
        end
        subgraph Student & Parent Apps
            P1_Res[Resident Mobile App: SMS OTP, Digital Gate Passcard with 4-Digit PIN, Bed ID]
            P1_Parent[Parent Guardian App: SMS OTP, Multi-Child Tracking, In-App Pass Approval]
        end

        P1_Res -->|Pass Request| P1_Cloud[HostelSafe Cloud Engine]
        P1_Cloud -->|In-App Notification| P1_Parent
        P1_Parent -->|In-App SMS OTP Endorsement| P1_Cloud
        P1_Cloud -->|Issues 4-Digit Gate PIN & Bed Clearance| P1_Res
        P1_Res -->|State PIN or Roll No at Gate| P1_WardenWeb
        P1_Res -->|State PIN or Roll No at Gate| P1_WardenApp
        P1_WardenWeb -->|1-Tap Clearance by Bed| P1_Cloud
        P1_WardenApp -->|1-Tap Clearance by Bed| P1_Cloud
        P1_Cloud -->|Real-Time Bed Telemetry| P1_AdminWeb
        P1_Cloud -->|Push Notifications| P1_InAppAlerts[In-App Notification Center on All Apps]
    end

    subgraph Phase 2: Hardware & IoT Turnstile Connected
        P2_Bio[USB OTG Biometric Sensor: Mantra MFS100] -->|Minutiae Verification| P2_Kiosk[Gate Turnstile Terminal Kiosk]
        P2_Face[AI Facial Recognition Kiosk] -->|Liveness Check| P2_Kiosk
        P2_Kiosk -->|Pass Clearance Check| P1_Cloud
        P2_Kiosk -->|Relay Pulse COM/GPIO| P2_Barrier[Motorized Flap Barrier / Tripod Turnstile]
        P2_Kiosk -->|Trigger Capture| P2_Camera[RTSP Surveillance Camera Snapshot]
    end
```

---

## Phase Comparison & Technical Matrix

| Capability | Phase 1: Software-Only SaaS (Multi-Bed, Non-QR) | Phase 2: Hardware & IoT Connected |
| :--- | :--- | :--- |
| **Target Customers** | Independent PGs, private hostels, coaching hub residences (Kota, Delhi, Pune, Bengaluru) | University campuses, premier residential institutions, enterprise student housing (Oxford, Shivalik, Stanza) |
| **Room & Bed Architecture** | **Multi-Bed Room Inventory**: Rooms support multiple beds (`Bed A`, `Bed B`, `Bed C`). Bed-level status (`Occupied`, `Vacant`, `Maintenance`), bed rent dues, and room sharing allocations | Same Multi-Bed Room Architecture + Gate hardware mapping per block/wing |
| **Hardware Required** | **None** (runs on existing laptops, smartphones, tablets, or office PCs) | USB OTG Biometric Sensor (Mantra MFS100 / SecuGen), Motorized Flap Barrier / Turnstile, IoT Relay Controller |
| **Gate Verification Method** | **Non-QR Digital Passcard**: 4-Digit Rotating Security PIN + Instant Roll No / Bed ID Lookup (Zero camera scanning needed) | Optical Fingerprint (ISO 19794-2 minutiae) + AI Facial Recognition + RFID Card |
| **Admin Surfaces** | **Both Website Dashboard & Mobile App**: Desktop room/bed inventory matrix, dues ledger, audit export + executive pocket mobile app | All Phase 1 Admin interfaces + Hardware fleet health monitor & device provisioning |
| **Warden Surfaces** | **Both Website Dashboard & Mobile App**: Front-desk gate PC PIN clearance desk + roaming mobile app with bed-by-bed floor headcount | All Phase 1 Warden interfaces + Gate Kiosk terminal app with relay pulse controls |
| **Setup & Onboarding Time** | **< 5 minutes** (Self-serve sign-up, bulk CSV bed & student import, instant activation) | **2 - 5 days** (Turnstile mounting, LAN wiring, biometric template enrollment) |
| **Upfront Capital Cost** | **₹0** (Zero CapEx) | ₹45,000 – ₹1,80,000 per gate lane |
| **Authentication & Verification** | **SMS OTP** for secure login & pass approval. Dynamic 4-Digit Gate PIN + Roll Number / Bed ID manual lookup | Optical Fingerprint (ISO 19794-2 minutiae) + AI Facial Recognition + RFID Card |
| **Notification Channel** | **Strictly In-App Notifications** (In-App Notification Center, push alerts, in-app pass approval requests; no external WhatsApp dependency) | In-App Notifications + Gate Kiosk audio-visual alerts + turnstile screen prompts |
| **Parent Authorization** | **In-App Parent Portal** with secure **SMS OTP verification** (Parent reviews request inside app and confirms via OTP) | In-App Parent Portal with SMS OTP + Biometric audit stamp on departure |
| **Surveillance Snapshots** | Optional manual device photo capture on gate check-out | Automated LAN RTSP camera frame capture upon turnstile unlocking |
| **Offline Reliability** | Progressive Web App (PWA) with client-side cache and background queue | Local Edge controller running embedded SQLite sync with fail-open emergency bypass |

---

## Phase 1 Feature Architecture

### 1. Multi-Bed Room Inventory System
- **Room Sharing Configurations**: Rooms can be configured as **Single, Double, Triple, or 4-Bed Dorm Sharing**.
- **Bed-Level Resident Binding**: Every student is bound to a specific bed identifier:
  - e.g. Room B-204 (Double Sharing) -> `Bed B-204-A` (Aarav Sharma), `Bed B-204-B` (Vacant / Available).
- **Bed Vacancy & Allocation Matrix**: Visual grid on the Admin Web Dashboard showing occupied vs. vacant beds in real-time, streamlining new student onboarding.
- **Bed-Level Rent & Billing**: Separate ledger entries for each bed occupant sharing the same room.

### 2. Non-QR Gate Clearance System
- **Why No QR Code**: Eliminates reliance on smartphone camera lenses, scratches, screen glare, camera permission prompts, and low-light issues at night hostel gates.
- **Dynamic 4-Digit Security PIN**: When an outing pass is approved, the resident's mobile app displays an active passcard featuring a **rotating 4-digit PIN** (e.g., `PIN: 8421`).
- **Fast Search & Clearance Desk**:
  - The Warden types the student's Roll Number (`CS2026-042`), Bed Number (`B-204-A`), or asks for the 4-digit PIN.
  - The system displays the student profile, photo, room and bed slot, active pass clearance, curfew deadline, and fee clearance status.
  - Warden taps **"Confirm Gate Exit"** or **"Confirm Gate Entry"** with 1 click.

### 3. Dual-Surface Admin Coverage
- **Admin Website Dashboard (Desktop/Laptop)**:
  - Multi-Property & Multi-City Switcher (oversee campuses across Delhi, Bengaluru, Kota).
  - Visual Floor & Bed Allocation Matrix (manage rooms, beds, and vacancies).
  - Rent Ledger & Dues Collection (track rent per bed).
  - Curfew Policy Engine (set per-hostel evening curfew deadlines and grace periods).
  - Compliance & Police Audit Export (1-click CSV/PDF export).
- **Admin Mobile App**:
  - Executive Pocket Overview (real-time bed occupancy and vacancy numbers).
  - 1-Tap Emergency Curfew Broadcast (dispatches urgent notifications to all resident & parent apps).
  - High-priority Pass Approval Escalations.

### 4. Dual-Surface Warden Coverage
- **Warden Website Station (Front-Desk PC / Laptop)**:
  - Fast Gate Clearance Desk by Roll No, Bed No, or 4-Digit Pass PIN.
  - Live Gate Audit Stream logging student name, room, bed, and exact timestamp.
  - Curfew Defaulter Monitor with real-time breach counters.
- **Warden Mobile App**:
  - Roaming Gate Search & 1-tap Check-in/out.
  - **Bed-by-Bed Night Floor Headcount Checklist**: Warden carries phone during evening rounds, checking off individual beds in each room (e.g. Bed A: Present, Bed B: Vacant, Bed C: On Pass).
  - Roaming Incident & Emergency Logger.

### 5. Resident Mobile App
- **Secure SMS OTP Login**: Passwordless, tamper-proof mobile verification.
- **Active Digital Passcard**: Displays resident photo, Roll No, Room & Bed (`Room B-204, Bed A`), Outing details, and **Live 4-Digit Gate PIN**.
- **Pass Application**: Apply for Day Outing, Night Outing, or Weekend Leave.
- **In-App Notification Center**: Instant in-app alerts on pass status, curfew countdown, and warden notices.

### 6. Parent Guardian Mobile App
- **Secure SMS OTP Login**: Quick access via registered guardian phone number.
- **Multi-Child Cross-City Tracking**: Monitor siblings staying in different hostels and room beds across multiple cities on one screen.
- **In-App Outing Pass Authorization**: Review outing details (destination, return time) and approve or reject securely using an **SMS OTP**.
- **In-App Safety Feed**: Live in-app notifications whenever the child exits or enters hostel premises.

---

## Commercial SaaS Pricing & Packaging

```
+---------------------------------------------------------------------------------------------------------+
|                                        HOSTELSAFE PRICING MATRIX                                        |
+------------------------------------+------------------------------------+-------------------------------+
| PHASE 1: SOFTWARE-ONLY SAAS        | PHASE 2: HARDWARE-CONNECTED SUITE  | ENTERPRISE MULTI-CITY CHAIN   |
+------------------------------------+------------------------------------+-------------------------------+
| Starter Digital    | Campus Pro    | Smart Hardware     | Hardware Pro  | Enterprise Matrix             |
| ₹1,999 / mo        | ₹5,999 / mo   | ₹14,999 / mo       | ₹24,999 / mo  | ₹39,999 / mo                  |
| Up to 80 Beds      | Up to 400 Beds| Up to 600 Beds     | Up to 1500 Beds| Unlimited Beds                |
| Zero Hardware      | Zero Hardware | 2 Turnstiles       | 4 Turnstiles  | Unlimited Gates & Metros      |
| Multi-Bed Ledger   | Multi-Bed     | Mantra USB OTG     | Face AI Kiosk | Dedicated SLA & Edge Sync     |
| PIN Gate Desk      | PIN Gate Desk | Motorized Flap     | CCTV Snapshots| Multi-City Hardware Fleet     |
+--------------------+---------------+--------------------+---------------+-------------------------------+
```

### Detailed Tier Breakdown

#### 1. Phase 1: Starter Digital (₹1,999 / month + GST)
*Best for: Boutique PGs and standalone hostel buildings up to 80 resident beds.*
- Up to **80 Resident Beds** (1 Property)
- **Zero Hardware Required** (runs on phone, tablet, laptop)
- **Multi-Bed Room Management** (Single, Double, Triple sharing)
- **Admin Website Dashboard + Admin Mobile App**
- **Warden Website Station + Warden Mobile App**
- **Non-QR Digital Passcards with 4-Digit Security PINs**
- Fast Roll Number & Bed ID Gate Clearance Desk
- **SMS OTP Authentication** for students and parents
- **In-App Notification Center** for pass status and curfew alerts
- Room & Bed Inventory & Rent/Dues Ledger
- Maintenance Complaint Ticketing

#### 2. Phase 1: Campus Digital Pro (₹5,999 / month + GST)
*Best for: Multi-wing private hostels and coaching hub residencies up to 400 students.*
- Up to **400 Resident Beds** (Up to 3 Properties/Wings)
- **Zero Hardware Required**
- Everything in Starter Digital, plus:
- Multi-Property Admin Web Dashboard with Visual Bed Allocation Matrix
- Roaming Warden Mobile App with **Bed-by-Bed Night Floor Headcount Checklist**
- **Multi-Child Family Guardian Portal** (parents track siblings across different cities)
- **In-App Outing Pass Approval with SMS OTP Verification**
- In-App Evening Curfew Defaulter Broadcast System
- Exportable Police & Institutional Regulatory Compliance Audit Logs
- Finance Analytics & Online Fee Collection Gateway integration

#### 3. Phase 2: Smart Hardware Campus (₹14,999 / month + GST)
*Best for: College hostels and student complexes needing automated biometric turnstiles.*
- Up to **600 Resident Beds**
- Supports up to **2 Physical Hardware Gate Turnstiles**
- Everything in Campus Digital Pro, plus:
- USB OTG Biometric Sensor Driver (Mantra MFS100 / Startek FM220)
- Motorized Flap Barrier Relay COM/GPIO driver (auto barrier opening)
- Biometric minutiae storage & tamper-proof validation
- Warden Kiosk terminal station with audible alarms for unauthorized access
- Priority 24/7 technical helpdesk

#### 4. Phase 2: Enterprise Turnstile Matrix (₹39,999 / month + GST)
*Best for: Multi-city university networks and large student housing groups (1000+ residents).*
- **Unlimited Resident Beds** across multiple cities
- Supports **Unlimited Physical Turnstiles, AI Face Kiosks & Flap Barriers**
- Everything in Smart Hardware Campus, plus:
- Automated LAN RTSP CCTV Snapshot capture upon turnstile unlocking
- Local Edge Server synchronization (zero gate delay, offline fail-safe)
- Anti-tailgating sensor beam integration
- Custom Active Directory / University ERP single sign-on (SSO)
- Dedicated Account Manager & on-site field engineering SLA
