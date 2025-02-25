---
description: Work In progress!
hidden: true
noIndex: true
---

# 1 Click (Draft)

The purpose of 1Click API is to make it easy to interact with the NEAR Intents protocol by temporarily transferring assets to the custody of a trusted agent

To swap asset on 1Click, you need to do the following:

* Request a quote using **Get Quote** API\
  &#x20;  The quote response will contain the address where the tokens need to be transferred to.\
  &#x20;  Deposit address is generated uniquely for each swap.
* Transfer tokens to the address specified in the **Get Quote** response\
  &#x20;  Once transferred, 1Click will start the swapping process automatically.
* **Submit Deposit Tx** using deposit transaction hash\
  &#x20;   This step is optional, but it can help speeding up the process
* **Get Execution Status** using deposit address from the first step

The swap will either succeed or fail, in case of a failure, the funds will end up on the refund address and a new attempt to swap would need to be triggered by a new transfer.

## API Speicifcation

{% openapi src="https://stoplight.io/api/v1/projects/chaindefuser/1click/nodes/reference/1click.yaml?fromExportButton=true&snapshotType=http_service" path="/quote" method="post" %}
[https://stoplight.io/api/v1/projects/chaindefuser/1click/nodes/reference/1click.yaml?fromExportButton=true&snapshotType=http_service](https://stoplight.io/api/v1/projects/chaindefuser/1click/nodes/reference/1click.yaml?fromExportButton=true&snapshotType=http_service)
{% endopenapi %}

{% openapi src="https://stoplight.io/api/v1/projects/chaindefuser/1click/nodes/reference/1click.yaml?fromExportButton=true&snapshotType=http_service" path="/status" method="get" %}
[https://stoplight.io/api/v1/projects/chaindefuser/1click/nodes/reference/1click.yaml?fromExportButton=true&snapshotType=http_service](https://stoplight.io/api/v1/projects/chaindefuser/1click/nodes/reference/1click.yaml?fromExportButton=true&snapshotType=http_service)
{% endopenapi %}

{% openapi src="https://stoplight.io/api/v1/projects/chaindefuser/1click/nodes/reference/1click.yaml?fromExportButton=true&snapshotType=http_service" path="/deposit/submit" method="post" %}
[https://stoplight.io/api/v1/projects/chaindefuser/1click/nodes/reference/1click.yaml?fromExportButton=true&snapshotType=http_service](https://stoplight.io/api/v1/projects/chaindefuser/1click/nodes/reference/1click.yaml?fromExportButton=true&snapshotType=http_service)
{% endopenapi %}
