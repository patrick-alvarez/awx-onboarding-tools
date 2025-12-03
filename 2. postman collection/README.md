# Step 2: KYC/KYB Onboarding via Postman

This guide walks you through executing the KYC and KYB onboarding process using the Postman collection. Before starting, ensure you have completed [Step 1: Preparation](../1.%20preparation/README.md) and have generated the required JSON files.

## Prerequisites

- Postman installed (download from [postman.com](https://www.postman.com/downloads/))
- Generated JSON files from Step 1:
  - `HotelName.json` (account creation payload)
  - `HotelNameBeneficiary.json` (beneficiary payload)
- Airwallex API credentials (`client_id` and `api_key`)
- Template IDs for KYC, KYB, and RFI flows

## 1. Create a New Workspace in Postman

1. Open Postman
2. Click on **Workspaces** in the top left corner
3. Select **Create Workspace**
4. Enter a workspace name (e.g., "Airwallex KYC/KYB Onboarding")
5. Choose workspace visibility:
   - **Personal**: Only visible to you
   - **Team**: Visible to your team members
6. Click **Create Workspace**

## 2. Set Up Environments

You'll need to create two environments: **Development** and **Production**.

### Creating an Environment

1. Click the **Environments** tab in the left sidebar (or the environment dropdown in the top right)
2. Click **Create Environment** (or the **+** icon)
3. Name the environment (e.g., "Airwallex - Development" or "Airwallex - Production")

### Required Environment Variables

Add the following variables to each environment:

| Variable | Type | Description | Example |
|----------|------|-------------|---------|
| `client_id` | default | Your Airwallex client ID | `your_client_id_here` |
| `api_key` | secret | Your Airwallex API key | `your_api_key_here` |
| `base_url` | default | Base URL for Airwallex API | Development: `https://api-demo.airwallex.com`<br>Production: `https://api.airwallex.com` |
| `file_url` | default | File upload URL | Development: `https://files-demo.airwallex.com`<br>Production: `https://files.airwallex.com` |
| `kyc_template_id` | default | KYC template identifier | `your_kyc_template_id` |
| `kyb_template_id` | default | KYB template identifier | `your_kyb_template_id` |
| `rfi_template_id` | default | RFI (Request for Information) template ID | `your_rfi_template_id` |

### Steps to Add Variables

1. In the environment editor, add each variable:
   - Enter the **Variable** name
   - Set the **Type** (default or secret)
   - Enter the **Initial Value** and **Current Value**
2. Click **Save** when done

**Note**: The `api_key` should be set as a **secret** type to hide its value in the Postman UI.

## 3. Import the Postman Collection

1. In your workspace, click **Import** in the top left
2. Navigate to the collection file:
   - Click **files** or drag and drop
   - Select `kyc-kyb-postman-colllection.json` from the `/2. postman collection` directory
3. Click **Import**
4. The collection "KYC / KYB" will appear in your Collections sidebar

## 4. Onboarding Flow - Step by Step

Select the appropriate environment (Development or Production) from the environment dropdown in the top right before starting.

### **Step 1: Authentication**

**Request:** `1. Authentication > Obtain access token`

- **Purpose**: Authenticates with Airwallex and obtains a bearer token
- **Method**: POST
- **Endpoint**: `{{base_url}}/api/v1/authentication/login`
- **Headers**: 
  - `x-client-id`: `{{client_id}}`
  - `x-api-key`: `{{api_key}}`

**What happens:**
- Request is sent with your credentials
- Response contains an access token
- Token is automatically saved to `{{bearer_token}}` variable
- Token expiry is saved to `{{bearer_token_expires_at}}`

**Action**: Click **Send**

---

### **Step 2: Create Account**

**Request:** `2. Create Account > Create an Account`

- **Purpose**: Creates a new connected account in Airwallex
- **Method**: POST
- **Endpoint**: `{{base_url}}/api/v1/accounts/create`
- **Authentication**: Bearer token (automatically applied)

**What to do:**
1. Click on the **Body** tab
2. Replace the entire JSON payload with the contents of your `HotelName.json` file (generated in Step 1)
3. Verify all fields are populated correctly
4. Click **Send**

**What happens:**
- Account is created in Airwallex
- Response contains `id` (connected_account_id) and `identifier` (enterprise_id)
- These values are automatically saved as collection variables:
  - `{{connected_account_id}}`
  - `{{enterprise_id}}`

**Expected Response**: Status 200/201 with account details

---

### **Step 3: KYC Flow**

#### **3A. Create KYC Hosted Flow**

**Request:** `3. KYC Flow > A. Create KYC Hosted Flow`

- **Purpose**: Creates a hosted KYC (Know Your Customer) verification flow
- **Method**: POST
- **Endpoint**: `{{base_url}}/api/v1/hosted_flows/create`
- **Body**:
```json
{
    "account_id": "{{connected_account_id}}",
    "template": "{{kyc_template_id}}",
    "return_url": "https://www.mews.com/en",
    "error_url": "https://www.mews.com/en/error",
    "metadata": {}
}
```

**What happens:**
- KYC hosted flow is created
- Flow ID is saved to `{{kyc_flow_id}}`

**Action**: Click **Send**

#### **3B. Authorize KYC Flow**

**Request:** `3. KYC Flow > B. Authorize KYC Flow`

- **Purpose**: Authorizes the KYC flow for the enterprise
- **Method**: POST
- **Endpoint**: `{{base_url}}/api/v1/hosted_flows/{{kyc_flow_id}}/authorize`
- **Body**:
```json
{
    "identity": "{{enterprise_id}}"
}
```

**What happens:**
- KYC flow is authorized
- Response contains the hosted flow URL and status

**Action**: Click **Send**

---

### **Step 4: KYB Flow**

#### **4A. Create KYB Flow**

**Request:** `4. KYB Flow > A. Create KYB Flow`

- **Purpose**: Creates a hosted KYB (Know Your Business) verification flow
- **Method**: POST
- **Endpoint**: `{{base_url}}/api/v1/hosted_flows/create`
- **Body**:
```json
{
    "account_id": "{{connected_account_id}}",
    "template": "{{kyb_template_id}}",
    "return_url": "https://www.mews.com/en",
    "error_url": "https://www.mews.com/en/error",
    "metadata": {}
}
```

**What happens:**
- KYB hosted flow is created
- Flow ID is saved to `{{kyb_flow_id}}`

**Action**: Click **Send**

#### **4B. Authorize KYB Flow**

**Request:** `4. KYB Flow > B. Authorize KYB Flow`

- **Purpose**: Authorizes the KYB flow for the enterprise
- **Method**: POST
- **Endpoint**: `{{base_url}}/api/v1/hosted_flows/{{kyb_flow_id}}/authorize`
- **Body**:
```json
{
    "identity": "{{enterprise_id}}"
}
```

**What happens:**
- KYB flow is authorized
- Response contains the hosted flow URL and status

**Action**: Click **Send**

---

### **Step 5: Verification & Status Check**

These requests help you verify the onboarding status and troubleshoot if needed.

#### **Get Account Details**

**Request:** `5. Verification & Status Check > Get Account Details`

- **Purpose**: Retrieves complete account information
- **Method**: GET
- **Endpoint**: `{{base_url}}/api/v1/accounts/{{connected_account_id}}`

**Action**: Click **Send** to view account status and details

#### **Get KYC Flow Status**

**Request:** `5. Verification & Status Check > Get KYC Flow Status`

- **Purpose**: Checks the status of the KYC hosted flow
- **Method**: GET
- **Endpoint**: `{{base_url}}/api/v1/hosted_flows/{{kyc_flow_id}}`

**Action**: Click **Send** to view KYC flow status

#### **Get KYB Flow Status**

**Request:** `5. Verification & Status Check > Get KYB Flow Status`

- **Purpose**: Checks the status of the KYB hosted flow
- **Method**: GET
- **Endpoint**: `{{base_url}}/api/v1/hosted_flows/{{kyb_flow_id}}`

**Action**: Click **Send** to view KYB flow status

---

## Collection Variables

The collection automatically manages the following variables (you don't need to set these manually):

- `connected_account_id` - Set after account creation
- `enterprise_id` - Set after account creation
- `kyc_flow_id` - Set after creating KYC flow
- `kyb_flow_id` - Set after creating KYB flow
- `bearer_token` - Set after authentication (auto-refreshes)
- `bearer_token_expires_at` - Token expiration timestamp

## Tips & Troubleshooting

### Authentication
- The bearer token automatically refreshes before each request via the collection's pre-request script
- If you see authentication errors, verify your `client_id` and `api_key` in the environment

### Account Creation
- Ensure all required fields in your JSON payload are populated
- Common issues:
  - Missing business registration numbers
  - Invalid country codes
  - Incomplete address information

### Flow Status
- Flows may take time to process
- Use the verification requests to check status
- Common statuses: `PENDING`, `IN_PROGRESS`, `COMPLETED`, `FAILED`

### Environment Switching
- Always verify you're using the correct environment (Development vs Production)
- Development environment is recommended for testing

### Saved Responses
- Use Postman's "Save Response" feature to keep records of successful onboardings
- This helps with troubleshooting and documentation

## Next Steps

After successful onboarding:
1. Verify account status is `ACTIVE`
2. Confirm KYC and KYB flows are `COMPLETED`
3. Proceed with beneficiary creation if required
4. Document the `connected_account_id` and `enterprise_id` for future reference

## Additional Resources

- [Airwallex API Documentation](https://www.airwallex.com/docs/api)
- [Step 1: Preparation Guide](../1.%20preparation/README.md)
- [Postman Learning Center](https://learning.postman.com/)