---
icon: computer-mouse
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

1Click simplifies NEAR Intents by temporarily transferring assets to a trusted swapping agent that coordinates with [Market Makers](../../market-makers/README.md) to execute your intent. This REST API abstracts away the complexity of intent creation, solver coordination, and transaction execution, letting you focus on your user experience.

It features:

- Simple REST endpoints for intent creation and management
- Automatic solver discovery and competitive pricing
- Built-in transaction handling and status tracking
- Support for cross-chain intents


## API Specification (v0)

Auto-generate clients using our [OpenAPI spec](https://explorer.near-intents.org/api/v0/openapi.yaml).

{% hint style="info" %}
Authentication: It is mantatory to [obtain a JWT token](https://docs.google.com/forms/d/e/1FAIpQLSdrSrqSkKOMb_a8XhwF0f7N5xZ0Y5CYgyzxiAuoC2g4a2N68g/viewform?usp=header) for higher rate limits, reliable service, and to help us maintain service quality.
{% endhint %}

### API Endpoints

{% openapi src="https://explorer.near-intents.org/api/v0/openapi.yaml" path="/api/v0/transactions" method="get" %}
[https://explorer.near-intents.org/api/v0/openapi.yaml](https://explorer.near-intents.org/api/v0/openapi.yaml)
{% endopenapi %}

