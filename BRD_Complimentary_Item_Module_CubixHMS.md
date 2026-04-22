# Business Requirements Document

## Complimentary Item Module

**Room Registration — Guest Details Tab**

- **System:** Cubix HMS (Hotel Management System)
- **Version:** 1.0
- **Date:** April 15, 2026
- **Prepared For:** Cubix HMS Project Team
- **Module:** Front Office > Room Registration > Complimentary Item Tab

---

## 1. Executive Summary

This Business Requirements Document (BRD) defines the functional and non-functional requirements for the **Complimentary Item** module within the Room Registration workflow of the Cubix Hotel Management System (HMS). The Complimentary Item tab is the third step in the Room Registration wizard, accessible under the Front Office module.

The Complimentary Item module enables Front Desk staff and Reservation Officers to assign a curated set of hotel-provided complimentary services to a guest at the time of registration. These services — ranging from in-room amenities and dining inclusions to recreational access and transportation arrangements — are pre-configured and displayed as a multi-select checklist. Staff can select any combination of services, with the option to select all at once via a master toggle.

The module is designed to enhance guest experience by ensuring that all complimentary entitlements agreed upon at booking or offered by hotel policy are accurately recorded and communicated to the relevant service departments. Selections made in this tab are persisted as part of the overall guest registration record and are visible on the Registration Card printed at check-in.

| Attribute | Details |
|---|---|
| System | Cubix HMS v13.1.0 |
| Module | Front Office |
| Sub-Module | Room Registration |
| Tab Name | Complimentary Item |
| Tab Position | 3rd tab (after Registration and Guest Details) |
| Primary Users | Front Desk Staff, Reservation Officers, Duty Managers |
| Document Version | 1.0 |
| Prepared Date | April 15, 2026 |
| Status | Draft for Review |

---

## 2. Module Context & Positioning

### 2. Dependencies

The Complimentary Item tab has the following technical and functional dependencies:

### 2.1 Upstream Dependencies

- **Room Registration Tab (Tab 1):** Room type and room number selection must be completed before the registration record can be saved. The complimentary services recorded in this tab are linked to a specific room-stay record.
- **Guest Details Tab (Tab 2):** At least one primary guest must be added to the registration. Services are associated with the guest registration record which requires a guest identity.
- **Room Reservation (Optional):** If the registration originates from a prior reservation, that reservation record may pre-populate certain fields such as check-in/out dates and room assignment. Pre-configured complimentary services from the reservation may be reflected in this tab.
- **Complimentary Guest field (Others Information Tab):** The Others Information tab contains a "Complimentary Guest" designation field. This field and the Complimentary Item tab together determine the full complimentary entitlement picture of the guest.
- **Room Rate & Rate Code (Registration Tab):** The "Complimentary" checkbox flag on the Registration tab (visible next to "No Post" and "House Use") signals that the room itself is complimentary. This flag may influence which services appear or are pre-selected in the Complimentary Item tab.

### 2.3.2 Downstream Dependencies

- **Check-in Process:** The Check-in button is available from any tab in the wizard. The complimentary items selected here are saved as part of the check-in record.
- **Blank Registration Card:** The registration card document (printout) references complimentary services, so this tab must be completed before printing for an accurate card.
- **Guest Bill / Folio:** Services recorded as complimentary must be excluded from billing or posted with zero value. The selection in this tab informs the billing engine.
- **Housekeeping / Service Departments:** Operational teams may receive a list of complimentary services to be set up or prepared in the room prior to arrival.
- **Night Audit:** Complimentary service entitlements may be audited nightly to ensure correct posting or non-posting of charges.

### 2.3.3 Data Dependencies

- **Master Service List:** The list of complimentary service items displayed in the checklist is sourced from a master configuration table (likely maintained by the admin/system setup module).
- **Guest Registration Record:** Selected services are stored against the room registration/check-in record ID.

---

## 3. Functional Requirements

This section details all functional requirements for the Complimentary Item tab, covering user interactions, system behaviours, data handling, and process flows.

### 3.1 Tab Navigation & Access

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-CI-001 | The system SHALL display the "Complimentary Item" tab as the 3rd tab in the Room Registration wizard navigation bar. | Must Have |
| FR-CI-002 | The tab SHALL be accessible by clicking the "Complimentary Item" tab link or by clicking the "Next" button from the Guest Details tab. | Must Have |
| FR-CI-003 | Clicking the "Previous" button on the Complimentary Item tab SHALL navigate the user back to the Guest Details tab without losing any entered data. | Must Have |
| FR-CI-004 | Clicking the "Next" button on the Complimentary Item tab SHALL navigate the user to the Others Information tab, saving any selections made. | Must Have |
| FR-CI-005 | The tab SHALL be accessible directly by clicking the tab header from any other tab in the wizard without mandatory sequential navigation. | Must Have |
| FR-CI-006 | The "Check-in" button SHALL remain visible and accessible from the Complimentary Item tab, allowing staff to finalise registration directly from this step. | Must Have |

### 3.2 Complimentary Services Display

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-CI-007 | The system SHALL display all available complimentary services as individual checkbox tiles arranged in a responsive multi-column grid layout (4 columns on desktop). | Must Have |
| FR-CI-008 | Each service tile SHALL contain a checkbox control and a text label describing the service. | Must Have |
| FR-CI-009 | A selected (checked) tile SHALL be visually distinguished from an unselected tile using a filled/highlighted background colour (dark navy/teal) vs. an outlined/white background. | Must Have |
| FR-CI-010 | The instruction text "Select the services included with your reservation" SHALL be displayed above the service tiles as a guiding prompt. | Must Have |
| FR-CI-011 | The complete list of 29 complimentary service items SHALL be displayed as defined in the master services configuration. See Section 5 for full list. | Must Have |

### 3.3 Select All Functionality

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-CI-012 | A "Select All" master checkbox SHALL be provided in the top-right corner of the Complimentary Item section header. | Must Have |
| FR-CI-013 | Clicking the "Select All" checkbox when all items are unchecked SHALL select all 29 service checkboxes simultaneously. | Must Have |
| FR-CI-014 | Clicking the "Select All" checkbox when all or some items are selected SHALL deselect all 29 service checkboxes simultaneously (toggle/deselect-all behaviour). | Must Have |
| FR-CI-015 | If a user manually selects all individual checkboxes, the "Select All" master checkbox SHALL automatically become checked/activated to reflect the all-selected state. | Should Have |
| FR-CI-016 | If a user deselects at least one individual checkbox after a "Select All" action, the "Select All" master checkbox SHALL automatically become unchecked. | Should Have |

### 3.4 Individual Service Selection

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-CI-017 | The user SHALL be able to select or deselect each complimentary service item independently by clicking on the checkbox or the tile area. | Must Have |
| FR-CI-018 | Multiple services MAY be selected simultaneously (multi-select support). | Must Have |
| FR-CI-019 | There SHALL be no minimum or maximum restriction on the number of services that can be selected (zero to all). | Must Have |
| FR-CI-020 | The system SHALL visually indicate the checked state of each tile immediately upon interaction without requiring a page reload. | Must Have |

### 3.5 Pre-Population from Reservation/Rate Code

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-CI-021 | If the registration is linked to a reservation that includes predefined complimentary services, the system SHALL pre-select the corresponding checkboxes when the tab is first loaded. | Must Have |
| FR-CI-022 | If the room rate code includes package components that map to complimentary services, the system SHALL pre-check those services automatically. | Should Have |
| FR-CI-023 | Staff SHALL be able to override pre-populated selections by manually checking or unchecking any tile, including those pre-populated by the system. | Must Have |

### 3.6 Data Persistence & Save Actions

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-CI-024 | Selections made on the Complimentary Item tab SHALL be saved when the user clicks "Next" to proceed to the next tab. | Must Have |
| FR-CI-025 | Selections made on the Complimentary Item tab SHALL be saved when the user clicks "Check-in" to finalise the registration. | Must Have |
| FR-CI-026 | If the user navigates away from the Complimentary Item tab using the tab header (not Previous/Next), the selections made SHALL be retained in the session until the form is submitted or cleared. | Must Have |
| FR-CI-027 | If the form is cancelled or the session expires, no partial data from the Complimentary Item tab SHALL be persisted to the database. | Must Have |
| FR-CI-028 | On re-opening an existing checked-in registration in edit mode, the previously saved complimentary selections SHALL be restored and displayed with their correct checked state. | Must Have |

### 3.7 Check-in Action from Complimentary Item Tab

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-CI-029 | The "Check-in" button SHALL be visible at the bottom of the page while the Complimentary Item tab is active. | Must Have |
| FR-CI-030 | Clicking "Check-in" SHALL validate that all mandatory fields across the Registration and Guest Details tabs are complete before proceeding. | Must Have |
| FR-CI-031 | Upon successful validation, clicking "Check-in" SHALL commit all data (including complimentary item selections) to the database and mark the guest as checked in. | Must Have |
| FR-CI-032 | If mandatory fields on prior tabs are incomplete, the system SHALL display a validation error message and prevent check-in. | Must Have |
| FR-CI-033 | After successful check-in, the system SHALL redirect to the appropriate confirmation or post-check-in screen and/or display a success notification. | Must Have |

### 3.8 Update Registration (Edit Mode)

| Req. ID | Requirement Description | Priority |
|---|---|---|
| FR-CI-034 | When a registration is opened for editing after check-in, an "Update Registration" button SHALL replace the "Check-in" button. | Must Have |
| FR-CI-035 | Staff SHALL be able to modify complimentary item selections in edit mode and save changes via "Update Registration". | Must Have |
| FR-CI-036 | The system SHALL log or audit changes made to complimentary item selections post-check-in for accountability purposes. | Should Have |

---

## 5. UI Components Summary

This section documents every UI component present on the Complimentary Item tab, including its type, purpose, behaviour, and state management.

### 5.1 Page-Level Components

| Component | Type | Location | Description & Behaviour |
|---|---|---|---|
| Tab Navigation Bar | Tab Strip | Top of page | Contains tabs: Registration, Guest Details, Complimentary Item, Others Information, Search, Blank Registration Card. Clicking any tab navigates to that section. Active tab is visually highlighted. |
| Complimentary Item Header | Section Header | Top of tab content area | Dark background banner with white text "Complimentary Item" identifying the current tab. Contains the "Select All" control at the right end. |
| Instruction Text | Static Text Label | Below header, above service grid | Displays the instructional text: "Select the services included with your reservation." Grey italic text to guide staff. |
| Service Tile Grid | Responsive Grid Container | Main content area | A 4-column responsive grid of 29 service tiles. Each tile has equal width and height. Tiles reflow to fewer columns on smaller screens. |
| Previous Button | Navigation Button (Yellow/Orange) | Bottom right of tab content | Navigates back to the Guest Details tab. Retains all current selections. Style: btn_modern_design warning_new_button. |
| Next Button | Navigation Button (Blue) | Bottom right of tab content | Saves current selections and navigates forward to the Others Information tab. Style: btn_modern_design primary_new_button. |
| Check-in Button | Action Button (Green) | Bottom center of page | Submits the entire registration form and triggers the check-in process. Visible on all tabs. Style: btn_modern_design success_new_button. Icon: calendar/briefcase icon. |
| Update Registration Button | Action Button (Yellow) | Bottom center of page (edit mode only) | Visible when editing an existing check-in registration. Replaces the Check-in button. Allows saving modifications to an active guest registration. Class: d-none by default (hidden unless in edit mode). |

### 5.2 Individual Service Tile Component

| Element | Type | States | Behaviour |
|---|---|---|---|
| Tile Container | Div / Card | Selected (dark bg), Unselected (white bg) | Clicking anywhere on the tile toggles its selected state. Visual feedback is immediate. |
| Checkbox Input | HTML Checkbox (input[type="checkbox"]) | Checked / Unchecked | Bound to the tile state. Selecting the checkbox marks the tile as selected and vice versa. |
| Service Label Text | Label / Text Node | Static text | Displays the service name. Clicking the label also toggles the checkbox due to label-input binding. |
| Selected Visual State | CSS Class Toggle | Active when checked | Tile background changes to dark navy/teal. Text may change to white. Checkbox shows a checkmark. |
| Unselected Visual State | CSS Class Toggle | Active when unchecked | Tile has white/light background with a border. Checkbox is empty. |

### 5.3 Select All Component

| Element | Type | Position | Behaviour |
|---|---|---|---|
| Select All Label | Text Label | Top-right of Complimentary Item section | Label text: "Select All". Paired with the master checkbox control. |
| Select All Checkbox | Master Checkbox (input[type="checkbox"]) | Immediately left of "Select All" label | When clicked: (1) If unchecked or indeterminate — selects ALL 29 service checkboxes. (2) If checked — deselects ALL 29 service checkboxes. Auto-updates based on individual selection state. |

### 5.4 System-Level UI Elements (Shared Across Tabs)

| Component | Type | Description |
|---|---|---|
| Top Navigation Bar | Fixed Header | Displays application name (Cubix HMS), application date (e.g., 05-03-2026), user profile icon, and notification bell. Present on all pages. |
| Live Clock | Dynamic Clock Display | Displays the current time (HH:MM:SS) in the left sidebar. Updates in real-time. |
| Left Sidebar Navigation | Collapsible Menu | Main module navigation. Includes Front Office sub-items. Active item "Room Registration" is highlighted. |
| Search Bar (Sidebar) | Text Input | Allows searching for modules/features within the HMS sidebar navigation. |
| Blank Registration Card Button | Link Button (Blue) | Navigates to the blank registration card printout template. Available from all tabs. |
| Page Footer | Static Footer | Displays copyright, version info (Cubix HMS V-13.1.0), and location (Dhaka, Bangladesh). |

---

## 6. Business Rules

Business rules govern the logic, constraints, and policies that the Complimentary Item module must enforce to ensure operational accuracy and compliance with hotel policies.

### 6.1 Selection Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-CI-001 | No minimum number of services is required to be selected. A registration may have zero complimentary items. | Zero selection is valid; the tab does not block navigation or check-in if no items are selected. |
| BR-CI-002 | All services are available for selection regardless of room type, rate code, or guest type, unless system-level configuration restricts specific services to specific room categories. | All 29 services are always visible and selectable unless the admin configures category-level restrictions. |
| BR-CI-003 | There is no maximum limit on the number of complimentary services that can be selected (up to all 29). | Select All is a valid action and yields a fully loaded complimentary package. |
| BR-CI-004 | Duplicate services cannot be assigned to the same registration (each service can only be selected once per stay). | The checkbox model inherently prevents duplication; each service appears once. |

### 6.2 Complimentary Room Flag Integration

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-CI-005 | If the "Complimentary" checkbox is flagged on the Registration tab (indicating the room rate is zero/complimentary), then the overall registration is marked as complimentary. This is separate from — but related to — the service-level complimentary items. | Complimentary Item services can still be added even when the room is not flagged as complimentary, and vice versa. |
| BR-CI-006 | The "Complimentary Guest" field on the Others Information tab designates the guest as a complimentary guest at the guest-type level. When set, all financial postings for this guest may be suppressed (No Post behaviour). | This field and the Complimentary Item selections together define the full scope of what is provided without charge. |
| BR-CI-007 | Services selected in the Complimentary Item tab SHALL NOT generate chargeable posting transactions to the guest folio. They must be either zero-rated or internally tracked as cost items. | The billing engine must treat selected complimentary items as non-chargeable for the associated registration. |

### 6.3 Data Integrity Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-CI-008 | Complimentary item selections are scoped per registration record. The same guest having multiple registrations (e.g., multiple stays) has independent service selections per stay. | Registration ID is the key link in the junction table. |
| BR-CI-009 | If a service is deactivated in the master list after a registration has been completed, the historical registration data must retain the previously selected service name and status. | Soft delete or archival approach required; historical records must not be broken by admin configuration changes. |
| BR-CI-010 | All changes to complimentary item selections after check-in must be performed by authorised staff (roles: Front Desk Supervisor, Duty Manager, Admin). Regular front desk agents may view but not modify post-check-in selections. | Role-based access control (RBAC) applies to the edit mode of this tab. |

### 6.4 Workflow & Process Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-CI-011 | The Complimentary Item tab selections must be saved to the database at the time of Check-in. They cannot be submitted independently in isolation (they form part of the unified registration record). | No separate save button on this tab; save is triggered by Check-in or Update Registration. |
| BR-CI-012 | If the registration is linked to a corporate/group account, the complimentary services agreed in the group contract should be pre-applied as per the rate code definition. | Rate code and group contract logic must drive auto-population. |
| BR-CI-013 | The Previous and Next navigation buttons on this tab SHALL NOT submit data to the database; they merely navigate within the wizard while holding form data in the browser session. | Actual database write occurs only on Check-in or Update Registration. |
| BR-CI-014 | Complimentary services involving external departments (e.g., Airport Pickup, Personal Butler) may trigger internal service requests or notifications to the relevant department upon check-in confirmation. | Integration with service request/workflow module may be required. |

### 6.5 Audit & Accountability Rules

| Rule ID | Business Rule | Implication |
|---|---|---|
| BR-CI-015 | All selections and modifications to complimentary items must be associated with a user ID (the staff member who made the selection/change). | selected_by and selected_at fields are mandatory in the junction table. |
| BR-CI-016 | Any modification to complimentary item selections after check-in must be logged with the previous state, the new state, the user who made the change, and a timestamp. | Audit trail / changelog table required for post-check-in modifications. |

---

## 7. Non-Functional Requirements

### 7.1 Performance Requirements

| NFR ID | Requirement | Target Metric |
|---|---|---|
| NFR-CI-001 | The Complimentary Item tab SHALL load all 29 service tiles within an acceptable response time from the point of tab activation. | < 2 seconds on standard network connection |
| NFR-CI-002 | Clicking a service tile to select/deselect SHALL provide instant visual feedback. | < 100ms (client-side DOM update) |
| NFR-CI-003 | The "Select All" operation SHALL select/deselect all 29 tiles simultaneously without perceptible lag. | < 200ms |
| NFR-CI-004 | The Check-in action (form submission including complimentary items) SHALL complete within an acceptable server-side processing time. | < 3 seconds under normal load |
| NFR-CI-005 | The system SHALL support concurrent registrations by multiple front desk agents without data collision. | Support 10+ concurrent users without degradation |

### 7.2 Usability Requirements

| NFR ID | Requirement | Standard |
|---|---|---|
| NFR-CI-006 | The service tile grid SHALL use a clear visual distinction between selected and unselected states (colour, border, checkmark). | WCAG 2.1 colour contrast guidelines |
| NFR-CI-007 | Service labels SHALL be fully readable without truncation on all standard desktop screen resolutions. | Minimum 1024x768 desktop resolution |
| NFR-CI-008 | The "Select All" control SHALL be prominently placed and clearly labelled to avoid ambiguity. | Usability heuristics — visibility of controls |
| NFR-CI-009 | Tab navigation (Previous/Next) buttons SHALL be clearly visible and appropriately coloured (yellow for Previous, blue for Next) to distinguish their functions. | Consistent with HMS design system |
| NFR-CI-010 | Staff with basic computer literacy (no advanced training) SHALL be able to complete complimentary item selection within 2 minutes for a standard registration. | Usability target for front desk efficiency |

### 7.3 Security Requirements

| NFR ID | Requirement | Notes |
|---|---|---|
| NFR-CI-011 | Only authenticated users with the appropriate HMS role SHALL be able to access the Room Registration module and the Complimentary Item tab. | Role-based access control enforced at the module level. |
| NFR-CI-012 | Post-check-in modification of complimentary items SHALL require elevated privileges (Supervisor/Manager role). | Prevents unauthorised changes after guest check-in. |
| NFR-CI-013 | All form data submitted via the Check-in action SHALL be transmitted over HTTPS to prevent interception. | TLS encryption required for all HMS communications. |
| NFR-CI-014 | The system SHALL prevent cross-site scripting (XSS) by sanitising all data rendered on the Complimentary Item tab. | Input/output sanitisation required. |

### 7.4 Reliability & Availability

| NFR ID | Requirement | Target |
|---|---|---|
| NFR-CI-015 | The HMS application (including the Complimentary Item module) SHALL be available during peak hotel operations hours. | 99.9% uptime during 6:00 AM – 11:00 PM hotel operations window |
| NFR-CI-016 | In the event of a session timeout while the Complimentary Item tab is active, the system SHALL prompt the user to re-authenticate without losing unsaved selections (via session storage). | Graceful timeout handling |
| NFR-CI-017 | Form data entered in the wizard SHALL be preserved in the browser session to prevent data loss from accidental tab switching or brief network interruptions. | Session-level state persistence |

### 7.5 Scalability & Maintainability

| NFR ID | Requirement | Notes |
|---|---|---|
| NFR-CI-018 | The master list of complimentary services SHALL be configurable by hotel administrators without requiring a code deployment. | Admin panel configuration required. |
| NFR-CI-019 | New complimentary services can be added to the master list and will automatically appear in the Complimentary Item tab checklist. | Dynamic rendering from database; no hardcoding. |
| NFR-CI-020 | Services can be deactivated (hidden) from the checklist by toggling the is_active flag in the master configuration table. | Soft-delete approach preserves historical data. |
| NFR-CI-021 | The tile grid layout SHALL gracefully accommodate up to 50 service items without requiring a page redesign. | Scalable grid CSS required. |
