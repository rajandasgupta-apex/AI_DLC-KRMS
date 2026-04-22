# BUSINESS REQUIREMENTS DOCUMENT

## Registration Tab Module

**Cubix Hotel Management System (HMS)**
Front Office Module | Room Registration | Registration Tab (Tab 1)

| Field | Value |
|---|---|
| Document Title | BRD – Registration Tab (Room Registration Module) |
| System | Cubix HMS V-9.3.0 (deployed) — BRD originally drafted against v13.1.0 |
| Module Path | Front Office > Room Registration > Registration (Tab 1) |
| Version | 1.0 |
| Prepared Date | April 13, 2026 |
| Status | Draft |
| Prepared For | Product / Development / QA Team |
| Application URL | https://demo.cubixsmb.com/front_office/new-room-registration |

---

## 1. Executive Summary

This Business Requirements Document (BRD) defines the detailed functional, data, UI, and business requirements for the **Registration Tab** — the first and primary tab within the Room Registration workflow of the Front Office module in Cubix HMS (Hotel Management System).

The Registration Tab is the entry point for all guest check-in activities in the hotel. It is the most data-intensive tab in the Room Registration wizard, responsible for capturing every critical piece of room assignment and billing information required to register a guest — whether the guest is a walk-in, linked to an existing reservation, or an in-house guest having their stay modified.

The tab is organized into three major functional sections: (1) the Room Registration Header section capturing booking-level metadata, (2) the Room Detailed Information section capturing per-room financial and stay details, and (3) the Additional Service section capturing supplementary services. Together, these sections drive the creation of a complete room registration record, enable room inventory management, and set up the financial parameters for the guest folio.

**Scope of this BRD:**
- Module: Registration Tab (Tab 1 of Room Registration)
- Parent System: Cubix HMS – Front Office
- Primary Users: Front Desk Agents, Reservation Agents, Duty Managers, Night Auditors
- Trigger: Walk-in guest check-in or reservation-linked room assignment
- Output: Room registration record with room assignment, billing parameters, and supplementary service data saved to the HMS database

**Business Objective:**
To enable hotel front-desk staff to efficiently, accurately, and completely capture all room registration details — including room assignment, rate configuration, billing flags, occupancy, additional services, and stay metadata — in a single, guided interface that drives all downstream hotel operations including housekeeping, billing, night audit, and reporting.

---

## 2. Module Context & Positioning

The Registration Tab does not function as a standalone module. It is Tab 1 of 5 within the Room Registration wizard — the core operational workflow of the Front Office module in Cubix HMS. Data entered on this tab is the foundational dataset for all subsequent tabs and all downstream hotel operations.

### 2.3 Dependencies

**Upstream Dependencies (what the Registration Tab depends on):**

| Dependency | Source | Requirement |
|---|---|---|
| User Authentication | HMS Auth / Session | Staff must be logged in with an active, valid session before accessing the Registration Tab |
| Role-Based Access | HMS RBAC Configuration | Only users with appropriate Front Office roles (e.g., Front Desk Agent, Reservation Agent, Duty Manager) can access and submit the Registration Tab |
| Room Inventory & Type Master | HMS Room Configuration | Room Types dropdown is populated from the active room type master; Room List modal is powered by real-time room inventory availability |
| Rate & Pricing Master | HMS Rate Configuration | Rack Rate, Service Charge %, VAT %, City Charge values are fetched from the rate plan master based on room type and stay dates |
| Currency Master | HMS Finance Configuration | Currency Name dropdown is populated from the currency master; default currency (BDT) and conversion rate (1.00) are system defaults |
| Listed Company Master | HMS Company Configuration | Listed Company dropdown is populated from the corporate company master maintained in HMS back-office |
| Additional Service Master | HMS Service Configuration | Service Name dropdown in the Additional Service section is populated from the HMS service charge master list |
| Market Segment Master | HMS Sales Configuration | Market Segment dropdown options (Dhaka Office, Cox's Office) are from the HMS market segment configuration |
| Guest Source Master | HMS Marketing Configuration | Guest Source dropdown options (Web Site, Marketing, Other Source, Broker) are from the HMS source master |
| Meal Plan Master | HMS F&B Configuration | Meal Plan dropdown options are from the HMS meal plan master (BED ONLY, BED & BREAKFAST, etc.) |
| Reference Master | HMS Front Office Configuration | Reference dropdown options (Front Office, Sales Team, booking portals, etc.) are from the HMS reference master |
| Reservation Records (Optional) | Room Reservation Module | If the stay is linked to a reservation, the Reservation dropdown fetches active reservations; selecting one pre-populates dates, room type, rate, and other data |
| Application Date | HMS System Date | The application date (e.g., 05-03-2026) is displayed in the header and used as the default Check-In Date; it controls night audit cycles and date validations |

**Downstream Dependencies (what depends on Registration Tab data):**

| Downstream Consumer | Dependency |
|---|---|
| Guest Details Tab (Tab 2) | Room Number and Occupancy (Adult/Child counts) from Tab 1 are used to set up per-room guest profile entries |
| Complimentary Item Tab (Tab 3) | Complimentary and House Use flags from Tab 1 govern the stay type and complimentary item assignment rules |
| Others Information Tab (Tab 4) | Billing context (No Post, Complimentary) from Tab 1 affects folio behavior captured in Tab 4 |
| Check-In Record | All fields from Tab 1 are included in the Check-In API payload; the registration record is created from Tab 1 data |
| Room Inventory | Room Number selection in Tab 1 marks the room as Occupied in the real-time room inventory upon check-in |
| Guest Folio / Billing | Total Room Rent Amount, Service Charge, VAT, City Charge, Additional Charges, Negotiated Rate, and Additional Service amounts from Tab 1 initialize the guest folio charges |
| Night Audit | Check-In Date, Departure Date, Total Nights, No Post flag, and billing parameters from Tab 1 drive night audit auto-posting and room revenue accounting |
| Housekeeping | Room Number and occupancy (Adult/Child) from Tab 1 trigger housekeeping room assignment and turndown service scheduling |
| Reports | Registration data (room type, rate, dates, company, market segment, guest source, meal plan, reference) flows into all Front Office operational and revenue reports |
| Room Calendar | Room Number and stay dates from Tab 1 update the Room Calendar occupancy display |
| Blank Registration Card | Registration data from Tab 1 (room, dates, rate, company, remarks) is used to generate the Blank Registration Card printout |
| Bill Transfer | Registration ID from Tab 1 is required for bill transfer operations between rooms or to company accounts |

---

## 3. Functional Requirements

This section details every actionable process, user interaction, system behavior, and data flow requirement on the Registration Tab.

### 3.1 Page Access & Tab Navigation

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-NAV-01 | The Registration Tab shall be the default active tab when the Room Registration page loads. | Must Have |
| FR-NAV-02 | The user shall be able to navigate to the Registration Tab by clicking the "Registration" tab button in the navigation bar from any other tab. | Must Have |
| FR-NAV-03 | The "Next" button on the Registration Tab shall navigate the user to the Guest Details tab (Tab 2) without losing any data entered on Tab 1. | Must Have |
| FR-NAV-04 | All data entered on the Registration Tab shall be retained in the current session when the user navigates to other tabs and returns. | Must Have |
| FR-NAV-05 | The "Blank Registration Card" button shall always be accessible from the Registration Tab and shall open the blank registration card in a new browser tab. | Must Have |
| FR-NAV-06 | The Check-In button shall be persistently visible at the bottom of the page from the Registration Tab, allowing staff to initiate check-in at any step. | Must Have |
| FR-NAV-07 | When the Check-In button is clicked from the Registration Tab, all tab data across all five tabs shall be validated and submitted. | Must Have |

### 3.2 Section A: Room Registration Header — Reservation Linkage

This section documents the requirements for the global reservation linking and booking-level fields visible in the "Room Registration" panel at the top of the tab.

#### 3.2.1 Réservation Checkbox & Dropdown

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-RES-01 | A toggle checkbox shall be displayed adjacent to the Reservation drop-down. When unchecked (default), the Reservation drop-down shall be disabled — this represents a walk-in registration. | Must Have |
| FR-RES-02 | When the Reservation checkbox is checked by the user, the Reservation dropdown shall be enabled and the user may select an existing active reservation. | Must Have |
| FR-RES-03 | The Reservation dropdown shall be populated with all active, pending reservations for the current property (format: Reservation No. – Guest Name & Source, e.g., "GR00001252 - Mr. Sadman Dhaka Sales"). | Must Have |
| FR-RES-04 | Upon selecting a reservation from the dropdown, the system shall auto-populate relevant fields including Check-In Date, Departure Date, Room Type, Rack Rate, Currency, Conversion Rate, and any pre-configured complementary items. | Must Have |
| FR-RES-05 | If the selected reservation has multiple rooms, the Room Detailed Information section shall allow adding each room individually. | Must Have |
| FR-RES-06 | The system shall store reservation_id as a hidden field (fo_from_reservation) to track whether the registration is walk-in (value: 0) or reservation-linked (value: reservation_id). | Must Have |

#### 3.2.2 Check-In Date

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-CID-01 | The Check-In Date field shall be pre-populated with the current application date (e.g., 2026-03-05) on page load. | Must Have |
| FR-CID-02 | The Check-In Date field shall be read-only (not directly editable) and shall only change via the application date or reservation selection. | Must Have |
| FR-CID-03 | The Check-In Date format shall be YYYY-MM-DD. | Must Have |
| FR-CID-04 | The Check-In Date shall be used as the default Service From Date in the Additional Service section. | Must Have |

#### 3.2.3 Check-In Time

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-CIT-01 | The Check-In Time field shall be editable and shall default to 02:00 PM (14:00:00) on page load. | Must Have |
| FR-CIT-02 | The Check-In Time shall be a time-picker input (type="time") displaying in 12-hour AM/PM format on the UI. | Must Have |
| FR-CIT-03 | The Check-In Time shall be stored in 24-hour format internally (e.g., 14:00:00). | Must Have |

#### 3.2.4 Departure Date

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-DD-01 | The Departure Date field shall default to the day following the Check-In Date (Check-In Date + 1 night). | Must Have |
| FR-DD-02 | The Departure Date field shall be disabled (not directly editable at the header level); it is derived from the room-level dates in the Room Detailed Information section or from the reservation. | Must Have |
| FR-DD-03 | The Departure Date must always be greater than the Check-In Date; the system shall enforce this validation. | Must Have |
| FR-DD-04 | The Departure Date format shall be YYYY-MM-DD. | Must Have |

#### 3.2.5 Checkout Time

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-COT-01 | The Checkout Time field shall default to 12:00 PM (noon) on page load. | Must Have |
| FR-COT-02 | The Checkout Time field shall be disabled at the header level; it may be configured via system settings. | Should Have |

#### 3.2.6 Total Nights

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-TN-01 | The Total Nights field shall be auto-calculated as the difference between the global Departure Date and Check-In Date (in days). | Must Have |
| FR-TN-02 | The Total Nights field shall be read-only; it is derived from the date fields. | Must Have |
| FR-TN-03 | A manual override checkbox (Checkout checkbox) adjacent to the Total Nights field shall allow authorized users to override the auto-calculated value. | Should Have |

#### 3.2.7 Listed Company

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-LC-01 | A toggle checkbox shall control whether the Listed Company dropdown is enabled. When unchecked (default), the dropdown is disabled — representing an individual guest stay. | Must Have |
| FR-LC-02 | When the Listed Company checkbox is checked, the dropdown shall be enabled and the user may select a corporate company. The selected company will be billed for the room. | Must Have |
| FR-LC-03 | The Listed Company dropdown shall be populated with all active corporate companies from the HMS company master (examples: Sea Side Traders, NUVISTA PHARMA, Ardent Marine Ltd., etc.). | Must Have |
| FR-LC-04 | Upon selecting a company, additional fields — Contact Person, Mobile Number, Payment Mode, and Pay For — shall become available/enabled for the company booking context. | Must Have |
| FR-LC-05 | Contact Person: Free-text field for the name of the corporate contact person (placeholder: "Contact Person"). | Must Have |
| FR-LC-06 | Mobile Number: Free-text field for the corporate contact mobile number (placeholder: "Mobile Number"). | Must Have |
| FR-LC-07 | Payment Mode: Dropdown with options: Self \| Company \| Before C/O (Company/Host). Determines who pays for the stay. | Must Have |
| FR-LC-08 | Pay For: Dropdown with options: Room Only \| Room and Food \| Room, Food and Laundry \| Everything including hard drink and mini bar \| Everything without. Determines the scope of company-covered charges. | Must Have |

#### 3.2.8 Currency Name & Conversion Rate

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-CUR-01 | The Currency Name dropdown shall default to BDT (Bangladeshi Taka) on page load. | Must Have |
| FR-CUR-02 | The Currency Name dropdown shall be populated with all active currencies configured in the HMS system. | Must Have |
| FR-CUR-03 | The Conversion Rate field shall be auto-populated based on the selected currency (default 1.00 for BDT). | Must Have |
| FR-CUR-04 | The Conversion Rate field shall be read-only and driven by the currency master configuration. | Must Have |
| FR-CUR-05 | Changing the Currency Name shall recalculate all monetary amounts using the new conversion rate. | Must Have |

### 3.3 Section B: Room Detailed Information

This section documents requirements for the "Room Detailed Information" card, which captures per-room assignment, rate, and billing details. The system supports adding multiple rooms in a single registration.

#### 3.3.1 Room Types

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-RT-01 | The Room Types dropdown shall be populated with all active room types configured for the property. | Must Have |
| FR-RT-02 | Selecting a Room Type shall trigger a system call to fetch the applicable Rack Rate, Service Charge, VAT Amount, City Charge, and Additional Charges for that room type based on the current rate plan. | Must Have |
| FR-RT-03 | Selecting a Room Type shall also update the Room List modal data, filtering available rooms by the selected type. | Must Have |
| FR-RT-04 | Room Types dropdown is mandatory before the Room List modal can be opened or a Room Number can be assigned. | Must Have |

#### 3.3.2 Adult (Per Room) & Child (Per Room)

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-OCC-01 | Adult (Per Room): A numeric input field (ID: roomPax, name: pax_in) with a default value of 1. This field is REQUIRED (HTML required attribute set). Minimum value: 1. | Must Have |
| FR-OCC-02 | Child (Per Room): A numeric input field (ID: roomChild, name: child_in) with a default value of 0. Optional field. Minimum value: 0. | Must Have |
| FR-OCC-03 | Adult and Child counts shall be passed to the Guest Details tab to set up the appropriate number of guest profile entries. | Must Have |

#### 3.3.3 Room List Button & Room Selection Modal

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-RL-01 | A "Room List" button shall be displayed adjacent to the Child (Per Room) field. Clicking it opens the Room Selection modal. | Must Have |
| FR-RL-02 | The Room List modal shall display all available (vacant) rooms filtered by the selected Room Type. | Must Have |
| FR-RL-03 | The Room List modal shall contain a table with columns: Select (radio/checkbox) and Room Number. | Must Have |
| FR-RL-04 | The user selects a room from the list and confirms (OK button). The selected Room Number is auto-populated into the Room Number field and the modal closes. | Must Have |
| FR-RL-05 | If the user clicks Cancel in the modal, no room is selected and the modal closes without changes. | Must Have |
| FR-RL-06 | Rooms already occupied or under maintenance shall not appear in the Room List modal. | Must Have |
| FR-RL-07 | The Room Number field shall become read-only after selection from the Room List modal (user cannot type directly). | Must Have |

#### 3.3.4 Room Number

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-RN-01 | The Room Number field (ID: fo_reg_room_number, name: room_number) shall be read-only (readOnly=true) and shall only be populated via the Room List modal selection. | Must Have |
| FR-RN-02 | Room Number is a required field before the registration can be submitted. | Must Have |
| FR-RN-03 | Once a Room Number is assigned via the modal, the hidden field room_number shall also be updated. | Must Have |

#### 3.3.5 Rack Rate

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-RR-01 | The Rack Rate field (ID: roomRackRate, name: rack_rate) shall be auto-populated when a Room Type is selected, based on the applicable rate plan. | Must Have |
| FR-RR-02 | The Rack Rate field shall be read-only (auto-fetched from rate master). | Must Have |
| FR-RR-03 | The Rack Rate is the base nightly room rate before any discounts or additional charges. | Must Have |

#### 3.3.6 Discount Type & Discount Amount

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-DT-01 | Discount Type (ID: fo_reg_discountType, name: discount_type): Dropdown with two options — Fixed (value: 1) and Percent (value: 2). Default: Fixed. | Must Have |
| FR-DT-02 | Discount Amount (ID: fo_reg_discountedAccount, name: discount_amount): Numeric input. Default value: 0. | Must Have |
| FR-DT-03 | When Discount Type is "Fixed", the Discount Amount is deducted directly from the Rack Rate in monetary units. | Must Have |
| FR-DT-04 | When Discount Type is "Percent", the Discount Amount is treated as a percentage (%) of the Rack Rate. | Must Have |
| FR-DT-05 | The discount shall reduce the Negotiated Rate or the base for charge calculations. The system shall enforce that the Discount Amount does not exceed the Rack Rate when type is Fixed. | Must Have |

#### 3.3.7 Negotiated Rate

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-NR-01 | Negotiated Rate (ID: fo_reg_negotiatedRate, name: negotiated_rate): An optional numeric field where staff may enter a manually agreed room rate. | Must Have |
| FR-NR-02 | If a Negotiated Rate is entered, it shall override the Rack Rate for billing calculations. | Must Have |
| FR-NR-03 | The Negotiated Rate field is typically used for corporate rate agreements or special management-approved rates. | Must Have |

#### 3.3.8 Service Charge, VAT Amount, City Charge, Additional Charges

These four charge fields follow the same pattern — each has a numeric input and an associated "apply" checkbox.

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-SC-01 | Service Charge (ID: fo_reg_serviceCharge, name: service_charge): Auto-populated at 10% (system default from service_charges_db hidden field). Field is disabled (auto-calculated). Required field (marked with *). | Must Have |
| FR-SC-02 | Service Charge Checkbox (ID: fo_reg_serviceChargeCheckbox): When checked (default), Service Charge is applied to the billing. When unchecked, Service Charge is waived for this room. | Must Have |
| FR-SC-03 | VAT Amount (ID: fo_reg_vatAmount, name: vat_amount): Auto-populated at 15% (system default from vat_charges_db hidden field). Field is disabled (auto-calculated). Required field. | Must Have |
| FR-SC-04 | VAT Checkbox (ID: fo_reg_vatAmountCheckbox): When checked (default), VAT is applied. When unchecked, VAT is waived. | Must Have |
| FR-SC-05 | City Charge (ID: fo_reg_cityCharge, name: city_charge): Auto-populated from rate configuration. Disabled field. Required field. | Must Have |
| FR-SC-06 | City Charge Checkbox (ID: fo_reg_cityChargeCheckbox): When checked (default), City Charge is applied. When unchecked, City Charge is waived. | Must Have |
| FR-SC-07 | Additional Charges (ID: fo_reg_additionalCharge, name: additional_charge): Auto-populated from rate configuration. Disabled field. Required field. | Must Have |
| FR-SC-08 | Additional Charges Checkbox (ID: fo_reg_additonalChargesCheckbox): When checked (default), Additional Charges are applied. When unchecked, they are waived. | Must Have |
| FR-SC-09 | All four charge fields shall be automatically recalculated whenever the Rack Rate, Discount Type, or Discount Amount changes. | Must Have |

#### 3.3.9 Total Room Rent Amount & RRC (Room Rate Calculator)

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-TRA-01 | Total Room Rent Amount (ID: fo_reg_total_room_rent, name: total_room_rate): The auto-calculated total nightly room charge including all applicable charges. Disabled (read-only, auto-calculated). Required field. | Must Have |
| FR-TRA-02 | The RRC (Room Rate Calculator) button shall open a modal dialog titled "Calculate Room Rate Inclusively". | Must Have |
| FR-TRA-03 | The RRC Modal shall display: Total Room Rent Amount (editable input), Rack Rate (read-only), Service Charge (read-only, with toggle checkbox), City Charge (read-only, with toggle checkbox), Additional Amount (read-only, with toggle checkbox), VAT Amount (read-only, with toggle checkbox), Discount Amount (read-only, with toggle checkbox). | Must Have |
| FR-TRA-04 | The RRC Modal purpose: The user enters the desired total inclusive rate in the Total Room Rent Amount field; the system back-calculates the base Rack Rate and individual charges (Service Charge, VAT, City Charge, etc.) from the inclusive total. | Must Have |
| FR-TRA-05 | Each charge component in the RRC modal has a checkbox — unchecking a component excludes it from the inclusive rate calculation. | Must Have |
| FR-TRA-06 | An "Apply Rate" button in the RRC modal applies the back-calculated rates to the main Room Detailed Information fields. | Must Have |
| FR-TRA-07 | A close (×) button in the RRC modal dismisses it without applying changes. | Must Have |

#### 3.3.10 Per-Room Check-In Date, Departure Date & Number of Night

In addition to the global dates in the Room Registration header, each room in the Room Detailed Information section has its own date and night count fields.

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-PD-01 | Per-Room Check-In Date (ID: fo_reg_check_in_date2, name: checkin_date2): Defaults to the global Check-In Date. Disabled when "Same as global date" is checked (default state). | Must Have |
| FR-PD-02 | Per-Room Departure Date (ID: fo_reg_departure_date2, name: checkout_date2): Defaults to the global Departure Date. Read-only, derived from per-room Check-In Date + Number of Night. | Must Have |
| FR-PD-03 | Number of Night (ID: number_of_night2, name: number_of_night2): Defaults to 1. Disabled when "Same as global date" is checked. Required field. | Must Have |
| FR-PD-04 | Same as global date checkbox (ID: is_global_date, name: is_global_date): When checked (default), per-room dates mirror the global Check-In and Departure Dates and the per-room date/night fields are disabled. | Must Have |
| FR-PD-05 | When "Same as global date" is unchecked, the per-room Check-In Date and Number of Night fields become editable, allowing a different stay period for this specific room within the same registration. | Must Have |
| FR-PD-06 | Changing the Number of Night value shall auto-update the per-room Departure Date accordingly. | Must Have |

#### 3.3.11 Stay Type Flags (No Post, Complimentary, House Use)

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-FLAG-01 | No Post (ID: is_no_post, name: is_no_post): Checkbox. Default: unchecked. When checked, no charges are posted to the guest folio (not even zero-value entries). The room rate still displays but is not billed. | Must Have |
| FR-FLAG-02 | Complimentary (ID: is_complimentary, name: is_complimentary): Checkbox. Default: unchecked. When checked, the stay is designated as complimentary (no-charge to the guest). This flag activates the Complimentary Item tab (Tab 3) workflow and links to the Complimentary Guest field in Tab 4. | Must Have |
| FR-FLAG-03 | House Use (ID: is_house_use, name: is_house_use): Checkbox. Default: unchecked. When checked, the room is designated for internal hotel use (not a paying or complimentary guest stay). Billing follows house use rules. | Must Have |
| FR-FLAG-04 | Only one of Complimentary or House Use should logically be active at a time; the system shall enforce mutual exclusivity between Complimentary and House Use flags (or issue a soft warning if both are selected). | Should Have |
| FR-FLAG-05 | All three flags persist in the registration record and affect: guest folio posting behavior, Night Audit logic, and Complimentary Item assignment rules. | Must Have |

### 3.4 Section C: Additional Service

The Additional Service section allows staff to attach one or more supplementary billable services to the registration. Examples include Airport Drop, Extra Breakfast, Laundry, Restaurant charges, etc.

#### 3.4.1 Service Name

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-AS-01 | Service Name (ID: extra_bed, name: extra_bed — select element): A dropdown populated with all active additional service types from the HMS service master. **Current live master (19 items)**: Room Rent, Airport Drop, Airport Pickup, Early Check in Fee, Extra Bed Fee, Late Checkout Fee, Laundry, Minibar, Miscellaneous, Resturant [sic — typo for "Restaurant", to be fixed], Transport, Missing Charge, Extra Breakfast, Damage Charge, Conference Hall, Sound System, Multimedia, Reservation Cancellation Charge, No-Show Charge. **Items targeted but NOT yet in master**: Additional Room Revenue, Business Center, Cafe, Gym, Half Day Charge, Full Day Charge, Swimming Pool, Recreation, Driver Accommodation, Massage Chair, Spa. | Must Have |
| FR-AS-02 | The Service Name dropdown shall default to "Please select" (empty selection). | Must Have |
| FR-AS-03 | Selecting a service shall auto-populate the applicable rate and enable the date fields. | Should Have |

#### 3.4.2 Service From Date & Service To Date

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-ASDT-01 | Service From Date (ID: extrabed_from_date, name: extrabed_from_date): Defaults to the Check-In Date. Disabled when no service is selected. | Must Have |
| FR-ASDT-02 | Service To Date (ID: extrabed_to_date, name: extrabed_to_date): Defaults to the Check-In Date. Disabled when no service is selected. | Must Have |
| FR-ASDT-03 | Both date fields shall follow YYYY-MM-DD format. Service From Date shall not be later than Service To Date. | Must Have |

#### 3.4.3 Total Service Amount & Additional RRC Button

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-ASSA-01 | Total Service Amount (ID: extrabed_total, name: extrabed_total): Disabled by default; auto-calculated based on selected service rate × duration (date range). | Must Have |
| FR-ASSA-02 | The RRC button adjacent to Total Service Amount opens the service-level "Calculate Room Rate Inclusively" modal, allowing back-calculation of the service amount. | Must Have |
| FR-ASSA-03 | The service-level RRC modal has the same structure as the room-level RRC modal (Total Amount, Rack Rate, Service Charge, City Charge, Additional Amount, VAT Amount, Discount Amount with individual checkboxes, and an Apply Rate button). | Must Have |

#### 3.4.4 Add, Update, Cancel Actions for Additional Services

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-ASACT-01 | Add button (ID: add_room_details): Adds the selected service and its details as a new row in the Room Registration Summary Table. | Must Have |
| FR-ASACT-02 | Update button (ID: update_room_details): Visible only when editing an existing additional service entry. Saves changes to the selected service row. Hidden by default (class: d-none). | Must Have |
| FR-ASACT-03 | Cancel button: Clears the Additional Service section form (Service Name, From/To Dates, Total Amount) without saving. Resets the section to its default state. | Must Have |
| FR-ASACT-04 | Multiple services may be added to the same registration; each appears as a separate row in the summary table. | Must Have |

### 3.5 Section D: Room Registration Summary Table

After adding room(s) and services, they appear in the Room Registration Summary Table — an editable grid within the Registration Tab.

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-TBL-01 | The summary table shall display all added rooms with columns: Room Type, Room Number, Adult, Child, Check-In Date, Checkout Date, No. Of Night, Room Rate, Additional Service, Status, Action. **Current UI defect**: column is mislabelled "Extra Bed" instead of "Additional Service" — to be renamed. | Must Have |
| FR-TBL-02 | The Action column shall contain Edit (info button) and Delete (warning button) action buttons for each row. | Must Have |
| FR-TBL-03 | Clicking Edit for a room row shall re-populate the Room Detailed Information fields with that room's data, allowing the user to modify it (and use the Update button to save changes). | Must Have |
| FR-TBL-04 | Clicking Delete for a room row shall remove that room from the registration (with a confirmation prompt). | Must Have |
| FR-TBL-05 | At least one room must be added to the summary table before the Check-In action can be completed. | Must Have |
| FR-TBL-06 | The Status column shall reflect the room's current availability status (e.g., Available, Reserved, Occupied). | Must Have |

### 3.6 Section E: Booking Metadata Fields

Below the Room Detailed Information and Additional Service sections, the Registration Tab includes additional booking metadata fields.

#### 3.6.1 Market Segment

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-MS-01 | Market Segment (ID: fo_reg_market_segment, name: fo_reg_market_segment): Dropdown for categorizing the booking source. Options: Dhaka Office, Cox's Office (and any others configured). Default: Please Select. | Must Have |

#### 3.6.2 Guest Source

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-GS-01 | Guest Source (id: fo_reg_guest_source, name: fo_reg_guest_source): Dropdown for categorizing how the guest found the hotel. Options: Web Site, Marketing, Other Source, Broker. Default: -- Please Select --. | Must Have |

#### 3.6.3 Meal Plan

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-MP-01 | Meal Plan (id: fo_reg_meal_plan, name: fo_reg_meal_plan): Required dropdown. **Current live master (5 items)**: BED ONLY, BED & BREAKFAST, HALF BOARD, FULL BOARD, "BED,FOOD,LAUNDRY EXCEPT HARD DRINKS". **Targeted additions still to be configured**: Honeymoon Package, Day Long Package, Iftar. Default: -- Please Select --. Required field (marked with *). | Must Have |
| FR-MP-02 | The selected Meal Plan shall affect the F&B service provisioning for the guest stay and may influence room rate calculations. | Must Have |

#### 3.6.4 Reference

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-REF-01 | Reference (id: fo_reg_reference, name: fo_reg_reference): Required dropdown. Lists the booking reference channels (Front Office, Mr. Reaz, Mr. Nurul, Online Sales, Booking.com, Expedia.com, Gozayaan, Share Trip, FIT, Sales Team, etc.). Current live master contains 7 entries + placeholder; additional channels to be configured via HMS back-office. Required field (marked with *). | Must Have |
| FR-REF-02 | The Reference field tracks how the booking was sourced and is used in commission and channel management reports. | Must Have |

#### 3.6.5 Hotel Remarks & POS Remarks

| Req ID | Requirement Description | Priority |
|---|---|---|
| FR-REM-01 | Hotel Remarks (ID: fo_reg_hotel_remarks, name: fo_reg_hotel_remarks): A multi-line textarea for internal hotel notes about the registration. Placeholder: "Hotel Remarks". Optional field. | Must Have |
| FR-REM-02 | POS Remarks (ID: fo_reg_pos_remarks, name: fo_reg_pos_remarks): A multi-line textarea for notes relevant to POS (Point of Sale / F&B) systems. Placeholder: "POS Remarks". Optional field. | Must Have |
| FR-REM-03 | Hotel Remarks are visible internally to hotel staff only; POS Remarks are shared with the restaurant/F&B POS system. | Should Have |

### 3.7 Primary Action Buttons

| Button | Type/Style | ID / Class | Action Description | Validation |
|---|---|---|---|---|
| Next | Primary (blue) | class: btnNext | Navigates to Tab 2 – Guest Details while preserving all Registration Tab data in session | None (soft navigation) |
| Check-In | Success (green) | class: success_new_button extra_padding | Validates all five tabs and submits the complete Room Registration to the server; creates the active guest registration record; marks the room as Occupied | All mandatory fields across all tabs must pass validation; at least one room must be added in the summary table |
| Update Registration | Warning (yellow) | class: warning_new_button extra_padding | Visible for existing checked-in registrations; saves changes to an existing registration record without triggering a new check-in | Same mandatory field validations as Check-In |
| Add (Room) | Primary (blue) | ID: add_room_details | Adds the configured room to the Room Registration Summary Table | Room Type and Room Number are required before Add can execute |
| Update (Room) | Primary (blue) | ID: update_room_details | Saves edits to an existing room row in the summary table; hidden (d-none) by default, shown during Edit mode | Room Type and Room Number are required |
| Cancel (Room) | Warning (yellow) | class: warning_new_button | Clears the Room Detailed Information form fields without saving; exits Edit mode | None |
| Room List | Info (cyan) | ID: room_list_btn | Opens the Room Selection modal showing available rooms by type | Room Type must be selected first |
| RRC (Room) | Info (cyan) | ID: rrcBtn | Opens the Room Rate Calculator modal for the room charge | None |
| Apply Rate (RRC) | Success (green) | ID: applyRate | Applies the back-calculated inclusive rate from the RRC modal to the main form | Total Room Rent Amount must be entered in the RRC modal |
| RRC (Service) | Info (cyan) | ID: e_rrcBtn | Opens the Room Rate Calculator modal for the additional service | Service Name must be selected |
| Apply Rate (Service RRC) | Success (green) | ID: e_applyRate | Applies the back-calculated rate from the service RRC modal to the service fields | Total Service Amount must be entered in the RRC modal |
| Add (Service) | Primary (blue) | class: primary_new_button mr-2 | Adds the additional service entry to the summary table | Service Name must be selected |
| Cancel (Service) | Warning (yellow) | class: warning_new_button | Clears the Additional Service form without saving | None |
| Blank Registration Card | Link/Button | class: link-style button | Opens the blank registration card PDF in a new browser tab | None |

---

## 5. UI Components Summary

This section provides a complete inventory of every UI component on the Registration Tab, organized by section.

### 5.1 Global Tab Navigation Bar

| Component | Type | Label | State on Registration Tab | Action |
|---|---|---|---|---|
| Registration Tab | Button (nav tab) | Registration | Active (selected – dark background, white text) | Current tab; no navigation action |
| Guest Details Tab | Button (nav tab) | Guest Details | Inactive | Click → navigate to Tab 2 |
| Complimentary Item Tab | Button (nav tab) | Complimentary Item | Inactive | Click → navigate to Tab 3 |
| Others Information Tab | Button (nav tab) | Others Information | Inactive | Click → navigate to Tab 4 |
| Search Tab | Button (nav tab) | Search | Inactive | Click → navigate to Tab 5 |
| Blank Registration Card | Link button (cyan border) | Blank Registration Card | Always visible | Opens /front_office/room-registration/blank-registration-card in new tab |

### 5.2 Section A: Room Registration Card — UI Components

| # | Component | Type | ID | Default | Required | Editable |
|---|---|---|---|---|---|---|
| 1 | Reservation Checkbox | Checkbox | fo_reg_reservation_checkbox | Unchecked | No | Yes |
| 2 | Reservation Dropdown | Select (disabled by default) | fo_reg_reservation | -- Please Select -- | No (unless reservation-linked) | When checkbox checked |
| 3 | Check-In Date | Text input (read-only, date picker) | fo_reg_check_in_date | Application date (2026-03-05) | Yes | Read-only |
| 4 | Check-In Time | Time input | fo-reg-check-in-time | 02:00 PM (14:00:00) | No | Yes |
| 5 | Departure Date | Text input (disabled) | fo_reg_departure_date | Application date + 1 | Yes | Disabled |
| 6 | Checkout Time | Time input (disabled) | checkout-time | 12:00 PM (12:00:00) | No | Disabled |
| 7 | Total Nights | Number input (disabled) | day_count | 1 | No | Disabled |
| 8 | Total Nights Override Checkbox | Checkbox | checkout_checkbox | Unchecked | No | Yes |
| 9 | Listed Company Checkbox | Checkbox | listed_company_checkbox | Unchecked | No | Yes |
| 10 | Listed Company Dropdown | Select (disabled by default) | fo_reg_listed_company | -- Please Select -- | No | When checkbox checked |
| 11 | Contact Person | Text input | fo_reg_contact_person | Empty | No | Yes |
| 12 | Mobile Number | Text input | fo_reg_mobile_number | Empty | No | Yes |
| 13 | Payment Mode | Select (disabled by default) | fo_reg_payment_mode | -- Please Select -- | No | When company checked |
| 14 | Pay For | Select (disabled by default) | fo_reg_pay_for | -- Please Select -- | No | When company checked |
| 15 | Currency Name | Select (dropdown) | fo_reg_currency_types | BDT | Yes | Yes |
| 16 | Conversion Rate | Text input (read-only) | fo_reg_conversion_rate | 1.00 | No | Read-only |

### 5.3 Section B: Room Detailed Information Card — UI Components

| # | Component | Type | ID | Default | Required | Editable |
|---|---|---|---|---|---|---|
| 1 | Room Types | Select (dropdown) | fo_reg_roomType | --- Please Select --- | Yes | Yes |
| 2 | Adult (Per Room) | Number input | roomPax | 1 | Yes | Yes |
| 3 | Child (Per Room) | Number input | roomChild | 0 | No | Yes |
| 4 | Room List Button | Button (cyan) | room_list_btn | — | — | Clickable |
| 5 | Room Number | Text input (read-only) | fo_reg_room_number | Empty | Yes | Via modal only |
| 6 | Rack Rate | Text input (read-only) | roomRackRate | Auto from rate plan | No | Read-only |
| 7 | Discount Type | Select (dropdown) | fo_reg_discountType | Fixed (1) | No | Yes |
| 8 | Discount Amount | Number input | fo_reg_discountedAccount | 0 | No | Yes |
| 9 | Negotiated Rate | Number input | fo_reg_negotiatedRate | Empty | No | Yes |
| 10 | Service Charge | Number input (disabled) | fo_reg_serviceCharge | 10% of rack rate | Yes | Auto-calc |
| 11 | Service Charge Checkbox | Checkbox | fo_reg_serviceChargeCheckbox | Checked | — | Yes |
| 12 | VAT Amount | Number input (disabled) | fo_reg_vatAmount | 15% of base | Yes | Auto-calc |
| 13 | VAT Checkbox | Checkbox | fo_reg_vatAmountCheckbox | Checked | — | Yes |
| 14 | City Charge | Number input (disabled) | fo_reg_cityCharge | Auto | Yes | Auto-calc |
| 15 | City Charge Checkbox | Checkbox | fo_reg_cityChargeCheckbox | Checked | — | Yes |
| 16 | Additional Charges | Number input (disabled) | fo_reg_additionalCharge | Auto | Yes | Auto-calc |
| 17 | Additional Charges Checkbox | Checkbox | fo_reg_additonalChargesCheckbox | Checked | — | Yes |
| 18 | Total Room Rent Amount | Number input (disabled) | fo_reg_total_room_rent | Auto-calc | Yes | Read-only |
| 19 | RRC Button (Room) | Button (cyan) | rrcBtn | — | — | Clickable |
| 20 | Per-Room Check-In Date | Text input (disabled) | fo_reg_check_in_date2 | Global Check-In Date | Yes | When "Same as global date" unchecked |
| 21 | Per-Room Departure Date | Text input (read-only) | fo_reg_departure_date2 | Global Departure Date | Yes | Derived |
| 22 | Number of Night | Number input (disabled) | number_of_night2 | 1 | Yes | When "Same as global date" unchecked |
| 23 | Same as global date Checkbox | Checkbox | is_global_date | Checked (default) | — | Yes |
| 24 | No Post Checkbox | Checkbox | is_no_post | Unchecked | — | Yes |
| 25 | Complimentary Checkbox | Checkbox | is_complimentary | Unchecked | — | Yes |
| 26 | House Use Checkbox | Checkbox | is_house_use | Unchecked | — | Yes |
| 27 | Add Button (Room) | Button (primary) | add_room_details | — | — | Clickable |
| 28 | Update Button (Room) | Button (primary, hidden) | update_room_details | Hidden (d-none) | — | Clickable in edit mode |
| 29 | Cancel Button (Room) | Button (warning) | — | — | — | Clickable |

### 5.4 RRC Modal — UI Components

| # | Component | Type | ID | Editable | Description |
|---|---|---|---|---|---|
| 1 | Total Room Rent Amount | Number input | rrc_total_room_rate | Yes | User-entered inclusive total; back-calculation base |
| 2 | Rack Rate | Number input (read-only) | rrc_rack_rate | No | Back-calculated base rack rate |
| 3 | Service Charge (w/ checkbox) | Text input + Checkbox | rrc_service_charge / rrc_service_charge_checkbox | No / Yes | SC component; checkbox includes/excludes from calc |
| 4 | City Charge (w/ checkbox) | Text input + Checkbox | rrc_city_charge / rrc_city_charge_checkbox | No / Yes | City charge component |
| 5 | Additional Amount (w/ checkbox) | Text input + Checkbox | rrc_additional_amount / rrc_additional_amount_checkbox | No / Yes | Additional charge component |
| 6 | VAT Amount (w/ checkbox) | Text input + Checkbox | rrc_vat_amount / rrc_vat_amount_checkbox | No / Yes | VAT component |
| 7 | Discount Amount (w/ checkbox) | Text input + Checkbox | rrc_discount_amount / rrc_discount_amount_checkbox | No / Yes | Discount component |
| 8 | Apply Rate Button | Button (success/green) | applyRate | — | Applies back-calculated rates to main form |
| 9 | Close (×) Button | Button | — | — | Dismisses modal without changes |

### 5.5 Section C: Additional Service — UI Components

| # | Component | Type | ID | Default | Required |
|---|---|---|---|---|---|
| 1 | Service Name | Select (dropdown) | extra_bed | Please select | Yes (to add) |
| 2 | Service From Date | Text input (read-only) | extrabed_from_date | Check-In Date | Yes (auto) |
| 3 | Service To Date | Text input (read-only) | extrabed_to_date | Check-In Date | Yes (auto) |
| 4 | Total Service Amount | Number input (disabled) | extrabed_total | Auto-calc | Yes (auto) |
| 5 | RRC Button (Service) | Button (cyan) | e_rrcBtn | — | — |
| 6 | Add Button (Service) | Button (primary) | — | — | — |
| 7 | Cancel Button (Service) | Button (warning) | — | — | — |

### 5.6 Section D: Room Summary Table — UI Components

| Column | Data Source | Editable |
|---|---|---|
| Room Type | Selected from Room Types dropdown | No (read-only in table) |
| Room Number | Selected from Room List modal | No |
| Adult | From Adult (Per Room) field | No |
| Child | From Child (Per Room) field | No |
| Checkin Date | Per-room Check-In Date | No |
| Checkout Date | Per-room Departure Date | No |
| No. Of Night | Per-room Number of Night | No |
| Room Rate | Total Room Rent Amount | No |
| Additional Service | Linked additional service (if any) | No |
| Status | Room status from inventory | No |
| Action – Edit | Info/cyan button | Triggers Edit mode; repopulates Room Detailed Information form |
| Action – Delete | Warning/orange button | Removes room row (with confirmation) |

### 5.7 Section E: Booking Metadata — UI Components

| # | Component | Type | ID | Options | Required |
|---|---|---|---|---|---|
| 1 | Market Segment | Select (dropdown) | fo_reg_market_segment | Dhaka Office, Cox's Office | No |
| 2 | Guest Source | Select (dropdown) | guest_source (name) | Web Site, Marketing, Other Source, Broker | No |
| 3 | Meal Plan | Select (dropdown) | meal_plan (name) | BED ONLY, BED & BREAKFAST, HALF BOARD, FULL BOARD, BED/FOOD/LAUNDRY, Honeymoon Package, Day Long Package, Iftar | Yes (*) |
| 4 | Reference | Select (dropdown) | reference (name) | Front Office, Sales Team, booking portals, individual staff names | Yes (*) |
| 5 | Hotel Remarks | Textarea | fo_reg_hotel_remarks | Empty (placeholder: Hotel Remarks) | No |
| 6 | POS Remarks | Textarea | fo_reg_pos_remarks | Empty (placeholder: POS Remarks) | No |

---

## 6. Business Rules

Business rules govern all logic, constraints, and conditional behaviors enforced on the Registration Tab.

| Rule ID | Rule Name | Condition | Action / Enforcement | Priority |
|---|---|---|---|---|
| BR-001 | Reservation vs. Walk-In | Reservation checkbox is unchecked | Walk-in mode: Reservation dropdown is disabled; registration is walk-in; no pre-population from reservation | Must Have |
| BR-002 | Reservation Pre-Population | Reservation checkbox is checked AND a reservation is selected | System auto-populates: Check-In Date, Departure Date, Room Type, Rack Rate, Currency, Conversion Rate, Complimentary Items; reservation_id stored in hidden field | Must Have |
| BR-003 | Listed Company Context | Listed Company checkbox is unchecked | Individual guest billing: Company fields (Contact Person, Mobile, Payment Mode, Pay For) are disabled/hidden | Must Have |
| BR-004 | Corporate Billing Context | Listed Company checkbox is checked and company selected | Corporate billing mode: Contact Person, Mobile Number, Payment Mode, Pay For fields become available; billing routed to company account | Must Have |
| BR-005 | Global Date Lock | Same as global date checkbox is CHECKED (default) | Per-room Check-In Date and Number of Night fields are disabled; dates mirror the global Check-In/Departure Dates | Must Have |
| BR-006 | Custom Room Date Override | Same as global date checkbox is UNCHECKED | Per-room Check-In Date and Number of Night fields are enabled; user may set a different stay period for this specific room within the same registration | Must Have |
| BR-007 | Date Validation | On any date entry | Departure Date must always be > Check-In Date; system shall prevent invalid date ranges and display an error | Must Have |
| BR-008 | Total Nights Auto-Calc | Check-In or Departure Date changes | Total Nights = Departure Date minus Check-In Date (days); recalculated automatically; Number of Night per-room also recalculated | Must Have |
| BR-009 | Rack Rate Auto-Fetch | Room Type is selected | Rack Rate is fetched from the active rate plan for the selected room type and stay period; it is not editable directly | Must Have |
| BR-010 | Service Charge Auto-Calc | Rack Rate changes or Service Charge checkbox toggled | Service Charge = Rack Rate × 10% (system default); when checkbox is unchecked, Service Charge = 0 for this room | Must Have |
| BR-011 | VAT Auto-Calc | Rack Rate changes or VAT checkbox toggled | VAT = (Rack Rate + Service Charge) × 15% (system default); when checkbox unchecked, VAT = 0 | Must Have |
| BR-012 | City Charge Auto-Calc | Rack Rate changes or City Charge checkbox toggled | City Charge is applied per rate plan configuration; checkbox controls whether it is included | Must Have |
| BR-013 | Additional Charges Auto-Calc | Rack Rate changes or Additional Charges checkbox toggled | Additional Charges per rate plan; checkbox controls inclusion | Must Have |
| BR-014 | Total Room Rent Calculation | Any billing field changes | Total Room Rent Amt = Rack Rate + Service Charge (if applied) + VAT (if applied) + City Charge (if applied) + Additional Charges (if applied) − Discount Amount; auto-recalculated on any change | Must Have |
| BR-015 | Discount Type Logic | Discount Type = Fixed | Discount Amount is deducted directly in currency units from the base rate; Discount Amount cannot exceed Rack Rate | Must Have |
| BR-016 | Discount Percent Logic | Discount Type = Percent | Discount Amount is treated as a % of Rack Rate; system converts to monetary value before deducting | Must Have |
| BR-017 | Negotiated Rate Override | Negotiated Rate is entered (non-zero) | Negotiated Rate replaces Rack Rate as the base for all charge calculations; overrides rate plan rate | Must Have |
| BR-018 | RRC Back-Calculation | User enters Total Room Rent Amt in RRC modal and clicks Apply Rate | System back-calculates Rack Rate and individual charge components from the inclusive total; individual component checkboxes determine which charges are factored in | Must Have |
| BR-019 | No Post Flag | is_no_post = 1 (checked) | No folio posting lines are created for this room; not even zero-value entries; room rate still recorded but not billed; affects Night Audit | Must Have |
| BR-020 | Complimentary Flag | is_complimentary = 1 (checked) | Stay is designated complimentary; all room charges are suppressed; Tab 3 (Complimentary Item) workflow is activated; links to Complimentary Guest field in Tab 4 | Must Have |
| BR-021 | House Use Flag | is_house_use = 1 (checked) | Room designated for internal hotel use; follows house use billing rules; no guest invoicing | Must Have |
| BR-022 | Complimentary + House Use Conflict | Both is_complimentary and is_house_use are checked simultaneously | System should display a soft warning: "A room cannot be both Complimentary and House Use. Please select only one." No hard block, but strong advisory. | Should Have |
| BR-023 | Room List Filter | Room List button clicked | Only vacant (Available) rooms of the selected Room Type are shown; rooms already occupied, under maintenance, or already added in the current session are excluded | Must Have |
| BR-024 | Mandatory Room Before Check-In | Check-In button clicked with zero rooms in summary table | System blocks check-in and displays: "Please add at least one room before checking in." | Must Have |
| BR-025 | Mandatory Field Validation at Check-In | Check-In button clicked | System validates: Room Type (required), Room Number (required), Adult count ≥ 1 (required), Total Room Rent Amount > 0 (when not complimentary/no-post), Meal Plan (required), Reference (required). Errors displayed per field. | Must Have |
| BR-026 | Multiple Room Registration | User adds more than one room via the Add Room flow | Each room is stored as a separate row in the Room Registration Detail entity; all rooms are linked to the same registration_id; each room can have different stay dates, rates, and flags | Must Have |
| BR-027 | Currency Conversion Impact | Currency Name is changed | All monetary fields (Rack Rate, charges, Total Room Rent) are recalculated using the new conversion rate from the currency master; BDT conversion rate = 1.00 (no conversion) | Must Have |
| BR-028 | Additional Service Duration Calc | Service Name selected and dates entered | Total Service Amount = Service Rack Rate × number of service days (Service To Date − Service From Date + 1) | Must Have |
| BR-029 | Additional Service Date Constraint | Service From Date and Service To Date are set | Service From Date ≥ Registration Check-In Date; Service To Date ≤ Departure Date; From Date ≤ To Date | Must Have |
| BR-030 | Session Persistence | User navigates away from Registration Tab and returns | All entered data (form fields, summary table rows) must be preserved in the current browser session without data loss | Must Have |
| BR-031 | Hotel Remarks Internal Only | Hotel Remarks entered | Hotel Remarks are for internal staff reference only; they must NOT be printed on guest-facing documents (e.g., guest folio, registration card handed to guest) | Must Have |
| BR-032 | POS Remarks to F&B System | POS Remarks entered | POS Remarks must be forwarded to the hotel F&B/POS system upon check-in so restaurant staff are informed of guest preferences | Should Have |
| BR-033 | Room Status Update on Check-In | Check-In action completes successfully | The room's status in the room inventory is updated to "Occupied"; the room disappears from the Room List modal for new registrations | Must Have |
| BR-034 | Update Registration vs. Check-In | User is editing an existing, checked-in registration | The "Update Registration" button is shown instead of (or alongside) "Check-In"; updates are applied to the existing record without creating a new check-in event | Must Have |

---

## 7. Non-Functional Requirements

### 7.1 Performance

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-PERF-01 | Room Registration page (Registration Tab) must load fully within 3 seconds on broadband. | < 3 seconds page load |
| NFR-PERF-02 | Room Types dropdown must populate within 500ms of page load. | < 500ms |
| NFR-PERF-03 | Rack Rate and charge fields must auto-populate within 1 second of Room Type selection. | < 1 second |
| NFR-PERF-04 | Room List modal must load and display available rooms within 2 seconds. | < 2 seconds |
| NFR-PERF-05 | Reservation dropdown must populate within 1 second of enabling (when reservation checkbox is checked). | < 1 second |
| NFR-PERF-06 | RRC back-calculation must complete client-side within 200ms of user input. | < 200ms |
| NFR-PERF-07 | Check-In API call must complete (server-side) within 5 seconds under normal load. | < 5 seconds |
| NFR-PERF-08 | System must support 50+ concurrent front-desk users submitting check-ins without performance degradation. | 50 concurrent users |

### 7.2 Usability

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-USE-01 | All form sections must be fully usable on a 1024px wide desktop monitor without horizontal scrolling. | Min 1024px width |
| NFR-USE-02 | Required fields must be clearly marked with an asterisk (*) and labelled consistently. | All required fields marked |
| NFR-USE-03 | Disabled fields must be visually distinct from editable fields (grey background, reduced opacity). | Visible disabled state |
| NFR-USE-04 | A front-desk agent must be able to complete a walk-in registration and check-in within 3 minutes. | < 3 minutes task time |
| NFR-USE-05 | Error messages must be specific, field-level, and actionable (e.g., "Room Type is required" not just "Invalid input"). | Field-level errors |
| NFR-USE-06 | The page must be responsive and usable on tablets (768px width) for mobile front-desk operations. | Responsive at 768px |
| NFR-USE-07 | Tab navigation must not cause data loss; the user must receive a warning before leaving the page (browser-level) if there are unsaved changes. | Unsaved changes warning |

### 7.3 Security

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-SEC-01 | All form submissions must include a CSRF token (csrf_test_name hidden field) validated server-side. | CSRF protection on all POSTs |
| NFR-SEC-02 | Users must be authenticated (active session) to access the Registration Tab; unauthenticated requests are redirected to the login page. | Session authentication required |
| NFR-SEC-03 | Role-based access control must restrict which user roles can create, edit, check-in, and delete room registrations. | RBAC enforced at API and UI levels |
| NFR-SEC-04 | Negotiated Rate entry and No Post/Complimentary/House Use flag setting may require elevated role (e.g., Duty Manager) depending on hotel policy. | Configurable role-based field restrictions |
| NFR-SEC-05 | All server-side inputs (room type IDs, room numbers, rate values) must be validated against the master data to prevent injection or data manipulation. | Server-side input validation |
| NFR-SEC-06 | Monetary fields must enforce numeric-only input with a maximum decimal precision of 2 decimal places. | Input type validation |

### 7.4 Reliability & Availability

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-REL-01 | The Registration Tab must be available as part of the HMS system with 99.5% uptime SLA. | 99.5% uptime |
| NFR-REL-02 | Check-In operation must be transactional: if any part of the multi-room check-in fails, the entire transaction must roll back. | Transactional integrity |
| NFR-REL-03 | If a rate fetch API call fails (e.g., network error), the system must display an error and allow the user to retry without losing other form data. | Graceful API error handling |
| NFR-REL-04 | Session data must persist if the user refreshes the page before submitting (via browser session storage or draft-save mechanism). | Session resilience |

### 7.5 Maintainability & Configurability

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-MAINT-01 | Service Charge (%) and VAT (%) defaults must be configurable via the HMS admin panel without code changes. | Admin-configurable |
| NFR-MAINT-02 | Room Types, Rate Plans, Listed Companies, Market Segments, Guest Sources, Meal Plans, References, and Service Names must all be maintainable via the HMS back-office configuration without code changes. | Admin-configurable master data |
| NFR-MAINT-03 | The Pay For and Payment Mode options must be configurable via the HMS admin panel. | Admin-configurable |

### 7.6 Audit & Compliance

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-AUD-01 | All room registration creation, modification, and check-in events must be logged in the system audit trail with user ID, timestamp, and action details. | Full audit trail |
| NFR-AUD-02 | Any changes to billing fields (Rack Rate, Discount, Negotiated Rate) on an existing registration must be logged with before/after values. | Change audit log |
| NFR-AUD-03 | Audit logs must be retained for a minimum of 3 years. | 3-year retention |
| NFR-AUD-04 | The system must support generating a report of all registrations within a date range, filterable by room type, company, market segment, meal plan, and reference channel. | Reporting capability |

---

## 8. AI-DLC Audit Update — 2026-04-22

This section records the findings of the live-application audit (Kazi Resort deployment, Cubix HMS V-9.3.0) against this BRD and captures the deltas that AI-DLC agents must treat as authoritative until the code or BRD is reconciled.

### 8.1 Verified field-ID map (live DOM)

| BRD reference | Live id / name | State |
|---|---|---|
| `meal_plan` (BRD name) | id/name = `fo_reg_meal_plan` | Name corrected in BRD |
| `guest_source` (BRD name) | id/name = `fo_reg_guest_source` | Name corrected in BRD |
| `reference` (BRD name) | id/name = `fo_reg_reference` | Name corrected in BRD |
| Extra Service select id | `extra_bed`, name `extra_bed` | Confirmed |
| CSRF token | `csrf_test_name` (hidden) | Confirmed |

### 8.2 Known defects to remediate

| ID | Defect | Impact |
|---|---|---|
| DEF-REG-01 | City Charge (`fo_reg_cityCharge`) and City Charge checkbox present in DOM but wrapped in `col-sm-2 hide` — not reachable in UI (FR-SC-05/06) | ❌ Blocks City-Charge billing rule BR-012 |
| DEF-REG-02 | Additional Charges (`fo_reg_additionalCharge`) and checkbox same wrapper class `hide` (FR-SC-07/08) | ❌ Blocks BR-013 |
| DEF-REG-03 | Hidden field `fo_from_reservation` (FR-RES-06) not present in DOM | ❌ Prevents reliable walk-in/reservation-linked classification |
| DEF-REG-04 | Summary-table column rendered as "Extra Bed" instead of "Additional Service" (FR-TBL-01) | ⚠ UI label only |
| DEF-REG-05 | Additional Service option "Resturant" — spelling bug | ⚠ |
| DEF-REG-06 | `roomNumber` input has no HTML `required` attribute (FR-RN-02) — relies on JS validation | ⚠ client-side only |
| DEF-REG-07 | Currency master loaded with BDT only — FR-CUR-02 expects "all active currencies" | ⚠ configuration |
| DEF-REG-08 | Listed Company master empty (FR-LC-03 expects populated corporate master) | ⚠ configuration |

### 8.3 Master-data gaps

| Master | BRD expectation | Live state | Action |
|---|---|---|---|
| Meal Plan | 8 values | 5 values | Add Honeymoon Package, Day Long Package, Iftar |
| Additional Service | 24 values | 19 values | Add 11 listed in §3.4; remove or review 6 property-specific extras; fix "Resturant" |
| Market Segment | BRD sample "Dhaka Office, Cox's Office" | Dhaka Office, Sea Moon Cox's Bazar, BD Tour | Property-specific — BRD treats Cox's Office/Sea Moon Cox's Bazar as equivalent |
| Reference | 10+ channels | 7 + placeholder | Extend master |
| Currency | Populated master | BDT only | Load currency master |
| Listed Company | Populated master | placeholder only | Load corporate master |

### 8.4 Business rules added

| Rule ID | Rule | Reason |
|---|---|---|
| BR-035 | **Rate-master fallback** — When Room Type is selected and no active rate plan exists for the stay window, the system SHALL NOT block the user but SHALL leave Rack Rate empty and surface an inline message "No rate plan configured for this room type and date range". | Supports codegen validation of the fetch-rate path. |
| BR-036 | **Summary-table column taxonomy** — The summary-table "Additional Service" column is authoritative; any UI rendering of "Extra Bed" must be treated as a regression. | Resolves DEF-REG-04 ambiguity. |
| BR-037 | **Hidden master items** — Any item deactivated in the Additional Service / Meal Plan / Reference masters after registrations have been created SHALL remain retrievable for historical registrations (soft-delete pattern). | Historical integrity. |

### 8.5 User journeys added

- **UJ-REG-A Rate fetch failure path**: Room Type selected → HMS returns 5xx → UI shows retry banner → user clicks retry → if still failing, Rack Rate stays empty, Total Room Rent = 0, Check-In button shows "Rate unavailable, contact manager".
- **UJ-REG-B Room-race concurrency**: Two agents open Room List simultaneously, both pick 1208 → first submit wins, second receives 409/room-taken message → modal reopens filtered list (1208 excluded).
- **UJ-REG-C Session expiry during entry**: Silent reauth attempt on each API call; if fails, modal offers "Re-login" without losing in-memory form state; on success, session resumes.
- **UJ-REG-D Night-audit boundary**: If Check-In action crosses midnight into a new business date, the server uses the application date as the effective registration date (not client wall-clock).
- **UJ-REG-E Multi-currency recalculation**: Currency change triggers recalculation of all monetary fields via BR-027; if recalculation fails (conversion-rate missing), original values are preserved and a soft warning is shown.

