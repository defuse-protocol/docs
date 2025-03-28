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

# 1Click API

The purpose of 1Click API is to make it easy to interact with the NEAR Intents protocol by temporarily transferring assets to the custody of a trusted agent.

To swap asset on 1Click, you need to do the following:

* Request a quote using **Get Quote** API:\
  The quote response will contain the address where the tokens need to be transferred to.\
  Deposit address is generated uniquely for each swap.
* Transfer tokens to the address specified in the Get Quote response:\
  Once transferred, 1Click will start the swapping process automatically.
* **Submit Deposit Tx** using deposit transaction hash:\
  This step is optional, but it can help speeding up the process.
* **Get Execution Status** using deposit address from the first step.

The swap will either succeed or fail; in case of a failure, the funds will end up on the refund address and a new attempt to swap would need to be triggered by a new transfer.

## API Specification (v0)

The [OpenAPI spec](https://1click.chaindefuser.com/docs/v0/openapi.yaml) is made available to auto-generate clients. Client SDKs for [TypeScript](https://github.com/defuse-protocol/one-click-sdk-typescript), [Go](https://github.com/defuse-protocol/one-click-sdk-go) and [Rust](https://github.com/defuse-protocol/one-click-sdk-rs) are already available on GitHub.

{% openapi src="https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28" path="/v0/tokens" method="get" %}
[https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28](https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28-01)
{% endopenapi %}

{% openapi src="https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28" path="/v0/quote" method="post" %}
[https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28](https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28-01)
{% endopenapi %}

{% openapi src="https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28" path="/v0/status" method="get" %}
[https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28](https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28-01)
{% endopenapi %}

{% openapi src="https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28" path="/v0/deposit/submit" method="post" %}
[https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28](https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-28-01)
{% endopenapi %}
