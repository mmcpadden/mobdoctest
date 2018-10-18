---
layout: default
title: Release 1.3.6
---


# Release 1.3.6


<br/>

## New Features

### Transferring Ownership of a Service Asset  
Three new order transaction types were added that enable transferring ownership of a service asset from corporate to personal and vice versa.
* ***Transfer Service Out*** (XFER_SERVICE_OUT) – Used to transfer ownership from a corporate/business account to a personal account.
* ***Transfer Service In*** (XFER_SERVICE_IN) – Used to transfer ownership from a personal account to a corporate/business account. This transaction type requires that the business owner supports the same service provider (i.e., carrier). If the carrier is not supported, the <a href="{{site.url}}/tutorials/orders/xfer_service_in_and_port/">Transfer Service In and Port</a> transaction type should be used instead.
* ***Transfer Service In and Port*** (XFER_SERVICE_IN_AND_PORT) – Used to transfer ownership from a personal account to a corporate/business account where the business owner does NOT support the same carrier. In this case, service must be ported to a new carrier.

### Acknowledgements
This feature enables the new order transaction types for transferring ownership of a service asset from corporate to personal and vice versa.
* ***/acknowledgements*** – This new resource is used to present the user with the information and acknowledgements that the vendor requires to be disclosed to the user when placing an order to transfer ownership. Some acknowledgments might require acceptance by the user in order for an order to be accepted by the vendor.


<br/>
<br/>

## Fixed Issues

(N/A)
