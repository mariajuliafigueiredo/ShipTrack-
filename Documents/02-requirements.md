# ShipTrack — Requirements

## 1. Functional Requirements

### Shipments
- **RF-01:** The system must allow operators to register a shipment, generating a unique tracking number (initial status: Label Created).
- **RF-02:** The system must allow operators to update the status of a shipment, respecting the valid transitions defined in the Business Rules.
- **RF-03:** The system must record a tracking event for every status change (date/time, status, location, notes, user).
- **RF-04:** The system must allow consulting a shipment and its full tracking history by tracking number.
- **RF-05:** The system must allow searching and filtering shipments by tracking number, status, customer and date.

### Delivery and returns
- **RF-06:** The system must control delivery attempts (maximum of 2) and record the attempt number in each delivery event.
- **RF-07:** The system must allow redirecting a shipment to a pickup point, recording the store name.
- **RF-08:** The system must create a new shipment, linked to the original, when a return is started.

### Delays and incidents
- **RF-09:** The system must allow recording incidents (incorrect address, customer unavailable, pickup deadline exceeded, damaged, lost).
- **RF-10:** The system must identify delayed shipments (estimated delivery date passed and not delivered) and allow recording the delay reason.

### Customers and users
- **RF-11:** The system must allow managing customer information (create, edit, consult).
- **RF-12:** The system must provide user authentication with two roles: Operations Staff and Administrator.
- **RF-13:** The system must allow administrators to manage users and their roles.

### Dashboard
- **RF-14:** The system must display a dashboard with operational indicators (shipments by status, delayed shipments, incidents by type, delay reasons).

## 2. Non-Functional Requirements

- **RNF-01:** The API must be documented with Swagger / OpenAPI.
- **RNF-02:** Passwords must be stored hashed, never in plain text.
- **RNF-03:** Input data must be validated on the backend.
- **RNF-04:** The interface must be responsive (desktop and mobile).
- **RNF-05:** Business deadlines (4 and 2 business days, 7 business days for pickup) are counted in business days; public holidays are ignored in the MVP.

## 3. Out of Scope (MVP)

External carrier integrations, automated notifications, customer access and real-time GPS tracking.
