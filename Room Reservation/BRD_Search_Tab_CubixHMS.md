# Business Requirements Document

## Search Module

**Room Registration — Search Tab**

- **System:** Cubix HMS V-9.3.0 (deployed) — BRD drafted against v13.1.0
- **Version:** 1.0 | **Date:** April 15, 2026
- **Module:** Front Office > Room Registration > Search Tab
- **Prepared For:** Cubix HMS Project Team

---

## 1. Executive Summary

This Business Requirements Document (BRD) defines the complete functional and non-functional requirements for the **Search** tab within the Room Registration module of the Cubix Hotel Management System (HMS). The Search tab is the fifth tab in the Room Registration wizard, accessible under Front Office > Room Registration.

The Search tab serves as a centralised lookup and management interface for all room registration records in the system. Unlike the other wizard tabs (Registration, Guest Details, Complimentary Item, Others Information) which are used to create new registrations, the Search tab enables staff to query, retrieve, and manage existing registration records across the full lifecycle of a guest stay — from active In-House registrations to historical Checked-Out records.

The tab provides 11 configurable search/filter fields, a paginated and sortable results table displaying 9 data columns, 5 per-row action controls (Edit, Re-activate Registration, Bill Preview, Split Bill Preview, Registration Card), real-time table search, adjustable page size, and access to key post-registration workflows including re-activation of cancelled/checked-out registrations and bill split management.

| Attribute | Details |
|---|---|
| System | Cubix HMS V-9.3.0 (deployed) — BRD drafted against v13.1.0 |
| Module | Front Office |
| Sub-Module | Room Registration |
| Tab Name | Search |
| Tab Position | 5th tab (utility lookup tab) |
| Total Filter Fields | 11 search/filter parameters specified (10 currently implemented; Reference filter pending — see §8) |
| Result Table Columns | 9 columns (Registration No., Guest Name, Mobile, Company, Room Info, Check In, Check Out, Status, Action) |
| Per-Row Action Buttons | 5 specified (3 live today: Edit, Bill Preview, Registration Card). Re-activate and Split Bill Preview are pending — see §8. |
| Total Records in System | 2,291 registration records (as of demo data) |
| Primary Users | Front Desk Staff, Supervisors, Duty Managers, Cashiers |
| Document Version | 1.0 |
| Prepared Date | April 15, 2026 |
| Status | Draft for Review |

---

## 2. Module Context & Positioning

### 2.3 Dependencies

#### 2.3.1 Upstream Dependencies

- **Room Registration Records (all 4 wizard tabs):** The Search tab queries the complete room registration database. All records displayed are created through the Registration wizard (Tabs 1-4) or via the Room Reservation module.
- **Room Types Master:** The "Room Types" filter dropdown is populated from the room type configuration master (8 types configured: Signature Suite, Business Class Twin, Super Deluxe King, Deluxe Twin, Deluxe King, Executive Suite, Business Class King, PM Room).
- **Company Master:** The "Company Name" filter is populated from the corporate accounts master list.
- **Country Master:** The "Country" filter is populated from the country master reference table.
- **Reference Master:** The "Reference" filter dropdown is populated from the sales reference/channel master (28 configured references including Front Office, OTAs like Expedia, Gozayaan, and individual sales agents).
- **Guest Profile Database:** "Name of the Guest" search queries the linked guest profile records.

#### 2.3.2 Downstream Dependencies

- **Edit Action → Registration Wizard:** Clicking "Edit" on a search result opens the full Room Registration wizard pre-populated with that registration's data, enabling modification of any field across all 4 wizard tabs.
- **Bill Preview → Billing Module:** The "Bill Preview" action navigates to the guest bill/folio view at: /front_office/room-registration/bill-preview/{registration_id}.
- **Registration Card → Printout:** The "Registration Card" action generates and opens the pre-registration card PDF at: /front_office/room-registration/pre-registration-card/{registration_id}.
- **Split Bill Preview → Guest Bill Split:** The "Split Bill Preview" action opens the Guest Bill Split Information modal, enabling the staff to view and manage split billing arrangements for multi-room or group registrations.
- **Re-activate Registration → Registration Status:** The "Re-active Registration" action changes a Checked-Out or cancelled registration back to an active/In-House status (subject to business rules and authorization).
- **Night Audit:** Registration status changes via Re-activation affect Night Audit records and room occupancy statistics.

#### 2.3.3 Data Dependencies

- Registration records are queried via the foRegSearch() function with filter parameters passed as a server-side query.
- Results are rendered in a DataTables (jQuery plugin) instance with ID "data_tbl_reg".
- Page size is configurable (10, 25, 50, 100 entries per page) with full server-side or client-side pagination across 230 pages for 2,291 total records.

---

## 3. Functional Requirements

This section details every functional requirement for the Search tab, covering each actionable process, filter behaviour, table interaction, action button workflow, and system response.

### 3.1 Tab Access & Navigation

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-SR-001 | The "Search" tab SHALL be accessible by clicking its header from any other tab in the Room Registration wizard at any time. | Must Have |
| FR-SR-002 | The Search tab SHALL load with all filter fields empty/default and the results table pre-populated with ALL registration records sorted by Registration No. descending (most recent first). | Must Have |
| FR-SR-003 | A "Previous" navigation button SHALL be available at the bottom of the Search tab, navigating back to the Others Information tab. | Must Have |
| FR-SR-004 | The Search tab SHALL operate independently and SHALL NOT require completion of any other wizard tab to function. | Must Have |

### 3.2 Search Filter Panel — All 11 Filter Fields

The filter panel at the top of the Search tab contains 11 input controls arranged in a 4-column responsive grid across 3 rows.

#### 3.2.1 Room Types Filter

| Attribute | Detail |
|---|---|
| Label | Room Types |
| Field ID | src_fo_reg_roomType |
| Name | room_type |
| Type | Dropdown (select) |
| Default | --- Please Select --- |
| Options | Signature Suite-River View \| Business Class Twin-River View \| Super Deluxe King-River View \| Deluxe Twin-River View \| Deluxe King-City View \| Executive Suite-River View \| Business Class King-River View \| PM Room |
| Behaviour | Filters results to show only registrations for the selected room type category. |

#### 3.2.2 Room Number Filter

| Attribute | Detail |
|---|---|
| Label | Room Number |
| Field ID | src_fo_reg_room_number |
| Name | fo_reg_room_number |
| Type | Number Input |
| Placeholder | Room Number |
| Behaviour | Filters results to registrations for a specific room number. Accepts integer room numbers (e.g., 1208, 1005). |

#### 3.2.3 Registration No. Filter

| Attribute | Detail |
|---|---|
| Label | Registration No. |
| Field ID | src_fo_reg_registration_no |
| Name | fo_reg_registration_no |
| Type | Text Input |
| Placeholder | Registration No. |
| Behaviour | Searches for a specific registration by its system-assigned registration number (format: RR00002292). Supports partial match. |

#### 3.2.4 Reservation Number Filter

| Attribute | Detail |
|---|---|
| Label | Reservation Number |
| Field ID | src_fo_reg_reservation_no |
| Name | fo_reg_reservation_no |
| Type | Text Input |
| Placeholder | Reservation Number |
| Behaviour | Filters registrations by the originating reservation number (for registrations linked to prior reservations). |

#### 3.2.5 Check-In Date Filter

| Attribute | Detail |
|---|---|
| Label | Check-In Date |
| Field ID | src_fo_reg_check_in_date3 |
| Name | fo_reg_check_in_date3 |
| Type | Date Input with jQuery Datepicker (class: date_picker hasDatepicker) |
| Placeholder | Check-In Date |
| Format | Date picker — calendar popup with date selection |
| Behaviour | Filters registrations by check-in date. Shows all registrations with the selected check-in date. |

#### 3.2.6 Company Name Filter

| Attribute | Detail |
|---|---|
| Label | Company Name |
| Field ID | src_fo_reg_company_name |
| Name | fo_reg_company_name |
| Type | Dropdown (select), searchable |
| Default | -- Please Select -- |
| Behaviour | Filters registrations by the corporate account/company associated with the booking. |

#### 3.2.7 Country Filter

| Attribute | Detail |
|---|---|
| Label | Country |
| Field ID | src_fo_reg_country |
| Name | fo_reg_country |
| Type | Dropdown (select), searchable |
| Default | -- Please Select -- |
| Behaviour | Filters registrations by the guest's country of residence/nationality. |

#### 3.2.8 Name of the Guest Filter

| Attribute | Detail |
|---|---|
| Label | Name Of the Guest |
| Field ID | src_fo_reg_guest_name |
| Name | fo_reg_guest_name |
| Type | Text Input |
| Placeholder | Name Of the Guest |
| Behaviour | Searches for registrations by guest name. Supports partial name match (e.g., entering "Test" returns all registrations with "TEST" or "Test" in the guest name). |

#### 3.2.9 Guest Phone Number Filter

| Attribute | Detail |
|---|---|
| Label | Guest Phone Number |
| Field ID | src_fo_reg_guest_phone_number |
| Name | fo_reg_guest_phone_number |
| Type | Text Input |
| Placeholder | Guest Phone Number |
| Behaviour | Filters registrations by the primary guest's phone/mobile number. Supports partial match. |

#### 3.2.10 Contact Person Phone Number Filter

| Attribute | Detail |
|---|---|
| Label | Contact Person Phone Number |
| Field ID | src_fo_reg_guest_contact_person_phone_number |
| Name | fo_reg_guest_contact_person_phone_number |
| Type | Text Input |
| Placeholder | Contact Person Phone Number |
| Behaviour | Filters registrations by the phone number of the group contact person (relevant for Family/Group registrations where a separate contact person was designated in Guest Details). |

#### 3.2.11 Reference Filter

| Attribute | Detail |
|---|---|
| Label | Reference |
| Field ID | src_reference_id |
| Name | src_reference_id |
| Type | Dropdown (select) |
| Default | -- Please Select -- |
| Total Options | 28 reference sources configured |
| Sample Options | Front Office \| Mr. Reaz \| Mr. Nurul \| Ms. Anika \| BW SKS Chattogram \| Dhaka Booking \| Dhaka Sales Office \| Expedia.com \| FIT \| Gozayaan \| and 18 more |
| Behaviour | Filters registrations by the booking/referral source or sales agent who handled the booking. Useful for sales channel performance analysis. |

### 3.3 Search & Clear Actions

#### 3.3.1 Search Button — Actionable Process

| Attribute | Detail |
|---|---|
| Button Label | Search |
| Button Style | Primary (Blue) — class: btn_modern_design primary_new_button mr-2 |
| JavaScript Function | foRegSearch() |
| Location | Top-right of filter panel |

**Process — Executing a Search:**

- **Step 1** — Staff enters one or more filter criteria in the filter panel fields (any combination of the 11 fields).
- **Step 2** — Staff clicks the "Search" button.
- **Step 3** — The system calls the foRegSearch() function, collecting all filter field values.
- **Step 4** — An AJAX/server-side request is sent with the filter parameters as query arguments.
- **Step 5** — The server executes the database query against the room registration records, applying all active filters as AND conditions.
- **Step 6** — The filtered results are returned and rendered in the DataTable results grid (table ID: data_tbl_reg).
- **Step 7** — The "Showing X to Y of Z entries" info text is updated to reflect the filtered record count.
- **Step 8** — If no records match the filter criteria, the table displays an empty state message: "No data available in table".
- **Step 9** — Pagination is reset to page 1 of the filtered results.

#### 3.3.2 Clear Button — Actionable Process

| Attribute | Detail |
|---|---|
| Button Label | Clear |
| Button Style | Warning (Yellow) — class: btn_modern_design warning_new_button |
| JavaScript Function | foRegSearchClear() |
| Location | Top-right of filter panel, adjacent to Search button |

**Process — Clearing Filters:**

- **Step 1** — Staff clicks the "Clear" button.
- **Step 2** — The foRegSearchClear() function is called.
- **Step 3** — All 11 filter fields are reset to their default/empty state (dropdowns reset to "-- Please Select --", text inputs cleared, date picker cleared).
- **Step 4** — The system automatically re-runs the search with no filters applied (equivalent to showing all records).
- **Step 5** — The results table is refreshed to display all registration records sorted by Registration No.

### 3.4 Results Table — Structure & Behaviour

The results are displayed in a DataTables-powered table (ID: data_tbl_reg) with the following specifications:

#### 3.4.1 Table Columns

| Column # | Column Name | Data Displayed | Sortable | Description |
|---|---|---|---|---|
| 1 | Registration No | System-generated registration number (e.g., RR00002292) | Yes (ascending/descending) | Unique identifier for each registration. Primary sort column. Default sort: ascending (most recent highest number). |
| 2 | Guest Name | Full name of the primary guest | No | Displays the guest's full name from the guest profile linked to this registration. |
| 3 | Mobile | Guest mobile/phone number | No | Displays the guest's primary phone number. Shows "-" if not provided. |
| 4 | Company | Associated corporate account name | No | Shows the company/corporate account linked to the booking. Shows "-" if not applicable. |
| 5 | Room Info | Room type and room number(s) | No | Displays the room type name and room number in parentheses (e.g., "Business Class King-River View (1208)"). For group/multi-room registrations, multiple rooms are listed. |
| 6 | Check In | Check-in date | No | Date in YYYY-MM-DD format (e.g., 2026-03-05). |
| 7 | Check Out | Scheduled check-out date | No | Date in YYYY-MM-DD format (e.g., 2026-03-06). |
| 8 | Status | Registration status with check-in/check-out staff info | No | Multi-line status cell showing: Check-In By: [username], Check-Out By: [username], and a colour-coded status badge. |
| 9 | Action | 5 action control buttons/links per row | No | Contains all per-row operational action controls. |

#### 3.4.2 Status Badge Types

| Badge Label | CSS Class | Colour | Meaning |
|---|---|---|---|
| Checked Out | badge badge-danger | Red | The guest has completed their stay and has been formally checked out of the hotel. |
| In-House | badge badge-primary | Blue | The guest is currently checked in and actively in residence at the hotel. |

Note: Other status values may exist for different registration states (e.g., Cancelled, No-Show, Reserved) depending on the full system configuration, though "Checked Out" and "In-House" are the two observed in the demo data.

### 3.5 Real-Time Table Search

| Control | Type | Behaviour |
|---|---|---|
| Search Box | Text Input (DataTables built-in search) | Located at top-right of the results table, labelled "Search:". Applies an instant client-side text filter across ALL visible columns simultaneously. Filters as the user types (real-time, no button click required). Works on all string-represented data: Registration No., Guest Name, Mobile, Company, Room Info, Status. |

**Process — Using the Real-Time Search:**

- Staff clicks the "Search:" text input at the top right of the results table.
- Staff types any text (e.g., a partial guest name, room number, or registration number).
- The DataTable instantly filters the visible rows to show only those containing the typed text in any column.
- The "Showing X to Y of Z entries" counter updates in real time to reflect the filtered count.
- Clearing the search box restores all rows.

### 3.6 Pagination Controls

| Control | Description | Behaviour |
|---|---|---|
| Show Entries Dropdown | Dropdown with options: 10, 25, 50, 100 entries per page (name: data_tbl_reg_length) | Staff selects the number of records to display per page. Changing this value immediately re-renders the table with the new page size. |
| Previous / Next Buttons | DataTables navigation buttons | Navigate between pages of results. |
| Page Number Buttons | Numbered page buttons (1, 2, 3 ... 230 for 2,291 records at 10/page) | Clicking a page number jumps directly to that page. |
| Showing X to Y of Z entries | Info text below table | Dynamically shows the range of records currently displayed and the total count matching current filters. |

### 3.7 Column Sorting

The "Registration No" column is the only sortable column (class: sorting_asc by default). Clicking the column header toggles sort between ascending and descending order. All other columns (Guest Name, Mobile, Company, Room Info, Check In, Check Out, Status, Action) have sorting disabled (class: sorting_disabled).

### 3.8 Per-Row Action Controls — All 5 Actions

Each row in the results table contains 5 action controls (3 buttons and 2 hyperlinks). These are the primary operational actions available from the Search tab.

#### 3.8.1 Action 1: Edit (Pencil Icon)

| Attribute | Detail |
|---|---|
| Title / Tooltip | Edit |
| Icon | Pencil — ti-pencil |
| Button Style | Info (Light Blue) — btn_modern_design info_new_button mini_padding |
| JavaScript Function | showFoRegGuestData(registration_id) |
| Type | Button (BUTTON tag) |

**Actionable Process — Edit Registration:**

- **Step 1** — Staff clicks the Edit (pencil) button for a registration row.
- **Step 2** — The showFoRegGuestData(registration_id) function is called with the selected registration's ID.
- **Step 3** — The system loads the full registration record from the database for the given registration_id.
- **Step 4** — The Room Registration wizard is re-populated with all saved data from all 4 tabs (Registration, Guest Details, Complimentary Item, Others Information).
- **Step 5** — The view navigates to (or activates) the Registration tab (Tab 1) of the wizard, now showing the selected record's data.
- **Step 6** — The "Check-in" button is replaced by the "Update Registration" button (class: d-none becomes visible).
- **Step 7** — Staff can modify any field across any tab and click "Update Registration" to save changes to the existing record.
- **Step 8** — Post-save, the record is updated in the database and staff can return to the Search tab to verify the change.

#### 3.8.2 Action 2: Re-activate Registration (Reload Icon)

| Attribute | Detail |
|---|---|
| Title / Tooltip | Re-active Registration |
| Icon | Reload/Refresh — ti-reload |
| Button Style | Warning (Yellow/Orange) — btn_modern_design warning_new_button mini_padding |
| JavaScript Function | reactiveRegistration(registration_id) |
| Type | Button (BUTTON tag) |

**Actionable Process — Re-activating a Registration:**

- **Step 1** — Staff clicks the Re-active Registration (reload) button for a Checked-Out or cancelled registration.
- **Step 2** — The reactiveRegistration(registration_id) function is called.
- **Step 3** — System displays a confirmation prompt asking staff to confirm the re-activation.
- **Step 4** — Upon confirmation: the registration status is changed from "Checked Out" back to "In-House" (or an appropriate active status).
- **Step 5** — Room availability is updated: the room is marked as Occupied again.
- **Step 6** — The Night Audit records and billing engine are notified of the status change.
- **Step 7** — The results table is refreshed; the status badge for the re-activated registration updates to "In-House".
- **Step 8** — This action is typically used when a guest is checked out in error or has extended their stay after checkout was processed.

#### 3.8.3 Action 3: Bill Preview (File Icon)

| Attribute | Detail |
|---|---|
| Title / Tooltip | Bill Preview |
| Icon | File — ti-file |
| Button Style | Success (Green) — btn_modern_design success_new_button mini_padding |
| Type | Hyperlink (A tag) — navigates to a new URL |
| URL Pattern | /front_office/room-registration/bill-preview/{registration_id} |

**Actionable Process — Viewing Guest Bill:**

- **Step 1** — Staff clicks the Bill Preview (file) button for a registration row.
- **Step 2** — Browser navigates to the bill preview page for that registration_id.
- **Step 3** — The bill preview page displays the complete guest folio/bill showing all charges, payments, and balance for the registration.
- **Step 4** — Staff can review, print, or further process the bill from this page.
- **Step 5** — Staff uses the browser's Back button or the HMS navigation to return to the Search tab.

#### 3.8.4 Action 4: Split Bill Preview (Scissors Icon)

| Attribute | Detail |
|---|---|
| Title / Tooltip | Split Bill Preview |
| Icon | Scissors — fa fa-cut |
| Button Style | Info (Light Blue) — btn_modern_design info_new_button mini_padding |
| JavaScript Function | openSplitBillModal(registration_id, group_id) |
| Type | Button (BUTTON tag) |

**Actionable Process — Split Bill Preview:**

- **Step 1** — Staff clicks the Split Bill Preview (scissors) button for a registration.
- **Step 2** — The openSplitBillModal(registration_id, group_id) function is called.
- **Step 3** — The "Guest Bill Split Information" modal (ID: splitBillModal) is opened as a modal overlay.
- **Step 4** — The modal displays the bill split information for the selected registration, showing how charges are distributed across multiple folios or guests.
- **Step 5** — Staff reviews the split bill information.
- **Step 6** — Staff clicks "Close" or the X button to dismiss the modal and return to the Search results.

Note: This action is particularly relevant for group registrations where billing may be split across multiple guests or accounts.

#### 3.8.5 Action 5: Registration Card (Receipt Icon)

| Attribute | Detail |
|---|---|
| Title / Tooltip | Registration card |
| Icon | Receipt — ti-receipt |
| Button Style | Secondary (Grey/Muted) — btn_modern_design secondary_new_button mini_padding |
| Type | Hyperlink (A tag) — navigates to a new URL |
| URL Pattern | /front_office/room-registration/pre-registration-card/{registration_id} |

**Actionable Process — Printing Registration Card:**

- **Step 1** — Staff clicks the Registration Card (receipt) button for a registration.
- **Step 2** — Browser navigates to the pre-registration card page for that registration_id.
- **Step 3** — A printable Registration Card document is generated, containing guest name, room details, check-in/out dates, complimentary services, visit purpose, and other registration details.
- **Step 4** — Staff prints the card for the guest to review and sign at check-in (or for reprinting if the original was misplaced).
- **Step 5** — Staff uses browser navigation to return to Search tab.

### 3.9 Previous Navigation Button

| Button | Style | Location | Behaviour |
|---|---|---|---|
| Previous | Primary (Blue) — class: btn_modern_design primary_new_button btnPrevious | Bottom of Search tab content area | Navigates back to the Others Information tab (Tab 4). Does NOT affect any data. Session state is preserved. |

---

## 5. UI Components Summary

This section documents every UI component on the Search tab with its type, visual style, behaviour, and interaction details.

### 5.1 Page-Level Components

| Component | Type | Location | Description |
|---|---|---|---|
| Tab Navigation Bar | Tab Strip (persistent) | Top of page | Contains 6 tabs: Registration, Guest Details, Complimentary Item, Others Information, Search (active), Blank Registration Card. Search tab is highlighted. |
| Search Header | Page Header (Dark Navy) | Top of tab content | Dark navy banner with white text "Search" identifying the active tab. |
| Filter Panel | Form Container | Top of tab content, below header | 3-row, 4-column responsive filter form with 11 inputs. No visible border/card — floats within tab content. |
| Search Button | Primary Blue Button | Top-right of filter panel | Executes foRegSearch() with current filter values. |
| Clear Button | Warning Yellow Button | Top-right of filter panel, right of Search | Executes foRegSearchClear(), resets all filters. |
| Show Entries Dropdown | Select Dropdown | Above results table, left side | Controls number of rows per page: 10, 25, 50, 100. |
| Real-Time Search Box | Text Input (DataTables) | Above results table, right side, labelled "Search:" | Instant client-side column filter. |
| Results Table | DataTable (jQuery) | Main content area, below filter panel | Table ID: data_tbl_reg. Displays registration records in 9 columns. |
| Table Headers (Row) | Dark Navy Header Row | First row of results table | Column headers for all 9 columns. Registration No. has sort arrow; others are static. |
| Results Data Rows | Table Body Rows | Within results table | One row per registration record. Alternating background for readability. |
| Pagination Controls | DataTables Pagination | Below results table, right side | Previous, numbered pages (1-230 for 2,291 records), Next. |
| Showing Info Text | Static Info Text | Below results table, left side | "Showing X to Y of Z entries" — updates dynamically. |
| Previous Button | Primary Blue Button (input type button) | Bottom of tab content area | Navigates back to Others Information tab. |

### 5.2 Filter Panel — Input Components

| Field Label | Type | Field ID | Row | Column | Notes |
|---|---|---|---|---|---|
| Room Types | Dropdown (select) | src_fo_reg_roomType | Row 1 | Col 1 | 8 room type options + blank default |
| Room Number | Number Input | src_fo_reg_room_number | Row 1 | Col 2 | Placeholder: "Room Number" |
| Registration No. | Text Input | src_fo_reg_registration_no | Row 1 | Col 3 | Placeholder: "Registration No." |
| Reservation Number | Text Input | src_fo_reg_reservation_no | Row 1 | Col 4 | Placeholder: "Reservation Number" |
| Check-In Date | Date Input (jQuery datepicker) | src_fo_reg_check_in_date3 | Row 2 | Col 1 | Class: date_picker hasDatepicker; calendar popup |
| Company Name | Searchable Dropdown (select2) | src_fo_reg_company_name | Row 2 | Col 2 | Populated from company master |
| Country | Searchable Dropdown (select2) | src_fo_reg_country | Row 2 | Col 3 | Populated from country master |
| Name Of the Guest | Text Input | src_fo_reg_guest_name | Row 2 | Col 4 | Placeholder: "Name Of the Guest" |
| Guest Phone Number | Text Input | src_fo_reg_guest_phone_number | Row 3 | Col 1 | Placeholder: "Guest Phone Number" |
| Contact Person Phone Number | Text Input | src_fo_reg_guest_contact_person_phone_number | Row 3 | Col 2 | Placeholder: "Contact Person Phone Number" |
| Reference | Dropdown (select) | src_reference_id | Row 3 | Col 3 | 28 reference options + blank default |

Note: Search and Clear buttons are positioned in Row 3, Col 4.

### 5.3 Results Table — Column Components

| Column | Width (approx.) | Alignment | Sort | Data Format |
|---|---|---|---|---|
| Registration No | ~120px | Center | Sortable (ASC/DESC) | String: RR + 8 digits |
| Guest Name | ~150px | Center | Disabled | Free text (full name) |
| Mobile | ~100px | Center | Disabled | Phone string or "-" |
| Company | ~120px | Center | Disabled | Company name or "-" |
| Room Info | ~200px | Center | Disabled | Type (Number); multi-line for groups |
| Check In | ~100px | Center | Disabled | Date: YYYY-MM-DD |
| Check Out | ~100px | Center | Disabled | Date: YYYY-MM-DD |
| Status | ~150px | Center | Disabled | Multi-line: CheckIn By + CheckOut By + badge |
| Action | ~180px | Center | Disabled | 5 icon buttons/links |

### 5.4 Action Column — Per-Row Controls

| Position | Action Name | Icon | Style | Type | Function/URL |
|---|---|---|---|---|---|
| 1st (leftmost) | Edit | Pencil (ti-pencil) | Info Blue (mini) | Button | showFoRegGuestData(id) |
| 2nd | Re-active Registration | Reload (ti-reload) | Warning Yellow (mini) | Button | reactiveRegistration(id) |
| 3rd | Bill Preview | File (ti-file) | Success Green (mini) | Hyperlink | ..../bill-preview/{id} |
| 4th | Split Bill Preview | Scissors (fa fa-cut) | Info Blue (mini) | Button | openSplitBillModal(id) |
| 5th (rightmost) | Registration card | Receipt (ti-receipt) | Secondary Grey (mini) | Hyperlink | ..../pre-registration-card/{id} |

### 5.5 Status Column — Badge Components

| Badge Text | CSS Class | Colour | Meaning |
|---|---|---|---|
| In-House | badge badge-primary | Blue | Guest is currently checked in and in residence. |
| Checked Out | badge badge-danger | Red | Guest has completed stay and checked out. |

### 5.6 Split Bill Modal

| Component | Detail |
|---|---|
| Modal ID | splitBillModal |
| Modal Title | Guest Bill Split Information |
| Trigger | openSplitBillModal(registration_id, group_id) called from the Split Bill Preview action button. |
| Content | Displays bill split breakdown for the selected registration. |
| Close Button | Standard × icon (top right) and "Close" button at modal footer. |

---

## 6. Business Rules

### 6.1 Search & Filter Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-SR-001 | When multiple filter fields are populated simultaneously, the system SHALL apply all filters as AND conditions — a record must match ALL active filters to appear in results. | Combined filters narrow results; no OR logic between filter fields. |
| BR-SR-002 | When no filter fields are populated and Search is clicked (or the tab first loads), ALL registration records in the system SHALL be returned, paginated. | The default state shows all records; no filter required to view the full list. |
| BR-SR-003 | Text-based filters (Registration No., Guest Name, Phone Number, Reservation Number) SHALL support partial/substring matching (LIKE search) so staff do not need to remember exact full values. | Partial names or partial registration numbers return matching results. |
| BR-SR-004 | The Check-In Date filter SHALL match exact date. Entering a date shows only registrations with that specific check-in date, not a range. | No date range filter available — single-date filter only in current implementation. |
| BR-SR-005 | Dropdown filters (Room Types, Company Name, Country, Reference) SHALL require exact match from the predefined list. Free-text input is not supported for these fields. | Dropdown values must correspond to valid master table entries. |
| BR-SR-006 | The real-time DataTables search box operates on client-side data only — it filters the currently loaded page, not the full database. | For comprehensive search across all records, the server-side filter (Search button) must be used. |

### 6.2 Edit Action Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-SR-007 | The Edit action (showFoRegGuestData) SHALL be available for ALL registration statuses — both In-House and Checked Out registrations can be edited. | No status restriction on editing registrations from Search results. |
| BR-SR-008 | Editing a registration via the Search tab SHALL follow the same role-based access control as the regular registration wizard. Post-check-in changes to sensitive fields (passport, billing) require Supervisor+ role. | RBAC applies consistently whether editing from Search or from the wizard. |
| BR-SR-009 | After editing a registration via Search, changes are saved via the "Update Registration" button (replaces "Check-in" in edit mode). The check-in date/time is NOT changed by an edit operation. | Update Registration only modifies data fields; it does not alter the original check-in timestamp. |

### 6.3 Re-Activate Registration Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-SR-010 | The Re-active Registration action SHALL only be executed with explicit user confirmation (a confirmation prompt must precede the status change). | Prevents accidental re-activation of checked-out registrations. |
| BR-SR-011 | Re-activating a registration changes the room status back to Occupied. The system must verify the room is not already occupied by another active registration before allowing re-activation. | Room availability check is required before re-activation to prevent double-occupancy. |
| BR-SR-012 | Re-activation should only be permitted by users with Supervisor or Duty Manager role, as it directly affects room inventory and billing. | Role-based restriction on re-activation action. |
| BR-SR-013 | Re-activating a registration does not restore the original check-out date — the check-out date may need to be updated manually via the Edit action after re-activation. | Post re-activation, staff should edit the registration to update the check-out date. |

### 6.4 Bill Preview & Split Bill Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-SR-014 | Bill Preview is a read-only view of the guest folio. No charges can be added, modified, or deleted directly from the bill preview page opened via this link. | Billing modifications are made through the Guest Bill Payment or Bill Adjustment modules, not via this action. |
| BR-SR-015 | Split Bill Preview displays how charges are allocated across different folios or guests. It is informational only from the Search tab — split bill configuration is managed in the billing module. | Read-only split bill view; no modification from this modal. |

### 6.5 Registration Card Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-SR-016 | The Registration Card can be generated and printed for any registration at any status (In-House or Checked Out). The card reflects the data as it was at the time of check-in (or as last updated). | Reprinting a registration card for past stays is supported. |
| BR-SR-017 | The Registration Card printout is generated from a server-side template at the /pre-registration-card/{id} endpoint. The output includes all relevant registration data populated at time of generation. | Server-side rendering ensures the most current data is reflected in the card. |

### 6.6 Data Visibility Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-SR-018 | The Search tab displays ALL registrations in the system regardless of which user created them. There is no user-level data isolation — all Front Desk staff can see all registrations. | System-wide visibility; no per-user filtering of records. |
| BR-SR-019 | The total record count (2,291 in demo) grows with each new check-in. The Search tab must perform efficiently even as the record count grows to tens of thousands. | Performance and indexing requirements apply to the search query. |
| BR-SR-020 | The Registration No column is the default sort column, showing the most recently created registrations first (highest registration number first at sort ascending from the bottom, or descending from top). The observed default is ascending. | Pagination starts with the highest/latest registration numbers at top. |

---

## 7. Non-Functional Requirements

### 7.1 Performance

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-SR-001 | The Search tab SHALL load with all registration records and the default results table within an acceptable time on tab activation. | <2 seconds initial load |
| NFR-SR-002 | Executing a server-side filtered search (clicking the Search button) SHALL return results within an acceptable time regardless of the number of active filters. | <3 seconds for queries against 10,000+ records |
| NFR-SR-003 | The real-time DataTables search box SHALL filter visible rows instantly without perceptible delay. | <100ms client-side filtering |
| NFR-SR-004 | Changing the page size (Show Entries) SHALL re-render the table within an acceptable time. | <1 second for page size change |
| NFR-SR-005 | The Edit action (loading a full registration record) SHALL complete within acceptable time. | <2 seconds to load registration data into wizard |
| NFR-SR-006 | Bill Preview and Registration Card generation SHALL complete within acceptable time for complex multi-room registrations. | <3 seconds for bill/card generation |

### 7.2 Usability

| NFR ID | Requirement | Standard |
|---|---|---|
| NFR-SR-007 | All 11 filter fields SHALL be clearly labelled and grouped logically in the filter panel to minimise search time. | Labels visible above or adjacent to each field |
| NFR-SR-008 | The status badges (In-House = blue, Checked Out = red) SHALL use distinct, high-contrast colours to allow staff to immediately identify registration status. | Colour-coded badges per HMS design system |
| NFR-SR-009 | All 5 action buttons SHALL display tooltips (title attribute) on hover to identify their function without requiring icon memorisation. | Tooltip on hover for all action controls |
| NFR-SR-010 | The table pagination controls SHALL remain accessible without scrolling on standard desktop displays (minimum 1024x768). | Pagination visible without scrolling |
| NFR-SR-011 | Staff should be able to find any registration within 30 seconds using the available search filters. | Operational efficiency target |
| NFR-SR-012 | The filter panel layout SHALL be responsive and remain functional on tablet-sized screens (768px+). | Responsive layout via Bootstrap grid |

### 7.3 Security

| NFR ID | Requirement | Notes |
|---|---|---|
| NFR-SR-013 | Only authenticated HMS users with Front Office roles SHALL have access to the Search tab and the registration records displayed. | RBAC at module level |
| NFR-SR-014 | The Re-active Registration action SHALL be restricted to Supervisor and Duty Manager roles. | Prevents unauthorised status changes |
| NFR-SR-015 | The Bill Preview page linked from the Search tab SHALL enforce the same authentication and authorisation requirements as the main billing module. | Consistent security across linked modules |
| NFR-SR-016 | All search queries transmitted to the server SHALL be sanitised against SQL injection and other injection attacks. | Input sanitisation for all 11 filter parameters |
| NFR-SR-017 | Sensitive guest data displayed in the results table (guest name, mobile) SHALL only be visible to authorised roles. Lower-privilege roles may see masked data. | Data masking for privacy compliance |

### 7.4 Reliability & Availability

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-SR-018 | The Search tab SHALL remain fully functional during peak operational periods (check-in/check-out rush hours 12:00-15:00 and 22:00-23:00). | Consistent performance under peak concurrent user load |
| NFR-SR-019 | The DataTables plugin and all action button JavaScript functions SHALL degrade gracefully if a network error occurs — displaying a user-friendly error message rather than silently failing. | Graceful error handling |
| NFR-SR-020 | The split bill modal SHALL handle cases where no split billing exists for the selected registration — displaying a clear "No split bill information available" message rather than an empty or broken modal. | Graceful empty-state handling |

### 7.5 Scalability

| NFR ID | Requirement | Notes |
|---|---|---|
| NFR-SR-021 | The search query and results table SHALL remain performant as the registration record count grows to 100,000+ records. | Database indexing on key filter fields required: registration_no, check_in_date, room_number, guest_name, phone_number |
| NFR-SR-022 | The pagination mechanism SHALL handle up to 10,000 pages without UI degradation (e.g., truncating page number display for very large record sets). | Ellipsis pagination (1, 2, 3 ... N) already implemented |
| NFR-SR-023 | The Reference filter master list SHALL support up to 100 reference options without the dropdown becoming unusable. | Currently 28 options; select2 search recommended for larger lists |

