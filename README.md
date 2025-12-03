# Airwallex KYC/KYB Onboarding Tools

This repository is used for the, currently, very manual process of the KYC and KYB onboarding of businesses onto Airwallex. These tools have been created to speed the process up and to share with the team.

## Overview

To perform KYC (Know Your Customer) and KYB (Know Your Business) onboarding, you currently have two tools available:

---

## 1. AI Instructions for JSON Generation

**Location:** [`1. preparation/`](./1.%20preparation/)

This tool helps you quickly create the JSON objects needed for KYC and KYB onboarding via Postman using an agentic IDE (like Cursor).

### How It Works:
- Export your hotel/business data from the **WT KYC-KYB(KYC-KYB)** Excel sheet as a CSV file
- Use simple AI prompts to automatically generate:
  - **Account creation JSON** using the `account-baseline.json` template
  - **Beneficiary creation JSON** using the `beneficiary-baseline.json` template
- The AI automatically handles CSV-to-JSON mapping, date format conversion, and country-specific requirements
- Generated files are saved to the `output/` directory, ready to use in Postman

**Example Prompts:**
```
Generate the onboarding json for Boutique Hotel Kugel Wien using WT KYC-KYB(KYC-KYB).csv file and the @1. preparation/account-baseline.json as the template.
```

📖 **[Read the full guide →](./1.%20preparation/README.md)**

---

## 2. Postman Collection for API Execution

**Location:** [`2. postman collection/`](./2.%20postman%20collection/)

This Postman collection provides a complete, step-by-step workflow for executing the KYC and KYB onboarding process via the Airwallex API.

### What's Included:
- **Pre-configured requests** for the entire onboarding flow:
  1. Authentication (obtain access token)
  2. Account creation
  3. KYC hosted flow creation and authorization
  4. KYB hosted flow creation and authorization
  5. Verification and status checks
- **Auto-managed variables** that pass data between requests (account IDs, flow IDs, tokens)
- **Environment templates** for both Development and Production
- **Automatic token refresh** via pre-request scripts

### Key Features:
- No manual copying/pasting of IDs between requests
- Bearer token automatically refreshes when needed
- Complete documentation for each API endpoint
- Ready-to-use environment variable templates

📖 **[Read the full guide →](./2.%20postman%20collection/README.md)**

---

## 3. Coming Soon: Test Payment Mini App

We hope to create a mini app that will allow non-developers to send test payments in development. Instead of creating fake bank transfers via Postman, a non-developer can use this mini app with a simple user interface to:
- Send test payments without API knowledge
- Simulate various payment scenarios
- Test the onboarding process end-to-end
- Validate beneficiary and account configurations

*Stay tuned for updates!*

---

## Quick Start

1. **[Step 1: Preparation](./1.%20preparation/README.md)** - Generate your JSON files using AI
2. **[Step 2: Postman Execution](./2.%20postman%20collection/README.md)** - Run the onboarding flow via Postman
3. Verify account and flow status
4. Start processing payments!

---

## Support

For questions or issues, please reach out to the team or consult the detailed README files in each directory.
