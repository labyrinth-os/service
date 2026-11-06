# service
Short service for short time

Links:
1. https://dev471447.service-now.com/login.do?user_name=admin&sys_action=sysverb_login&user_password=wi6IB-E%25i3vF
2. https://chatgpt.com/share/6aed128d-4eec-83e8-b9d0-5ee3bb8fea12?ogimg=plain

# JEEP
# ServiceNow Lab Practical -- Experiments 1 to 10

> **Quick reference:** This guide follows the supplied practical list
> exactly. It contains every experiment and sub-part from 1 to 10. Menu
> names can vary slightly between ServiceNow releases and roles. Use the
> application navigator's search if a module is not visible.

## Contents

1.  [Table Creation and
    Customization](#1-table-creation-and-customization)
2.  [Table Creation and Extension](#2-table-creation-and-extension)
3.  [Data Import Using Import Sets](#3-data-import-using-import-sets)
4.  [Data Import and Duplicate
    Handling](#4-data-import-and-duplicate-handling)
5.  [UI Policies for Incident
    Management](#5-ui-policies-for-incident-management)
6.  [Conditional Mandatory Fields](#6-conditional-mandatory-fields)
7.  [Data Policy and Child Table
    Configuration](#7-data-policy-and-child-table-configuration)
8.  [Incident Form Buttons](#8-incident-form-buttons)
9.  [Client-Side Form Button](#9-client-side-form-button)
10. [Incident Form Link](#10-incident-form-link)

------------------------------------------------------------------------

## 1. Table Creation and Customization

### 1(a) Create a custom table named Employee Asset

**Aim:** Create a custom table to store employee asset details.

**Navigation and steps** 1. Sign in to your ServiceNow instance. 2. In
the Application Navigator, search **Tables** and open **System
Definition → Tables**. 3. Select **New**. 4. Enter: - **Label:**
Employee Asset - **Name:** `u_employee_asset` (normally generated
automatically) - **Extends table:** leave as None - **Create module:**
select if you want a navigation module 5. Select **Submit**. 6. Open the
new table and use its **Columns** related list to add fields. Suggested
fields:

  Label           Type
  --------------- ---------------------------------
  Employee        Reference → User \[`sys_user`\]
  Asset Name      String
  Asset Tag       String
  Serial Number   String
  Assigned Date   Date

7.  Save each column and verify the table by opening its module or list.

### 1(b) Add a Purchase Date column to Employee Asset

**Navigation and steps** 1. Open **System Definition → Tables**. 2.
Search for and open **Employee Asset**. 3. In **Columns**, select
**New**. 4. Enter: - **Column label:** Purchase Date - **Type:** Date -
**Column name:** allow ServiceNow to generate it (typically
`u_purchase_date`) 5. Select **Submit**. 6. Open a new Employee Asset
record and verify that **Purchase Date** is available.

**Code required:** No. Both parts use table and dictionary
configuration.

------------------------------------------------------------------------

## 2. Table Creation and Extension

### 2(a) Create a custom table named Office Visitor

**Aim:** Create a table to record front-desk visitors.

**Navigation and steps** 1. Open **System Definition → Tables → New**.
2. Enter: - **Label:** Office Visitor - **Name:** `u_office_visitor`
(normally generated) - **Extends table:** None 3. Select **Submit**. 4.
Open the table and add suggested columns:

  Label             Type
  ----------------- ---------------------------------
  Visitor Name      String
  Visitor Company   String
  Contact Number    Phone Number or String
  Email             Email
  Person to Meet    Reference → User \[`sys_user`\]
  Visit Date        Date
  Check-in Time     Date/Time
  Check-out Time    Date/Time

5.  Save the columns and create a test visitor record.

### 2(b) Create Network Incident as a child of Incident

**Aim:** Create a child table that inherits Incident fields and adds
network-specific fields.

**Navigation and steps** 1. Open **System Definition → Tables → New**.
2. Enter: - **Label:** Network Incident - **Name:** `u_network_incident`
(normally generated) - **Extends table:** Incident \[`incident`\] 3.
Select **Submit**. 4. Open Network Incident and add child-specific
columns:

  Label            Type
  ---------------- --------------------------------------
  Network Device   String
  IP Address       String
  Network Type     Choice (for example LAN, WAN, Wi-Fi)
  Port Number      Integer

5.  Open a new Network Incident record. Confirm that inherited Incident
    fields and the new network fields are present.

**Key point:** Incident extends Task. Network Incident extends Incident,
so it inherits Incident fields as well as fields inherited by Incident
from Task.

**Code required:** No.

------------------------------------------------------------------------

## 3. Data Import Using Import Sets

### 3(a) Load an Excel file of users into a staging table

**Aim:** Import user data into an Import Set staging table.

**Prepare `users.xlsx`** with headers such as:

  First Name   Last Name   User Name      Email
  ------------ ----------- -------------- -------------------
  Arun         Kumar       arun.kumar     arun@example.com
  Priya        Sharma      priya.sharma   priya@example.com

**Navigation and steps** 1. Search the navigator for **Load Data**
(usually under **System Import Sets**). 2. Select the Excel file
`users.xlsx`. 3. Choose **Create table** for the import set table. 4.
Set the label to **User Import Staging**. 5. Upload the file and review
the detected sheet and column headers. 6. Select **Load All Records**
(wording may vary). 7. Open the generated Import Set and verify that the
rows are in the staging table.

**Important:** At this point, the records are in the staging table only.
They have not yet been inserted into `sys_user`.

### 3(b) Create a Transform Map to move users into the User table

**Navigation and steps** 1. From the Import Set, open its **Transform
Maps** related list and select **New**. Alternatively, search for
**Transform Maps**. 2. Configure: - **Name:** User Import Transform -
**Source table:** User Import Staging - **Target table:** User
\[`sys_user`\] - **Active:** selected 3. Save the Transform Map. 4. In
its **Field Maps** related list, create these mappings:

  Source field   Target field
  -------------- --------------
  First Name     First name
  Last Name      Last name
  User Name      User ID
  Email          Email

5.  Return to the Import Set and select **Transform**.
6.  Select the correct Transform Map and run it.
7.  Open **User Administration → Users** and verify the imported
    records.

**Code required:** No for straightforward field mapping. Transform Map
scripts are only needed for special transformations.

**Important:** Use test data and ensure the imported usernames are valid
and unique. User records may be subject to instance access controls and
business rules.

------------------------------------------------------------------------

## 4. Data Import and Duplicate Handling

### 4(a) Avoid duplicate users during an import using Coalesce

**Aim:** Update an existing user rather than inserting a duplicate when
the imported user identifier matches.

**Prepare a test file** with a unique identifier such as User ID:

  User ID        First Name   Last Name   Email
  -------------- ------------ ----------- ----------------------
  arun.kumar     Arun         Kumar       arun.new@example.com
  priya.sharma   Priya        Sharma      priya@example.com

**Navigation and steps** 1. Use **System Import Sets → Load Data** to
load the file into a new staging table. 2. Create a Transform Map
with: - **Source table:** the new staging table - **Target table:** User
\[`sys_user`\] 3. Create field maps for User ID, First name, Last name,
and Email. 4. Open the **User ID → User ID** field map. 5. Select
**Coalesce** and save the field map. 6. Run the Transform Map from the
Import Set. 7. Verify that a matching User ID updates the existing user,
while a non-matching User ID inserts a new user.

**Key point:** Coalesce matches records in the target table. Choose a
stable, unique identifier such as User ID; do not use a non-unique field
such as First Name.

### 4(b) Import computers from CSV into the Computer table

**Prepare `computers.csv`:**

``` csv
Name,Serial Number,Manufacturer,Model ID
Dell-Laptop-01,DL001,Dell,Latitude 5420
HP-Desktop-01,HP001,HP,ProDesk 400
Lenovo-Laptop-01,LN001,Lenovo,ThinkPad E14
```

**Navigation and steps** 1. Search **Load Data** and upload
`computers.csv`. 2. Choose **Create table** and name the staging table
**Computer Import Staging**. 3. Load all records and verify the staging
rows. 4. Create a Transform Map: - **Source table:** Computer Import
Staging - **Target table:** Computer \[`cmdb_ci_computer`\] 5. Map the
source fields to the corresponding target fields: - Name → Name - Serial
Number → Serial number - Manufacturer → Manufacturer - Model ID → Model
ID 6. For reference fields such as Manufacturer and Model ID, ensure the
referenced manufacturer/model records exist and configure reference
matching appropriately. Do not assume that plain text will automatically
resolve every reference field. 7. Optionally enable **Coalesce** on
Serial Number if it is reliably unique. 8. Run the Transform Map and
verify the records in the Computers list (search the navigator for
**Computers**).

**Code required:** No for standard imports.

------------------------------------------------------------------------

## 5. UI Policies for Incident Management

### 5(a) Make Close Notes read-only when Incident State is Closed

**Aim:** Make the Close Notes field read-only when the Incident State
becomes Closed.

**Navigation and steps** 1. Search for **UI Policies** (usually **System
UI → UI Policies**) and select **New**. 2. Configure: - **Short
description:** Make Close Notes Read Only When Closed - **Table:**
Incident \[`incident`\] - **Active:** selected - **On load:** selected -
**Reverse if false:** selected 3. Set the condition: **Incident State is
Closed**. 4. Save the UI Policy. 5. In **UI Policy Actions**, select
**New**. 6. Set: - **Field name:** Close notes - **Read-only:** True -
Leave Mandatory and Visible unchanged. 7. Save the action. 8. Test on an
Incident form: when State is Closed, Close Notes should be read-only;
when the condition becomes false, it should return to its prior editable
behavior.

**Code required:** No.

**Note:** A UI Policy controls the form interface. It is not a
substitute for server-side enforcement against API or other non-form
updates.

------------------------------------------------------------------------

## 6. Conditional Mandatory Fields

### 6(a) Make Assignment Group mandatory when Priority is 1 - Critical, and optional again when Priority changes

**Aim:** Dynamically make Assignment Group mandatory only for Critical
priority.

**Navigation and steps** 1. Open **System UI → UI Policies → New**. 2.
Configure: - **Short description:** Assignment Group Mandatory for
Critical Priority - **Table:** Incident \[`incident`\] - **Active:**
selected - **On load:** selected - **Reverse if false:** selected 3. Set
the condition: **Priority is 1 - Critical**. 4. Save the UI Policy. 5.
Under **UI Policy Actions**, select **New**. 6. Set: - **Field name:**
Assignment group - **Mandatory:** True - Leave Read-only and Visible
unchanged. 7. Save and test: - Priority 1 - Critical → Assignment Group
mandatory. - Any other priority → the UI Policy action is reversed and
Assignment Group is no longer mandatory.

**Code required:** No.

### 6(b) Make Assignment Group mandatory only on the Hardware Incident child table

**Aim:** Make Assignment Group mandatory on Hardware Incident without
applying this UI Policy to the parent Incident form.

**Prerequisite:** A Hardware Incident child table must exist and extend
Incident \[`incident`\]. If it does not exist, create it through
**System Definition → Tables → New**, selecting Incident as the parent
table.

**Navigation and steps** 1. Search **UI Policies** and select **New**.
2. Set: - **Short description:** Assignment Group Mandatory on Hardware
Incident - **Table:** Hardware Incident (select the actual child
table) - **Active:** selected - **On load:** selected 3. Leave the
condition empty so the policy applies to all records on that table. 4.
Save the UI Policy. 5. In **UI Policy Actions**, select **New**. 6. Set
**Field name** to Assignment group and **Mandatory** to True. 7. Save
and test a Hardware Incident form. Confirm the field is mandatory there.
Check a normal Incident form to confirm this child-table policy has not
been configured on the parent.

**Code required:** No.

------------------------------------------------------------------------

## 7. Data Policy and Child Table Configuration

### 7(a) Make Short Description mandatory for imports and web services using a Data Policy

**Aim:** Enforce Short Description as mandatory beyond ordinary form
entry.

**Navigation and steps** 1. Search for **Data Policies** (usually
**System Policy → Rules → Data Policies**) and select **New**. 2.
Configure: - **Short description:** Make Short Description Mandatory -
**Table:** Incident \[`incident`\] - **Active:** selected - **Apply to
import sets:** selected - **Apply to SOAP:** selected, if SOAP
web-service enforcement is required - **Use as UI Policy on client:**
select if the same rule should also be reflected on the form 3. Save the
Data Policy. 4. In **Data Policy Rules**, select **New**. 5. Set: -
**Field name:** Short description - **Mandatory:** True - **Read only:**
leave unchanged 6. Save the rule. 7. Test using an Incident form and an
Import Set. Inspect import/transform logs for rejected or failed rows
when Short Description is empty.

**Important:** "Apply to SOAP" applies to SOAP web services; it does not
mean all REST integrations are automatically covered. Data Policy
enforcement depends on the data-entry path and configuration. Validate
the actual integration path used by your instance.

### 7(b) Set default Category to Network on Network Incident

**Aim:** Set the inherited Category field's default value to Network
only for Network Incident records.

**Navigation and steps** 1. Open **System Definition → Tables** and open
**Network Incident**. 2. In **Columns**, locate the inherited
**Category** field and open its dictionary entry. Do not create a second
Category field. 3. In **Dictionary Overrides**, select **New**. 4. Set
**Table** to Network Incident. 5. Select **Override default value**. 6.
Set **Default value** to the internal choice value for Network (commonly
`network` in the standard Incident category choices; verify the choice
value in your instance). 7. Save and create a new Network Incident to
verify the default.

### 7(c) Make Priority read-only only on Network Incident

**Aim:** Make the inherited Priority field read-only on Network Incident
without changing the parent Incident dictionary behavior.

**Navigation and steps** 1. Open **System Definition → Tables → Network
Incident**. 2. In **Columns**, open the inherited **Priority**
dictionary entry. 3. In **Dictionary Overrides**, select **New**. 4. Set
**Table** to Network Incident. 5. Select **Override read only** and set
**Read only** to true. 6. Save. 7. Open a Network Incident and verify
Priority is read-only. Check a normal Incident form separately.

**Note:** This is a form-level field configuration. It does not, by
itself, prevent updates through every server-side integration or script.

### 7(d) Set default Impact to 1 - High on Hardware Incident

**Aim:** Set the inherited Impact field's default value to High only for
Hardware Incident.

**Navigation and steps** 1. Open **System Definition → Tables → Hardware
Incident**. 2. In **Columns**, open the inherited **Impact** dictionary
entry. Do not create another Impact field. 3. In **Dictionary
Overrides**, select **New**. 4. Set **Table** to Hardware Incident. 5.
Select **Override default value**. 6. Set the default to the internal
value for **1 - High** (commonly `1`; verify the choice value in your
instance). 7. Save and create a new Hardware Incident to verify the
default.

**Note:** A default value affects new records; it does not automatically
change existing records.

------------------------------------------------------------------------

## 8. Incident Form Buttons

### 8(a) Create a Quick Resolve button that resolves the Incident and adds a resolution note

**Aim:** Add a server-side UI Action button that resolves an Incident
and populates Close Notes.

**Navigation and configuration** 1. Search **UI Actions** (usually
**System Definition → UI Actions**) and select **New**. 2. Configure: -
**Name:** Quick Resolve - **Table:** Incident \[`incident`\] - **Action
name:** `quick_resolve` - **Active:** selected - **Form button:**
selected - **Client:** not selected (server-side action) 3. In
**Script**, use:

``` javascript
current.state = 6; // Standard Incident state: Resolved
current.close_notes = 'Incident resolved using Quick Resolve button.';

// Some instances require a resolution code before resolving.
// If required, set the appropriate valid choice value for your instance:
// current.close_code = '<valid_resolution_code>';

current.update();
action.setRedirectURL(current);
```

4.  Select **Submit**.
5.  Open an active Incident and click **Quick Resolve**.
6.  Verify State is Resolved and Close Notes contains the message.

**Important:** Standard Incident state value `6` commonly represents
Resolved, but verify choice values in your instance. Some configurations
require a valid Resolution Code and/or other resolution fields. Use a
valid configured choice value if required.

### 8(b) Create a Reopen Incident button for a resolved Incident

**Aim:** Add a server-side UI Action button that reopens a resolved
Incident.

**Navigation and configuration** 1. Open **System Definition → UI
Actions → New**. 2. Configure: - **Name:** Reopen Incident - **Table:**
Incident \[`incident`\] - **Action name:** `reopen_incident` -
**Active:** selected - **Form button:** selected - **Client:** not
selected - **Condition:** `current.state == 6` (show only when Resolved,
using the standard state value) 3. In **Script**, use:

``` javascript
current.state = 2; // Standard Incident state: In Progress
current.update();
action.setRedirectURL(current);
```

4.  Select **Submit**.
5.  Open a resolved Incident and click **Reopen Incident**.
6.  Verify that the state changes to In Progress.

**Note:** Confirm the correct state choice values and any mandatory
fields or business rules in your instance.

------------------------------------------------------------------------

## 9. Client-Side Form Button

### 9(a) Create a client-side button that reminds users to add Work Notes

**Aim:** Create a client-side UI Action on the Incident form that
displays a reminder and focuses the Work Notes field.

**Navigation and configuration** 1. Open **System Definition → UI
Actions → New**. 2. Configure: - **Name:** Work Notes Reminder -
**Table:** Incident \[`incident`\] - **Action name:**
`work_notes_reminder` - **Active:** selected - **Form button:**
selected - **Client:** selected - **Onclick:**
`showWorkNotesReminder();` 3. In the **Script** field, enter:

``` javascript
function showWorkNotesReminder() {
    alert('Reminder: Please add Work Notes before updating the incident.');
    g_form.setFocus('work_notes');
}
```

4.  Select **Submit**.
5.  Open an Incident form and click **Work Notes Reminder**.
6.  Verify that the alert appears and focus moves to the Work Notes
    field.

**Key point:** `g_form` is the client-side form API. `work_notes` is the
field's internal name. This action displays a reminder; it does not
itself save Work Notes.

------------------------------------------------------------------------

## 10. Incident Form Link

### 10(a) Add an Escalate Priority link that sets Priority to Critical

**Aim:** Add a Form Link UI Action that escalates an Incident to
Priority 1 - Critical.

**Navigation and configuration** 1. Open **System Definition → UI
Actions → New**. 2. Configure: - **Name:** Escalate Priority -
**Table:** Incident \[`incident`\] - **Action name:**
`escalate_priority


## 8. UI Action
a)
```javascript
// Quick Resolve Incident

current.state = 6;
current.close_notes = 'Incident resolved using Quick Resolve button.';

current.update();

action.setRedirectURL(current);
```

b)
```javascript
// Reopen Incident

current.state = 2;

current.update();

action.setRedirectURL(current);

// specify condition current.state = 6
```
## 9. Client side UI policy
```javascript
function showWorkNotesReminder() {
    alert("Reminder: Please add Work Notes before updating the incident.");

    g_form.setFocus('work_notes');
}
```

## 10.
```javascript
// Escalate Incident Priority

current.impact = 1;
current.urgency = 1;

current.update();

action.setRedirectURL(current);
```

