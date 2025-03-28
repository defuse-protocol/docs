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

The purpose of 1Click is to make it easy to use NEAR Intents by temporarily transferring assets to the custody of a trusted agent:

* Request a quote using **Get Quote** endpoint:\
  The response will contain a deposit address, unique for each quote
* Transfer tokens to the deposit address:\
  Once transferred, 1Click will start the swapping process automatically.
* **Submit Deposit Tx** using deposit transaction hash:\
  This step is optional, but it can help speeding up the process.
* **Get Execution Status** to see the progress.

The swap will either succeed or fail; in case of a failure, the funds will end up on the refund address and a new attempt to swap would need to be triggered by repeating this flow.

## API Specification (v0)

The [OpenAPI spec](https://1click.chaindefuser.com/docs/v0/openapi.yaml) is made available to auto-generate clients. Client SDKs for [TypeScript](https://github.com/defuse-protocol/one-click-sdk-typescript), [Go](https://github.com/defuse-protocol/one-click-sdk-go) and [Rust](https://github.com/defuse-protocol/one-click-sdk-rs) are already available on GitHub.

{% openapi src="https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-29-01" path="/v0/tokens" method="get" %}
[https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-29-01](https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-29-01)
{% endopenapi %}

{% openapi src="https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-29-01" path="/v0/quote" method="post" %}
[https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-29-01](https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-29-01)
{% endopenapi %}

{% openapi src="https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-29-01" path="/v0/status" method="get" %}
[https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-29-01](https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-29-01)
{% endopenapi %}

{% openapi src="https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-29-01" path="/v0/deposit/submit" method="post" %}
[https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-29-01](https://1click.chaindefuser.com/docs/v0/openapi.yaml?2025-03-29-01)
{% endopenapi %}
