# ShipTrack — Business Rules

## 1. Shipment Statuses

| Status | Description |
|--------|-------------|
| Label Created | Shipping label created; shipment not yet collected |
| Picked Up | Shipment collected from the sender |
| Held at Origin | Export issue (e.g. missing documentation). Shipment stays at origin without movement after pickup; the sender must contact the company |
| In Transit | Moving through the network. Facility arrivals/departures are recorded as events with location |
| Held at Customs | Shipment retained at customs (import) |
| At Delivery Center | Arrived at the delivery center of the destination region |
| Pending Address Info | Address incorrect or incomplete; customer is asked for more details or GPS coordinates |
| Out for Delivery | Shipment is with the driver (first or second attempt) |
| Delivered | Delivery completed |
| Delivery Failed | Delivery attempt failed (e.g. customer unavailable) |
| Awaiting Customer Instructions | Both attempts failed; shipment held at the delivery center for 4 business days waiting for instructions |
| Redirected to Pickup Point | Shipment sent to a nearby store (store name recorded) |
| Awaiting Pickup | Waiting at the store for the customer |
| Collected at Pickup Point | Customer collected the shipment at the store |
| Pickup Expired | Pickup deadline exceeded; shipment collected by the driver. Customer has 2 business days to make contact |
| Return Requested | Return requested by the customer or sender |
| Return In Progress | Return underway, with a new linked tracking number |
| Damaged | Shipment damaged; contact the sender |
| Lost | Shipment lost; contact the sender |

## 2. Valid Status Transitions

- Label Created → Picked Up
- Picked Up → In Transit
- Picked Up → Held at Origin (export issue)
- Held at Origin → In Transit (issue resolved)
- In Transit → Held at Customs (import)
- Held at Customs → In Transit (released)
- In Transit → At Delivery Center
- At Delivery Center → Out for Delivery (address correct)
- At Delivery Center → Pending Address Info (address incorrect)
- Pending Address Info → Out for Delivery (data updated)
- Out for Delivery → Delivered
- Out for Delivery → Delivery Failed
- Delivery Failed → Out for Delivery (2nd attempt, different day and time)
- Delivery Failed → Awaiting Customer Instructions (after the 2nd failed attempt)
- Awaiting Customer Instructions → Out for Delivery (new delivery requested)
- Awaiting Customer Instructions → Redirected to Pickup Point
- Awaiting Customer Instructions → Return In Progress (no contact within 4 business days)
- Redirected to Pickup Point → Awaiting Pickup
- Awaiting Pickup → Collected at Pickup Point
- Awaiting Pickup → Pickup Expired
- Pickup Expired → Out for Delivery (customer makes contact within 2 business days)
- Pickup Expired → Return In Progress (no contact within 2 business days)
- Delivered / Collected at Pickup Point → Return Requested
- Return Requested → Return In Progress (new tracking number created)
- In Transit / At Delivery Center / Out for Delivery → Damaged
- In Transit / At Delivery Center / Out for Delivery → Lost

## 3. Incident Types

| Type | Description |
|------|-------------|
| Incorrect Address | Address is wrong or incomplete |
| Customer Unavailable | Nobody to receive the shipment |
| Pickup Deadline Exceeded | Not collected at the store in time |
| Damaged Shipment | Damaged during handling or transport; contact the sender |
| Lost Shipment | Cannot be located; contact the sender |

## 4. Delay Rules

A shipment is considered **delayed** when the estimated delivery date has passed and the shipment has not been delivered. Each delay should have a recorded reason.

### Delay Reasons

| Reason | Description |
|--------|-------------|
| Natural Causes | Weather or other factors outside the carrier's control |
| Missed Connection | Shipment arrived late at the facility and the plane/truck had already departed |
| Strike or Disruption | Strikes or other events that prevent normal operation |
| Export Issue | Occurs at origin. After pickup, the shipment stays without movement due to missing or inadequate documentation. The sender must contact the company |
| Import / Customs Issue | Shipment retained at customs (missing documents or pending release by a public authority) |
| Irregular Zone | Destination area without daily deliveries |

### Notes
- Delay reasons are separate from incidents. An incident happens to the shipment; a delay reason explains why the deadline was not met.
- Natural Causes and Strike or Disruption are outside the carrier's control. This distinction can be used in dashboard reports.
- Irregular Zone is selected manually by the operator in the MVP. Automatic estimated-date calculation by zone is a future improvement.

## 5. Business Limits

| Rule | Value |
|------|-------|
| Delivery attempts | 2, on different days and at different times |
| Attempt counter after a new delivery is requested | Restarts, with 2 new attempts |
| Hold period after failed attempts | 4 business days at the delivery center, waiting for customer instructions |
| Action if no contact after hold period | Shipment is returned to the sender immediately |
| Pickup point deadline | 7 business days |
| Contact period after pickup deadline exceeded | 2 business days, counted from the driver's collection |
| Action if no contact after contact period | Shipment is returned to the sender immediately |

### Notes
- All deadlines are counted in business days (Monday to Friday). Public holidays are ignored in the MVP; this is a known limitation.

## 6. Tracking Events

Every status change generates an event with: date/time, status, location, notes, user and (for deliveries) attempt number.

Returns create a new shipment with its own tracking number, linked to the original shipment.
