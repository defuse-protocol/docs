---
icon: book
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: false
  pagination:
    visible: false
---

# Intents Explorer API

## What is Intents Explorer API

The **Intents Explorer API** provides programmatic access to historical 1Click Swap transactions and their statuses, mirroring the data available on the [NEAR Intents Explorer](https://explorer.near-intents.org/). This API is read-only and features a single HTTP GET endpoint designed specifically for distribution channels and analytical services to fetch and filter 1Click Swap history.

### Key Features

- The API is tailored exclusively for 1Click Swap transactions and is not intended for general NEAR Intents activity.
- Retrieve and filter swaps by token symbols, transaction hashes, timestamps, and status, enabling precise integration with your analytics or dashboarding tools.
- Mirrors the same real-time and historical swap data shown in the [NEAR Intents Explorer](https://explorer.near-intents.org/) interface.

### Documentation & Access
- **Swagger UI**: [https://explorer.near-intents.org/api/docs](https://explorer.near-intents.org/api/docs)  
- **OpenAPI Spec (YAML)**: [https://explorer.near-intents.org/api/v0/openapi.yaml](https://explorer.near-intents.org/api/v0/openapi.yaml)
- **API Key Form**: [https://docs.google.com/forms/d/e/1FAIpQLSdrSrqSkKOMb_a8XhwF0f7N5xZ0Y5CYgyzxiAuoC2g4a2N68g/viewform](https://docs.google.com/forms/d/e/1FAIpQLSdrSrqSkKOMb_a8XhwF0f7N5xZ0Y5CYgyzxiAuoC2g4a2N68g/viewform)

## API Specification (v0)

Auto-generate clients using our [OpenAPI spec](https://explorer.near-intents.org/api/v0/openapi.yaml).

{% hint style="info" %}
Authentication: It is mantatory to [obtain a JWT token](https://docs.google.com/forms/d/e/1FAIpQLSdrSrqSkKOMb_a8XhwF0f7N5xZ0Y5CYgyzxiAuoC2g4a2N68g/viewform) for higher rate limits, reliable service, and to help us maintain service quality.
{% endhint %}

### API Endpoints

{% openapi src="https://explorer.near-intents.org/api/v0/openapi.yaml" path="/api/v0/transactions" method="get" %}
[https://explorer.near-intents.org/api/v0/openapi.yaml](https://explorer.near-intents.org/api/v0/openapi.yaml)
{% endopenapi %}

