# Risk & Compliance

## Introduction
At NEAR Intents, we are deeply committed to implementing best practices in compliance and financial integrity. 
Transparency, accountability, and adherence to international standards are not just regulatory requirements for us — they are guiding principles. 
Our goal is to ensure that all transactions routed through NEAR Intents are secure, transparent, and fully aligned with global efforts to combat money laundering, sanctions violations, and other forms of financial crime.

## Current Implementation

### 1. Real-time compliance screening for 1Click Swap API quote requests
Every quote request undergoes automated compliance checks against multiple trusted data sources:

- **NEAR Intents AML Portal** - ([aml.near-intents.org](https://aml.near-intents.org))
- **Binance AML**
- **AMLBot & PureFi**

These checks are designed to identify any overlap between addresses provided in the request and addresses flagged in external databases.

### 2. Enhanced screening for 1Click Swap API non-dry quote requests
In addition to the above, all non-dry quote requests are screened against [TRM Labs](https://www.trmlabs.com/) datasets. At present, we automatically block quote issuance for any address that TRM Labs flags with:

- Sanctions (with `riskType: OWNERSHIP`)
- Blocking behavior

When a match is detected, the quote request is immediately rejected, and the corresponding user or distribution channel receives a clear response that the quote has been blocked. 
This ensures that potentially tainted flows are contained at the very first interaction point.

