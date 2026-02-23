# Auto Pickup for Grocery Stores — Prototype Requirements

## 1) Objective
Build a working prototype for an **automatic pickup system** where:
1. Customer places grocery products on a scanning platform.
2. System identifies products using **Computer Vision**.
3. Cart is generated automatically.
4. Customer takes products and walks to their car.
5. Customer receives a mobile notification with purchase summary.

---

## 2) Prototype Scope (MVP)
### In scope
- Product detection/recognition from platform camera feed.
- Session/cart creation and real-time updates.
- Basic checkout summary generation.
- Mobile push notification when session is completed.

### Out of scope (for prototype)
- Full payment gateway integration.
- Full ERP integration.
- Multi-store production deployment.

---

## 3) Functional Requirements

### FR-01 Session Start
- The system must create a unique `session_id` when customer starts pickup (QR, kiosk, or app).

### FR-02 Product Scanning
- The system must process camera frames from platform and detect products.
- Detected product events must include: `session_id`, `product_id/label`, `confidence`, `timestamp`.

### FR-03 Cart Update
- The backend must update cart quantities in real time as products are added/removed.

### FR-04 Product Removal Confirmation
- The system must detect when customer retrieves products and close scanning state.

### FR-05 Notification Delivery
- On completed session, system must send push notification with:
  - Order/session id
  - Product list and quantities
  - Total estimated amount
  - Timestamp

### FR-06 Manual Correction
- Staff/customer must be able to manually correct cart in case of misdetection.

---

## 4) Non-Functional Requirements

### NFR-01 Performance
- Inference latency target: **<= 300 ms/frame** on prototype hardware.
- End-to-end cart update delay: **<= 1 second**.

### NFR-02 Accuracy
- Initial detection precision target: **>= 90%** for selected SKU subset.
- False positives must be logged for model improvement.

### NFR-03 Reliability
- If CV service is unavailable, backend must keep session open and raise error status.

### NFR-04 Security
- API endpoints must require authentication for staff/admin actions.
- Device tokens and session data must be stored securely.

### NFR-05 Observability
- Log all detection events, cart updates, and notification attempts.

---

## 5) System Components (Prototype)

1. **Vision Service**
   - Camera input, CV model inference, optional object tracking.
   - Emits detection events to backend.

2. **Backend API**
   - Session lifecycle management.
   - Cart logic and reconciliation.
   - Notification trigger.

3. **Database**
   - Products, sessions, session_items, notification logs.

4. **Mobile Notification Layer**
   - FCM/APNs integration for push messages.

---

## 6) Data Requirements

### Product master data
- `product_id`, `name`, `price`, `category`, `vision_label`, `expected_weight(optional)`.

### Session data
- `session_id`, `customer_id(optional)`, `status`, `started_at`, `ended_at`.

### Event data
- Detection events with model confidence and timestamps.

### Notification data
- Delivery status (`queued`, `sent`, `failed`) and provider response.

---

## 7) Computer Vision Requirements

### CVR-01 Dataset
- Collect/store dataset for selected pilot SKUs under realistic lighting and occlusion.
- Annotate images with bounding boxes/class labels.

### CVR-02 Model
- Use real-time detector (e.g., YOLO family) suitable for edge inference.

### CVR-03 Tracking (recommended)
- Add object tracking to reduce duplicate counts across consecutive frames.

### CVR-04 Validation
- Evaluate model with precision/recall and confusion matrix before pilot.

---

## 8) API Requirements (Minimum)

- `POST /sessions/start`
- `POST /sessions/{id}/events/detection`
- `PATCH /sessions/{id}/items`
- `POST /sessions/{id}/complete`
- `POST /notifications/send`
- `GET /sessions/{id}/summary`

---

## 9) Acceptance Criteria (Prototype Done)
- A demo session can be started and completed end-to-end.
- At least 10–20 target grocery products can be recognized.
- Cart summary is generated from CV events.
- Customer test device receives push notification after session completion.
- Logs are available for detections, corrections, and notification status.

---

## 10) Suggested Repository Structure for this Project

```text
projects/
  auto-pickup-grocery/
    README.md
    requirements/
      prototype-requirements.md
    vision-service/
    backend-api/
    mobile-app/
    infrastructure/
    docs/
```

---

## 11) Next Implementation Steps
1. Define pilot SKU list (10–20 items).
2. Record and annotate initial dataset.
3. Train/evaluate baseline detector.
4. Build minimal backend session/cart endpoints.
5. Integrate push notification provider.
6. Run end-to-end in-store simulation.
