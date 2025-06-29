## Booking Creation Workflow

1. Guest Initiates Booking
Component: Guest (User) → Backend API

**Action**:

    http
    POST /api/bookings
    Body contains attributes such as  property_id, start_date, and end_date

**Purpose**: Guest submits booking request with dates and property ID.

2. Backend Checks Availability
Component: Backend → Database

**Action**:

```sql
    SELECT * FROM bookings
    WHERE property_id = 123...
    AND (start_date <= '2023-10-05' AND end_date >= '2023-10-01')
```
**Purpose**: Verify no overlapping bookings exist for the selected dates.

3. Database Responds
Component: Database → Backend

**Response**:

    If available: Empty result set

    If booked: Existing booking details

    Logic: Backend proceeds only if no conflicts are found.

4. Payment Processing
Component: Backend → Payment Gateway

**Action**:

```javascript
    stripe.charge({
    amount: total_price,
    currency: 'USD',
    source: 'tok_visa'
    });
```
**Purpose**: Charge the guest’s card for the booking total.

5. Payment Confirmation
Component: Payment Gateway → Backend

**Response**:

```json
    { status: "succeeded", transaction_id: "ch_1JABC..." }
    Next Step: Backend creates the booking record in the database.
```
6. Booking Confirmation Email
Component: Backend → Email Service

**Action**:

```javascript
    sendgrid.send({
    to: guest@email.com,
    subject: "Booking Confirmed!",
    text: "Your stay at Property XYZ is confirmed."
    });
```
**Purpose**: Notify the guest of successful booking.
