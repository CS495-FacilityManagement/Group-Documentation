# Testing

## Components / Services Tested

The following backend components will be tested:

- **Facilities Service**
  - Create, update, delete, and retrieve facilities
- **Buildings Service**
  - CRUD operations for buildings within a facility
- **Assets Service**
  - Asset creation, updates, and retrieval
  - Risk score calculation logic
- **Maintenance Service**
  - Work order creation, retrieval, and completion
- **Authentication & Authorization**
  - JWT validation via AWS Cognito
  - Role-based access control using `require_facility_role`

---

## Significant Test Cases

### 1. Facility Creation
- Created a facility using POST `/api/facilities`
- Verified:
  - Facility persisted in database
  - User assigned as "Facility Manager"

---

### 2. Authorization Enforcement
- Attempted restricted routes as non-manager users
- Expected result:
  - 403 Forbidden response

---

### 3. Asset Creation & Risk Calculation
- Created assets with different install dates
- Verified:
  - Criticality assigned based on asset type
  - Risk score increases with asset age
  - Assets past expected life marked as "Critical"

---

### 4. Maintenance Lifecycle
- Created work order via POST `/api/maintenance`
- Completed work order via PUT `/api/maintenance/{id}/complete`
- Verified:
  - Status transitions from OPEN → COMPLETE
  - `completed_at` timestamp is set

---

### 5. Maintenance Impact on Risk
- Compared asset risk before and after maintenance
- Verified:
  - Recent maintenance reduces risk score

---

### 6. Filtering & Query Validation
- Tested:
  - `/api/assets?facility_id={id}`
  - `/api/assets?facility_id={id}&building_id={id}`
  - `/api/maintenance?facility_id={id}&status=OPEN`
- Verified:
  - Results filtered correctly

---

## Test Scripts / Execution

Testing was performed manually using API tools:

- **Hoppscotch / Postman**
- **FastAPI Swagger UI (`/docs`)**
- **curl commands**

### Example Request

```bash
curl -X GET \
  "https://<api-url>/api/assets?facility_id=1" \
  -H "Authorization: Bearer <jwt_token>"