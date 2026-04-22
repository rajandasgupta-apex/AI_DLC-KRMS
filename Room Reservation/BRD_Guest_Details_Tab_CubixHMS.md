# Business Requirements Document

## Guest Details Module

**Room Registration — Guest Details Tab**

- **System:** Cubix HMS (Hotel Management System)
- **Version:** 1.0 | **Date:** April 15, 2026
- **Module:** Front Office > Room Registration > Guest Details Tab
- **Prepared For:** Cubix HMS Project Team

---

## 1. Executive Summary

This Business Requirements Document (BRD) defines the complete functional and non-functional requirements for the **Guest Details** tab within the Room Registration module of the Cubix Hotel Management System (HMS). The Guest Details tab is the second step in the multi-tab Room Registration wizard, positioned under Front Office > Room Registration.

The Guest Details tab is the primary data-capture interface for recording all guest identity, contact, travel document, and profile information at the time of hotel check-in. It supports both individual and group/family guest scenarios, enables guest profile lookup from historical records, and allows multiple guest entries per room booking.

This tab is central to operational compliance, guest experience, and audit readiness — capturing legal identity documents (passports, visas, national IDs), contact details, and guest status flags that drive downstream billing, housekeeping, and reporting processes.

| Attribute | Details |
|---|---|
| System | Cubix HMS V-9.3.0 (deployed) — BRD drafted against v13.1.0 |
| Module | Front Office |
| Sub-Module | Room Registration |
| Tab Name | Guest Details |
| Tab Position | 2nd tab (between Registration and Complimentary Item) |
| Primary Users | Front Desk Staff, Reservation Officers, Duty Managers |
| Document Version | 1.0 |
| Prepared Date | April 15, 2026 |
| Status | Draft for Review |

---

## 2. Module Context & Positioning

### 2.3 Dependencies

#### 2.3.1 Upstream Dependencies

- **Registration Tab (Tab 1):** The Room Number selected in the Registration tab is carried forward and is reflected in the Room No. field within the Guest Details tab for each guest entry. At least one room must be selected before the guest can be assigned.
- **Guest History / Profile Database:** The Search functionality on this tab queries the existing guest profile database (from past registrations) to pre-fill guest details for returning guests, avoiding re-entry.
- **Company Master List:** The Company Name dropdown is populated from the master company/corporate accounts database maintained in the system configuration.
- **Country Master List:** The Country dropdown is populated from a master country reference table. Nationality is auto-populated from country selection in some configurations.
- **Profession Master List:** The Profession dropdown is populated from a configurable master list in system settings.
- **Room Reservation (Optional):** If the registration originates from a prior reservation, guest details from the reservation record (name, contact, passport) may be pre-populated in this tab.

#### 2.3.2 Downstream Dependencies

- **Complimentary Item Tab (Tab 3):** Complimentary services are assigned per registration record which is tied to the guest(s) captured here.
- **Check-in Record:** Mandatory guest fields (Title, First Name, Country) must be completed for the Check-in action to succeed.
- **Guest Bill / Folio:** Guest identity data captured here (name, company, room number) appears on the guest bill and printed receipts.
- **Blank Registration Card:** The guest name, contact, nationality, passport, and visa details appear on the printed Registration Card generated from this module.
- **Guest Search Module:** Guest data entered here is stored in the guest profile database and becomes searchable in future registrations via the Guest Search module.
- **Reports:** Guest demographic data (nationality, country, gender, profession) feeds into front office reports and statutory reports (e.g., police/immigration reporting).
- **Housekeeping:** Guest name and room assignment from this tab appear in housekeeping room status reports.

#### 2.3.3 Data Dependencies

- **Guest Profile Table:** Historical guest records are queried during the Search action and can populate form fields.
- **Room Registration Record:** All guest entries in this tab are associated with the parent Room Registration record (registration_id) created in Tab 1.
- **Guest Document Store:** Uploaded documents (passport scan, photo ID) are stored in the server file system or document management system, linked by guest ID.

---

## 3. Functional Requirements

This section details all functional requirements for the Guest Details tab, covering every actionable process, user interaction, data entry workflow, and system behaviour observed in the Cubix HMS implementation.

### 3.1 Tab Navigation & Access

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-GD-001 | The system SHALL display the "Guest Details" tab as the 2nd tab in the Room Registration wizard navigation bar. | Must Have |
| FR-GD-002 | The Guest Details tab SHALL be accessible by clicking the "Guest Details" tab header from any other tab in the wizard. | Must Have |
| FR-GD-003 | Clicking the "Previous" button on the Guest Details tab SHALL navigate back to the Registration tab without data loss. | Must Have |
| FR-GD-004 | Clicking the "Next" button on the Guest Details tab SHALL save all current guest entries and navigate to the Complimentary Item tab. | Must Have |
| FR-GD-005 | The "Check-in" button SHALL remain permanently visible at the bottom of the page while the Guest Details tab is active, enabling direct check-in from this step. | Must Have |

### 3.2 Guest Count Entry (Person Count Area)

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-GD-006 | The system SHALL display two numeric input fields at the top of the Guest Details tab: "Person (Adult)" (mandatory, field ID: fo_reg_person_adult) and "Person (Child)" (optional, field ID: fo_reg_person_child). | Must Have |
| FR-GD-007 | The "Person (Adult)" field SHALL default to the value 1 (minimum one adult per registration). | Must Have |
| FR-GD-008 | The "Person (Child)" field SHALL default to 0. | Must Have |
| FR-GD-009 | Both Person (Adult) / Person (Child) fields are **read-only** on this tab; values are mirrored from the Registration tab's Adult (Per Room) / Child (Per Room) counts. To change them, the user returns to Tab 1. | Must Have |
| FR-GD-010 | The guest count entered here SHALL be used to determine how many individual guest information forms need to be completed (one per person or a group form depending on guest type). | Should Have |

### 3.3 Guest Type Selection (Individual vs. Family/Group/Couple)

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-GD-011 | The system SHALL display a "Family/Group/Couple" checkbox (field ID: fo_reg_guest_type) at the top-right of the Guest Details area. | Must Have |
| FR-GD-012 | When the "Family/Group/Couple" checkbox is UNCHECKED (default state), the form SHALL operate in Individual Guest mode: a single Individual Guest Information form is displayed for the primary guest. | Must Have |
| FR-GD-013 | When the "Family/Group/Couple" checkbox is CHECKED, the form SHALL switch to Group mode: the section heading changes to reflect group registration, and the system SHALL support adding multiple guest entries to a shared guest list table. | Must Have |
| FR-GD-014 | Toggling between Individual and Group mode SHALL NOT erase previously entered guest data in the session without user confirmation. | Should Have |

### 3.4 Individual Guest Information Form — Sections & Fields

The Individual Guest Information section is the primary data entry area. It is divided into five sub-sections:

#### 3.4.1 Basic Information

| Field Name | Field ID | Type | Required | Description & Rules |
|---|---|---|---|---|
| Title | fo_reg_guest_title | Dropdown (Select) | Yes (*) | Salutation/honorific. Live master: MR., MRS., MS., MR. & MRS., DR., NOT APPLICABLE, MR. MRS. Note: "MR. & MRS." and "MR. MRS." are duplicate entries — master requires consolidation. |
| First Name | fo_reg_guest_first_name | Text Input | Yes (*) | Guest's first/given name. Free text. Placeholder: "First Name". |
| Last Name | fo_reg_guest_last_name | Text Input | No | Guest's surname/family name. Free text. Placeholder: "Last Name". |
| Gender | fo_reg_guest_gender | Dropdown (Select) | No | Guest's gender. Options: Male, Female, Other (or as configured). |
| Room No. | fo_reg_roomNumber | Dropdown (Select) | Yes (*) | Assigns the guest to a specific room number. Populated from available rooms in the Registration tab. Carries forward from Tab 1. |
| Full Guest Name | fo_reg_guest_full_name | Text Input (Read-only/Auto) | Yes (*) | Auto-concatenated full name composed from Title + First Name + Last Name. Read-only; system-generated. Placeholder: "Full Guest Name". |
| Date of Birth | fo_reg_guest_birth | Date Input | No | Guest date of birth. Format: DD/MM/YYYY. Date picker enabled. |
| Search Button | (action) | Button (Blue) | N/A | Triggers the Guest Search modal (see Section 3.9). Allows retrieval of existing guest profiles from historical records to pre-populate the form. |

#### 3.4.2 Contact Information

| Field Name | Field ID | Type | Required | Description & Rules |
|---|---|---|---|---|
| Email Address | fo_reg_guest_email | Email Input | No | Guest's email address. Validated as proper email format (e.g., user@domain.com). |
| Phone Number | fo_reg_guest_phn | Text Input | No | Guest's contact number. Placeholder: "+880 XXX-XXXXXXX". Accepts international format. |
| Profession | fo_reg_guest_profession | Dropdown (Select) | No | Guest's occupation/profession. Populated from configurable master profession list. **Live master has only 3 entries (DR, EN, Other); enrich before go-live.** |
| Company Name | fo_reg_guest_company_name | Dropdown (Select) | No | Associated company or corporate account. Searchable dropdown populated from corporate accounts master. |
| Address | guest_address | Text Input | No | Street address of the guest. Placeholder: "Street Address". |
| City | fo_reg_guest_city | Text Input | No | Guest's city of residence. |
| Zip Code | fo_reg_guest_zip_code | Number Input | No | Postal/ZIP code. Numeric only. |
| Country | fo_reg_guest_country | Dropdown (Select) | Yes (*) | Guest's country of residence. Defaults to "Bangladesh". Must be selected from country master list. |
| Nationality | fo_reg_guest_nationality | Text Input | No | Guest's nationality. Auto-populated as "Bangladeshi" by default; editable. Placeholder: "Nationality". |
| National ID | fo_reg_guest_national_id | Number Input | No | National Identification Number. Numeric only. |
| Driving License | fo_reg_guest_driving_license | Text Input | No | Driving license number. Placeholder: "Driving License No.". |

#### 3.4.3 Visa Information

| Field Name | Field ID | Type | Required | Description & Rules |
|---|---|---|---|---|
| Visa Number | fo_reg_guest_visa_number | Text Input | No | Guest's visa number. Alphanumeric. Placeholder: "Visa Number". |
| Visa Issue Date | fo_reg_guest_visa_issue_date | Date Input | No | Date the visa was issued. Format: DD/MM/YYYY. Date picker enabled. |
| Visa Expiry Date | fo_reg_visa_expiry_date | Date Input | No | Date the visa expires. Format: DD/MM/YYYY. Should be after Issue Date if both are entered. |

#### 3.4.4 Passport Information

| Field Name | Field ID | Type | Required | Description & Rules |
|---|---|---|---|---|
| Passport Number | fo_reg_guest_pass_number | Text Input | No | Guest's passport number. Alphanumeric. Placeholder: "Passport Number". |
| Passport Issue Date | fo_reg_passport_issue_date | Date Input | No | Date the passport was issued. Format: DD/MM/YYYY. |
| Passport Expiry Date | fo_reg_passport_expiry_date | Date Input | No | Date the passport expires. Format: DD/MM/YYYY. Should be after Issue Date if both are entered. |

#### 3.4.5 Additional Information

| Field Name | Field ID | Type | Required | Description & Rules |
|---|---|---|---|---|
| Guest Document | fo_reg_guest_document | File Upload Input | No | Allows upload of a guest identity document (e.g., passport scan, photo ID). Accepts image/document file types. Stored in server file store linked to guest record. |
| Blocked Guest (Checkbox) | fo_reg_guest_block (blocked_guest) | Checkbox | No | Flags the guest as a "Blocked Guest". When checked, the system marks the guest profile with a blocked status, which may restrict future bookings or trigger alerts. Default: unchecked. |
| Is Contact Person (Checkbox) | is_contact_person | Checkbox | No | Flags the current guest entry as the primary contact person for a group/family registration. Default: unchecked. |
| Guest Status (Display) | (read-only display) | Label/Indicator | N/A | Displays the current status of the guest record (e.g., Active, Blocked). Visual indicator driven by the Blocked Guest flag. |

### 3.5 Form Action Buttons

| Button | Type/Style | Location | Behaviour & Process |
|---|---|---|---|
| Search | Primary (Blue) — "btn-block" | Top-right of Basic Information section | Opens the Guest Search Modal (see Section 3.9). Allows staff to find an existing guest profile and auto-populate all guest fields from historical data. |
| Add | Primary (Blue) | Bottom of Individual Guest Information form | Validates all entered guest details and adds the current guest entry to the Guest List Table below. After adding, the form clears for the next guest entry. Visible in both Individual and Group modes. |
| Update | Primary (Blue) — hidden by default (d-none) | Bottom of Individual Guest Information form | Becomes visible when an existing guest entry from the Guest List is selected for editing. Replaces the Add button. Saves modifications to the selected guest record. |
| Clear | Danger (Red) | Bottom of Individual Guest Information form | Clears/resets all fields in the Individual Guest Information form without deleting existing guest entries in the table. Triggered via fo_reg_clear_guest() function. |
| Previous | Warning (Yellow) | Bottom-right of tab content area | Navigates back to the Registration tab. Does not submit data. |
| Next | Primary (Blue) | Bottom-right of tab content area | Saves current session data and advances to the Complimentary Item tab. |

### 3.6 Guest List Table

| Column Name | Data Displayed | Description |
|---|---|---|
| Guest Name | Full name of the added guest | Displays the concatenated full name (Title + First + Last). |
| Email | Guest email address | Displayed from the Contact Information section. |
| Room Number | Assigned room number | The room to which this guest is assigned within the registration. |
| Is Contact Person | Yes / No indicator | Shows whether this guest is flagged as the primary contact for the group. |
| Action | Edit / Delete buttons | Icon buttons allowing the user to edit a specific guest entry (populates the form above for editing) or remove a guest from the registration. |

**Process — Adding a Guest Entry to the Table:**
- Staff completes the Individual Guest Information form fields.
- Staff clicks the "Add" button.
- System validates mandatory fields (Title, First Name, Country at minimum).
- If validation passes: guest entry is appended to the Guest List Table; the form is cleared for next entry.
- If validation fails: error indicators are shown on failing fields; form is NOT cleared.

**Process — Editing a Guest Entry:**
- Staff clicks the Edit action button in the Action column of the desired guest row.
- The system populates the Individual Guest Information form with that guest's data.
- The "Add" button is hidden and replaced with the "Update" button.
- Staff modifies desired fields and clicks "Update".
- System validates and saves changes; the guest list table is refreshed with updated data.
- The "Add" button is restored; form is cleared.

**Process — Removing a Guest Entry:**
- Staff clicks the Delete/Remove action button in the Action column of the desired guest row.
- System prompts for confirmation (if configured) before removing the record.
- Upon confirmation, the guest entry is removed from the Guest List Table.
- The primary guest (first entry) cannot be deleted as long as it is the only guest in the registration.

### 3.7 Guest Type Toggle: Family/Group/Couple Mode

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-GD-015 | When "Family/Group/Couple" is checked, the system SHALL display the Individual Guest Information form for repeated use — staff can enter each group member one by one and add them to the Guest List Table. | Must Have |
| FR-GD-016 | In group mode, the Guest List Table becomes the primary record of all members. Each member can have a different room assignment if the booking covers multiple rooms. | Must Have |
| FR-GD-017 | One of the group members SHALL be designated as the "Is Contact Person" by checking the corresponding checkbox. This member's contact details are used for primary communication. | Must Have |
| FR-GD-018 | The count of guests in the Guest List Table should logically correspond to the Person (Adult) + Person (Child) count entered at the top of the tab. | Should Have |

### 3.8 Full Guest Name Auto-Generation

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-GD-019 | The "Full Guest Name" field (fo_reg_guest_full_name) SHALL be automatically composed by concatenating: Title + First Name + Last Name whenever any of these three fields are updated. | Must Have |
| FR-GD-020 | The Full Guest Name field SHALL be read-only and non-editable by the user directly; it reflects only the combined value of Title, First Name, and Last Name. | Must Have |
| FR-GD-021 | If Last Name is blank, the Full Guest Name SHALL be composed from Title + First Name only (no trailing space). | Must Have |

### 3.9 Guest Search / Profile Lookup Process

The Search button (next to Date of Birth in the Basic Information section) triggers the Guest Search Modal, allowing staff to find and auto-populate guest details from previous registration records.

#### 3.9.1 Guest Search Modal — Search Fields

| Field Name | Field ID/Name | Type | Description |
|---|---|---|---|
| Guest Name | guest_name | Text Input | Partial or full guest name search. |
| Company Name | company_name | Dropdown | Filter by associated corporate company. |
| Email | email | Text Input | Search by guest email address. |
| Mobile No | mobile_no | Text Input | Search by guest mobile/phone number. |
| National ID | national_id | Text Input | Search by national identification number. |
| Date of Birth | dob | Date Input | Filter by date of birth (DD/MM/YYYY). |
| Passport No | passport_number | Text Input | Search by passport number. |
| Room No. | room_number | Text Input | Filter by previously assigned room number. |
| Registration No. | registration_no | Text Input | Search by prior registration number. |
| From Date | from_date | Date Input | Start of date range for prior stay filter. |
| To Date | to_date | Date Input | End of date range for prior stay filter. |

#### 3.9.2 Guest Search Modal — Search Results

Search results are displayed in a table with the following columns: Guest Name, Country, Phone, Email.

#### 3.9.3 Guest Search Modal — Actions

| Button | Behaviour |
|---|---|
| Search (Blue) | Executes the search query against the guest profile database using the entered filter criteria. Results are displayed in the results table. |
| Clear/Reset (Yellow icon) | Clears all search filter fields to allow a fresh search. |
| SELECT GUEST (Blue button on result row) | Selects the clicked guest record and auto-populates ALL applicable fields in the Individual Guest Information form (name, contact, passport, visa, nationality, DOB, etc.). |
| Close (Red) | Closes the modal without making any selection. The form remains unchanged. |
| X (Close icon) | Dismisses the modal without selection. |

#### 3.9.4 Guest Profile Pre-Population Process

When a guest is selected from search results:
- System retrieves the full guest profile from the historical records.
- All matching fields in the Individual Guest Information form are populated: First Name, Last Name, Title, Gender, Date of Birth, Email, Phone, Profession, Company, Address, City, Zip Code, Country, Nationality, National ID, Driving License, Visa Number, Visa Issue Date, Visa Expiry Date, Passport Number, Passport Issue Date, Passport Expiry Date, Guest Document reference.
- Staff can review and modify any pre-populated field before adding the guest to the Guest List Table.
- The guest's stay history (Registration Number, Arrival Date, Checkout Date, Room Info) is displayed in the Guest Info modal for reference.

### 3.10 Guest Info View Modal

A "Guest Info" modal (ID: guestModal) is available to view detailed information about a selected guest from the Guest List Table. It displays a read-only summary of the guest profile along with complete stay history. The modal contains:

- **Guest Information section:** All personal details (name, DOB, gender, email, phone, company, address, city, zip, nationality, driving license, profession, national ID, passport number, passport issue/expiry dates, passport issue place, visa number, visa issue/expiry dates, country).
- **Guest Document:** Uploaded document reference (if any).
- **Guest History table:** Columns — Registration Number, Arrival Date, Checkout Date, Room Info, Action.
- **"SELECT GUEST" button** to select the guest and populate the main form.

### 3.11 Document Upload Process

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-GD-022 | The "Guest Document" file upload control (fo_reg_guest_document) SHALL allow staff to upload a scanned copy of the guest's identity document (passport, national ID, visa page). | Must Have |
| FR-GD-023 | The uploaded document SHALL be stored server-side and linked to the guest profile record by guest ID. | Must Have |
| FR-GD-024 | The system SHOULD display a preview thumbnail or filename confirmation after a document is selected for upload. | Should Have |
| FR-GD-025 | Uploaded documents SHALL be accessible from the Guest Info modal under the "Guest Document" section. | Must Have |
| FR-GD-026 | File type and size validations SHALL be applied. Accepted formats SHOULD include: JPG, PNG, PDF. Maximum file size SHOULD be configurable. | Should Have |

### 3.12 Blocked Guest Flag Process

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-GD-027 | When the "Blocked Guest" checkbox (fo_reg_guest_block) is checked for a guest, the system SHALL set a blocked status flag on that guest's profile in the database. | Must Have |
| FR-GD-028 | A blocked guest flag SHALL trigger a visual indicator (e.g., warning label) in the "Guest Status" display area on the form. | Must Have |
| FR-GD-029 | When a blocked guest is retrieved via Search, the system SHALL display an alert or warning to notify staff of the blocked status before allowing form population. | Must Have |
| FR-GD-030 | Blocking a guest SHALL NOT prevent completing the current registration if the hotel policy permits. It serves as an informational flag for staff. | Should Have |

### 3.13 Check-in Action from Guest Details Tab

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-GD-031 | The "Check-in" button (green, check-in icon) SHALL be permanently visible at the bottom of the page while the Guest Details tab is active. | Must Have |
| FR-GD-032 | Clicking "Check-in" SHALL trigger full form validation across the Registration tab and the Guest Details tab before committing to the database. | Must Have |
| FR-GD-033 | Validation SHALL check: at least one guest has been added to the Guest List Table with a valid Title, First Name, and Country. Room Number must be assigned. Check-in date must not be in the past. | Must Have |
| FR-GD-034 | If validation passes, the system SHALL save all guest detail records, create the registration/check-in record, and display a success confirmation. | Must Have |
| FR-GD-035 | If validation fails, the system SHALL highlight the failing fields and display descriptive error messages without clearing the form. | Must Have |
| FR-GD-036 | After successful check-in, the system SHALL navigate to a post-check-in confirmation screen or remain on the registration with the status updated to "Checked-In". | Must Have |

### 3.14 Update Registration (Edit Mode)

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-GD-037 | When a checked-in registration is re-opened for editing, the "Check-in" button SHALL be replaced with an "Update Registration" button. | Must Have |
| FR-GD-038 | All Guest Details fields SHALL be pre-populated with the saved data from the checked-in registration. | Must Have |
| FR-GD-039 | Staff SHALL be able to modify guest details (contact info, document numbers, blocked status) in edit mode and save changes via "Update Registration". | Must Have |
| FR-GD-040 | Changes to mandatory identity fields (passport number, visa number) post-check-in SHALL be logged in an audit trail. | Should Have |

---

## 5. UI Components Summary

This section documents every UI component present on the Guest Details tab, including its type, purpose, state management, and interaction behaviour.

### 5.1 Page-Level Components

| Component | Type | Location | Description & Behaviour |
|---|---|---|---|
| Tab Navigation Bar | Tab Strip | Top of page (fixed) | Contains tabs: Registration, Guest Details, Complimentary Item, Others Information, Search, Blank Registration Card. Active tab (Guest Details) is visually highlighted/bold. |
| Guest Details Header | Section Header (Dark Navy) | Top of tab content | Dark navy banner with white text "Guest Details". Identifies the active tab content area. |
| Person (Adult) Field | Number Input | Top-left of tab | Mandatory count of adult guests. Default: 1. Minimum: 1. |
| Person (Child) Field | Number Input | Top-center of tab | Optional count of child guests. Default: 0. |
| Family/Group/Couple Checkbox | Checkbox | Top-right of tab | Toggles between Individual and Group guest entry mode. |
| Individual Guest Information Header | Sub-Section Header (Dark Navy) | Below person count area | Labels the guest data entry section. Title reads "Individual Guest Information". |
| BASIC INFORMATION Label | Section Title Divider | Within Individual Guest Info | Dark line divider with "BASIC INFORMATION" text. Separates sections visually. |
| CONTACT INFORMATION Label | Section Title Divider | Within Individual Guest Info | Separates Contact Information fields from Basic Information. |
| VISA INFORMATION Label | Section Title Divider | Within Individual Guest Info | Separates Visa Information fields. |
| PASSPORT INFORMATION Label | Section Title Divider | Within Individual Guest Info | Separates Passport Information fields. |
| ADDITIONAL INFORMATION Label | Section Title Divider | Within Individual Guest Info | Separates Additional Information fields (document upload, guest status flags). |
| Guest List Table | Data Table | Below Individual Guest Info form | Displays all guest entries added to this registration. Columns: Guest Name, Email, Room Number, Is Contact Person, Action. |
| Previous Button | Navigation Button (Yellow) | Bottom-right | Navigates to Registration tab. Class: btnPrevious. |
| Next Button | Navigation Button (Blue) | Bottom-right | Navigates to Complimentary Item tab. Class: btnNext. |
| Check-in Button | Action Button (Green) | Bottom-center (persistent) | Submits the full registration. Visible across all tabs. Icon: calendar/suitcase. |
| Update Registration Button | Action Button (Yellow, hidden) | Bottom-center (edit mode only) | Replaces Check-in in edit mode. Class: d-none by default. |

### 5.2 Form Input Components — Basic Information

| Component | Type | Validation | Default | Notes |
|---|---|---|---|---|
| Title Dropdown | select-one | Required (*) | -- Select -- | Mr./Mrs./Ms./Dr./etc. |
| First Name Input | text | Required (*) | Empty | Placeholder: "First Name" |
| Last Name Input | text | Optional | Empty | Placeholder: "Last Name" |
| Gender Dropdown | select-one | Optional | -- Select -- | Male/Female options |
| Room No. Dropdown | select-one | Required (*) | Empty | Carried from Registration tab |
| Full Guest Name Input | text (auto-fill) | Required (*) | Empty | Read-only; auto-generated |
| Date of Birth Input | text (date-picker) | Optional | Empty | Format: DD/MM/YYYY; date picker active |
| Search Button | button | N/A | Blue | Opens Guest Search Modal |

### 5.3 Form Input Components — Contact Information

| Component | Type | Validation | Default | Notes |
|---|---|---|---|---|
| Email Address Input | email | Optional; format validation | Empty | Placeholder: "email@example.com" |
| Phone Number Input | text | Optional | Empty | Placeholder: "+880 XXX-XXXXXXX" |
| Profession Dropdown | select-one | Optional | -- Select -- | From master list |
| Company Name Dropdown | select-one (searchable) | Optional | -- Select -- | From corporate accounts master |
| Address Input | text | Optional | Empty | Placeholder: "Street Address" |
| City Input | text | Optional | Empty | |
| Zip Code Input | number | Optional; numeric only | Empty | |
| Country Dropdown | select-one | Required (*) | Bangladesh | From country master list |
| Nationality Input | text | Optional | Bangladeshi | Auto-set from country selection |
| National ID Input | number | Optional; numeric only | Empty | |
| Driving License Input | text | Optional | Empty | Placeholder: "Driving License No." |

### 5.4 Form Input Components — Visa, Passport & Additional Info

| Component | Type | Validation | Notes |
|---|---|---|---|
| Visa Number Input | text | Optional | Alphanumeric visa document number |
| Visa Issue Date Input | text (date picker) | Optional | DD/MM/YYYY format |
| Visa Expiry Date Input | text (date picker) | Optional | DD/MM/YYYY; should be > Issue Date |
| Passport Number Input | text | Optional | Alphanumeric passport number |
| Passport Issue Date Input | text (date picker) | Optional | DD/MM/YYYY format |
| Passport Expiry Date Input | text (date picker) | Optional | DD/MM/YYYY; should be > Issue Date |
| Guest Document File Upload | file | Optional | Accepts document/image files; stored server-side |
| Blocked Guest Checkbox | checkbox | Optional | Default: unchecked; flags guest as blocked |
| Is Contact Person Checkbox | checkbox | Optional | Default: unchecked; marks group contact |
| Guest Status Display | read-only label | N/A | Driven by Blocked Guest flag; shows guest status |

### 5.5 Guest Search Modal Components

| Component | Type | Description |
|---|---|---|
| Modal Title | Static Text | Displays "Search Guest" in modal header. |
| 11 Search Filter Fields | Text Inputs + Dropdowns + Date Pickers | Guest Name, Company, Email, Mobile, National ID, DOB, Passport No, Room No, Registration No, From Date, To Date. |
| Search Button | Primary (Blue) Button | Executes search query. |
| Reset/Clear Button | Warning (Yellow) Icon Button | Clears all filter fields. |
| Search Results Table | Data Table | Columns: Guest Name, Country, Phone, Email. One row per matching guest. |
| SELECT GUEST Button (per row) | Primary (Blue) Button | Selects guest from results and populates the main form. |
| Close Button (Red) | Danger Button | Closes modal without selection. |
| X (Close Icon) | Icon Button | Dismisses modal. |

### 5.6 Guest Info Modal Components

| Component | Type | Description |
|---|---|---|
| Modal Title | Static Text | "Guest Info" in header. |
| Guest Information Section | Read-only display fields | Displays all guest profile fields (25+ data points including passport, visa, nationality details). |
| Guest Document Section | File reference/preview | Shows uploaded document reference. |
| Guest History Table | Data Table | Columns: Registration Number, Arrival Date, Checkout Date, Room Info, Action. Shows all prior stays. |
| SELECT GUEST Button | Primary (Blue) Button | Populates main form with this guest's data. |
| Close Button | Secondary Button | Closes the modal. |

---

## 6. Business Rules

### 6.1 Guest Identity & Mandatory Field Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-GD-001 | A minimum of one adult guest must be entered and added to the Guest List Table before the Check-in action can succeed. | At least one guest entry is required for registration to be complete. |
| BR-GD-002 | The Title field is mandatory for all guest entries. No guest can be added to the Guest List Table without a selected title. | Add button is disabled or validation fails if Title is not selected. |
| BR-GD-003 | The First Name field is mandatory. A guest entry cannot be saved without a first name. | Add action requires a non-empty First Name value. |
| BR-GD-004 | Country is a mandatory field and defaults to Bangladesh. It must retain a valid value from the country master list. | Country cannot be cleared or left as "-- Select --". |
| BR-GD-005 | The Full Guest Name is always system-generated from Title + First Name + Last Name. It cannot be manually overridden. | Full Guest Name is read-only; source fields drive its value. |
| BR-GD-006 | If both Visa Issue Date and Visa Expiry Date are entered, the Expiry Date must be after the Issue Date. | Date range validation is applied; invalid ranges produce an error. |
| BR-GD-007 | If both Passport Issue Date and Passport Expiry Date are entered, the Expiry Date must be after the Issue Date. | Date range validation is applied for passport dates. |

### 6.2 Guest Mode Rules (Individual vs. Group)

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-GD-008 | In Individual mode (Family/Group/Couple unchecked), the standard expectation is one primary guest per registration. Additional guests may still be added to the list but the mode does not require multiple entries. | Single guest entry is sufficient for individual registration. |
| BR-GD-009 | In Group/Family mode (Family/Group/Couple checked), multiple guests are expected. Exactly one guest must be flagged as "Is Contact Person". | Contact Person flag must be assigned to exactly one member of the group. |
| BR-GD-010 | The total number of guest entries added to the Guest List Table should not exceed the sum of Person (Adult) + Person (Child) count specified at the top. | System may warn (or enforce) that guest entries match the declared person count. |

### 6.3 Blocked Guest Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-GD-011 | When a blocked guest is retrieved via the Search modal, the system must display a visible warning to staff before allowing them to proceed with that guest's registration. | Staff awareness is required; the decision to proceed rests with the Duty Manager. |
| BR-GD-012 | The Blocked Guest flag is stored on the guest profile level, not the registration level. It persists across future searches and registrations. | Blocking a guest affects all future registrations using that guest profile. |
| BR-GD-013 | Only authorised staff (Supervisors, Duty Managers) should be able to set or remove the Blocked Guest flag. | RBAC (role-based access) controls the blocked_guest checkbox. |

### 6.4 Document Upload Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-GD-014 | Each guest can have at most one primary document uploaded per registration. If a new document is uploaded, it replaces the previous one. | One document per guest per registration. |
| BR-GD-015 | Guest documents are considered sensitive personal data and must be stored securely with access restricted to authorised hotel staff. | Document store must implement access control and encryption. |
| BR-GD-016 | Uploaded documents must not exceed the system-configured maximum file size. Invalid file types must be rejected with a descriptive error message. | File validation must occur before upload is accepted. |

### 6.5 Returning Guest / Profile Lookup Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-GD-017 | When a returning guest profile is selected from the Search modal, all historical data is pre-populated. Staff must verify the data accuracy before clicking Add. | Pre-population is a convenience; staff are responsible for verifying accuracy. |
| BR-GD-018 | If a returning guest has updated their passport or contact details, the new details entered in the form (not the historical data) become the record for this registration. The guest profile may be updated accordingly. | Registration-specific details may differ from historical profile data. |
| BR-GD-019 | Guest profiles are shared across all registrations system-wide. Modifications to a guest profile (such as blocking) affect all users of that profile. | Profile changes have system-wide scope. |

### 6.6 Data Integrity & Save Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-GD-020 | Guest entries in the Guest List Table are held in the browser session until the Check-in (or Update Registration) action is executed. They are not written to the database by clicking "Add" alone. | Actual DB writes occur only at Check-in / Update Registration. |
| BR-GD-021 | If the user navigates away from the Guest Details tab using the tab header (not Next/Previous), all Guest List Table entries must be preserved in the session. | Session state must persist across tab navigation within the wizard. |
| BR-GD-022 | Cancelling the registration (browser close / session expiry) without checking in must result in no partial guest data being persisted. | Atomic commit: all data is saved at check-in or nothing. |

---

## 7. Non-Functional Requirements

### 7.1 Performance

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-GD-001 | The Guest Details tab SHALL load all fields and sections within an acceptable time after tab activation. | <2 seconds on standard network |
| NFR-GD-002 | The Guest Search modal SHALL return results from the database within an acceptable response time. | <3 seconds for up to 500 matching records |
| NFR-GD-003 | Auto-population of the Full Guest Name field SHALL occur immediately upon each keystroke in First Name, Last Name, or Title selection. | <100ms (client-side) |
| NFR-GD-004 | File upload for Guest Document SHALL complete without blocking the UI (async upload preferred). | Upload progress indicator; UI remains responsive |
| NFR-GD-005 | The Guest List Table SHALL render up to 20 guest entries without perceptible performance degradation. | Smooth rendering for group registrations |

### 7.2 Usability

| NFR ID | Requirement | Standard |
|---|---|---|
| NFR-GD-006 | Mandatory fields (Title, First Name, Country) SHALL be clearly indicated with a red asterisk (*) visible at all times. | Visual clarity; WCAG contrast compliance |
| NFR-GD-007 | Date picker controls for date fields (DOB, visa dates, passport dates) SHALL be user-friendly with calendar popups supporting date selection without manual typing. | Consistent date-picker UI across all date fields |
| NFR-GD-008 | The Guest Search modal SHALL be resizable or scrollable to accommodate a large number of search results without the modal cutting off data. | Scrollable results table |
| NFR-GD-009 | Validation error messages SHALL appear adjacent to the failing field (inline validation) rather than only at form top. | Usability best practice |
| NFR-GD-010 | A Front Desk staff member with basic computer literacy SHALL be able to complete the full Guest Details tab (individual guest) within 3 minutes. | Operational efficiency target |
| NFR-GD-011 | The form SHALL support keyboard navigation (Tab key) across all fields in logical left-to-right, top-to-bottom order. | Accessibility — keyboard-only operation support |

### 7.3 Security

| NFR ID | Requirement | Notes |
|---|---|---|
| NFR-GD-012 | Only authenticated HMS users with appropriate Front Office roles SHALL access the Guest Details tab. | RBAC enforced at module level |
| NFR-GD-013 | Passport, visa, and national ID data are classified as Personally Identifiable Information (PII). These fields SHALL be transmitted over HTTPS and stored encrypted at rest. | PII data protection requirement |
| NFR-GD-014 | The Blocked Guest checkbox and Guest Status flag SHALL only be modifiable by users with Supervisor or higher roles. | Privilege-based field access control |
| NFR-GD-015 | Guest document uploads SHALL be scanned for malware/viruses before storage. | Security validation on file uploads |
| NFR-GD-016 | All guest PII data displayed in the Guest Search modal SHALL be masked for staff roles that do not have full PII view permissions (e.g., showing only partial passport numbers). | Data masking for privacy compliance |

### 7.4 Reliability & Availability

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-GD-017 | Guest Details data entered in the wizard SHALL be preserved in browser session storage to survive accidental tab switches or brief network interruptions. | Session persistence |
| NFR-GD-018 | If the Guest Search modal fails to connect to the database (network error), a user-friendly error message SHALL be displayed without crashing the main form. | Graceful error handling |
| NFR-GD-019 | The document upload feature SHALL fail gracefully if the file server is unavailable — displaying an error without blocking the rest of the registration process. | Non-blocking failure for optional features |

### 7.5 Data Quality & Compliance

| NFR ID | Requirement | Notes |
|---|---|---|
| NFR-GD-020 | The system SHALL enforce email format validation (RFC 5321 standard) on the Email Address field. | Format: user@domain.tld |
| NFR-GD-021 | Date fields SHALL reject logically impossible dates (e.g., 30-Feb, future date-of-birth). | Calendar-aware date validation |
| NFR-GD-022 | The system SHALL support guest data collection in compliance with local hotel industry regulations and immigration reporting requirements (e.g., C-Form equivalent for foreign nationals). | Regulatory compliance |
| NFR-GD-023 | Guest profile data changes (especially post-check-in edits to passport/visa info) SHALL be logged in an audit trail with user ID, timestamp, and old/new values. | Audit trail requirement |

