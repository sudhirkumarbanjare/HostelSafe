# HostelSafe: Phased Architecture & SaaS Commercial Strategy

**HostelSafe** is a cloud-native, multi-tenant Student Living & Hostel Safety Governance Platform. It is engineered to support everything from boutique independent PGs to multi-city university campus chains with thousands of residents.

To provide a frictionless path to market and accommodate diverse hostel operating budgets, HostelSafe is structured into **two distinct deployment phases**:

- **Phase 1: Pure Software / Dual-Surface SaaS (Website Dashboards + Mobile Apps)**: Zero hardware investment, instant 5-minute setup, dynamic QR passes, SMS OTP authentication, all notifications strictly in-app, **dedicated Website Dashboards AND Mobile Apps for both Admin and Warden**, plus Resident & Parent Mobile Apps.
- **Phase 2: IoT Hardware & Biometric Turnstile Security Suite**: Physical access barriers, USB OTG biometric scanners, AI face kiosks, motorized flap barrier relays, RTSP surveillance integration.

---

```mermaid
graph TD
    subgraph Phase 1: Dual-Surface Software SaaS (Zero Hardware)
        subgraph Admin Interfaces
            P1_AdminWeb[Admin Website Dashboard: Multi-Hostel, Ledger, Rooms, Policy, Compliance]
            P1_AdminApp[Admin Mobile App: Executive KPI Summary, 1-Tap Curfew Broadcast, Alerts]
        end
        subgraph Warden Interfaces
            P1_WardenWeb[Warden Website Dashboard: Front-Desk Gate PC, WebCam Scanner, Roll Search Desk]
            P1_WardenApp[Warden Mobile App: Phone Camera Gate Scanner, Night Floor Headcount, Field Incident Log]
        end
        subgraph Student & Parent Apps
            P1_Res[Resident Mobile App: SMS OTP Login, 30s Dynamic QR Pass, Leave Request]
            P1_Parent[Parent Guardian App: SMS OTP, Multi-Child Tracking, In-App Pass Approval]
        end

        P1_Res -->|Pass Request| P1_Cloud[HostelSafe Cloud Engine]
        P1_Cloud -->|In-App Notification| P1_Parent
        P1_Parent -->|In-App SMS OTP Endorsement| P1_Cloud
        P1_Cloud -->|Approved QR Pass| P1_Res
        P1_Res -->|Present QR Pass| P1_WardenWeb
        P1_Res -->|Present QR Pass| P1_WardenApp
        P1_WardenWeb -->|Sync Gate Audit| P1_Cloud
        P1_WardenApp -->|Sync Gate Audit| P1_Cloud
        P1_Cloud -->|Live Telemetry| P1_AdminWeb
        P1_Cloud -->|Push Status| P1_AdminApp
        P1_Cloud -->|Curfew Warning| P1_InAppAlerts[In-App Notification Center on All Apps]
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

| Capability | Phase 1: Software-Only SaaS (Web Dashboards + Apps) | Phase 2: Hardware & IoT Connected |
| :--- | :--- | :--- |
| **Target Customers** | Independent PGs, private hostels, coaching hub residences (Kota, Delhi, Pune, Bengaluru) | University campuses, premier residential institutions, enterprise student housing (Oxford, Shivalik, Stanza) |
| **Hardware Required** | **None** (runs on existing laptops, smartphones, tablets, or office PCs) | USB OTG Biometric Sensor (Mantra MFS100 / SecuGen), Motorized Flap Barrier / Turnstile, IoT Relay Controller |
| **Admin Surfaces** | **Both Website Dashboard & Mobile App**: Comprehensive desktop management web dashboard + executive pocket mobile app | All Phase 1 Admin interfaces + Hardware fleet health monitor & device provisioning |
| **Warden Surfaces** | **Both Website Dashboard & Mobile App**: Front-desk gate PC/webcam monitor + roaming mobile app for floor rounds & phone camera scanning | All Phase 1 Warden interfaces + Gate Kiosk terminal app with relay pulse controls |
| **Setup & Onboarding Time** | **< 5 minutes** (Self-serve sign-up, bulk CSV student import, instant activation) | **2 - 5 days** (Turnstile mounting, LAN wiring, biometric template enrollment) |
| **Upfront Capital Cost** | **₹0** (Zero CapEx) | ₹45,000 – ₹1,80,000 per gate lane |
| **Authentication & Verification** | **SMS OTP** for secure login & pass approval. Dynamic QR Code (30s rotation) + Roll Number manual lookup | Optical Fingerprint (ISO 19794-2 minutiae) + AI Facial Recognition + RFID Card |
| **Notification Channel** | **Strictly In-App Notifications** (In-App Notification Center, push alerts, in-app pass approval requests; no external WhatsApp dependency) | In-App Notifications + Gate Kiosk audio-visual alerts + turnstile screen prompts |
| **Barrier Operation** | Warden visual green/red approval on web/app + audible prompt for manual guard gate | Motorized automated barrier release via electrical relay pulse (GPIO / RS485 / TCP Relay) |
| **Parent Authorization** | **In-App Parent Portal** with secure **SMS OTP verification** (Parent reviews request inside app and confirms via OTP) | In-App Parent Portal with SMS OTP + Biometric audit stamp on departure |
| **Surveillance Snapshots** | Optional manual device camera capture on gate check-out | Automated LAN RTSP camera frame capture upon turnstile unlocking |
| **Offline Reliability** | Progressive Web App (PWA) with client-side cache and background queue | Local Edge controller running embedded SQLite sync with fail-open emergency bypass |

---

## Phase 1 Feature Architecture (Dual-Surface Coverage)

### 1. Admin Interfaces (Website Dashboard + Mobile App)
- **Admin Website Dashboard**:
  - Multi-Property & Multi-City Switcher (oversee campuses across Delhi, Bengaluru, Kota).
  - Occupancy & Room/Bed Ledger (real-time bed allocations, dues collection, revenue metrics).
  - Curfew Policy Engine (set per-hostel evening curfew deadlines and grace periods).
  - Staff Access Control (manage Wardens, Accountants, and Guards).
  - Regulatory Compliance & Police Audit Log Export (instant CSV/PDF report).
- **Admin Mobile App**:
  - Executive Pocket Overview (real-time occupancy numbers and breach tallies).
  - 1-Tap Emergency Curfew Broadcast (dispatches urgent notifications to all resident & parent apps).
  - High-priority Pass Approval Escalations.
  - Live revenue & outstanding rent summary.

### 2. Warden Interfaces (Website Dashboard + Mobile App)
- **Warden Website Dashboard**:
  - Front-Desk Gate Operations Station (designed for gate PC, laptop, or fixed tablet).
  - WebCam Dynamic QR Scanner (uses laptop webcam or connected USB camera without specialized scanners).
  - Manual Roster Search Desk (instant search by Roll Number, Room Number, or Name).
  - Curfew Defaulter Monitor with real-time breach counters.
  - Live Gate Audit Stream with color-coded entry/exit logs.
- **Warden Mobile App**:
  - Handheld Gate Scanner (uses smartphone rear camera for ultra-fast QR pass scanning at gate).
  - Night Floor Headcount Checklist (warden carries phone during evening rounds, marking bed occupancy).
  - Direct In-App Ping to Defaulters (prompts late residents directly on their phone).
  - Roaming Incident & Emergency Logger (record medical or disciplinary notes on the spot).

### 3. Resident Mobile App
- **Secure SMS OTP Login**: Passwordless, tamper-proof mobile verification.
- **Dynamic QR Gate Pass**: Time-rotating HMAC QR code that refreshes every 30 seconds to prevent screenshot sharing.
- **Pass Application**: Apply for Day Outing, Night Outing, or Weekend Leave.
- **In-App Notification Center**: Instant in-app alerts on pass status, curfew countdown, and warden notices.

### 4. Parent Guardian Mobile App
- **Secure SMS OTP Login**: Quick access via registered guardian phone number.
- **Multi-Child Cross-City Tracking**: Monitor siblings staying in different hostels across multiple cities on one screen.
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
| Web & Apps for All | Web & Apps    | Mantra USB OTG     | Face AI Kiosk | Dedicated SLA & Edge Sync     |
+--------------------+---------------+--------------------+---------------+-------------------------------+
```

### Detailed Tier Breakdown

#### 1. Phase 1: Starter Digital (₹1,999 / month + GST)
*Best for: Boutique PGs and standalone hostel buildings up to 80 residents.*
- Up to **80 Resident Beds** (1 Property)
- **Zero Hardware Required** (runs on phone, tablet, laptop)
- **Admin Website Dashboard + Admin Mobile App**
- **Warden Website Dashboard + Warden Mobile App**
- Dynamic QR Gate Pass system with anti-screenshot 30s rotation
- **SMS OTP Authentication** for students and parents
- **In-App Notification Center** for pass status and curfew alerts
- Room Inventory & Rent/Dues Ledger
- Maintenance Complaint Ticketing

#### 2. Phase 1: Campus Digital Pro (₹5,999 / month + GST)
*Best for: Multi-wing private hostels and coaching hub residencies up to 400 students.*
- Up to **400 Resident Beds** (Up to 3 Properties/Wings)
- **Zero Hardware Required**
- Everything in Starter Digital, plus:
- Multi-Property Admin Web Dashboard with cross-wing analytics
- Roaming Warden Mobile App with Night Floor Headcount Checklist
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
