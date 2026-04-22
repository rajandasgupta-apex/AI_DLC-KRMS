# Business Requirements Document

## Others Information Module

**Room Registration — Others Information Tab**

- **System:** Cubix HMS V-9.3.0 (deployed) — BRD drafted against v13.1.0
- **Version:** 1.0 | **Date:** April 15, 2026
- **Module:** Front Office > Room Registration > Others Information Tab
- **Prepared For:** Cubix HMS Project Team

---

## 1. Executive Summary

This Business Requirements Document (BRD) defines the complete functional and non-functional requirements for the **Others Information** tab within the Room Registration module of the Cubix Hotel Management System (HMS). The Others Information tab is the fourth and final data-entry step in the Room Registration wizard, positioned under Front Office > Room Registration.

The Others Information tab consolidates supplementary guest stay data that does not fit within the primary Registration, Guest Details, or Complimentary Item tabs. It is divided into three functional sections: (1) **Other Information** — capturing travel context, guest classification flags, and room usage designation; (2) **Departure Information** — recording airport drop logistics and flight details; and (3) **Credit Card Information** — capturing the guest's payment card for security deposit or incidental charges.

Together, these three sections complete the guest registration record, enabling the hotel to manage transportation logistics, classify guests by status (VIP, returning, complimentary, house use), designate room ownership, and secure a payment guarantee before Check-in.

| Attribute | Details |
|---|---|
| System | Cubix HMS V-9.3.0 (deployed) — BRD drafted against v13.1.0 |
| Module | Front Office |
| Sub-Module | Room Registration |
| Tab Name | Others Information |
| Tab Position | 4th tab (final data-entry step before Check-in) |
| Total Sections | 3 (Other Information, Departure Information, Credit Card Information) |
| Total Fields | 17 input fields across 3 sections |
| Primary Users | Front Desk Staff, Reservation Officers, Duty Managers |
| Document Version | 1.0 |
| Prepared Date | April 15, 2026 |
| Status | Draft for Review |

---

## 2. Module Context & Positioning

### 2 Dependencies

#### 2.3.1 Upstream Dependencies

- **Registration Tab (Tab 1):** The room assignment and registration context (room number, check-in/out dates, rate) must exist before Others Information data can be saved via Check-in. The "Complimentary" checkbox on Tab 1 is related to (but distinct from) the "Complimentary Guest" dropdown on this tab.
- **Guest Details Tab (Tab 2):** The primary guest identity (name, room number) must be established. The "Is Guest VIP?" and "Is Previously Visited Guest?" flags on this tab are supplementary to the guest profile created in Tab 2.
- **Airport Drop field on Complimentary Item Tab:** The Complimentary Item tab includes an "Airport Drop" service option. The Departure Information section here captures the operational logistics (airline, flight number, departure time) for the actual drop service.
- **Room Type / Room Configuration:** The "Room Owner" flag on this tab is relevant to rooms designated for owner/property use. This is linked to the room configuration in the system.

#### 2.3.2 Downstream Dependencies

- **Check-in Record:** All three sections on this tab are saved as part of the unified check-in record when the "Check-in" button is clicked. No section is saved independently.
- **Guest Bill / Folio:** The Credit Card information captured here is associated with the guest folio as the payment guarantee. Card Type and Card Number are referenced for billing and checkout settlement.
- **Blank Registration Card Printout:** Fields such as Coming From, Next Destination, Visit Purpose, and VIP status appear on the printed Registration Card document.
- **Transportation / Concierge Department:** The Departure Information section (Airport Drop, Airline, Flight Number, ETD) is communicated to the concierge or transportation team to arrange the departure transfer.
- **Front Office Reports:** Visit Purpose, Coming From, Next Destination, and guest classification flags (VIP, House Use, Complimentary) feed into management reports and statutory tourism/immigration reporting.
- **Housekeeping:** House Use designation affects room status in housekeeping workflows (rooms flagged as House Use are treated differently from guest rooms).
- **Finance / Night Audit:** Complimentary Guest and House Use flags affect posting rules — charges for complimentary or house-use rooms may be zero-rated or posted to internal accounts.

#### 2.3.3 Data Dependencies

- **Airline Master List:** The "Airlines Name" dropdown in the Departure Information section is populated from a master airline configuration table (64 airlines currently configured).
- **Credit Card Type Master List:** The "Card Type" dropdown is populated from a configurable payment card type master (5 types: American Express, Master Card, Visa Card, Discover Card, Taka Pay).
- **Guest Profile (from Tab 2):** VIP and Previously Visited flags stored here supplement the guest profile record created in the Guest Details tab.
- **Registration Record:** All Others Information data is stored against the parent registration_id created when the room is assigned in Tab 1.

---

## 3. Functional Requirements

This section details all functional requirements for the Others Information tab, covering every actionable process, field interaction, system behaviour, and data workflow observed in the Cubix HMS implementation.

### 3.1 Tab Navigation & Access

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-OI-001 | The system SHALL display the "Others Information" tab as the 4th tab in the Room Registration wizard navigation bar. | Must Have |
| FR-OI-002 | The Others Information tab SHALL be accessible by clicking its tab header from any other tab in the wizard. | Must Have |
| FR-OI-003 | The tab SHALL also be accessible by clicking "Next" from the Complimentary Item tab (Tab 3). | Must Have |
| FR-OI-004 | Clicking the "Previous" button on the Others Information tab SHALL navigate back to the Complimentary Item tab without any data loss. | Must Have |
| FR-OI-005 | Since Others Information is the last data-entry tab, there is NO "Next" navigation button — only a "Previous" button and the "Check-in" action button. | Must Have |
| FR-OI-006 | The "Check-in" button SHALL remain permanently visible at the bottom of the page throughout the entire session, including while this tab is active. | Must Have |

### 3.2 Section A — Other Information

The "Other Information" section (card header: "Other Information") is the primary sub-section of this tab. It captures travel context, guest classification, and room designation data.

#### 3.2.1 Coming From Field

| Attribute | Detail |
|---|---|
| Label | Coming From |
| Field ID | fo_reg_coming_from |
| Field Name | fo_reg_coming_from |
| Input Type | Text Input |
| Required | No |
| Placeholder | Coming From |
| Description | Free-text field capturing the city, country, or location from which the guest is arriving. Used for travel context, front office reports, and the printed registration card. |
| Actionable Process | Staff types the guest's origin location (e.g., "London", "Dubai International Airport"). No dropdown or auto-complete in current implementation. |

#### 3.2.2 Next Destination Field

| Attribute | Detail |
|---|---|
| Label | Next Destination |
| Field ID | fo_reg_next_destination |
| Field Name | fo_reg_next_destination |
| Input Type | Text Input |
| Required | No |
| Placeholder | Next Destination |
| Description | Free-text field capturing the city or country the guest will travel to after checking out. Used for immigration reporting, front office analytics, and the registration card. |
| Actionable Process | Staff types the guest's onward destination (e.g., "Bangkok", "New York"). No dropdown in current implementation. |

#### 3.2.3 Visit Purpose Field

| Attribute | Detail |
|---|---|
| Label | Visit Purpose |
| Field ID | fo_reg_visite_purpose |
| Field Name | fo_reg_visite_purpose |
| Input Type | Text Input |
| Required | No |
| Placeholder | Visit Purpose |
| Description | Free-text field for the reason for the guest's visit (e.g., Business, Leisure, Conference, Medical, Transit). Appears on the Registration Card and contributes to front office reporting. |
| Actionable Process | Staff types or asks the guest their purpose of visit and enters the value. Common values: Business, Tourism, Conference, Medical. |

#### 3.2.4 Complimentary Guest Dropdown

| Attribute | Detail |
|---|---|
| Label | Complimentary Guest |
| Field ID | fo_reg_complimentary_guest |
| Field Name | fo_reg_complimentary_guest |
| Input Type | Dropdown (select) |
| Required | No |
| Default | -- Please Select -- |
| Options | -- Please Select -- (blank) \| NO (value: 0) \| YES (value: 1) |
| Description | Designates the guest as a complimentary (non-paying) guest at the guest-classification level. When set to YES, the system may suppress posting of charges (No Post behaviour) to the guest's folio, in conjunction with the billing configuration. |
| Actionable Process | Staff selects YES if the entire guest stay is complimentary (e.g., hotel owner guest, press/media, prize winner). Selecting YES triggers downstream billing rules that suppress charge postings. |
| Integration | Works in conjunction with the "Complimentary" flag on Registration Tab 1 and the "No Post" flag. A guest may be complimentary at the guest level (this field) independently of the room-level complimentary flag. |

#### 3.2.5 House Use Dropdown

| Attribute | Detail |
|---|---|
| Label | House Use |
| Field ID | fo_reg_house_use |
| Field Name | fo_reg_house_use |
| Input Type | Dropdown (select) |
| Required | No |
| Default | -- Please Select -- |
| Options | -- Please Select -- (blank) \| YES (value: 1) \| NO (value: 0) |
| Description | Flags whether the room is being used for hotel/house purposes rather than by a paying guest (e.g., staff accommodation, show room, management use). A House Use designation typically means no revenue is posted. |
| Actionable Process | Staff selects YES if the room is being occupied for internal hotel use rather than by a revenue-generating guest. This designation is typically set for non-revenue registrations. |
| Downstream Impact | House Use = YES affects housekeeping room status, Night Audit posting rules, and reporting (House Use rooms are excluded from revenue occupancy statistics). |

#### 3.2.6 Room Owner Dropdown

| Attribute | Detail |
|---|---|
| Label | Room Owner |
| Field ID | fo_reg_room_owner |
| Field Name | fo_reg_room_owner |
| Input Type | Dropdown (select) |
| Required | No |
| Default | -- Please Select -- |
| Options | -- Please Select -- (blank) \| NO (value: 0) \| YES (value: 1) |
| Description | Flags whether the room is being occupied by the room owner (relevant for hotels with property ownership arrangements, e.g., serviced apartments or fractional ownership hotels). When YES, the room is designated as owner-occupied. |
| Actionable Process | Staff selects YES if the registered guest is the owner of the room (e.g., in a managed-residence or apart-hotel model). This triggers owner-use designation in occupancy reporting and billing. |

#### 3.2.7 Is Previously Visited Guest? Checkbox

| Attribute | Detail |
|---|---|
| Label | Is Previously Visited Guest? |
| Field ID | fo_reg_old_guest |
| Field Name | fo_reg_is_previously_visited |
| Input Type | Checkbox |
| Required | No |
| Default | Unchecked |
| Value when Checked | 1 |
| Description | Marks the guest as a returning/repeat guest who has stayed at the hotel previously. Used for guest relationship management, loyalty tracking, and reporting of repeat business percentage. |
| Actionable Process | Staff checks this box if the guest is a known returning visitor (confirmed via the Guest Details Search history or verbal confirmation). When checked, the system records the "returning guest" flag against this registration, enabling repeat guest analytics and personalised service. |

#### 3.2.8 Is Guest VIP? Checkbox

| Attribute | Detail |
|---|---|
| Label | Is Guest VIP? |
| Field ID | fo_reg_guest_vip |
| Field Name | fo_reg_guest_vip |
| Input Type | Checkbox |
| Required | No |
| Default | Unchecked |
| Value when Checked | 1 |
| Description | Flags the guest as a VIP (Very Important Person). VIP designation triggers elevated service protocols, potential room upgrades, amenity deliveries, and management awareness notifications. |
| Actionable Process | Staff checks this box for guests who are designated VIPs by hotel policy (e.g., government officials, executives, celebrities, loyal high-spend guests). When checked, the VIP flag is persisted to the registration record. Front of house and operational teams are notified of VIP arrivals. |

### 3.3 Section B — Departure Information

The "Departure Information" section captures the logistics of the guest's departure transportation, specifically for hotel-arranged airport drop services. It contains four fields. **Conditional visibility rule**: the Airlines Name, Flight Number, and Departure Time (ETD) fields are wrapped inside `<div id="airportDrop" class="d-none">` and are hidden until the **Airport Drop** dropdown is set to YES or TBA. When Airport Drop = NO (or empty), these three fields remain hidden and are not submitted. When the user changes Airport Drop to YES/TBA, the `d-none` class is removed and the three fields become visible and editable. See FR-OI-021 below.

#### 3.3.1 Airport Drop Dropdown

| Attribute | Detail |
|---|---|
| Label | Airport Drop |
| Field ID | fo_reg_airport_drop |
| Field Name | fo_reg_airport_drop |
| Input Type | Dropdown (select) |
| Required | No |
| Default | --- Please Select --- |
| Options | --- Please Select --- (blank) \| NO (value: 0) \| YES (value: 1) \| TBA (value: 2) |
| Description | Indicates whether a hotel-arranged airport drop (departure transfer) service has been arranged for the guest. TBA (To Be Arranged) indicates the guest has requested the service but the details are not yet confirmed. |
| Actionable Processes | (1) NO: No airport drop required. No transportation assignment. (2) YES: Airport drop is confirmed. Staff should complete Airlines Name, Flight Number, and Departure Time (ETD) fields. (3) TBA: Drop is requested but pending confirmation — staff enters partial details if available and updates later. |

#### 3.3.2 Airlines Name Field

| Attribute | Detail |
|---|---|
| Label | Airlines Name |
| Field ID | fo_reg_airline_name |
| Field Name | fo_reg_airline_name |
| Input Type | Searchable Dropdown (select2 + text input hybrid) |
| Required | No (recommended when Airport Drop = YES or TBA) |
| Default | -- Please Select -- |
| Total Options | 64 airlines + TBA option |
| Airlines Available (sample) | Aeroflot, Air Arabia, Air Asia, Air Canada, Air France, Air India, Air Macau, Alitalia, All Nippon Airways, Asiana Airlines, Aviana Airways, Best Air, Biman (BG), Cathay Pacific, China Eastern, China Southern, Emirates (EK), Etihad, Eva Air, Gulf Air, IndiGo, Jet Airways, Kuwait Airways, Malaysia Airlines, Novo Air, Pakistan Airlines, Qatar Airways, Regent Airways, Saudia, Singapore Airlines, SriLankan Air, Thai Airways, Turkish Airlines (TK), US Bangla, Air Astra, TBA (and 30+ others) |
| Description | Specifies the airline the guest is departing on. Used by the concierge/transport team to time the airport drop correctly and for transportation scheduling. |
| Actionable Process | Staff selects the airline from the dropdown (searchable via type-ahead). If the airline is not in the list, "TBA" can be selected. The selection is stored as a numeric ID referencing the airline master table. |

#### 3.3.3 Flight Number Field

| Attribute | Detail |
|---|---|
| Label | Flight Number |
| Field ID | fo_reg_flight_number |
| Field Name | fo_reg_flight_number |
| Input Type | Text Input |
| Required | No |
| Placeholder | Flight Number |
| Description | The flight number of the guest's departing flight (e.g., EK585, BG048). Used for transportation scheduling — the concierge team uses this to verify departure time with the airline. |
| Actionable Process | Staff types the flight number as provided by the guest (e.g., from their itinerary or boarding pass). Alphanumeric free text. Combined with Airline Name and ETD for full departure logistics. |

#### 3.3.4 Departure Time (ETD) Field

| Attribute | Detail |
|---|---|
| Label | Departure Time (ETD) |
| Field ID | fo_reg_departure_time |
| Field Name | fo_reg_departure_time |
| Input Type | Time Input (HTML5 time type) |
| Required | No |
| Placeholder | Departure Time (ETD) |
| Format | HH:MM (24-hour clock) |
| Description | The Estimated Time of Departure (ETD) of the guest's flight. The concierge uses this to calculate when to dispatch the airport drop vehicle (typically 3-4 hours before ETD for international flights). |
| Actionable Process | Staff enters the flight departure time in 24-hour format using the time picker control (e.g., 14:30 for a 2:30 PM flight). The time is saved against the registration record for transport scheduling and can be referenced during checkout. |

### 3.4 Section C — Credit Card Information

The "Credit Card Information" section captures the guest's payment card details for room charge guarantee, pre-authorisation, or incidental charges. It contains five fields. Note: Per system security policy, Claude (as the automation agent) cannot enter actual card numbers — this section is documented for BRD purposes only.

#### 3.4.1 Card Type Dropdown

| Attribute | Detail |
|---|---|
| Label | Card Type |
| Field ID | fo_reg_card_type |
| Field Name | fo_reg_card_type |
| Input Type | Dropdown (select) |
| Required | No |
| Default | -- Please Select -- |
| Options | -- Please Select -- \| American Express (1) \| Master Card (2) \| Visa Card (3) \| Discover Card (4) \| Taka Pay (5) |
| Description | Specifies the payment card network/type for the guarantee card. The card type determines which payment processor to use and affects card number validation rules. |
| Actionable Process | Staff selects the card type from the dropdown based on the physical card presented by the guest. The type determines the expected card number length and format for validation. |

#### 3.4.2 Card Number Field

| Attribute | Detail |
|---|---|
| Label | Card Number |
| Field ID | fo_reg_card_number |
| Field Name | fo_reg_card_number |
| Input Type | Number Input |
| Required | No |
| Placeholder | Card Number |
| Description | The payment card number (PAN — Primary Account Number). Stored for payment guarantee purposes. Must be handled in accordance with PCI-DSS data security standards. |
| Actionable Process | Staff enters the card number as presented by the guest. The field accepts numeric input only. PCI-DSS compliance requires this number to be masked/encrypted in storage and display (last 4 digits shown, remainder masked as XXXX-XXXX-XXXX-1234). |
| Security Note | Card number storage must comply with PCI-DSS Level requirements. Plain-text storage is not permitted. Tokenisation or encryption is mandatory. |

#### 3.4.3 Card Holder Name Field

| Attribute | Detail |
|---|---|
| Label | Card Holder Name |
| Field ID | fo_reg_card_holder_name |
| Field Name | fo_reg_card_holder_name |
| Input Type | Text Input |
| Required | No |
| Placeholder | Card Holder Name |
| Description | The name of the cardholder as it appears on the payment card. Used for payment verification and authorisation. |
| Actionable Process | Staff enters the name exactly as printed on the card (e.g., "JOHN A SMITH"). Used for card verification at checkout. |

#### 3.4.4 Expiry Date Field

| Attribute | Detail |
|---|---|
| Label | Expiry Date |
| Field ID | fo_reg_card_expiry_date |
| Field Name | fo_reg_card_expiry_date |
| Input Type | Text Input |
| Required | No |
| Placeholder | Expiry Date |
| Format | MM/YY (card standard format) |
| Description | The expiry date of the payment card. Used to verify card validity at time of registration and at checkout. |
| Actionable Process | Staff enters the card's expiry date in MM/YY format (e.g., 09/28 for September 2028). System should validate that the expiry date is not in the past. |

#### 3.4.5 Card Reference Field

| Attribute | Detail |
|---|---|
| Label | Card Reference |
| Field ID | fo_reg_card_reference |
| Field Name | fo_reg_card_reference |
| Input Type | Text Input |
| Required | No |
| Placeholder | Card Reference |
| Description | A reference number associated with the card pre-authorisation or guarantee transaction. This is typically the authorisation code returned by the payment terminal after a pre-authorisation swipe. |
| Actionable Process | After swiping/tapping the guest's card at the payment terminal for pre-authorisation, staff enters the authorisation reference code returned by the terminal into this field. This creates a linkage between the HMS record and the payment terminal transaction. |

### 3.5 Action Buttons & Navigation Controls

| Button/Control | Type | Location | Actionable Process |
|---|---|---|---|
| Previous | Warning/Yellow Button (input[type="button"]) | Bottom-right of tab content, class: btnPrevious | Navigates back to the Complimentary Item tab (Tab 3). Does NOT submit or save any data entered on this tab. All Others Information data entered in the current session is retained in browser memory for when the user returns. |
| Check-in | Success/Green Button | Bottom-center of page (persistent across all tabs) | The primary submission action. Collects ALL data from all 4 wizard tabs (Registration, Guest Details, Complimentary Item, Others Information) and submits to the server as a single unified Check-in transaction. On success: creates the Room Registration record, marks the room as Occupied, creates guest profile entries, saves all Others Information fields, saves credit card reference, and confirms check-in. On failure: displays validation errors. |
| Update Registration | Warning/Yellow Button (hidden by default, class: d-none) | Bottom-center of page (edit mode only) | Visible only when an existing checked-in registration is opened for modification. Replaces the Check-in button in edit mode. Saves all modified fields (including Others Information changes) to the existing registration record. |

### 3.6 Check-in Process from Others Information Tab

The Others Information tab is the last step before Check-in. The complete Check-in process initiated from this tab is:

- **Step 1** — Staff completes all desired fields across all 4 tabs.
- **Step 2** — Staff reviews the completed Others Information fields (travel details, VIP status, card info).
- **Step 3** — Staff clicks the green "Check-in" button at the bottom of the page.
- **Step 4** — System performs cross-tab validation: checks that mandatory fields on Registration (Room Type, Room Number, Check-in Date) and Guest Details (Title, First Name, Country) are complete.
- **Step 5** — If validation fails: error messages are displayed identifying the incomplete fields; system redirects to the tab containing the failing fields; Check-in is aborted.
- **Step 6** — If validation passes: system submits all data to the database simultaneously as a single atomic transaction.
- **Step 7** — Database operations: (a) Room Registration record is created/updated. (b) Guest profile records are saved/linked. (c) Complimentary items are saved to the junction table. (d) Others Information fields (all 17 fields) are saved. (e) Room status is updated to Occupied. (f) Credit card reference is stored (securely, per PCI-DSS).
- **Step 8** — On success: confirmation notification is displayed; system may redirect to post-check-in screen or display the registration record with status "Checked-In".
- **Step 9** — A Blank Registration Card can be printed from the tab strip for the guest's signature.

### 3.7 Edit Mode (Update Registration) Process

When an existing checked-in registration is reopened from the Search tab or guest search:

- **Step 1** — All previously saved Others Information fields are pre-populated from the database record.
- **Step 2** — Staff modifies the desired fields (e.g., updates flight number, adds card reference, changes VIP status).
- **Step 3** — Staff clicks "Update Registration" (which has replaced the Check-in button).
- **Step 4** — System validates modified fields and saves changes to the existing registration record.
- **Step 5** — Success confirmation is displayed; the record is updated without altering the check-in date/time.

---

## 5. UI Components Summary

This section documents every UI component present on the Others Information tab, its type, visual characteristics, interaction behaviour, and state management.

### 5.1 Page-Level Components

| Component | Type | Location | Description & Behaviour |
|---|---|---|---|
| Tab Navigation Bar | Tab Strip | Top of page (persistent) | Contains 6 tab links: Registration, Guest Details, Complimentary Item, Others Information, Search, Blank Registration Card. The "Others Information" tab is highlighted/active. Clicking any tab navigates to it. |
| Others Information Header | Page Header (Dark Navy) | Top of tab content area | Dark navy banner with white text "Others Information" — identifies the active tab. |
| Other Information Card | Card/Panel with dark header | First section within tab | Dark navy card header "Other Information". Contains 8 fields: Coming From, Next Destination, Visit Purpose, Complimentary Guest, House Use, Room Owner, Is Previously Visited Guest?, Is Guest VIP? |
| Departure Information Card | Card/Panel with dark header | Second section within tab | Dark navy card header "Departure Information". Contains 4 fields: Airport Drop, Airlines Name, Flight Number, Departure Time (ETD). |
| Credit Card Information Card | Card/Panel with dark header | Third section within tab | Dark navy card header "Credit Card Information". Contains 5 fields: Card Type, Card Number, Card Holder Name, Expiry Date, Card Reference. |
| Previous Button | Warning/Yellow Button (input[type="button"]) | Bottom-right of tab content | Navigates back to Complimentary Item tab. Class: btn_modern_design warning_new_button btnPrevious. |
| Check-in Button | Success/Green Button | Bottom-center of page (persistent) | Primary submission action. Commits all wizard data to the database. Icon: calendar/suitcase. Always visible. |
| Update Registration Button | Warning/Yellow Button (hidden, class: d-none) | Bottom-center (edit mode only) | Appears when editing a checked-in registration. Replaces Check-in. |

### 5.2 Section A — Other Information: Field Components

| Field Label | Component Type | Field ID | Required | Default Value | Visual Notes |
|---|---|---|---|---|---|
| Coming From | Text Input | fo_reg_coming_from | No | Empty (placeholder: "Coming From") | Full-width in row; simple text box |
| Next Destination | Text Input | fo_reg_next_destination | No | Empty (placeholder: "Next Destination") | Mid-width text box |
| Visit Purpose | Text Input | fo_reg_visite_purpose | No | Empty (placeholder: "Visit Purpose") | Mid-width text box; right-aligned in row |
| Complimentary Guest | Dropdown (select) | fo_reg_complimentary_guest | No | -- Please Select -- | Left dropdown; options: blank, NO, YES |
| House Use | Dropdown (select) | fo_reg_house_use | No | -- Please Select -- | Center dropdown; options: blank, YES, NO |
| Room Owner | Dropdown (select) | fo_reg_room_owner | No | -- Please Select -- | Right dropdown; options: blank, NO, YES |
| Is Previously Visited Guest? | Checkbox | fo_reg_old_guest | No | Unchecked | Left-aligned checkbox with inline label |
| Is Guest VIP? | Checkbox | fo_reg_guest_vip | No | Unchecked | Right-aligned checkbox with inline label; centered in row |

### 5.3 Section B — Departure Information: Field Components

| Field Label | Component Type | Field ID | Required | Default Value | Visual Notes |
|---|---|---|---|---|---|
| Airport Drop | Dropdown (select) | fo_reg_airport_drop | No | --- Please Select --- | Half-width dropdown; options: blank, NO, YES, TBA |
| Airlines Name | Searchable Dropdown (select2) | fo_reg_airline_name | No | -- Please Select -- | Full-width searchable dropdown; 64 options + TBA; type-ahead search supported; hidden text input companion for select2 |
| Flight Number | Text Input | fo_reg_flight_number | No | Empty (placeholder: "Flight Number") | Half-width text box |
| Departure Time (ETD) | Time Input (HTML5 type="time") | fo_reg_departure_time | No | Empty (placeholder: "Departure Time (ETD)") | Half-width time picker; 24-hour HH:MM format; browser-native time picker UI |

### 5.4 Section C — Credit Card Information: Field Components

| Field Label | Component Type | Field ID | Required | Default Value | Visual Notes |
|---|---|---|---|---|---|
| Card Type | Dropdown (select) | fo_reg_card_type | No | -- Please Select -- | Left-aligned half-width dropdown; 5 card type options |
| Card Number | Number Input | fo_reg_card_number | No | Empty (placeholder: "Card Number") | Center half-width; numeric only; should be masked in display |
| Card Holder Name | Text Input | fo_reg_card_holder_name | No | Empty (placeholder: "Card Holder Name") | Right-aligned; free text |
| Expiry Date | Text Input | fo_reg_card_expiry_date | No | Empty (placeholder: "Expiry Date") | Half-width; MM/YY format expected; no native date picker |
| Card Reference | Text Input | fo_reg_card_reference | No | Empty (placeholder: "Card Reference") | Half-width; alphanumeric reference code |

### 5.5 Visual Layout Summary

The Others Information tab has a clean, vertically stacked layout with three dark-header cards. The layout is responsive (Bootstrap-based) with a 12-column grid:

- **Other Information card:** Row 1 — Coming From (col-6), Next Destination (col-4), Visit Purpose (col-4). Row 2 — Complimentary Guest (col-4), House Use (col-4), Room Owner (col-4). Row 3 — Is Previously Visited (col-4 with checkbox), Is Guest VIP? (col-4 offset, with checkbox).
- **Departure Information card:** Row 1 — Airport Drop (col-6). Row 2 — Airlines Name (col-12, full width). Row 3 — Flight Number (col-6), Departure Time ETD (col-6).
- **Credit Card Information card:** Row 1 — Card Type (col-4), Card Number (col-4), Card Holder Name (col-4). Row 2 — Expiry Date (col-6), Card Reference (col-6).
- **Bottom navigation:** Previous button (right-aligned), Check-in button (center, persistent).

---

## 6. Business Rules

### 6.1 Guest Classification Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-OI-001 | The "Complimentary Guest" field (YES), the "House Use" field (YES), and the "Room Owner" field (YES) are mutually exclusive — only one of these three classifications should apply to any single registration. A room cannot simultaneously be a Complimentary Guest room AND a House Use room. | System should ideally enforce or warn against selecting YES for more than one of these three classification fields simultaneously. |
| BR-OI-002 | When "Complimentary Guest" = YES, the billing engine SHALL suppress standard charge postings to the guest's folio, treating all room charges as zero-revenue (or posting to a complimentary expense account). This works in conjunction with the "No Post" flag on the Registration tab. | Billing integration must check this flag before posting nightly room charges. |
| BR-OI-003 | When "House Use" = YES, the room SHALL be excluded from revenue-generating occupancy statistics in night audit and reporting. Room charges are posted to an internal house account. | Night Audit must apply House Use posting rules for this registration. |
| BR-OI-004 | When "Room Owner" = YES, the room is designated as owner-occupied. Posting rules for owner rooms depend on hotel configuration (may be zero-rated or posted to an owner account). | Owner-use posting rules must be defined in system configuration. |
| BR-OI-005 | The "Is Previously Visited Guest?" flag is an informational classifier. It does not block or alter any workflow. It enriches guest history data for CRM and repeat-business reporting. | No workflow restriction; data analytics use only. |
| BR-OI-006 | The "Is Guest VIP?" flag SHALL trigger operational notifications. When a VIP guest registration is checked in, the system SHALL generate an alert or notification to the Front Office Manager and relevant department heads. | Notification mechanism required for VIP check-ins. |

### 6.2 Departure Information Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-OI-007 | When "Airport Drop" = YES or TBA, it is strongly recommended (should) to complete the Airlines Name, Flight Number, and Departure Time (ETD) fields to enable the transport team to plan the departure transfer. | These fields should be conditionally highlighted or prompted when Airport Drop is YES/TBA. |
| BR-OI-008 | The Departure Time (ETD) must be a valid time value in HH:MM format. Future times are expected (relative to the guest's checkout date), but the system does not currently enforce time-against-date validation. | Time format validation must be applied; future-time validation is a recommended enhancement. |
| BR-OI-009 | The Airlines Name dropdown must reference a valid entry from the airline master table. "TBA" (value: 64) is a valid selection when the airline is not yet confirmed. | FK integrity required; custom airline entry not supported in current implementation. |
| BR-OI-010 | If "Airport Drop" = NO, the Airlines Name, Flight Number, and Departure Time fields are informational only and may be left blank without affecting validation. | No mandatory dependency when Airport Drop = NO. |

### 6.3 Credit Card Information Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-OI-011 | Credit card data captured in this section is a guarantee only — it does NOT constitute a financial transaction. The card is pre-authorised (swiped at the terminal) and the reference code is entered in the "Card Reference" field. | No payment is processed through the HMS UI. Card data is for reference and guarantee purposes only. |
| BR-OI-012 | Card number storage MUST comply with PCI-DSS (Payment Card Industry Data Security Standard). Card numbers must be encrypted or tokenised in the database. They must not be stored or displayed in plain text. | Critical security requirement — encryption/tokenisation in the database is mandatory. |
| BR-OI-013 | Card expiry date must be validated to ensure it is not in the past at the time of registration. An expired card should not be accepted as a valid guarantee. | Expiry date must be checked against the current date at Check-in submission. |
| BR-OI-014 | The credit card section is entirely optional. A registration can be checked in without any credit card information if the hotel's policy permits (e.g., cash deposit or corporate billing arrangements). | No field in the credit card section is mandatory for Check-in. |
| BR-OI-015 | The "Card Reference" field links the HMS record to the physical payment terminal authorisation. This reference must be retained for chargebacks, disputes, and audit trail purposes. | Card Reference is a critical operational field despite being optional in the UI. |

### 6.4 Navigation & Save Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-OI-016 | Data entered on the Others Information tab is NOT saved to the database by merely clicking "Previous" or navigating to another tab. It is held in the browser session only. | All data is saved exclusively at Check-in or Update Registration submission. |
| BR-OI-017 | The Check-in action is atomic — either all data across all 4 tabs is saved successfully, or no data is saved. A partial save is not permitted. | Database transaction must be wrapped in a single atomic commit. |
| BR-OI-018 | If the user returns to the Others Information tab after navigating away (without submitting), all previously entered fields must be retained from the browser session. | Session state management must persist across wizard tab navigation. |
| BR-OI-019 | Since Others Information is the final data-entry tab, staff should be encouraged to review all entries before clicking Check-in. A pre-Check-in summary review is recommended. | UX recommendation: show a summary modal before Check-in is confirmed. |

---

## 7. Non-Functional Requirements

### 7.1 Performance Requirements

| NFR ID | Requirement | Target Metric |
|---|---|---|
| NFR-OI-001 | The Others Information tab SHALL load all fields, dropdowns, and section cards within an acceptable response time from tab activation. | <2 seconds on standard broadband |
| NFR-OI-002 | The Airlines Name searchable dropdown SHALL respond to type-ahead filtering within an acceptable time as the user types. | <300ms for type-ahead filtering of 64 options (client-side) |
| NFR-OI-003 | The Departure Time (ETD) time picker SHALL open immediately upon field interaction. | <100ms (native browser time picker) |
| NFR-OI-004 | The full Check-in submission (including all 4 tabs' data) SHALL complete within an acceptable server response time under normal load. | <3 seconds under up to 20 concurrent check-ins |

### 7.2 Usability Requirements

| NFR ID | Requirement | Standard |
|---|---|---|
| NFR-OI-005 | The three section cards (Other Information, Departure Information, Credit Card Information) SHALL be clearly visually separated with distinct dark-header card panels. | Consistent with HMS design system |
| NFR-OI-006 | Dropdown options (Complimentary Guest, House Use, Room Owner, Airport Drop, Card Type) SHALL present clear, unambiguous option labels including a blank default option. | Clear option labelling; no abbreviated or cryptic values |
| NFR-OI-007 | The Departure Time (ETD) field SHALL use a native HTML5 time picker to prevent format errors and improve data entry speed. | HTML5 type="time" enforces HH:MM format natively |
| NFR-OI-008 | The Card Number field SHALL mask entered digits after the first 6 and before the last 4 in the display (PAN masking) to prevent shoulder-surfing. | PCI-DSS display masking requirement |
| NFR-OI-009 | Staff with basic computer literacy SHALL be able to complete the full Others Information tab within 2 minutes for a standard registration. | Operational efficiency target |
| NFR-OI-010 | All form fields SHALL support keyboard-only navigation using the Tab key in logical left-to-right, top-to-bottom sequence across all three sections. | Accessibility — keyboard navigation support |

### 7.3 Security Requirements

| NFR ID | Requirement | Notes |
|---|---|---|
| NFR-OI-011 | Only authenticated HMS users with Front Office roles (Front Desk Agent, Supervisor, Duty Manager) SHALL access the Others Information tab. | RBAC enforced at module and tab level |
| NFR-OI-012 | Credit card data (Card Number, Expiry Date, Card Holder Name) SHALL be transmitted over TLS/HTTPS encrypted connections only. Plain HTTP transmission is prohibited. | PCI-DSS Requirement 4.1 |
| NFR-OI-013 | Card numbers stored in the database SHALL be encrypted using industry-standard encryption (AES-256 minimum) or replaced with a payment token from a certified tokenisation service. | PCI-DSS Requirement 3.4 |
| NFR-OI-014 | The Card Number field SHALL not be pre-populated or auto-filled by the browser. The autocomplete attribute must be set to "off" or "cc-number" as appropriate. | Prevents browser-saved card data from auto-filling into wrong forms |
| NFR-OI-015 | Setting the VIP flag ("Is Guest VIP?") SHALL require the user's role to be at least Front Desk Supervisor level to prevent unauthorised VIP designations. | Privilege-level control on VIP flag |
| NFR-OI-016 | Setting the Complimentary Guest or House Use flag to YES SHALL require Supervisor or Duty Manager role, as these directly affect revenue posting. | Financial impact; elevated privilege required |

### 7.4 Reliability & Availability

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-OI-017 | All Others Information data entered during the registration session SHALL be preserved in browser session storage to survive accidental tab navigation or brief network interruptions. | Session persistence across wizard tab switches |
| NFR-OI-018 | If the Check-in submission fails due to a server error, the system SHALL display a descriptive error message and retain all entered data for re-submission without requiring re-entry. | Graceful failure — no data loss on server error |
| NFR-OI-019 | The Others Information tab SHALL remain fully functional during peak hotel check-in periods (typically 14:00-18:00) with multiple concurrent users. | Load tested for 20+ concurrent check-in operations |

### 7.5 Data Quality & Compliance

| NFR ID | Requirement | Notes |
|---|---|---|
| NFR-OI-020 | The Departure Time (ETD) field SHALL accept only valid time values in HH:MM format. Invalid time strings SHALL be rejected before submission. | Time format validation |
| NFR-OI-021 | The Card Expiry Date field SHALL validate that the entered date is not in the past relative to the current system date at the time of Check-in submission. | Prevents expired card registration |
| NFR-OI-022 | Guest classification flags (VIP, Complimentary, House Use, Room Owner, Previously Visited) SHALL be logged in an audit trail with the setting user ID and timestamp when changed post-check-in. | Audit trail for sensitive classification changes |
| NFR-OI-023 | The system SHALL comply with local hotel industry regulations requiring collection of guest travel data (Coming From, Next Destination, Visit Purpose) for immigration reporting purposes (e.g., tourism statistics, C-Form equivalents). | Regulatory compliance for foreign national guests |

---

## 8. AI-DLC Audit Update — 2026-04-22

Findings from live audit (Cubix HMS V-9.3.0, Kazi Resort).

### 8.1 Verified field-ID map
All 17 field IDs in §3.2–3.4 and §5.2–5.4 match the live DOM. Airport Drop options confirmed as: `-- Please Select --, NO, YES, TBA`. Card Type master confirmed with 5 entries + placeholder: American Express, Master Card, Visa Card, Discover Card, Taka Pay. Airline master contains 65 entries (64 airlines + TBA) inside select2.

### 8.2 Known defects / behaviours

| ID | Behaviour | Impact |
|---|---|---|
| DEF-OI-01 | Airlines Name / Flight Number / Departure Time (ETD) are hidden inside `<div id="airportDrop" class="d-none">` — visibility is conditional on Airport Drop selection (YES/TBA). | **Now documented** in §3.3 and FR-OI-021 |
| DEF-OI-02 | Expiry Date input has no MM/YY pattern mask — format is validated only server-side at Check-in | Add client-side pattern mask |
| DEF-OI-03 | Mutual-exclusivity between Complimentary Guest / House Use / Room Owner not enforced in UI (BR-OI-001 says "ideally enforce or warn" — current state is neither) | Decide: hard block vs soft warning |
| DEF-OI-04 | Card Number field does not mask digits after first 6 / before last 4 on blur (NFR-OI-008) | Add display masking |

### 8.3 Functional requirements added

| FR ID | Requirement |
|---|---|
| FR-OI-021 | **Conditional reveal of Airport Drop logistics** — When the Airport Drop dropdown value changes to `YES` or `TBA`, the `#airportDrop` wrapper SHALL have `d-none` removed and the Airlines Name (select2), Flight Number (text), and Departure Time (ETD) (type=time) fields SHALL become visible and editable. When the value changes back to `NO` or empty, the wrapper SHALL re-add `d-none` and the three fields SHALL be cleared before submit. |
| FR-OI-022 | **Expiry Date pattern mask** — The Credit Card Expiry Date field SHALL enforce MM/YY format client-side with a pattern mask; invalid patterns SHALL be rejected on blur. |
| FR-OI-023 | **Card Number masking on blur** — The Card Number field SHALL display the entered value with digits 7 through (length − 4) replaced by `X` immediately after the field loses focus. |

### 8.4 Business rules added

| ID | Rule |
|---|---|
| BR-OI-020 | **Airport Drop field clearing** — When Airport Drop = NO or empty at Check-in, any previously entered Airlines Name / Flight Number / Departure Time SHALL be cleared and not persisted. |
| BR-OI-021 | **Mutual exclusivity enforcement** — System SHALL prevent selecting YES simultaneously on more than one of (Complimentary Guest, House Use, Room Owner). Attempting the second selection displays a blocking modal; the previous YES must be cleared first. (Upgraded from BR-OI-001 "soft warning".) |
| BR-OI-022 | **VIP-flag audit** — Setting Is Guest VIP? SHALL be logged in an audit trail regardless of check-in state; the flag's state changes are visible to Duty Manager role only. |
| BR-OI-023 | **Airline TBA handling** — When Airlines Name = TBA, staff SHALL be prompted at check-out to update it with the confirmed airline before folio finalisation. |

### 8.5 User journeys added
- UJ-OI-A Airport Drop reveal/hide toggle — user toggles Airport Drop YES → three fields appear → user fills → toggles back to NO → fields clear and hide before submit.
- UJ-OI-B Classification conflict — user sets Complimentary Guest = YES, then attempts House Use = YES → blocking modal "A room cannot be both Complimentary Guest and House Use; please clear the other first" → user clears first selection and proceeds.
- UJ-OI-C Credit card expired — user enters MM/YY in the past → on blur, error "Card expired; please use a current card"; field border red; Check-in blocked until corrected or field cleared.
- UJ-OI-D Session expiry mid-card-entry — on session timeout, card number and holder-name fields are cleared for security; other fields repopulate from session state after re-login.
- UJ-OI-E Role-based lockout — Front Desk Agent opens tab; VIP, Complimentary Guest, House Use fields are visually disabled with tooltip "Requires Supervisor privilege"; Supervisor sees them enabled.

