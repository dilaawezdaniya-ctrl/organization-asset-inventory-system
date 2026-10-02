# Product Decision Log

## September 6, 2026

### Decision 1: Serial Number is Optional
Not every physical asset has a manufacturer serial number.
Therefore, serial number will not be mandatory.

### Decision 2: Department Name is Unique Within an Organization
Two different organizations may have departments with the same name.
Therefore, department names only need to be unique within the same organization.

### Decision 3: Asset Location is Linked to Room
Assets should be connected to rooms so the system can track their current location.

### Decision 4: Security Information is Separate From Asset Information
Firewall, antivirus and other security information mainly applies to IT assets.
Therefore, computer/security information will be handled separately from the main Asset table.

### Decision 5: Department Assets Are Derived From Asset Records
The system should not require users to manually enter a list of assets owned by a department.
Department assets will be determined from the assets linked to that department.

## September 7, 2026

### Decision 6: Computer Details are Separate From Asset
Computer-specific information such as processor, RAM, storage and operating system
will be stored separately because these details only apply to computer assets.

### Decision 7: Security Details are Separate From Computer Details
Antivirus, firewall, security status and security-check information will be stored
in a separate table so security information can be expanded independently.

### Decision 8: Supplier Information is Stored Separately
Supplier information will be stored in a separate table because one supplier can
provide many purchases and assets.

## September 8, 2026

### Decision 9: Purchase and Purchase Items are Separate
Purchase information and the individual items within a purchase will be stored separately.
One purchase can contain multiple purchase items.

### Decision 10: Purchased Trackable Items Become Individual Assets
Trackable physical items such as laptops will be represented as individual Asset records,
even when multiple units are purchased together.

### Decision 11: Purchase-to-Asset Traceability
Each asset should be linked to the purchase item from which it was purchased.
This allows the organization to trace an asset back to its purchase and supplier.

### Decision 12: Invoice Number is Unique
Each purchase invoice number must be unique to prevent the same invoice from being
recorded multiple times.

## September 9, 2026

### Decision 13: Employee Information is Separate From Asset Information
Employee records will be stored separately from asset records.
An employee can have multiple assets over time.

### Decision 14: Assignment is Stored as a Separate Record
Asset assignments will be stored in a separate Assignment table instead of
storing employee information directly in the Asset table.

### Decision 15: Assignment History is Preserved
When an asset is returned, the assignment record will not be deleted.
The return date and assignment status will be updated so the organization
can maintain historical records.

### Decision 16: Asset Status Reflects Current Availability
An asset's status will indicate its current state, such as Available,
Assigned, Under Maintenance, Damaged, or Disposed.

## September 10, 2026

### Decision 17: Asset Transfers are Stored as Separate Transactions
Asset transfers will be recorded as separate transactions rather than
overwriting historical information. The current department and room will
remain in the Asset record, while previous movements will be preserved
in the Asset Transfer table.

## September 22, 2026

### Decision 18: Maintenance is Stored as a Separate Transaction
Maintenance information will be stored in a separate Maintenance table because
one asset can have multiple maintenance events during its lifetime.

### Decision 19: Maintenance Uses the Existing Supplier Table
Maintenance vendors will use the existing Supplier records instead of storing
supplier names repeatedly in the Maintenance table.

### Decision 20: Maintenance Affects Current Asset Status
When an asset is undergoing maintenance, its current Asset status will be
`Under Maintenance`.

When maintenance is completed, the asset can return to `Available` unless a
later workflow requires it to be returned to a previous user.

### Decision 21: Current Asset State and Historical Events are Separate
The Asset table represents the asset's current state, while transaction tables
such as Assignment, Asset Transfer and Maintenance preserve historical events.

## September 2026 — Consumable Inventory Management

### Decision 22: Consumables are Separate From Trackable Assets

Consumables will be managed separately from trackable assets.

Trackable assets such as laptops, printers and projectors are individually identified and managed through Asset records.

Consumables such as printer paper, toner and stationery are quantity-based inventory and will be managed through stock quantities and transactions.

### Decision 23: Inventory Manager Controls Stock Issuing

The Inventory Manager will be responsible for receiving and issuing consumable stock.

The Department Head can request consumables, while the Inventory Manager records the actual stock issue.

Workflow:

Department Head → Request → Inventory Manager → Approve/Issue → Stock Transaction → Current Quantity Updated

### Decision 24: Consumables Have a Storage Location

Each consumable inventory item will have a physical storage location.

The existing Building → Room structure will be used instead of storing a free-text location.

### Decision 25: Stock Changes are Recorded as Transactions

Every consumable stock movement will be stored as a separate transaction.

STOCK_IN adds quantity to inventory.

STOCK_OUT removes quantity from inventory.

Transaction history will preserve stock movement information instead of overwriting previous records.

### Decision 26: Current Stock Quantity is Stored

Each consumable will store its current quantity directly.

Every STOCK_IN or STOCK_OUT transaction will update the current quantity.

Transaction records will preserve the historical stock movements.

Stock quantity must never become negative.

### Decision 27: Low Stock is Based on Minimum Stock Level

Each consumable will have a minimum stock level.

Low Stock is determined when:

Current Quantity <= Minimum Stock Level

When current quantity reaches zero, the consumable is considered Out of Stock.

### Decision 28: Consumables Use a Defined Unit

Each consumable will have a defined unit of measurement.

Examples include:

* Pack
* Piece
* Cartridge
* Bottle

The unit will be used when recording and displaying stock quantities.

### Decision 29: STOCK_OUT Records the Receiving Department

Every STOCK_OUT transaction will record the department receiving the consumable.

This allows department-wise consumption tracking and reporting.

STOCK_IN does not require a department because received stock enters central inventory.

### Decision 30: Purchase-to-Stock Traceability

Consumable STOCK_IN transactions will be connected to the existing purchase_item records.

The workflow is:

Purchase → Purchase Item → Stock In → Current Stock

This provides traceability from inventory stock back to the purchase and supplier without creating a separate purchasing system.

### Decision 31: Consumables Use a Separate Category Table

Consumables will use a dedicated consumable_category table rather than the existing asset category table.

This keeps asset classification and consumable classification separate.

Examples of consumable categories include:

* Stationery
* Printing
* Cleaning
* Electrical

### Decision 32: Consumable Identity

Each consumable will have:

* Database ID
* Unique consumable code
* Consumable name
* Consumable category
* Unit

The database ID is used for database identity, while the consumable code is intended for human-facing identification, searching and labels.

### Decision 33: Quantity and Stock Level

Each consumable will store:

* Current Quantity
* Minimum Stock Level

Current quantity represents the available stock.

Minimum stock level represents the threshold used to determine Low Stock.

### Decision 34: Consumable Storage Uses Room

Each consumable will be linked to a physical room using room_id.

The existing organization location structure will therefore be reused:

Building → Room → Consumable

This avoids inconsistent free-text locations.

### Decision 35: Stock Transaction Type

Every stock movement will be stored as a separate transaction.

The transaction type will be one of:

* STOCK_IN
* STOCK_OUT

STOCK_IN increases current stock.

STOCK_OUT decreases current stock.

### Decision 36: Stock Transaction Date

Every stock transaction will store the date on which the stock movement actually occurred.

This supports stock history, reporting, consumption analysis and auditing.

### Decision 37: Department for STOCK_OUT

Every STOCK_OUT transaction will record the receiving department_id.

STOCK_IN does not require a department because stock is received into central inventory.

### Decision 38: Stock Transaction User

Stock transactions will eventually record the system user who performed the transaction.

The application user/role system will be used for this purpose rather than directly using the employee table.

This keeps employee information separate from application access control.

### Decision 39: Stock Transaction Reason and Remarks

Every stock transaction will have a reason.

An optional remarks field will allow additional details to be recorded.

The reason supports structured reporting and filtering, while remarks provide additional context.

### Decision 40: Prevent Negative Stock

A STOCK_OUT transaction cannot be completed when the requested quantity is greater than the current available quantity.

Stock quantity must never become negative.

### Decision 41: Stock Status is Derived

Stock status will not be stored as a separate database field.

It will be calculated from current_quantity and minimum_stock_level.

Examples:

* Current Quantity > Minimum Stock Level → Normal
* Current Quantity <= Minimum Stock Level → Low Stock
* Current Quantity = 0 → Out of Stock

### Decision 42: Transaction Quantity Validation

Every stock transaction must have a quantity greater than zero.

The transaction type determines how the quantity affects current stock:

* STOCK_IN → current quantity + transaction quantity
* STOCK_OUT → current quantity - transaction quantity

## Consumable Inventory Management — Continued

> **Note:** Decision 43 (Purchase Reference for Stock In) remains pending confirmation and is intentionally not recorded as a finalized decision below.

### Decision 44: Consumable Category Structure

The `consumable_category` table will contain a unique category ID, a unique category name, and an optional description.

### Decision 45: Consumable Table

The `consumable` table will store:

* `consumable_id`
* `consumable_code`
* `consumable_name`
* `category_id`
* `unit_id`
* `current_quantity`
* `minimum_stock_level`
* `room_id`

Stock status will be derived rather than stored.

### Decision 46: Decimal Stock Quantities

Consumable stock quantities and stock transaction quantities will support decimal values using `DECIMAL(12,2)`.

This allows quantities such as `100.00 Packs` or `12.50 Litres`.

### Decision 47: Controlled Measurement Units

Consumables will use a controlled measurement-unit table.

Each consumable will reference a `unit_id` instead of storing the unit as free text.

### Decision 48: Unit Table Structure

The `unit` table will contain:

* `unit_id`
* `unit_name`
* `abbreviation`

Both `unit_name` and `abbreviation` will be unique.

### Decision 49: Inactive Measurement Units

The `unit` table will include `is_active`.

Inactive units cannot be assigned to new consumables but remain available for existing records and historical transactions.

Inactive units may be reactivated later.

### Decision 50: Inactive Consumable Categories

The `consumable_category` table will include `is_active`.

Inactive categories cannot be assigned to new consumables but remain available for existing records and can be reactivated later.

### Decision 51: Organization Ownership of Consumables

Each consumable will directly reference its `organization_id` to establish ownership.

The `room_id` will continue to represent the physical storage location.

### Decision 52: Organization and Storage Location Consistency

A consumable's `room_id` must belong to the same organization as its `organization_id`.

The system must prevent assigning a consumable to a room belonging to another organization.

### Decision 53: Consumable Code Uniqueness

`consumable_code` must be unique within an organization.

The same consumable code may be reused by different organizations.

### Decision 54: Consumable Active Status

Each consumable will have an `is_active` field.

Inactive consumables cannot be used in new stock transactions but remain available for historical records and may be reactivated later.

### Decision 55: Inactive Consumables Cannot Receive New Transactions

The system will reject new `STOCK_IN` and `STOCK_OUT` transactions for inactive consumables.

Existing historical transactions remain preserved.

### Decision 56: Stock Transaction Identity

Each stock movement will have a unique `transaction_id` as its database primary key.

Each transaction will reference:

* the consumable
* the transaction type
* the transaction quantity

### Decision 57: Stock Quantity Snapshots

Each stock transaction will store:

* transaction quantity
* `quantity_before`
* `quantity_after`

These values provide a direct audit snapshot of stock immediately before and after the transaction.

### Decision 58: Stock Transaction Timestamp

Every stock transaction will record the exact date and time of the stock movement using `transaction_datetime`.

This allows transactions occurring on the same day to be correctly ordered.

### Decision 59: Stock Transaction Performed By

Each stock transaction will record the application user who performed it using `performed_by_user_id`.

This will reference the application user/authentication structure rather than the employee table.

### Decision 60: STOCK_OUT Can Reference a Request

When a consumable is issued against a formal department request, the `STOCK_OUT` transaction should reference that request.

The request will be stored as a separate record.

### Decision 61: Consumable Requests are Optional for STOCK_OUT

A `STOCK_OUT` transaction may be created either from a formal consumable request or as an authorized direct issue.

When a request exists, the transaction will reference it.

When no request exists, the request reference remains optional.

### Decision 62: Request Approval and Physical Issue are Separate

Approval of a consumable request will not automatically create a `STOCK_OUT`.

`STOCK_OUT` will be recorded when the Inventory Manager actually issues the consumable.

### Decision 63: Partial Stock Issue

A consumable request may be fulfilled through multiple `STOCK_OUT` transactions.

The request will track requested, issued, and remaining quantities until it is fully fulfilled or otherwise closed.

### Decision 64: Consumable Request Status

Consumable requests will use the following statuses:

* `PENDING`
* `APPROVED`
* `PARTIALLY_FULFILLED`
* `COMPLETED`
* `CANCELLED`

The normal fulfillment flow is:

`PENDING → APPROVED → PARTIALLY_FULFILLED → COMPLETED`

### Decision 65: Consumable Request Basic Identity

Each consumable request will have:

* `request_id`
* `request_code`
* request timestamp
* requesting department
* requesting system user
* status
* reason
* optional remarks

`request_id` is the database identity.

`request_code` is the human-facing request identifier.

### Decision 66: Multiple Consumables per Request

A consumable request can contain multiple consumables.

Request-level information will be stored in `consumable_request`.

Each requested consumable and its quantity will be stored in `consumable_request_item`.

### Decision 67: Request Item Quantities

Each `consumable_request_item` will store:

* `requested_quantity`
* `issued_quantity`

Remaining quantity will be calculated as:

`requested_quantity - issued_quantity`

### Decision 68: Request Quantity Validation

Each request item must have a requested quantity greater than zero.

Issued quantity must be zero or greater and cannot exceed requested quantity.

### Decision 69: No Duplicate Consumables in One Request

A single consumable can appear only once within a consumable request.

Duplicate request items for the same consumable are not allowed.

### Decision 70: Request Editing

A consumable request can be edited only while its status is `PENDING`.

Once approved, partially fulfilled, completed, or cancelled, the request becomes read-only.

### Decision 71: Request Cancellation Permissions

The requesting Department Head may cancel `PENDING` requests.

The Inventory Manager may cancel `APPROVED` or `PARTIALLY_FULFILLED` requests.

Completed requests cannot be cancelled.

Cancelled requests cannot be cancelled again.

### Decision 72: Request Approval User

An approved consumable request will record:

* `approved_by_user_id`
* `approved_at`

These fields remain empty until the request is approved.

### Decision 73: Request Cancellation Information

A cancelled consumable request will record:

* `cancelled_by_user_id`
* `cancelled_at`
* `cancellation_reason`

These fields remain empty for requests that have not been cancelled.

### Decision 74: Request Submission Timestamp

Each consumable request will store the exact submission date and time using `requested_at`.

### Decision 75: Request Organization

Each consumable request will directly reference `organization_id`.

The request, requesting department, and requested consumables must belong to the same organization.

### Decision 76: Request Code Uniqueness

`request_code` must be unique within an organization.

The same request code may be reused by different organizations.

### Decision 77: Request Item Organization Validation

Every consumable request item must reference a consumable belonging to the same organization as the parent request.

### Decision 78: Request Item Fulfillment Status is Derived

Request-item fulfillment status will be derived from requested and issued quantities rather than stored as a separate database field.

Examples:

* `issued_quantity = 0` → Not Fulfilled
* `0 < issued_quantity < requested_quantity` → Partially Fulfilled
* `issued_quantity = requested_quantity` → Fully Fulfilled

### Decision 79: Request Item Editing

Consumable request items may be added, edited, or removed only while the parent request is `PENDING`.

After approval, request items become read-only.
