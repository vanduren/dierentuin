# Requirements — Online Zoo Ticketing System

> Consolidated from the documents in `documents/`:
> - `README.md` — initial idea list (ticket types, QR codes, admin, extras)
> - `start idee joan.txt` — assignment context and open questions
> - `Emma-Ticketgeneratiesysteem.pdf` — ticket generation approach and tech stack
> - `Arshia ProjectCasus.pdf` — UX / service design case study (3-phase flow, error recovery)

## 1. Background & Goals

Twenty zoos in the Netherlands are affiliated in one organization. The organization wants a ticket system in which customers can buy zoo tickets online. The system must cover the complete journey: online booking, payment, ticket delivery, and physical gate entry.

Primary goals:

- Let visitors buy tickets online **without creating an account**.
- Reduce digital cart abandonment during checkout.
- Eliminate physical bottlenecks at the zoo entrance.

## 2. Roles

| Role | Description |
|---|---|
| Visitor | Browses options, books and pays for tickets, receives QR tickets, enters the zoo |
| Gate staff | Scans QR codes and validates entry at the entrance |
| Administrator | Manages prices, opening hours, bookings and capacity |

## 3. Functional Requirements

### 3.1 Ticket types & pricing

- **FR-1** The system supports different ticket types: adult, child, and free tickets for babies.
- **FR-2** A clear price overview is shown before payment.
- **FR-3** *(Optional)* Family discount or discount codes.

### 3.2 Phase 1 — Plan & book online

- **FR-4** Visitors select ticket type(s) and quantity.
- **FR-5** Visitors select a visit date from a calendar.
  - **FR-5a** Tickets are only valid on the selected date.
  - **FR-5b** Days on which the zoo is closed or that are sold out cannot be booked.
- **FR-6** *(Optional)* Visitors can select an arrival time window so that not everyone arrives at the same time. Visitors may stay until closing time.
- **FR-7** Visitors enter contact details (at minimum an e-mail address).
- **FR-8** Inline validation with a "correct details" loop: invalid input (e.g. bad e-mail format) is corrected in place, immediately, without losing previously entered data. Validation happens before the payment step starts.
- **FR-9** Tickets can be purchased without creating an account.
- **FR-10** Families can buy multiple tickets in one order; each ticket gets its own unique QR code so family members can also arrive separately.

### 3.3 Phase 2 — Review & pay

- **FR-11** The visitor reviews the full order before paying.
- **FR-12** Payment is **simulated** for the school version. The system checks payment status before issuing tickets.
- **FR-13** If payment fails, the visitor can retry or choose another payment method **without** losing the selected date or ticket allocation (no form reset, no restarting the flow).
- **FR-14** After successful payment, the visitor immediately sees the booking confirmation and can open the ticket directly on the website — the flow must not depend on the confirmation e-mail.

### 3.4 Ticket generation & delivery

- **FR-15** Each ticket record contains: visit date, expiry date, quantity, and price.
- **FR-16** Every ticket has a **unique QR code**.
- **FR-17** Ticket tokens are generated securely and stored **hashed** in the database.
- **FR-18** Tickets and all related data are stored in a database.
- **FR-19** Tickets are delivered by e-mail and can be **downloaded as a PDF** (with the unique QR code) for offline access or printing.

### 3.5 Phase 3 — Zoo entry & validation

- **FR-20** Staff scan the ticket's QR code using a phone camera.
- **FR-21** The system validates that the ticket is valid, paid, for the correct date, and not previously used.
- **FR-22** A ticket can be used **only once**; after a successful scan it is marked as used.
- **FR-23** Invalid, expired, or cancelled tickets are rejected **with a clear reason**.
- **FR-24** If a scan fails (unreadable QR code, already used, poor reception at the gate), the visitor is routed to a dedicated "go to staff for help" path so the main entry queue keeps moving.

### 3.6 Management module

- **FR-25** Administrators can manage prices.
- **FR-26** Administrators can manage opening hours (including closed days).
- **FR-27** Administrators can manage bookings.
- **FR-28** Administrators can set and manage the maximum number of visitors per day (available capacity).
- **FR-29** *(Optional)* A daily overview showing the number of expected visitors and how many have already entered.

### 3.7 Post-purchase

- **FR-30** *(Optional)* Visitors can change the visit date of a ticket, if the ticket has not been used and capacity is available on the new date.

## 4. Non-Functional Requirements

- **NFR-1 Mobile-first**: most visitors (parents, tourists) book on mobile devices; the site must work well on phones.
- **NFR-2 Offline resilience**: the downloaded PDF ticket must work when cellular reception at the entrance is poor.
- **NFR-3 Gate speed**: QR scanning must be fast and standardized to avoid queues at the turnstiles.
- **NFR-4 Error recovery**: every step has an isolated error-recovery loop; a failure at any step must never force the visitor to restart the whole flow.
- **NFR-5 Security**: unique ticket tokens, hashed storage, single-use enforcement.
- **NFR-6 Maintainability**: code must be readable and understandable — the team uses AI-generated code and must be able to review and improve it.

## 5. Technical Constraints

Proposed stack (from Emma's proposal):

- **Backend**: Python + FastAPI
- **QR code generation**: Python library `qrcode`
- **Frontend**: HTML, CSS, JavaScript (kept simple; JavaScript handles QR scanning and backend communication)
- **Storage**: database for tickets and booking data
- **QR scanning**: phone camera

## 6. Success Metrics

- Higher checkout conversion rate (isolated error loops in phases 1 and 2 prevent session drop-offs).
- Zero e-mail-dependency delays (immediate on-screen ticket and PDF generation eliminate "missing confirmation e-mail" problems).
- Faster gate throughput (standardized QR scanning, offline-ready PDFs).
- Clear exception handling for both digital failures (payment) and physical failures (gate scanning).

## 7. Open Questions

From the initial idea — to be decided with the team:

- Web interface and/or native app?
- Is a full management module required (assumed yes — see FR-25…FR-29)?
- Which languages must the system support?
- Only tickets, or also other products (e.g. merchandise, tours)?

## 8. Out of Scope (School Version)

- Real payment processing (simulated instead).
- Optional features listed as possible extras (date change, discount codes, daily visitor overview) may be added if time allows.
