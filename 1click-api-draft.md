---
description: Work In progress!
hidden: true
noIndex: true
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: false
  outline:
    visible: false
  pagination:
    visible: false
---

# 1Click API (Draft)

The purpose of 1Click API is to make it easy to interact with the NEAR Intents protocol by temporarily transferring assets to the custody of a trusted agent.

To swap asset on 1Click, you need to do the following:

* Request a quote using **Get Quote** API:\
  &#x20;  The quote response will contain the address where the tokens need to be transferred to.\
  &#x20;  Deposit address is generated uniquely for each swap.
* Transfer tokens to the address specified in the Get Quote response:\
  &#x20;  Once transferred, 1Click will start the swapping process automatically.
* **Submit Deposit Tx** using deposit transaction hash:\
  &#x20;   This step is optional, but it can help speeding up the process.
* **Get Execution Status** using deposit address from the first step.

The swap will either succeed or fail; in case of a failure, the funds will end up on the refund address and a new attempt to swap would need to be triggered by a new transfer.

## API Specification (2025-03-28)



{% openapi src="https://1click.chaindefuser.com/docs/v0/openapi.yaml" path="/v0/tokens" method="get" %}
[https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28](https://1click.chaindefuser.com/docs/v0/openapi.yaml)
{% endopenapi %}

{% openapi src="https://1click.chaindefuser.com/docs/v0/openapi.yaml" path="/v0/quote" method="post" %}
[https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28](https://1click.chaindefuser.com/docs/v0/openapi.yaml)
{% endopenapi %}

{% openapi src="https://1click.chaindefuser.com/docs/v0/openapi.yaml" path="/v0/status" method="get" %}
[https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28](https://1click.chaindefuser.com/docs/v0/openapi.yaml)
{% endopenapi %}

{% openapi src="https://1click.chaindefuser.com/docs/v0/openapi.yaml" path="/v0/deposit/submit" method="post" %}
[https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28](https://1click.chaindefuser.com/docs/v0/openapi.yaml)
{% endopenapi %}

