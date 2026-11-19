# Feature Specification: Grainpure Order Portal

**Feature Branch**: `001-grainpure-order-portal`

**Created**: 2026-11-19

**Status**: Draft

**Input**: User description: "Implement the Grainpure Order Management Portal described in Grainpure_PRD_v1.1.pdf, using logo.jpg for the company's logo wherever needed."

## User Scenarios & Testing _(mandatory)_

### User Story 1 - User Onboarding and Role Access (Priority: P1)

An Admin invites or directly creates Broker and Warehouse Supervisor accounts. Invitees activate their accounts, set passwords, and enter the portal through a role-specific dashboard. Deactivated users are prevented from logging in, and every role receives only its authorised capabilities.

**Why this priority**: Account setup and access control are prerequisites for every operational workflow and protect all order, customer, and dispatch data.

**Independent Test**: Can be fully tested by inviting and activating one Broker and one Warehouse Supervisor, logging in with both roles, deactivating an account, and verifying that protected capabilities and data remain unavailable.

**Acceptance Scenarios**:

1. **Given** an Administrator with account management access, **When** the Administrator invites a Broker or Warehouse Supervisor, **Then** the invitation is role-specific, uniquely tracked, and usable once within its validity period.
2. **Given** a valid invitation, **When** the recipient registers and sets a password, **Then** the account is activated and the recipient is directed to the appropriate role dashboard.
3. **Given** an active account, **When** an Administrator deactivates it, **Then** the user can no longer log in or access protected workflows.
4. **Given** an authenticated Warehouse Supervisor, **When** the user attempts an Admin-only action, **Then** the action is denied by the system and no data changes.

---

### User Story 2 - Broker Customer and Order Management (Priority: P1)

A Broker manages customers assigned to its own account and creates a multi-product order for a customer or for a separate self-customer record. The Broker reviews the order, submits it for approval, and can edit or cancel/resubmit it only while permitted by the order workflow.

**Why this priority**: Order initiation is the central business entry point and must produce accurate, immutable-at-approval order items and totals.

**Independent Test**: Can be fully tested by a Broker adding a customer, creating an order containing several products, verifying separate item records and total amount, submitting it, and confirming pre-approval edit behavior.

**Acceptance Scenarios**:

1. **Given** an authenticated Broker, **When** the Broker adds a customer, **Then** the customer is linked only to that Broker and is available for future orders.
2. **Given** a Broker placing a self-order, **When** the Broker selects the customer, **Then** the system requires a separate customer record linked to the Broker rather than exposing the Broker account itself as a customer.
3. **Given** active products and a selected customer, **When** the Broker adds products and quantities, **Then** the system retains each product as a separate order item and calculates the order total from the captured product prices.
4. **Given** an order awaiting approval, **When** the Broker edits item quantities or products, **Then** item records and the total remain consistent and the change is recorded in history.
5. **Given** an approved order, **When** the Broker attempts to edit or delete it, **Then** the system prevents the change.

---

### User Story 3 - Admin Review and Approval (Priority: P1)

An Admin reviews a submitted order, including its Broker, customer, product quantities, and totals, then approves or rejects it. Approval records who decided and when, releases the order to the warehouse, and locks Broker editing; rejection records the reason and prevents dispatch.

**Why this priority**: Administrative approval is the control point that makes an order eligible for fulfilment and establishes the audit record.

**Independent Test**: Can be fully tested by approving and rejecting separate submitted orders and verifying that only approved orders become dispatchable and that both decisions remain in history.

**Acceptance Scenarios**:

1. **Given** a submitted Pending Approval order, **When** an Admin approves it, **Then** it becomes Approved, the approving identity and timestamp are recorded, and it becomes available to warehouse operations.
2. **Given** a submitted order, **When** an Admin rejects it with a reason, **Then** it becomes Rejected and cannot be dispatched.
3. **Given** a rejection or approval, **When** the decision is saved, **Then** the status history and decision details remain visible and cannot be silently removed.
4. **Given** an approved order, **When** an Admin or Broker attempts to change order items through normal workflows, **Then** the change is denied.

---

### User Story 4 - Warehouse Dispatch Management (Priority: P1)

A Warehouse Supervisor views approved orders and records one or more dispatch transactions, possibly partially dispatching several products. The system validates pending quantities, preserves each dispatch event and item, recalculates product balances and statuses, and prevents over-dispatching and concurrent race conditions.

**Why this priority**: Accurate fulfilment tracking is the principal operational outcome of the portal.

**Independent Test**: Can be fully tested by dispatching one product partially, adding another dispatch for the remaining quantity, attempting over-dispatch, and verifying immutable history and final Completed status.

**Acceptance Scenarios**:

1. **Given** an Approved order, **When** a Warehouse Supervisor saves quantities greater than zero within each pending balance, **Then** the dispatch is stored with its own number, supervisor, date, and dispatch items.
2. **Given** a product with remaining quantity, **When** the Supervisor performs multiple dispatch transactions, **Then** each transaction is retained separately and cumulative dispatched and pending quantities remain accurate.
3. **Given** a fully dispatched product, **When** a Supervisor attempts another dispatch for that product, **Then** it is rejected.
4. **Given** an order whose dispatch data is changing concurrently, **When** multiple submissions attempt to reserve quantities, **Then** the system validates the latest available balance and prevents over-dispatch.
5. **Given** all order items are fully dispatched, **When** the final valid dispatch succeeds, **Then** the order becomes Completed automatically and remains available for historical viewing.

---

### User Story 5 - Role Dashboards, Search, and Operational History (Priority: P2)

Authorized users see role-relevant dashboards and can search, filter, and sort operational records. Admins monitor the full operation, Brokers see their own records subject to visibility decisions, and Warehouse Supervisors focus on approved and partially dispatched work.

**Why this priority**: Clear operational visibility reduces response time and enables efficient management while maintaining role and ownership boundaries.

**Independent Test**: Can be fully tested by seeding records for all roles and verifying summary counts, search/filter results, sorting, pagination, and cross-role access restrictions.

**Acceptance Scenarios**:

1. **Given** an authenticated Admin, **When** the dashboard loads, **Then** it shows operational totals, pending invitations, recent orders, and recent dispatches.
2. **Given** an authenticated Broker, **When** the user searches or views an order, **Then** the results include only that Broker's orders and customers, subject to the approved order/dispatch visibility decision.
3. **Given** an authenticated Warehouse Supervisor, **When** the user opens the dashboard, **Then** it identifies approved and partially dispatched orders, pending quantities, recent dispatches, and dispatch history.
4. **Given** search or filter controls, **When** a user enters valid criteria, **Then** matching records can be sorted and paginated without exposing unauthorized data.

---

### Edge Cases

- An invitation expires, is revoked, or was already used; the system must reject it and explain that it is no longer valid.
- Duplicate active accounts use the same email; the system must prevent another active account from being created.
- A customer, product, or account is deactivated while it is referenced by an existing order or workflow; existing records remain viewable and historical integrity is preserved.
- A product becomes inactive after an order is submitted or approved; existing order items retain the captured price and remain dispatchable from approved orders.
- A Broker attempts to edit or cancel a submitted order after approval or attempts to access another Broker's customer or order.
- A dispatch quantity is zero, negative, in a different or unsupported unit, or exceeds the remaining pending quantity.
- Several dispatch submissions occur simultaneously for the last remaining quantity; only a valid final balance is accepted.
- Rejected orders are returned to an editable state or cancellation state: [NEEDS CLARIFICATION: confirm whether rejected orders can be edited and resubmitted, including after any required confirmation].
- A saved dispatch transaction needs correction: [NEEDS CLARIFICATION: confirm whether supervisors can edit or cancel dispatches and whether correction must use a reversal or audit-preserving workflow].
- Broker visibility to order status and dispatch details: [NEEDS CLARIFICATION: confirm whether Brokers may see status and dispatch progress for their own orders or are limited to order creation and customer management].

## Requirements _(mandatory)_

### Functional Requirements

- **FR-001**: The system MUST support authentication and secure session management for Admin, Broker, and Warehouse Supervisor roles.
- **FR-002**: The system MUST prevent protected workflows and pages from being accessed by unauthorized roles or account owners.
- **FR-003**: Admin MUST be able to invite or directly create Broker and Warehouse Supervisor accounts and set or resend activation access.
- **FR-004**: The system MUST generate unique, role-linked, expiring, single-use invitations and store only a hashed invitation-token representation.
- **FR-005**: The system MUST reject expired, revoked, or previously used invitations and expose invitation status and audit timestamps to Admin.
- **FR-006**: Admin MUST be able to view, search, filter, add, edit, activate, and deactivate brokers and Warehouse Supervisors.
- **FR-007**: Brokers MUST be able to manage customers associated with their own account, including a separate self-customer record.
- **FR-008**: The system MUST prevent Brokers from viewing or managing customers or orders belonging to another Broker.
- **FR-009**: Admin MUST be able to manage all customer records and add, edit, activate, deactivate, and price products.
- **FR-010**: Brokers MUST be able to add only active products to a new order.
- **FR-011**: The system MUST store a unit price and item total at order creation and retain those values when product prices change later.
- **FR-012**: An order MUST contain one order record and a separate order-item record for every product included.
- **FR-013**: On submission, the system MUST generate a unique order number and place the order in Pending Approval.
- **FR-014**: Broker editing MUST be allowed only before Admin approval and must not create silent or deleted submitted-order records.
- **FR-015**: The system MUST record order creation, Broker/customer selection, products, quantities, submission, edits, status changes, approval, and rejection in operational history.
- **FR-016**: Admin MUST be able to approve or reject a submitted order and must record approving identity or rejecting identity, timestamp, and rejection reason when applicable.
- **FR-017**: Approved orders MUST become available for warehouse dispatch and MUST lock Broker editing of the order and its order items.
- **FR-018**: Rejected orders MUST not be dispatchable and must follow the stakeholder-approved edit/resubmission policy recorded in the final specification.
- **FR-019**: Warehouse Supervisors MUST be able to record full, partial, and multiple dispatch transactions for approved orders without further Admin approval.
- **FR-020**: Each dispatch transaction MUST record a unique dispatch number, order, Supervisor, date/time, remarks, and separate dispatch items for each included order item.
- **FR-021**: The system MUST reject dispatch quantities that are zero or negative and quantities that exceed the remaining pending quantity for the relevant order item.
- **FR-022**: The system MUST preserve prior dispatch records and prevent ordinary workflows from overwriting or permanently deleting them.
- **FR-023**: The system MUST validate dispatch quantities atomically and prevent over-dispatch during concurrent dispatch attempts.
- **FR-024**: The system MUST calculate dispatched quantity and pending quantity for every order item and calculate Product and overall order status automatically.
- **FR-025**: The system MUST mark an order Completed only when every order item is fully dispatched.
- **FR-026**: The system MUST retain completed order and dispatch history and make it searchable and filterable according to role permissions.
- **FR-027**: Admin dashboards MUST show order totals by status, Broker and Supervisor counts, pending invitations, and recent orders and dispatches.
- **FR-028**: Warehouse dashboards MUST show approved and partially dispatched orders, pending quantities, recent dispatches, and dispatch history.
- **FR-029**: Search and filtering MUST support order identity, Broker, customer, product, status, and relevant dates, with sorting and pagination.
- **FR-030**: The system MUST record sensitive operational decisions and authentication events in structured audit information without exposing secrets or unnecessary personal data.
- **FR-031**: The system MUST support soft removal only where business policy permits and MUST protect approval and dispatch history from ordinary permanent deletion.
- **FR-032**: The system MUST support regular database backups, transaction-safe failure handling, and documented recovery procedures.
- **FR-033**: The system MUST present loading, empty, success, warning, and failure states and provide clear validation feedback.
- **FR-34**: The system MUST maintain a Grainpure-branded, responsive, accessible interface using `logo.jpg` wherever a visible company logo is required.

### Key Entities _(include if feature involves data)_

- **User**: An authenticated account with name, email, secure password representation, role, activation state, and timestamps.
- **Broker**: A Broker profile linked to a unique User account and associated with customers and orders.
- **Warehouse Supervisor**: An operational profile linked to a unique User account and associated with recorded dispatches.
- **Customer**: A record owned by one Broker for ordering, billing, and delivery purposes; may represent a separate self-customer record.
- **Product**: An active or inactive catalogue item with name, description, price, unit of measurement, and timestamps.
- **Order**: A Broker-created transaction for one customer containing a total, approval state, submission details, and status history.
- **Order Item**: A product entry in an order with ordered quantity, captured unit price, item total, and dispatch progress.
- **Dispatch**: An independent fulfilment event containing a unique number, order, Supervisor, date, remarks, and dispatch items.
- **Dispatch Item**: A quantity dispatched in a dispatch against one order item.
- **Invitation**: A role-specific, unique, expiring, single-use onboarding record with hashed token and status history.
- **Order Status History**: An immutable operational record of previous status, new status, actor or system source, remarks, and timestamp.

## Success Criteria _(mandatory)_

### Measurable Outcomes

- **SC-001**: An authorized Broker can complete customer selection, multi-product order review, and submission in under 5 minutes in a standard usability test.
- **SC-002**: 95% of users successfully complete their primary journey on the first attempt during acceptance testing.
- **SC-003**: 100% of unauthorized page and API attempts are denied before protected data or actions are returned.
- **SC-004**: 100% of valid dispatch transactions preserve the correct cumulative dispatched, pending, and product status values immediately after save.
- **SC-005**: No dispatch test or concurrency scenario permits dispatched quantity to exceed ordered quantity.
- **SC-006**: Typical page loads and common dashboard searches or filters complete within 3 seconds under normal operating conditions.
- **SC-007**: 100% of approval, rejection, status-change, and dispatch history entries remain available after the related order is completed or rejected.
- **SC-008**: At least 95% of validation and permission errors give a clear, actionable message and no protected data leak.
- **SC-009**: On supported mobile, tablet, and desktop screen sizes, primary workflows remain readable, keyboard accessible, and responsive.
- **SC-010**: In acceptance testing, expired, revoked, and previously used invitations are rejected 100% of the time.

## Assumptions

- The initial release does not provide independent customer login accounts.
- Customer addresses, Billing and Delivery are maintained as separate fields.
- Rejected orders, saved dispatch corrections, and Broker dispatch visibility require stakeholder decisions for the open questions listed in Edge Cases.
- Admin-managed product prices are the source of order-line prices; Brokers do not negotiate prices through the order workflow.
- Invitation validity period and exact unit types are operational configuration decisions, with reasonable secure defaults to be applied during planning unless stakeholders provide explicit values.
- The Grainpure brand identity and `logo.jpg` remain the approved product logo for visible branding in the initial release.
- Desktop is the primary operational environment, while responsive layouts support tablet and mobile access.
- Payment, inventory, supplier, GPS, mobile-app, and advanced analytics capabilities are outside the initial release.
