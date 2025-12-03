# Preparation: Generate KYC and KYB JSON Objects
This repository is used for the, currently, very manual process of the KYC and KYB onboarding of businesses onto Airwallex. These tools have been created to speed the process up and to share with the team.

## Quick Start Guide

Follow these steps to generate the required JSON objects for KYC/KYB onboarding:

### 1. Clone the Repository
```bash
git clone <repository-url>
cd awx-onboarding-tools
```

### 2. Prepare the CSV Data
- Export the **WT KYC-KYB(KYC-KYB)** sheet from the Excel workbook
- Save it as `WT KYC-KYB(KYC-KYB).csv`
- Place the CSV file in the `/1. preparation` folder

### 3. Generate the Onboarding JSON Object
Use your agentic IDE (e.g., Cursor) with the following prompt:

```
Generate the onboarding json for {{hotel name}} using {{CSV file}} file and the @1. preparation/account-baseline.json as the template.
```

**Example:**
```
Generate the onboarding json for Boutique Hotel Kugel Wien using WT KYC-KYB(KYC-KYB).csv file and the @1. preparation/account-baseline.json as the template.
```

This will create a file named `HotelName.json` in the `/1. preparation/output` directory.

### 4. Generate the Beneficiary Request JSON Object
Use your agentic IDE with the following prompt:

```
Generate the beneficiary json for {{hotel name}} using {{CSV file}} file and the @1. preparation/beneficiary-baseline.json as the template.
```

**Example:**
```
Generate the beneficiary json for Boutique Hotel Kugel Wien using WT KYC-KYB(KYC-KYB).csv file and the @1. preparation/beneficiary-baseline.json as the template.
```

This will create a file named `HotelNameBeneficiary.json` in the `/1. preparation/output` directory.

### 5. Ready to Onboard!
You're now ready to perform the KYC and KYB onboarding via Postman using the generated JSON files.

## Additional Resources
- See `summary.md` for detailed information about JSON structure, country-specific requirements, and field mappings
- See `account-baseline.json` for the onboarding request template
- See `beneficiary-baseline.json` for the beneficiary request template

## Tips
- The agentic IDE will automatically handle CSV-to-JSON mapping, date format conversion, and country-specific requirements
- Files are automatically saved to the `/1. preparation/output` directory
- Review the generated JSON files before submitting to ensure all placeholders are replaced with actual values
