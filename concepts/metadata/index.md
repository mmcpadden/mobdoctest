---
layout: default
title: Metadata
youtubeId: 5Rk2I2AwG_I
---

# Metadata

In many places, this API’s responses include blocks of metadata relating to data points contained within that response. These supplementary blocks, which are always named “_meta,” typically contain fully-qualified URLs that resolve to other API calls for retrieving related detail data.

For example, the API call for a collection of employees might return items containing just a few selected details about each employee. However, each item also contains a “_meta” block populated with a URL that can be used to obtain that specific employee’s full detail record. 

<br/>

## Video Tutorial

The following video provides a quick explanation of the “_meta” block included in most API responses — as well as explain what it typically includes.

{% include youtube.html id=page.youtubeId %}

We also recommend browsing all of our tutorials in our Video Library at [http://mobproc.tv](http://mobproc.tv). 

<br />

## Detail Record Example

Metadata blocks make it very easy for the API consumer to easily obtain additional detail data for a selected item without having to bloat the collection by supplying those same details for every item returned within that collection. 

This pattern is particularly useful for use cases such as a list page containing a “view details” button for every item in the list. The end user will likely want to scan a list and choose to find out more details about just one particular item, rather than view all the details of every item. Therefore, it is unnecessary to bloat the API response with a huge volume of detail that the end user is unlikely to want. Moreover, a smaller result set of more targeted data will enable the API to return results much faster.

Below is an illustration of a metadata block contained within a response for an employee collection.

```
{
  "_meta": {
    "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/48068931",
    "hrefDevices": "https://tngo-mobproc.cloudhub.io/mobproc/v1/assets/devices?limit=10&offset=0&employee=48068931",
    "hrefServices": "https://tngo-mobproc.cloudhub.io/mobproc/v1/assets/services?limit=10&offset=0&employee=48068931"
  },
  "companyEmployeeId": "barbara.andrews@example.com",
  "department": {
    "id": "80022",
    "name": "Marketing"
  },
  "firstName": "Barbara",
  "id": "48068931",
  "lastName": "Andrews",
  "status": "ACTIVE"
},
{
  "_meta": {
    "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/48068987",
    "hrefDevices": "https://tngo-mobproc.cloudhub.io/mobproc/v1/assets/devices?limit=10&offset=0&employee=48068987",
    "hrefServices": "https://tngo-mobproc.cloudhub.io/mobproc/v1/assets/services?limit=10&offset=0&employee=48068987"
  },
  "companyEmployeeId": "james.carpenter@example.com",
  "department": {
    "id": "80022",
    "name": "Marketing"
  },
  "firstName": "James",
  "id": "48068987",
  "lastName": "Carpenter",
  "status": "ACTIVE"
},
...
```
<br />

## Pagination Data Example
Meta blocks are not limited to providing URLs for fetching detailed data. They can also hold data that is useful for supporting paginated lists, such as a total count or an items per page limit. For example:


```
"_meta": {
  "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees?limit=10&offset=30&sortAscending=true",
  "hrefEnd": "https://tngo--mobproc.cloudhub.io/mobproc/v1/employees?sortAscending=true&offset=70&limit=10",
  "hrefNext": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees?sortAscending=true&offset=40&limit=10",
  "hrefPrevious": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees?sortAscending=true&offset=20&limit=10",
  "hrefStart": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees?sortAscending=true&offset=0&limit=10",
  "limit": 10,
  "offset": 30,
  "totalCount": 72
}
```