# BUSINESS REQUIREMENTS DOCUMENT

## Blank Registration Card

**Pre Registration Card Module**

- **Property:** Kazi Resort Ltd — Surjonarayanpur, Kapasia, Gazipur, Bangladesh
- **System:** Cubix HMS V-9.3.0
- **Module:** Front Office > Room Registration > Blank Registration Card
- **URL:** /front_office/room-registration/blank-registration-card
- **Document:** Business Requirements Document (BRD)
- **Date:** April 16, 2026

---

## 1. Executive Summary

The **Blank Registration Card** is a purpose-built, static print document within the Kazi Resort HMS (Cubix HMS V-9.3.0). It is accessed by clicking the "Blank Registration Card" button — the sixth and final tab in the Room Registration workflow — which navigates to a standalone, print-optimised URL: /front_office/room-registration/blank-registration-card.

The document is titled "Pre Registration Card" and functions as a standardised blank paper form that front-desk staff print and hand to arriving guests to complete by hand before or during check-in. It consolidates all essential guest identity, stay, payment, and contact information into a single A4-compatible page with 27 labeled data entry fields, 14 hotel policy clauses, a guest consent declaration, and two signature lines.

**Key business capabilities:**

- **PRINT BLANK FORM:** Staff open the page and use the browser native Print function (Ctrl+P) to produce a blank, pre-formatted registration card for guest handwriting.
- **HOTEL BRANDING:** Every printed card carries the Kazi Resort logo, property name, address, hotline numbers, website, and email.
- **ROOM IDENTIFICATION:** A "Room No" field at the top-right links the completed card to a specific room assignment.
- **GUEST IDENTITY CAPTURE:** Four guest rows (Title / First name / Surname) allow up to four guests per registration.
- **BOOKING REFERENCE:** Reservation No. and Registration No. fields link the paper card to the digital HMS record.
- **STAY AND ROOM DETAILS:** Arrival/Departure dates, Room type, Pax count, Room tariff, Advance payment, and Payment mode.
- **IDENTITY DOCUMENT CAPTURE:** Country, Passport/NID No., Issue Date, Expiry Date, Date of Birth, Visa details for legal compliance.
- **CONTACT AND CORPORATE INFO:** Phone, Email, Home Address, Company name, Reference, and Remarks.
- **POLICY ACKNOWLEDGEMENT:** 14 hotel policy clauses printed on the card; guest signs to acknowledge them.
- **DATA CONSENT:** GDPR/privacy consent declaration above the signature lines.
- **DUAL SIGNATURES:** "Checked in By*" (staff) and "Guest Signature*" (guest) establish mutual accountability.
- **CHECK-IN TIME CONFIRMATION:** Physical check-in and check-out time fields at the bottom.

The page is intentionally static — it contains no interactive form elements, no database queries, no user-specific data pre-fill, and no JavaScript. It produces an identical printed form for all users and all registration scenarios. Its sole actionable process from the system perspective is being navigated to and printed.

---

## 2. Module Context and Positioning

### 2.3 Dependencies

| Type | Dependency | Nature |
|---|---|---|
| Primary Access | Room Registration tab bar (/front_office/new-room-registration) | The "Blank Registration Card" button is the 6th item in the Room Registration tab navigation. This is the primary discovery path. |
| Direct Access | Direct URL (/front_office/room-registration/blank-registration-card) | Accessible via direct URL without going through Room Registration, as long as session is authenticated. |
| Authentication | HMS Session Middleware | Unauthenticated access redirects to login. All authenticated users see the same identical card regardless of role. |
| Branding Asset | Hotel Logo (/assets/img/icons/{date}/1.jpeg) | Logo image fetched from HMS asset storage. If unavailable, alt text "Logo" renders instead. |
| Template Content | Server-side Blade/PHP template | All 14 policy clauses, consent statement, hotel contact info, and time reminders are hard-coded. Not database-driven. |
| Print Mechanism | Browser Native Print (Ctrl+P) | No in-app print button. Relies entirely on browser print dialog and Bootstrap print CSS utilities. |
| Digital Counterpart (Upstream) | Guest Details tab (identity fields) | 27 fields on paper card correspond to Guest Details fields. Guest writes on paper; staff transcribes into HMS digitally. |
| Digital Counterpart (Upstream) | Registration tab (booking fields) | Room No., Res. No., Reg. No., Dates, Room Type, Pax, Tariff, Payment Mode on card link to Registration tab records. |
| No Dependency | Room Setting > Complementary module | Complementary items from Room Setting are NOT displayed on the Blank Registration Card. |

---

## 3. Functional Requirements

All actionable processes within the Blank Registration Card module are documented below with full step-by-step detail.

### FR-BRC-001: Navigate to the Blank Registration Card

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-001 |
| Title | Navigate to and Load the Blank Registration Card Page |
| Primary Path | User is on Room Registration. Clicks "Blank Registration Card" tab button (6th, rightmost in tab bar). |
| Secondary Path | User navigates directly to /front_office/room-registration/blank-registration-card via browser address bar. |
| Step-by-Step Process | 1. User is authenticated on the HMS system. 2. User navigates to Room Registration (/front_office/new-room-registration). 3. User locates the "Blank Registration Card" button at the far right of the tab navigation bar (teal/cyan button). 4. User clicks the "Blank Registration Card" button. 5. Browser initiates navigation to https://kaziresort.cubixsmb.com/front_office/room-registration/blank-registration-card. 6. Server verifies the authenticated user session. 7. If session is invalid: redirect to login page. 8. If session is valid: server renders the static Pre Registration Card HTML template. 9. Browser receives and renders the complete page. 10. Browser tab title updates to "Pre Registration Card". 11. The full card is visible — hotel header, 27 fields, policy clauses, signatures. |
| Tab Button Behaviour | The "Blank Registration Card" button is styled as a tab but acts as a hyperlink (type="button", href to full URL). It does NOT render content inline within the Room Registration tab panel — it navigates away to a standalone page. |
| Pre-condition | Active, authenticated HMS user session. |
| Post-condition | Blank Registration Card page is fully rendered in the browser, ready for printing. |
| Back Navigation | Browser Back button returns user to Room Registration (document.referrer: /front_office/new-room-registration). |

### FR-BRC-002: Render Hotel Branding Header

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-002 |
| Title | Render Hotel Logo, Name, Address, and Contact Details in Page Header |
| Process | 1. System renders the Kazi Resort logo image (img element, centered) at the very top of the page. 2. Below the logo, system renders hotel name: "Kazi Resort Ltd" as an H3 heading, centered. 3. System renders address line: "Surjonarayanpur, Kapasia, Gazipur, Bangladesh". 4. System renders contact: "Hotline: +8801894803511, phone Number: +8801894814765, +8801894814761, Web: www.kaziresort.com". 5. System renders email: "Email: reservation@kaziresort.com". 6. All header content is centered and contained in a single full-width table cell (T0). |
| Content Type | Static hard-coded HTML — not database-driven |
| Logo Image | src: /assets/img/icons/{date}/1.jpeg \| alt: Logo |
| Fallback | If logo image fails to load, alt text "Logo" displayed; text header renders normally |
| Post-condition | Hotel identity prominently displayed at top of every printed card |

### FR-BRC-003: Render Card Title and Room Number Field

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-003 |
| Title | Render "Pre Registration Card" H1 Title and Room No. Input Field |
| Process | 1. System renders "Pre Registration Card" as a left-aligned H1 heading. 2. System renders a bordered "Room No" box on the right side of the same header row. 3. Room No box: table with 2 cells — label cell (80px, text: "Room No", thick 2pt borders) and blank input cell (480px wide, 2pt borders, no text). 4. The blank 480px cell is the handwriting area where staff writes the assigned room number. |
| Layout | Two-column header row: Title (left ~60%) \| Room No box (right ~40%) |
| Field | Room No — blank cell, no asterisk (not mandatory) |
| Post-condition | Card title and room number field printable at top of card body |

### FR-BRC-004: Render Four Guest Name Entry Rows

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-004 |
| Title | Render 4 Guest Name Rows (Title / First name / Surname) for Up to 4 Guests |
| Process | 1. System renders Guest 1 row (T2): 4 cells — Title*:(10%) \| First name*:(20%) \| blank(20%) \| Surname*:(50%). 2. System renders Guests 2-4 rows (T3, 3 rows): each row identical to Guest 1 structure. 3. All labels bold. Asterisk (*) on all three field labels indicates mandatory completion. 4. "Title" (10%) accepts handwritten honorific (Mr./Mrs./Ms./Dr./etc.). 5. "First name*:" label occupies 20% followed by 20% blank writing area. 6. "Surname*:" label + writing space together occupy 50% of row width. 7. Guest 1 row uses thick 2pt borders top/bottom. Guests 2-4 use standard 1pt borders. |
| Row Count | 4 guest rows total (1 primary + 3 accompanying) |
| Fields per Row | Title*: \| First name*: \| [blank] \| Surname*: |
| Business Purpose | Supports group/family registrations — up to 4 guests recorded on one card |
| Post-condition | Staff can record salutation, first name, and surname for up to 4 guests |

### FR-BRC-005: Render Booking Reference Fields

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-005 |
| Title | Render Reservation No. and Registration No. Fields |
| Process | 1. System renders T4 with 2 cells (50% each): "Reservation No.:" \| "Registration No.". 2. Each cell has label on left and blank writing space on right. 3. "Reservation No." links to HMS reservation record. "Registration No." links to HMS check-in registration record. |
| Fields | Reservation No.: (50%) \| Registration No. (50%) |
| Post-condition | Both booking reference numbers can be cross-referenced between paper card and digital HMS records |

### FR-BRC-006: Render Stay Dates and Room Detail Fields

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-006 |
| Title | Render Arrival Date, Departure Date, Room Type, and Pax Fields |
| Process | 1. System renders T5 with 2 rows: Row 0: Arrival Date: (50%) \| Departure date*: (50%). Row 1: Room type: (50%) \| Pax: (50%). 2. Departure date* is mandatory (asterisk). All other fields optional. 3. "Pax" refers to total occupant count (persons in the room). |
| Fields | Arrival Date: \| Departure date*: \| Room type: \| Pax: |
| Post-condition | Check-in/check-out dates, room category, and occupancy count captured |

### FR-BRC-007: Render Payment Fields

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-007 |
| Title | Render Room Tariff, Advance, and Payment Mode Fields |
| Process | 1. System renders T6 with 6 cells: Cell 1: "Room tariff:" label (10%, left+right+bottom borders). Cell 2: Blank (15%) — tariff amount writing area. Cell 3: "Advance" (10%, bold, centered) — advance payment sub-label. Cell 4: Blank (15%) — advance amount writing area. Cell 5: "Payment mode:" label (20%, left+right+bottom borders). Cell 6: Blank (30%) — payment method writing area. 2. "Advance" is bolded and centered to distinguish it as a column header. |
| Fields | Room tariff: [amount] \| Advance [amount] \| Payment mode: [method] |
| Post-condition | Room rate, advance collected, and payment method recorded on card |

### FR-BRC-008: Render Company Name Field

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-008 |
| Title | Render Company Name Full-Width Field |
| Process | 1. System renders T7 as single full-width (100%) cell: label "Company name:" on left, remainder is blank writing space. 2. Used for corporate guests billed to a company account. |
| Field | Company name: (100% width) |
| Post-condition | Company or agency name can be entered for corporate/group bookings |

### FR-BRC-009: Render Country and Passport/NID Fields

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-009 |
| Title | Render Country and Passport/NID Number Fields |
| Process | 1. System renders T8 with 2 cells (50% each): Country*: (mandatory) \| Passport/NID No*: (mandatory). 2. Country: nationality or country of residence. 3. Passport/NID No*: primary identity document — passport for international guests, National ID for domestic. |
| Fields | Country*: \| Passport/NID No*: (both mandatory) |
| Compliance | Supports hotel policy #6 (valid ID required) and immigration/police reporting requirements |
| Post-condition | Guest nationality and primary identity document number captured |

### FR-BRC-010: Render Passport Detail Fields

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-010 |
| Title | Render Issue Date, Expiry Date, and Date of Birth Fields |
| Process | 1. System renders T9 with 3 cells (33% \| 33% \| 34%): Issue Date: \| Expiry Date: \| Date of Birth*: 2. Issue Date and Expiry Date relate to the passport/NID document. 3. Date of Birth* (mandatory) provides age verification. 4. These three fields together form the complete identity document record for compliance. |
| Fields | Issue Date: \| Expiry Date: \| Date of Birth*: (mandatory) |
| Post-condition | Document validity and guest date of birth captured for compliance |

### FR-BRC-011: Render Visa Information Fields

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-011 |
| Title | Render Visa No., Visa Issue Date, and Visa Expiry Date Fields |
| Process | 1. System renders T10 with 3 cells (33% \| 33% \| 34%): Visa No: \| Issue Date*: (mandatory) \| Expiry Date*: (mandatory). 2. Applies to international guests requiring Bangladesh visa. 3. Issue Date* and Expiry Date* confirm visa validity at check-in. |
| Fields | Visa No: \| Issue Date*: \| Expiry Date*: (last two mandatory) |
| Compliance | Immigration and legal compliance for non-citizen guests |
| Post-condition | Visa details captured for foreign national guests |

### FR-BRC-012: Render Phone and Email Fields

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-012 |
| Title | Render Phone and E-mail Contact Fields |
| Process | 1. System renders T11 with 2 cells (50% each): Phone*: (mandatory) \| E-mail*: (mandatory). 2. Phone used for contact during stay and emergency. 3. Email used for invoicing, communication, and future correspondence. |
| Fields | Phone*: \| E-mail*: (both mandatory) |
| Post-condition | Guest primary contact details captured |

### FR-BRC-013: Render Home Address, Reference, and Remarks Fields

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-013 |
| Title | Render Full-Width Home Address, Reference, and Remarks Fields |
| Process | 1. System renders three separate full-width (100%) single-row tables: T12: "Home Address:" — full postal/street address writing area. T13: "Reference:" — booking source, agent, or referral. T14: "Remarks:" — special requests, notes, observations. 2. Full-width layout provides ample writing space for longer text. |
| Fields | Home Address: \| Reference: \| Remarks: (all 100% width, none mandatory) |
| Post-condition | Guest permanent address, booking source, and special notes captured |

### FR-BRC-014: Render 14-Clause Guest Policy Notice

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-014 |
| Title | Render "Dear Guest, Please note the following" Section with 14 Policy Clauses |
| Process | 1. System renders H2 heading: "Dear Guest, Please note the following:" 2. System renders an ordered list (OL) with 14 LI items. 3. Item 14 is bolded (smoking/non-smoking penalty notice). 4. All content is hard-coded in the HTML template — not configurable via HMS settings. |
| Clause 1 | The management will not be responsible for any of your valuable left in your room. |
| Clause 2 | Bill must be settled upon check-in or during check-out time. |
| Clause 3 | The bill will be generated in local currency. |
| Clause 4 | The management reserves the right to charge you or your credit card for any missing items from your room after your departure. |
| Clause 5 | All complains after check-out is not applicable or recivable. |
| Clause 6 | Guests has to provide any administrative document as per hotel policy (Valid ID/Passport). |
| Clause 7 | Guests are subject to accept all security procedure ask by Kazi resort. |
| Clause 8 | Credit can not be given unless prior arrangements have made with the hotel. |
| Clause 9 | Please handover keys to reception before leaving the hotel at any given time. Loss room keys will incur a BDT 600.00 charge. |
| Clause 10 | Pets are not allowed inside the hotel premises. |
| Clause 11 | Guests are not permited to carry weapons inside the hotel premises (Firearms, ammunition, knives and any kind of narcotic drugs). |
| Clause 12 | Our standard Check-in time **14:00:00 HRS** and Check-out time **12:00:00 HRS**. |
| Clause 13 | The hotel guest cannot hand over a room key to third person even if the period for which the guest paid has not yet expired. |
| Clause 14 (Bold) | Please be advised that all our rooms are non-smoking. If evidence of smoking is found in a room, a penalty will be applied to cover the cost of odor removal. The charge for odor cleaning is 2,500 BDT for a Junior Suite and 2,000 BDT for a Premier Room. We appreciate your understanding and cooperation in maintaining a smoke-free environment. |
| Penalties Stated | Lost key: BDT 600.00 \| Smoking - Junior Suite: BDT 2,500 \| Smoking - Premier Room: BDT 2,000 |
| Post-condition | All 14 policy clauses printed on every card for guest review before signing |

### FR-BRC-015: Render Data Privacy Consent Statement

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-015 |
| Title | Render GDPR/Privacy Consent Declaration |
| Process | 1. Below the policy list, system renders a centered H2 element: "By signing this form, I consent to the use of my personal information for the purpose described above." 2. Placed immediately above the signature section. 3. By signing, guest explicitly consents to personal data collection. |
| Consent Text | By signing this form, I consent to the use of my personal information for the purpose described above. |
| Legal Purpose | GDPR and local privacy law compliance — explicit informed consent for data collection at check-in |
| Post-condition | Consent statement printed on every card, establishing legal basis for data collection when guest signs |

### FR-BRC-016: Render Dual Signature Section

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-016 |
| Title | Render Checked-In By (Staff) and Guest Signature Lines |
| Process | 1. System renders T15 (empty row) with two cells — visual underline signature lines. 2. System renders T16 with 2 cells (50% each): Left: "Checked in By*" — front-desk staff signature. Right: "Guest Signature*" — primary guest signature. 3. Both labels carry asterisk (*) indicating both signatures are mandatory. 4. The physical signature lines in T15 provide the handwriting space above the T16 labels. |
| Signature 1 | Checked in By* — signed by front-desk staff who processed the check-in |
| Signature 2 | Guest Signature* — signed by the primary registered guest |
| Legal Status | A completed and dual-signed card constitutes a legally binding check-in acknowledgement record |
| Post-condition | Two mandatory signature blocks printed and ready for staff and guest signing |

### FR-BRC-017: Render Check-In and Check-Out Time Entry Fields

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-017 |
| Title | Render Physical Check-In and Check-Out Time Record Line |
| Process | 1. System renders a centered text line below T16: "Check-In at ...dots... HRS. Check-Out at ...dots... HRS." 2. The dots/ellipsis patterns provide visual handwriting guides. 3. Staff writes the exact check-in time when the guest arrives and the check-out time when they depart. |
| Content | Check-In at ...........HRS. Check-Out at ...........HRS. |
| Purpose | Physical time-stamp record for the paper card; complements the digital HMS timestamp |
| Post-condition | Staff fills in actual arrival and departure times, completing the physical check-in record |

### FR-BRC-018: Render Standard Check-Out Time Reminder

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-018 |
| Title | Render Final Check-Out Time Reminder at Bottom of Card |
| Process | 1. System renders a final centered message at the very bottom of the card: "We respectfully remind you that check-out time at 12:00:00 HRS." 2. The time "12:00:00 HRS" is displayed in bold. 3. Consistent with policy clause #12. |
| Content | We respectfully remind you that check-out time at 12:00:00 HRS. (bold time) |
| Post-condition | Guest is aware of 12:00 noon check-out deadline — the last visible text before/after signing |

### FR-BRC-019: Print the Blank Registration Card

| Attribute | Detail |
|---|---|
| FR ID | FR-BRC-019 |
| Title | Print the Blank Registration Card via Browser Native Print |
| Trigger | Staff presses Ctrl+P (Windows/Linux) or Cmd+P (Mac), or uses browser File > Print menu |
| Process | 1. Staff opens the Blank Registration Card page in the browser. 2. Staff initiates print via Ctrl+P or browser menu. 3. Browser opens the native Print dialog with a print preview. 4. Bootstrap print CSS (d-print-none, etc.) suppresses any non-printable elements. 5. Table borders, logo, and all text render in print preview. 6. Staff selects printer (physical printer or Save as PDF). 7. Staff configures settings: Paper = A4, Orientation = Portrait, Margins = Default, Background Graphics = On. 8. Staff clicks Print in the browser dialog. 9. One copy of the blank Pre Registration Card is printed. 10. Printed card handed to arriving guest for manual completion. 11. Completed card collected from guest by staff. 12. Staff transcribes handwritten data into HMS digitally (Guest Details + Registration tabs). 13. Signed card filed physically as a legal check-in record. |
| In-App Print Button | None — no print button in the HMS application for this page. Browser Ctrl+P is the sole method. |
| Recommended Print Settings | Paper: A4 \| Orientation: Portrait \| Margins: Default \| Background graphics: On (for borders) |
| Output | One blank printed form — all 27 fields empty, 14 policies visible, signature lines present |
| Post-Print Workflow | Guest fills fields by hand -> Staff collects card -> Staff transcribes data into HMS -> Card filed for records retention |
| Post-condition | Signed, completed physical card is archived as a legal record of check-in consent |

---

## 5. UI Components Summary

Every visible and structural component of the Blank Registration Card page is documented below. This page has no navigation bar, no sidebar, no interactive elements, and no JavaScript — it is a minimal, print-optimised standalone document.

### 5.1 Page-Level

| Component | Details |
|---|---|
| Browser Title | Pre Registration Card (HTML title tag) |
| Page URL | /front_office/room-registration/blank-registration-card |
| Container | div.reservation-pdf.container — Bootstrap container, max-width 1140px, white background |
| Navigation | None — no sidebar, nav bar, breadcrumbs, or header/footer navigation |
| Interactive Elements | None — zero input fields, buttons, forms, links, or JavaScript |
| Print Method | Browser Ctrl+P — no in-app print button provided |

### 5.2 Header Branding Block (T0)

| Element | Type | Content |
|---|---|---|
| Logo | img (centered) | Kazi Resort and Hotel logo image (alt=Logo) |
| Hotel Name | h3 | Kazi Resort Ltd — bold, centered |
| Address Line | p | Surjonarayanpur, Kapasia, Gazipur, Bangladesh |
| Contact Line | p | Hotline: +8801894803511, phone: +8801894814765, +8801894814761, Web: www.kaziresort.com **(current live template renders the "Hotline:" prefix twice — "Hotline: Hotline: +880…" — to be corrected in the template)** |
| Email Line | p | Email: reservation@kaziresort.com |
| Container | Table T0, 1 row, 1 cell | text-align:center, line-height:0px, full page width |
| Divider | Horizontal rule | Separates header from card body |

### 5.3 Card Title and Room Number (T1)

| Element | Type | Width | Content |
|---|---|---|---|
| Card Title | h1 heading | ~60% (left col) | Pre Registration Card — left-aligned |
| Room No Label | td, T1 cell 1 | 80px — 2pt borders all sides | Text: Room No (H1 heading styled) |
| Room No Blank | td, T1 cell 2 | 480px — 2pt borders | Empty — handwriting space for room number |

### 5.4 Guest Name Rows (T2, T3)

| Table | Row | Cell 1 | Cell 2 | Cell 3 | Cell 4 |
|---|---|---|---|---|---|
| T2 | Guest 1 | Title*: (10%, 2pt border, bold) | First name*: (20%, bold) | [blank 20%] | Surname*: (50%, bold) |
| T3 | Guests 2-4 (3 rows) | Title*: (10%, 1pt border, bold) | First name*: (20%, bold) | [blank 20%] | Surname*: (50%, bold) |

### 5.5 Data Entry Field Tables (T4-T14)

| Table | Field Labels | Width Split | Notes |
|---|---|---|---|
| T4 | Reservation No.: \| Registration No. | 50% \| 50% | Both labels act as label+write-space in same cell |
| T5 row 0 | Arrival Date: \| Departure date*: | 50% \| 50% | Departure date has asterisk (mandatory) |
| T5 row 1 | Room type: \| Pax: | 50% \| 50% | — |
| T6 | Room tariff: \| [blank] \| Advance \| [blank] \| Payment mode: \| [blank] | 10\|15\|10\|15\|20\|30% | Advance is bold centered; 3 label+blank pairs |
| T7 | Company name: | 100% | Single full-width label+write-space |
| T8 | Country*: \| Passport/NID No*: | 50% \| 50% | Both mandatory (*) |
| T9 | Issue Date: \| Expiry Date: \| Date of Birth*: | 33\|33\|34% | Date of Birth mandatory (*) |
| T10 | Visa No: \| Issue Date*: \| Expiry Date*: | 33\|33\|34% | Issue Date and Expiry Date mandatory (*) |
| T11 | Phone*: \| E-mail*: | 50% \| 50% | Both mandatory (*) |
| T12 | Home Address: | 100% | Full-width write-space |
| T13 | Reference: | 100% | Full-width write-space |
| T14 | Remarks: | 100% | Full-width write-space |

### 5.6 Policy, Consent, and Signature Section

| Element | Type | Content |
|---|---|---|
| Policy Heading | h2 bold | Dear Guest, Please note the following: |
| Policy Clauses | ol > 14 x li | 14 numbered clauses (1-13 normal weight, 14 bold). See FR-BRC-014 for full text. |
| Consent Statement | h2 centered | By signing this form, I consent to the use of my personal information for the purpose described above. |
| Signature Lines | T15 — 2 blank cells | Visual underline signature line areas (no text — purely graphical signature space) |
| Signature Labels | T16 — 2 cells (50%/50%) | Checked in By* (left) \| Guest Signature* (right) — both asterisk-marked mandatory |
| Time Record | Centered div/paragraph | Check-In at ...dots... HRS. Check-Out at ...dots... HRS. (physical time log) |
| Checkout Reminder | Centered paragraph | We respectfully remind you that check-out time at 12:00:00 HRS. (time in bold) |

---

## 6. Business Rules

The following business rules govern the Blank Registration Card, its rendering, its use in the check-in workflow, and the physical data collection process.

| Rule ID | Rule Title | Description | Enforcement Method |
|---|---|---|---|
| BR-BRC-001 | Card is Fully Blank on Load | The Blank Registration Card must always render with all 27 field cells completely empty. No guest-specific data, no pre-filled dates, no default values. Every instance of the page must be a clean blank template. | Server-side rendering: no dynamic data injection; all cells are static empty HTML. |
| BR-BRC-002 | Identical Output for All Users | Every authenticated user who accesses the Blank Registration Card URL must receive an identical page — regardless of role (receptionist, manager, admin). No personalisation. | Static template with no role-based conditional rendering. |
| BR-BRC-003 | Mandatory Fields Marked with Asterisk | Fields required by hotel policy are marked with asterisk (*). These are: Title, First name, Surname, Departure date, Date of Birth, Country, Passport/NID No, Visa Issue Date, Visa Expiry Date, Phone, E-mail, Checked in By signature, Guest Signature. (13 required fields total) | Asterisk (*) appended to field labels in HTML template. Visual-only — no programmatic validation (paper form). |
| BR-BRC-004 | Four Guest Rows Always Displayed | The card always displays exactly 4 guest name rows (Title/First name/Surname), regardless of the number of actual guests. Unused rows are left blank by the guest. | 4 rows hard-coded in HTML template (T2 + 3 rows of T3). |
| BR-BRC-005 | Advance Payment Sub-Field Always Present | The "Advance" label and its blank cell must always appear between the Room tariff and Payment mode fields. It cannot be removed or hidden for non-advance bookings. | Hard-coded in T6 structure. Blank if not applicable. |
| BR-BRC-006 | All 14 Policy Clauses Must Print | All 14 policy clauses must be visible and legible on the printed card. No clause may be omitted or truncated by page layout. | Hard-coded in ordered list in HTML template; no conditional rendering. |
| BR-BRC-007 | Consent Statement is Mandatory on Card | The privacy consent statement ("By signing this form, I consent to the use of my personal information...") must be present on every printed card, positioned above the signature lines. | Hard-coded in HTML template as H2 element. |
| BR-BRC-008 | Dual Signatures are Mandatory | Both "Checked in By*" (staff) and "Guest Signature*" (guest) signature lines must appear on every card. Both are marked mandatory with asterisk (*). | Hard-coded in T15 and T16 signature tables. Visual-only enforcement on paper. |
| BR-BRC-009 | Checkout Reminder is Permanent | The check-out time reminder ("We respectfully remind you that check-out time at 12:00:00 HRS.") must appear at the bottom of every card. | Hard-coded in HTML template. |
| BR-BRC-010 | Authentication Required | The page must only be accessible to authenticated users. Unauthenticated access must redirect to the login page. | Server-side session middleware on /front_office/* routes. |
| BR-BRC-011 | No In-App Print Button | There is no dedicated "Print" button within the HMS application on this page. Staff must use the browser native print function (Ctrl+P). | By design — page is print-friendly via CSS without requiring a JS-triggered print dialog. |
| BR-BRC-012 | Hotel Contact Info is Static | Hotel name, address, phone numbers, website, and email on the card header are hard-coded in the HTML template. Updates to hotel contact details require a template code change, not a settings update. | Hard-coded in server-side template (Blade/PHP view file). |
| BR-BRC-013 | Lost Key Penalty Stated | The card must state the lost key penalty (BDT 600.00) in policy clause 9, establishing the fee in writing before guest signature. | Hard-coded in policy clause 9 in the OL/LI list. |
| BR-BRC-014 | Smoking Penalty Stated and Bolded | Clause 14 (non-smoking policy and cleaning fees) must be rendered in bold to visually emphasize the smoking penalty (BDT 2,500 for Junior Suite; BDT 2,000 for Premier Room). | Hard-coded in policy clause 14 with bold HTML styling. |
| BR-BRC-015 | Physical Record Retention | After the guest completes and signs the Blank Registration Card, the completed physical card must be retained by the hotel as a legal record of check-in consent. This is a hotel operational rule, not enforced by the HMS system itself. | Operational policy — staff responsibility. |
| BR-BRC-016 | Manual Data Transcription Required | Data written by guests on the Blank Registration Card must be manually transcribed by front-desk staff into the HMS digital system (Guest Details and Registration tabs). The HMS system does not automatically read or scan the paper card. | Operational procedure — no digital-to-paper sync mechanism exists. |
| BR-BRC-017 | Check-In and Check-Out Time Fields are Handwritten | The "Check-In at ...HRS. Check-Out at ...HRS." fields are physical writing guides on paper only — not connected to the HMS digital timestamp. | Static text in HTML — visual guides only. |

---

## 7. Non-Functional Requirements

### 7.1 Performance

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-BRC-P01 | Page Load Time | The Blank Registration Card page must fully render within 2 seconds on a standard broadband connection. Target: sub-1 second since no dynamic content or database calls are made. |
| NFR-BRC-P02 | Print Render Time | The browser print preview must open and render within 2 seconds of Ctrl+P being pressed. |
| NFR-BRC-P03 | No Background API Calls | The page must make zero AJAX or API calls after the initial HTML is loaded. No background processing, polling, or data fetching should occur. |
| NFR-BRC-P04 | Scalability | The page must load with identical performance regardless of the number of concurrent HMS users or the volume of HMS data (registrations, guests, rooms). |

### 7.2 Print Quality

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-BRC-Q01 | A4 Fit | All card content (header, 27 fields, 14 policy clauses, consent, signatures) must fit on a single A4 page when printed at 100% scale with default margins. |
| NFR-BRC-Q02 | Table Borders Visible | All field borders must print visibly. CSS property print-color-adjust: exact must be applied to ensure borders render in print mode. |
| NFR-BRC-Q03 | Logo Print Quality | The hotel logo image must print clearly at print resolution (300 DPI minimum recommended). |
| NFR-BRC-Q04 | Font Legibility | All label text and policy clauses must be legible at printed size. Minimum effective font size: 9pt in print output. |
| NFR-BRC-Q05 | Writing Space Adequacy | Blank cells must provide sufficient height and width for handwritten entries. Each single-line field row should have a minimum height of 18pt in print output. |

### 7.3 Security

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-BRC-S01 | Authentication Enforcement | Access to /front_office/room-registration/blank-registration-card requires valid HMS session. Unauthenticated access redirects to /login. |
| NFR-BRC-S02 | No Sensitive Data Exposure | The page must not expose any guest personal data, registration IDs, or system internals — it is intentionally blank. No guest-specific URL parameters must be accepted or rendered. |
| NFR-BRC-S03 | XSS Prevention | Even though the page is static, if any dynamic content is ever introduced in the future, all output must be HTML-escaped to prevent XSS. |
| NFR-BRC-S04 | Physical Document Security | Completed and signed registration cards contain PII (passport numbers, dates of birth, phone numbers, email). Physical copies must be stored securely per hotel data retention policy. |

### 7.4 Usability

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-BRC-U01 | Self-Explanatory Layout | The printed card layout must be intuitive enough for guests to complete without verbal instruction. Field labels must be clear and unambiguous. |
| NFR-BRC-U02 | Mandatory Field Visibility | All 13 mandatory fields (marked *) must be visually distinguishable on the printed card. |
| NFR-BRC-U03 | Policy Readability | All 14 policy clauses must be printed in a font size legible to standard adult vision without magnification (minimum 9pt). |
| NFR-BRC-U04 | Signature Area Clarity | Signature lines must be clearly visible and appropriately sized for handwritten signatures. "Checked in By*" and "Guest Signature*" labels must be unambiguously positioned below their respective signature lines. |
| NFR-BRC-U05 | Quick Access | Staff must be able to navigate from the Room Registration page to the Blank Registration Card page in a single click (one-click access via the tab button). |

### 7.5 Maintainability

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-BRC-M01 | Configurable Policy Content | Hotel policy clauses (particularly penalty amounts and check-in/check-out times) should be configurable via an HMS settings panel rather than hard-coded in the template, to allow updates without code deployment. |
| NFR-BRC-M02 | Configurable Hotel Contact Info | Hotel name, address, phone numbers, website, and email in the card header should be sourced from the HMS hotel settings configuration, not hard-coded. |
| NFR-BRC-M03 | Template Versioning | Changes to the registration card template (policy text, field layout, branding) must be version-controlled to maintain audit history. |
| NFR-BRC-M04 | Print CSS Maintenance | Bootstrap print utility classes used on this page must be maintained and tested after any Bootstrap version upgrade. |

---

## 8. AI-DLC Audit Update — 2026-04-22

Findings from live audit (Cubix HMS V-9.3.0, Kazi Resort).

### 8.1 Verified content (live)

| Element | Result |
|---|---|
| Title = "Pre Registration Card" | ✓ |
| Zero interactive elements (0 inputs / forms / buttons / links / scripts) | ✓ |
| H1 count = 2 ("Pre Registration Card", "Room No") | ✓ |
| H2 count = 2 ("Dear Guest…", GDPR consent) | ✓ |
| H3 "Kazi Resort Ltd" | ✓ |
| Logo src = `/assets/img/icons/2026-04-15/1.jpeg`, alt=`Logo` | ✓ (date-parameterised path) |
| 14 `<li>` policy clauses | ✓ |
| Clause 9 "BDT 600.00" | ✓ |
| Clause 12 "14:00:00 HRS / 12:00:00 HRS" | ✓ |
| Clause 14 "2,500 BDT / 2,000 BDT" | ✓ |
| "Title*:" × 4, "First name*:" × 4, "Surname*:" × 4 | ✓ 4 guest rows |
| Consent + Checked in By* + Guest Signature* | ✓ |
| Checkout reminder bolded time | ✓ |

### 8.2 Known defects

| ID | Defect | Impact |
|---|---|---|
| DEF-BRC-01 | "Hotline:" prefix duplicated in rendered contact line ("Hotline: Hotline: +880…") | ⚠ template text bug |
| DEF-BRC-02 | Original Clause 14 text in BRD missed the trailing sentence "We appreciate your understanding and cooperation in maintaining a smoke-free environment." | ✓ now aligned |
| DEF-BRC-03 | Logo path is date-parameterised (`/assets/img/icons/2026-04-15/1.jpeg`); no BR described the resolution rule | ✓ new BR-BRC-018 |
| DEF-BRC-04 | Page renders outside the HMS shell (no navbar/sidebar/footer) — behaviour implicit only | ✓ new BR-BRC-019 |
| DEF-BRC-05 | Policy clause typos "recivable" (clause 5), "permited" (clause 11) are present intentionally in live | ✓ new BR-BRC-020 |

### 8.3 Business rules added

| ID | Rule |
|---|---|
| BR-BRC-018 | **Logo-path resolution** — The logo image URL SHALL resolve to `/assets/img/icons/{logo_effective_date}/1.jpeg`, where `logo_effective_date` is the last date on which the hotel logo was updated in HMS settings. If the resource 404s, the `alt="Logo"` text is rendered unchanged and the page otherwise prints normally. |
| BR-BRC-019 | **Out-of-shell rendering** — The Blank Registration Card page SHALL render without the HMS sidebar, global navbar, or footer. It is a standalone, print-optimised document. Authentication is still enforced. |
| BR-BRC-020 | **Verbatim policy text** — Policy clause wording (including historically present typographical errors such as "recivable", "permited") SHALL be treated as legally-reviewed fixed text; edits require legal sign-off. Any change must be version-controlled and dated. |
| BR-BRC-021 | **Contact-line de-duplication** — The header contact line MUST contain the "Hotline:" prefix exactly once. Duplicate prefixes are defects to be corrected at the template level. |

### 8.4 User journeys added
- UJ-BRC-A Logo 404 fallback — image resource missing → browser renders alt text "Logo"; rest of the card prints normally.
- UJ-BRC-B Save-as-PDF alternative — staff chooses browser "Save as PDF" instead of physical printer → file saved locally; archival folder convention `/card-archive/{registration_no}.pdf` is recommended but not enforced by system.
- UJ-BRC-C Physical retention / destruction — completed signed card retained per hotel records policy (default: 3 years); destruction log required for GDPR compliance when retention expires.
- UJ-BRC-D Hotel contact-info update — admin updates hotline/email in HMS settings → new template deployment replaces hard-coded values; until then, card keeps the old values.

