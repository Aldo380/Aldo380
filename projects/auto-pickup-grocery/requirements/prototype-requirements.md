# Auto Pickup for Grocery Stores — Formal Software Requirements (Prototype)

## 1. Purpose
Define formal requirements for a Software Engineering prototype of an **auto pickup grocery system** where a customer places products on a reading platform, products are recognized using computer vision, and the customer receives purchase information on mobile.

## 2. Scope
The prototype covers:
- Product identification on a reading platform.
- Automatic cart/session generation.
- Session completion and customer notification.
- Basic monitoring/reporting of user activity.

The prototype excludes:
- Full financial/ERP integration.
- Production-scale deployment.

---

## 3. Functional Requirements

### 3.1 Data / Information Requirements
**FR-D01 Product Master Data**
- The system shall maintain product catalog data including:
  - `product_id`, `name`, `category`, `price`, `stock_level`, `vision_label`.

**FR-D02 Inventory Information**
- The system shall store and expose **store stock information** (`stock_level`) for each product.
- The system shall update stock after successful session completion.

**FR-D03 Session & Event Data**
- The system shall store session records:
  - `session_id`, `user_id(optional)`, `status`, `started_at`, `completed_at`.
- The system shall store detection events:
  - `session_id`, `product_label`, `confidence`, `timestamp`, `camera_id`.

**FR-D04 Reporting Data**
- The system shall record user activity logs for analytics and audit:
  - session starts/completions,
  - cart edits,
  - detection errors/manual corrections,
  - notification delivery status.

---

### 3.2 Interface Requirements
**FR-I01 Physical Interaction Interface**
- The system shall provide a **reading platform interface** where users place products for scanning.
- The interface shall show scanning state (`ready`, `scanning`, `done`, `error`).

**FR-I02 Application Interface (UI)**
- The UI shall display current cart items, quantities, confidence/verification status, and estimated total.
- The UI shall allow staff or user correction when product detection is incorrect.

**FR-I03 Computer Vision Interface**
- The platform shall integrate with a CV inference service.
- The CV module may use one or more architectures depending on task:
  - **Object detection** (recommended baseline, e.g., YOLO family) for item identification.
  - **Segmentation** (e.g., U-Net variants) as optional enhancement for overlap/occlusion scenarios.
- The system shall expose a defined interface/event schema between CV and backend.

---

### 3.3 Navigation Requirements
**FR-N01 User Navigation Flow**
- The system shall support the following user flow:
  1. Start pickup session.
  2. Place products on platform.
  3. Review auto-generated cart.
  4. Confirm/revise cart.
  5. Complete session.
  6. Receive mobile notification.

**FR-N02 UI Navigation States**
- The system shall provide clear transitions between states:
  - `home` → `session-start` → `scanning` → `cart-review` → `session-complete`.
- The UI shall always provide a visible “back” or “cancel session” option before completion.

---

### 3.4 Personalization Requirements
**FR-P01 User Profile Preferences**
- The system shall support optional user preferences:
  - language,
  - notification channel,
  - accessibility settings (font/contrast).

**FR-P02 Notification Personalization**
- Notification content shall be personalized with user/session context:
  - user name (if available),
  - purchase summary,
  - session timestamp,
  - store identifier.

---

### 3.5 Transactions / Internal Functionalities
**FR-T01 Session Transaction Management**
- The system shall treat each pickup interaction as a transaction-like session with status:
  - `created`, `scanning`, `review`, `completed`, `cancelled`, `error`.

**FR-T02 Cart Reconciliation**
- The system shall reconcile CV detections with cart state and allow manual overrides.

**FR-T03 Activity Report Generation**
- The system shall generate activity reports, including:
  - sessions per day,
  - products scanned,
  - correction rate,
  - notification success rate.

**FR-T04 Notification Triggering**
- On successful session completion, the system shall trigger customer notification with purchase summary.

---

## 4. Non-Functional Requirements

### 4.1 Performance
**NFR-01** Inference latency should be <= 300 ms/frame on prototype hardware.

**NFR-02** Cart UI update delay should be <= 1 second after detection event.

### 4.2 Accuracy & Quality
**NFR-03** Initial detection precision for pilot SKUs should be >= 90%.

**NFR-04** The system shall log false positives/false negatives for model improvement.

### 4.3 Reliability & Availability
**NFR-05** If CV service fails, the session shall remain recoverable and enter `error` state with user feedback.

**NFR-06** Notification retries shall be attempted on temporary delivery failures.

### 4.4 Security & Privacy
**NFR-07** Staff/admin endpoints shall require authentication and authorization.

**NFR-08** Sensitive user/session data shall be protected in transit and at rest.

### 4.5 Usability
**NFR-09** The interface shall be understandable for first-time users with minimal training.

**NFR-10** User errors (misplacement, wrong item) shall be recoverable through guided UI actions.

### 4.6 Maintainability & Observability
**NFR-11** The system shall maintain structured logs for CV events, cart updates, and notifications.

**NFR-12** Requirements traceability shall be preserved by requirement IDs (FR-*, NFR-*).

---

## 5. Minimum External/API Contracts (Prototype)
- `POST /sessions/start`
- `POST /sessions/{id}/events/detection`
- `PATCH /sessions/{id}/cart`
- `POST /sessions/{id}/complete`
- `POST /sessions/{id}/cancel`
- `GET /sessions/{id}/summary`
- `GET /reports/activity`

---

## 6. Acceptance Criteria
1. A user can complete full flow from session start to notification delivery.
2. Store stock information is captured and updated after completed session.
3. System supports cart review, correction, and final confirmation.
4. Activity report can be generated for completed sessions.
5. Performance/accuracy targets for prototype are measurable through logs.

---

## 7. Suggested Next Engineering Steps
1. Build requirements traceability matrix (FR/NFR ↔ components/tests).
2. Define UI wireframes for required navigation states.
3. Select baseline CV model (detector first; segmentation optional).
4. Implement event contracts between vision service and backend.
5. Define test plan (functional tests + non-functional benchmarks).
