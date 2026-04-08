# Centaurus Merchant Services CRM - Soon to be openly released

A PHP and Bootstrap based merchant services intake and CRM system for collecting low-rate quote requests, reviewing merchant onboarding data, organizing supporting documents, and preparing approved files for contract generation and underwriting.

## Overview

This system is designed for **Centaurus Merchant Services** as an internal merchant intake CRM and public-facing quote workflow. It is not positioned as a direct instant-approval merchant account signup. Instead, it collects the business, ownership, processing, equipment, and underwriting details needed to review a merchant file, calculate pricing, prepare contract documents, and move qualified submissions into underwriting.

The platform is built around three goals:

1. Give merchants a clean, professional, mobile-friendly quote request experience.
2. Give staff a structured CRM record with all required intake details in one place.
3. Reduce fraud, incomplete submissions, and underwriting delays by requiring supporting documents and identity verification steps up front.

---

## What the system does

### Public application workflow

The public intake side allows a merchant to submit a **Merchant Services Low Rate Quote** request through a structured application flow.

The current implementation includes:

- Business profile collection
- Ownership and contact details
- Processing and payment acceptance details
- EBT / SNAP related intake fields
- Credit card acceptance mix and transaction method details
- Equipment and pricing preference capture
- Secure supporting document uploads
- Client-side and server-side validation
- Flash messaging and required field error handling
- CSRF protection on submission forms
- Internal CRM record creation on submit

### CRM workflow

Once a form is submitted, the data is intended to become a **merchant CRM record** for internal review. The CRM side is used to:

- Review incoming merchant quote submissions
- Check completeness of required fields and uploads
- Review identity and banking support documents
- Compare current processing setup and statement data
- Organize merchant records before contract preparation
- Prepare files for underwriting handoff after contract execution

### Fraud prevention and verification workflow

Because the business receives a high volume of fraudulent or incomplete submissions, this CRM is structured to support stronger verification controls before a file moves forward.

The current and requested workflow includes:

- Driver's license upload
- Voided check upload
- Conditional merchant statement upload if the merchant already processes cards
- Conditional FNS certificate upload if the merchant accepts EBT
- Additional identity verification steps for suspicious or higher-risk applications
- Support for applicant selfie-with-ID review
- Planned Veriff API based identity verification integration

---

## Key business logic

### This is a quote intake, not instant boarding

The system is intentionally written around a **preliminary rate review workflow**.

Merchant submissions are used to:

- gather business and ownership data,
- evaluate the merchant profile,
- estimate pricing,
- determine equipment needs,
- prepare the proposed contract,
- and only then move the signed package into underwriting.

This helps protect the company from pushing incomplete or fraudulent files into the boarding process too early.

### Underwriting flow

High-level workflow:

1. Merchant completes quote intake form.
2. Merchant uploads required supporting documents.
3. CRM stores the submission as an internal merchant record.
4. Staff reviews completeness, fraud flags, and document quality.
5. Rates and terms are prepared.
6. Contract is generated and sent electronically.
7. Once signed, the file and supporting documents are sent to underwriting.

---

## Features

### Merchant intake form

The intake form is broken into logical sections so merchants can complete it more easily and staff can review submissions in a consistent format.

#### 1. Business Information
Includes fields such as:

- business name / DBA
- established date
- number of locations
- business structure
- EIN
- business address
- business phone and email
- business website
- business description
- owner name
- ownership percentage
- whether the merchant currently accepts credit cards
- whether the merchant will accept EBT
- FNS number when applicable

#### 2. Payment acceptance and processing profile
The system is designed to capture how the merchant currently accepts payments and how transactions are structured. This supports pricing review and underwriting preparation.

Examples include:

- whether cards are already being accepted
- merchant statement requirement for existing processors
- average ticket and volume related intake
- payment timing questions
- deposit related questions
- card acceptance mix by method

#### 3. Credit card acceptance mix
The application includes a transaction mix section so staff can better evaluate risk and pricing.

Examples include:

- over-the-phone percentage
- in-person percentage
- transaction type classification such as swiped, keyed, card-not-present, MOTO, dip, tap/contactless, or EMV
- total percentage validation so card mix equals 100%

#### 4. Equipment and onboarding preferences
The system supports merchant equipment and service preference capture, which can be used during pricing and contract preparation.

This may include:

- terminal / POS needs
- Clover equipment interest
- surcharge program interest
- other account setup preferences

#### 5. Supporting document uploads
The current application supports required and conditional uploads.

Required uploads:

- Driver's License
- Voided Check

Conditional uploads:

- FNS Certificate, if EBT is selected
- Merchant Statement, if the business already accepts credit cards

The upload system accepts common formats such as:

- PDF
- JPG
- JPEG
- PNG
- WEBP

---

## Current UX and interface details

The public-facing form is designed with a strong emphasis on clarity and conversion:

- Bootstrap-based responsive layout
- mobile-friendly form sections
- instructional messaging and application notes
- visible fraud and privacy notices
- quick checklist sidebar
- validation feedback on required inputs
- secure submission messaging
- optional instructional video support on the intake page

The landing and quote flow also align with the broader Centaurus Merchant Services positioning, including:

- transparent pricing
- wholesale / interchange-style rate review
- free Clover equipment offers for qualified merchants
- 0% surcharge program messaging
- next-day funding oriented sales positioning
- veteran-owned business branding

---

## Security and compliance posture

This CRM handles sensitive business and personal onboarding data, so the workflow is structured around least-necessary handling and fraud prevention.

### Current security-oriented elements

- CSRF token generation and verification
- required-field validation
- conditional validation rules for uploads and processing questions
- centralized helper functions through shared merchant database / utility code
- secure file upload handling model
- internal-only review flow before underwriting
- privacy notice language that limits disclosure to legitimate processing, fraud prevention, onboarding, compliance, and legal requirements

### Data sensitivity

The system may handle:

- business identity data
- owner identity data
- tax ID / EIN data
- phone and email contact data
- business banking proof
- government ID images
- merchant processing statements
- EBT / FNS documentation

Because of this, deployment should assume:

- HTTPS only
- access controls for admin / CRM users
- secure file storage outside public web access where possible
- strict upload validation
- server hardening
- database backup and retention controls
- audit-friendly admin handling

---

## Veriff API implementation

This repository is being expanded to include **Veriff API based identity verification** as part of the merchant anti-fraud workflow.

### Purpose of the Veriff integration

The Veriff implementation is intended to strengthen identity verification for merchant applicants by adding a formal KYC style verification layer before a file is approved for contract generation or underwriting.

### Planned / included Veriff workflow

The implementation is expected to support the following flow:

1. Create a Veriff verification session from the CRM after a merchant reaches the verification step.
2. Send the applicant through the Veriff verification flow.
3. Capture identity media and verification results.
4. Receive decision data by webhook and/or poll session decision status.
5. Store the verification result in the merchant CRM record.
6. Allow staff to review the verification outcome before moving the file forward.

### Veriff features relevant to this CRM

Based on Veriff's current developer documentation, the integration can be structured around:

- session creation through `POST /v1/sessions`
- redirecting the user to the returned verification URL
- optional media handling and additional data collection
- webhook-based decision notifications
- decision retrieval through the session decision endpoint

### Why Veriff matters here

For a merchant services onboarding pipeline, Veriff can help with:

- identity confirmation
- fraud reduction
- better document-to-person matching
- stronger underwriting readiness
- reduced manual review time on suspicious submissions

### Suggested implementation model

A practical implementation inside this CRM would include:

- a Veriff service class or integration helper in PHP
- environment variables for API credentials and webhook secrets
- a verification status column in the merchant applications table
- admin-visible verification results in the CRM record view
- fallback manual review when Veriff is unavailable or inconclusive
- support for both manual ID upload review and Veriff-assisted verification

### Important note

Veriff's current documentation shows that a standard identity verification flow begins by creating a verification session and receiving a session ID plus a verification URL, with results delivered by webhook and/or session decision retrieval. See the official Veriff developer documentation for the current API behavior before final implementation details are locked in.

---

## Suggested project structure

A practical project structure for this CRM would look something like this:

```text
/
├─ index.php
├─ apply.php
├─ quote.php
├─ affiliate.php
├─ privacy-policy.php
├─ admin/
│  ├─ index.php
│  ├─ applications.php
│  ├─ application-view.php
│  ├─ uploads/
│  └─ notes.php
├─ includes/
│  ├─ merchant_db.php
│  ├─ auth.php
│  ├─ helpers.php
│  ├─ upload_helpers.php
│  └─ veriff_service.php
├─ merchant_intake_submit.php
├─ webhook_veriff.php
├─ assets/
│  ├─ css/
│  ├─ js/
│  ├─ img/
│  └─ video/
└─ uploads/
```

Your actual structure may vary, but the application is already clearly centered around shared merchant helper/database code and page-level PHP templates.

---

## Recommended database design

A practical schema for this CRM would typically include tables such as:

### `merchant_applications`
Stores the main merchant intake record.

Example fields:

- id
- created_at
- updated_at
- status
- hear_about
- business_established
- business_name
- num_locations
- business_structure
- ein
- biz_address1
- biz_address2
- biz_city
- biz_state
- biz_zip
- business_phone
- business_email
- business_owner_name
- ownership_percent
- accept_cc
- accept_ebt
- fns_number
- business_website
- business_description
- verification_status
- underwriting_status
- notes

### `merchant_application_uploads`
Stores uploaded document metadata.

Example fields:

- id
- application_id
- document_type
- file_path
- original_filename
- mime_type
- uploaded_at

### `merchant_application_notes`
Stores internal staff notes and review actions.

### `merchant_application_status_history`
Tracks status changes for audit and workflow visibility.

### `merchant_veriff_sessions`
Stores Veriff session and decision metadata.

Example fields:

- id
- application_id
- veriff_session_id
- vendor_data
- status
- decision
- decision_received_at
- raw_response_json

---

## Admin / CRM functions to support

For internal operations, the CRM should support an admin panel with features such as:

- admin authentication
- application list view
- status filtering
- record detail view
- document preview and download
- manual notes and internal comments
- verification status tracking
- contract-ready status
- underwriting-ready status
- archived / declined / pending states
- visibility into ID upload and selfie-with-ID review
- Veriff result visibility once implemented

---

## Setup notes

### Basic environment

Recommended stack:

- PHP 8+
- MySQL or MariaDB
- Apache or Nginx
- Bootstrap 5
- HTTPS-enabled deployment

### Configuration

Create a configuration pattern for:

- database credentials
- upload paths
- max upload size
- CSRF/session settings
- mail settings for notifications
- Veriff API credentials
- Veriff webhook secret

### Deployment recommendations

- store uploads outside the public document root when possible
- restrict admin access
- disable directory listing
- sanitize all uploads and form inputs
- log verification and underwriting events
- rotate secrets and API credentials

---

## Example end-to-end workflow

```text
Visitor lands on marketing site
    -> opens Apply / Quote page
    -> completes intake form
    -> uploads required documents
    -> submission is validated
    -> merchant CRM record is created
    -> staff reviews submission
    -> identity is manually reviewed and/or Veriff is triggered
    -> rates are calculated
    -> contract is prepared and sent
    -> signed package is forwarded to underwriting
```

---

## Future improvements

Recommended next enhancements:

- Full Veriff session creation and webhook handling
- Admin dashboard with searchable merchant pipeline
- Application status badges and workflow stages
- Email notifications to staff for new submissions
- Merchant-facing confirmation and follow-up status pages
- Contract generation module
- Audit trail for admin changes
- Role-based access controls
- Document image preview inside admin panel
- Duplicate/fraud flagging rules
- IP logging and device fingerprinting where legally appropriate
- Export tools for underwriting packages

---

## Disclaimer

This project is an internal merchant intake and onboarding support CRM. It is intended to help collect and organize merchant information for pricing review, contract preparation, fraud prevention, identity verification, and underwriting preparation. Final merchant account approval remains subject to internal review, contract execution, processor requirements, and underwriting approval.

---

## Credits

Built for **Centaurus Merchant Services**.

Focused on merchant onboarding efficiency, fraud prevention, better quote intake, and scalable identity verification with Veriff support.
