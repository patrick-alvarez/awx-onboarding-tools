# Airwallex Onboarding JSON Generation Guide

## Overview
This guide explains how to create Airwallex onboarding request bodies and beneficiary request objects for hotels/businesses in different countries based on CSV data and country-specific KYC requirements.

There are two types of JSON objects you'll need to create:
1. **Onboarding Request** - For creating connected accounts (KYC/KYB)
2. **Beneficiary Request** - For registering payment beneficiaries

## Process

### 1. Understand the Structure

#### For Onboarding Requests
- **Baseline.json**: Template for general onboarding structure
- **Country-specific templates**: France.json, Germany.json, Netherlands.json, etc.
- **CSV Data**: Source of truth for actual business and person information

#### For Beneficiary Requests
- **BeneficiaryBaseline.json**: Template for beneficiary structure
- **CSV Data**: Beneficiary columns (City, Country Code, postcode, street address, IBAN, etc.)
- **Naming Convention**: `{HotelName}Beneficiary.json` (e.g., `FaultyTowersBeneficiary.json`)

### 2. Find Country-Specific Requirements
Each country has specific KYC requirements from Airwallex documentation:
- **Documentation URL Pattern**: `https://www.airwallex.com/docs/payments-for-platforms__onboard-connected-accounts__kyc-and-onboarding__native-api__business-kyc-requirements__[country-code]`
  - Example: France (FR), Germany (DE), Netherlands (NL), Ireland (IE), Belgium (BE), Austria (AT)

### 3. Key Country-Specific Differences

#### Business Identifiers
- **France**: SIREN number, type: `BRN`, country_code: `FR`
- **Germany**: Nummer der Firma (e.g., HRB101811), type: `BRN`, country_code: `DE`
- **Netherlands**: RSIN number, type: `BRN`, country_code: `NL`
- **Belgium**: Enterprise number (e.g., 0833431225), type: `BRN`, country_code: `BE`
- **Ireland**: Company number, type: `BRN`, country_code: `IE`
- **Austria**: Registration number, type: `BRN`, country_code: `AT`

#### Business Document Tags
- **COMPANY**: Use `COMPANY_CERTIFICATE` tag
- **PARTNERSHIP**: Use `COMPANY_CERTIFICATE` and `PARTNERSHIP_AGREEMENT` tags
- **SOLE_PROPRIETOR**: Use `COMPANY_CERTIFICATE` or `CONFIRMATION_STATEMENT` tag

#### Business Structures
- `COMPANY` - Private Limited Company (most common)
- `PARTNERSHIP` - Partnership structures
- `SOLE_PROPRIETOR` - Sole proprietorships

#### Personal Identification
All countries require:
- `identification_type`: `PERSONAL_ID`, `PASSPORT`, or `DRIVERS_LICENCE`
- `issuing_country_code`: Match the person's nationality
- `front_file_id` and `back_file_id` (for PERSONAL_ID and DRIVERS_LICENCE)
- `front_file_id` only (for PASSPORT)

### 4. Required Fields (All Countries)

#### Business Details
```json
{
  "business_name": "Company Name",
  "business_structure": "COMPANY | PARTNERSHIP | SOLE_PROPRIETOR",
  "business_identifiers": [{
    "country_code": "XX",
    "number": "registration_number",
    "type": "BRN"
  }],
  "registration_address": { /* Full address with state */ },
  "business_address": { /* Full address with state */ },
  "operating_country": ["XX"],
  "industry_category_code": "ICCV3_000300",
  "description_of_goods_or_services": "Description",
  "url": "https://website.com",
  "account_usage": {
    "estimated_monthly_revenue": {
      "amount": "100000",
      "currency": "EUR"
    },
    "product_reference": ["RECEIVE_TRANSFERS"]
  }
}
```

#### Business Person Details
```json
{
  "first_name": "John",
  "last_name": "Doe",
  "date_of_birth": "1970-01-01", // Format: YYYY-MM-DD
  "nationality": "XX", // 2-letter country code
  "email": "email@example.com",
  "residential_address": { /* Full address with state */ },
  "roles": ["DIRECTOR", "BENEFICIAL_OWNER", "AUTHORISED_PERSON"],
  "identifications": { /* See above */ }
}
```

#### Roles by Business Structure
- **COMPANY**: `["DIRECTOR", "BENEFICIAL_OWNER", "AUTHORISED_PERSON"]`
- **PARTNERSHIP**: `["BENEFICIAL_OWNER", "AUTHORISED_PERSON"]`
- **SOLE_PROPRIETOR**: `["BENEFICIAL_OWNER", "AUTHORISED_PERSON"]`

### 5. Critical Fields

#### MUST be uppercase
- `legal_entity_type`: Always `"BUSINESS"` (not "Business")

#### MUST include state field
All addresses MUST have a `state` field:
- France: "Île-de-France", "Provence-Alpes-Côte d'Azur", etc.
- Germany: "Berlin", "Bavaria", "North Rhine-Westphalia", etc.
- Netherlands: "North Holland", "South Holland", "Utrecht", etc.
- Belgium: "Brussels", "Antwerp", "Flemish Brabant", "Namur", etc.
- Ireland: "Dublin", "Cork", "Galway", etc.
- Austria: "Vienna", "Salzburg", "Tyrol", etc.

#### Date Format
- CSV format: `DD/MM/YYYY` (e.g., "30/08/1992")
- JSON format: `YYYY-MM-DD` (e.g., "1992-08-30")

#### Identifier Field
- Maps to CSV column: `Enterprise ID`
- Critical: This field is essential for tracking

### 6. CSV to JSON Mapping

| CSV Column | JSON Path |
|------------|-----------|
| Enterprise ID | `identifier` |
| First Name | `account_details.business_person_details[0].first_name` |
| Last Name | `account_details.business_person_details[0].last_name` |
| Date of Birth | `account_details.business_person_details[0].date_of_birth` |
| Nationality | `account_details.business_person_details[0].nationality` |
| Residential Address | `account_details.business_person_details[0].residential_address` |
| Email | `account_details.business_person_details[0].email` |
| Business name | `account_details.business_details.business_name` |
| Business structure | `account_details.business_details.business_structure` |
| Registration number | `account_details.business_details.business_identifiers[0].number` |
| Description of goods/services | `account_details.business_details.description_of_goods_or_services` |
| Operating country | `account_details.business_details.operating_country[0]` |
| Website / URL | `account_details.business_details.url` |
| Estimated monthly revenue | `account_details.business_details.account_usage.estimated_monthly_revenue.amount` |
| Registered address | `account_details.business_details.registration_address` |
| Operating/business address | `account_details.business_details.business_address` |

### 7. Common Placeholders
Always replace these before submission:
- `{{file_id}}` - Document file IDs from Salesforce/SharePoint
- `{{id_number}}` - Personal identification number
- `{{$guid}}` - Enterprise ID from CSV
- `{{email}}` - Contact email addresses

### 8. File Naming Convention
Use the hotel/business name without special characters:
- "Glück in Sicht Ostseelodges" → `GluckinSichtOstseelodges.json`
- "Jordans Untermühle" → `JordansUntermuehle.json`
- "Hotel Le 830" → `HotelLe830.json`

### 9. Quality Checklist
- [ ] `legal_entity_type` is `"BUSINESS"` (uppercase)
- [ ] All addresses have `state` field
- [ ] Date format is `YYYY-MM-DD`
- [ ] `identifier` matches Enterprise ID from CSV
- [ ] `business_structure` matches CSV (COMPANY/PARTNERSHIP/SOLE_PROPRIETOR)
- [ ] Roles match business structure
- [ ] Country codes are 2-letter uppercase (DE, FR, NL, etc.)
- [ ] Personal ID has both `front_file_id` and `back_file_id` (except passport)
- [ ] Website has `https://` prefix
- [ ] Registration number format matches country requirements
- [ ] Email addresses are valid
- [ ] All currency is `"EUR"` for European countries

### 10. Examples Created
- **France**: `France.json` (template)
- **Germany**: `Germany.json` (template), `Rheinhof.json`, `GluckinSichtOstseelodges.json`, `HotelAndanteRust.json`, `JordansUntermuehle.json`
- **Netherlands**: `Netherlands.json` (template), `Bommelje.json`
- **Belgium**: `HotelLe830.json`, `HotelHofvanAragon.json`
- **Ireland**: `AirwallexGoLive.json`, `AirwallexDemoHotel.json`
- **Austria**: `PanoramaGuggenbichl.json`

### 11. Resources
- Airwallex API Documentation: https://www.airwallex.com/docs/api
- Country KYC Requirements: Follow the URL pattern in section 2
- CSV Data: `WT KYC-KYB(KYC-KYB).csv` (latest version with complete data)

### 12. Beneficiary Request Objects

Beneficiary requests are simpler than onboarding requests and are used to register payment recipients.

#### Structure
```json
{
    "request_id": "{{$guid}}",
    "beneficiary": {
        "address": {
            "city": "City Name",
            "country_code": "XX",
            "postcode": "12345",
            "street_address": "Street Address"
        },
        "bank_details": {
            "account_currency": "EUR",
            "account_name": "Account Holder Name",
            "bank_country_code": "XX",
            "iban": "XX00000000000000000000"
        },
        "company_name": "Company Name",
        "entity_type": "COMPANY",
        "first_name": "First",
        "last_name": "Last"
    },
    "transfer_methods": ["LOCAL"],
    "nickname": "Display Name"
}
// Enterprise ID: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### CSV to Beneficiary JSON Mapping

| CSV Column | JSON Path |
|------------|-----------|
| City | `beneficiary.address.city` |
| Country Code | `beneficiary.address.country_code` |
| postcode | `beneficiary.address.postcode` |
| street address | `beneficiary.address.street_address` |
| Account currency | `beneficiary.bank_details.account_currency` |
| Account name | `beneficiary.bank_details.account_name` |
| bank country code | `beneficiary.bank_details.bank_country_code` |
| iBAN | `beneficiary.bank_details.iban` (remove spaces) |
| Company Name | `beneficiary.company_name` |
| entity type | `beneficiary.entity_type` (see mapping below) |
| First name | `beneficiary.first_name` |
| Last name | `beneficiary.last_name` |
| Transfer methods | `transfer_methods` (array) |
| Nickname | `nickname` |
| Enterprise ID | Comment at bottom of file |

#### Critical Beneficiary Rules

**Entity Type Mapping** (Different from onboarding!)
- CSV: `"Private Limited Company"` → JSON: `"COMPANY"`
- CSV: `"Partnership"` → JSON: `"COMPANY"`
- CSV: `"Sole Proprietorship"` → JSON: `"PERSONAL"`

**IMPORTANT**: `entity_type` can ONLY be:
- `"COMPANY"` - For companies and partnerships
- `"PERSONAL"` - For sole proprietors and individuals

**IBAN Formatting**
- Remove all spaces from IBAN in CSV
- Example: `"BE83 0689 3480 7215"` → `"BE83068934807215"`

**Transfer Methods**
- Usually `["LOCAL"]` for European transfers
- Must be an array

**Enterprise ID Comment**
- Add as comment at bottom: `// Enterprise ID: xxx`
- Note: Comments are not valid JSON but useful for documentation

#### Beneficiary Examples Created
- `HotelLe830Beneficiary.json` (Belgium, COMPANY)
- `RheinhofBeneficiary.json` (Germany, COMPANY)
- `HotelHofvanAragonBeneficiary.json` (Belgium, COMPANY)
- `GluckinSichtOstseelodgesBeneficiary.json` (Germany, COMPANY)
- `HotelAndanteRustBeneficiary.json` (Germany, PARTNERSHIP → COMPANY)
- `JordansUntermuhleBeneficiary.json` (Germany, COMPANY)

### 13. Workflow Summary

**For Onboarding Requests:**
1. Find country template or create from Baseline.json
2. Map CSV data to JSON fields
3. Verify country-specific requirements (business identifiers, document tags)
4. Check all addresses have state field
5. Ensure `legal_entity_type` is `"BUSINESS"` (uppercase)
6. Match roles to business structure type
7. Convert dates to YYYY-MM-DD format
8. Save as `{HotelName}.json` in the /output directory

**For Beneficiary Requests:**
1. Use BeneficiaryBaseline.json as template
2. Map CSV beneficiary columns to JSON fields
3. Map business structure to entity_type (COMPANY or PERSONAL only)
4. Remove spaces from IBAN
5. Add Enterprise ID as comment at bottom
6. Save as `{HotelName}Beneficiary.json` in the /output directory

### 14. Notes
- Empty fields in CSV should be filled with placeholders or requested from the client
- Partnership structures don't typically have DIRECTOR role in onboarding requests
- Partnerships use `entity_type: "COMPANY"` in beneficiary requests (not PARTNERSHIP)
- Sole proprietorships use owner's name as business name in onboarding
- Sole proprietorships use `entity_type: "PERSONAL"` in beneficiary requests
- Always verify state/province names match actual geography
- Some hotels may have empty IBAN fields in CSV - leave as empty string `""`
