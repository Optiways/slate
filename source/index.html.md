---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Padam API
---

# Introduction

Welcome to the Padam API! You can use our API to access Padam API endpoints.

# Authentication

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: secret"
```

> Make sure to replace `secret` with your API key.

Padam uses API keys to allow access to the API. You can register a new Padam API key at our 
[developer portal](http://example.com/developers).

Padam expects for the API key to be included in all API requests to the server in a header that looks like the 
following:

`Authorization: secret`

<aside class="notice">
You must replace <code>secret</code> with your personal API key.
</aside>

# About the API

## Dates

In the Padam API, any date are formatted, as strings, by using the 
[ISO 8601 international standard](https://en.wikipedia.org/wiki/ISO_8601). 

As an example, the 2022, 17th of march, at noon can be written as following (in UTC):
 - `"2022-03-17T13:44:49+00:00"`

<aside class="warning">
Any date that is given to the API and that don't respect the ISO 8601 format is susceptible to cause a 
<code>Bad Request</code> error.
</aside>

## Pagination

Most of the collections of resources that are returned by the Padam API are paginated. In order to ease the display of
the results for frontend application, we use a Page Number Pagination.

See also: [Page Number Pagination with DRF](https://www.django-rest-framework.org/api-guide/pagination/#pagenumberpagination)

> Here is how to query a paginated endpoint

```shell
# The page parameter is optional. If not given, its value is `1`
curl "https://api.example.org/nodes/?page=4" \
  -H "Authorization: secret"
```

> Here is a sample of a paginated endpoint output. Results are located under the `results` key, which is an array.

```json
{
    "count": 1023,
    "next": "https://api.example.org/accounts/?page=5",
    "previous": "https://api.example.org/accounts/?page=3",
    "results": [
    ]
}
```

# Nodes

A node is the representation of a location. It is represented by coordinates and information that gives more context
about its place. Node ids can be used as `origin` and `destination` when searching for itineraries for users.

## The node object

### Attributes

| Name                  | Type     | Description                                            |
|-----------------------|----------|--------------------------------------------------------|
| id                    | `int`    | The id of the node.                                    |
| name                  | `str`    | A human readable name that can be displayed to users.  |
| coordinates           | `object` | Coordinates. Can be used to display the node on a map. |
| coordinates.longitude | `float`  | The longitude of the node.                             |
| coordinates.latitude  | `float`  | The latitude of the node.                              |

> Json representation of an object:

```json
{
  "id": 42,
  "name": "Place de la mairie",
  "coordinates": {
    "longitude": 2.466311814729767,
    "latitude": 49.192376994101174
  }
}
```

## Search among nodes

```shell
curl "http://example.com/api/nodes/search?value=place" \
  -H "Authorization: secret"
```

> The above command returns JSON structured like this:

```json
{
  "count": 1,
  "next": "http://example.com",
  "previous": "http://example.com",
  "results": [{
    "id": 42,
    "name": "Place de la mairie",
    "coordinates": {
      "longitude": 2.466311814729767,
      "latitude": 49.192376994101174
    }
  }]
}
```

This endpoint allows searching among all nodes.

### HTTP Request

`GET http://example.com/api/nodes/search`

### URL Parameters

| Parameter | Description                                      |
|-----------|--------------------------------------------------|
| value     | Optional. If given, filter against node's names. |

# Itineraries

The itinerary API allows you to search for itineraries for one or more passengers, to display propositions to them and
to validate a given proposition.

## The passenger object

Describe a passenger for an [itinerary](#the-itinerary-object). A single passenger can book a trip for one or more 
person and this object has to be used to communicate the specificities of each passenger to Padam (as an example, if 
the passenger has a wheelchair, we have to know it in order to be able to make the best propositions to it).

### Attributes

| Parameter              | Description                                                                              | Required | Example            |
|------------------------|------------------------------------------------------------------------------------------|----------|--------------------|
| email                  | The email of the passenger. I can be used to send notifications to the passenger.        | False    | `jean@example.com` |
| age                    | The age the passenger. Passenger can benefit for better prices depending of their age.   | False    | `42`               |
| subscription_card_id   | The id of a subscription card (if any). Will be used to calculate the price of the trip. | False    | `XF-CC`            |
| has_wheelchair         | Does the passenger has a wheelchair. Not all vehicles are able to transport it.          | False    | `True`             |
| is_accompanying_person | Accompanying persons are not charged the same.                                           | False    | `True`             |

<aside class="warning">
We value privacy of users so none of those fields are required. But the more details regarding your passengers you give
to us, more accurate the propositions will be.
</aside>

## The itinerary object

At Padam, a search performed by a user to go from an `origin` to a `destination` is called an `itinerary`. Each 
search for an `itinerary` is saved into our database, has an `id` and is related to `propositions` that you can display
to users.

<aside class="notice">
Validating a <code>proposition</code> require both <code>itinerary</code> and <code>proposition</code> ids.
</aside>

### Attributes

| Name           | Type    | Description                                                                                             |
|----------------|---------|---------------------------------------------------------------------------------------------------------|
| id             | `str`   | The id of the itinerary (as a uuid).                                                                    |
| origin         | `Node`  | The node from which the user want to go. Please refer to [The node object](#the-node-object).           |
| destination    | `Node`  | The node to which the user want to go. Please refer to [The node object](#the-node-object).             |
| departure_time | `str`   | The time at which the user desire to start its journey.                                                 |
| arrival_time   | `str`   | The time at which the user desire to arrive.                                                            |
| passengers     | `array` | The passengers that want to book a trip. Please refer to [The passenger object](#the-passenger-object). |

> Json representation of an object:

```json
{
  "id": "0b2c0e76-7ecc-42c8-9239-913a4e5f5544",
  "origin": 42,
  "destination": 43,
  "departure_time": "2022-03-16T09:56:49Z",
  "arrival_time": null,
  "passengers": [{
    "email": "jean@example.com"
  },{
  }]
}
```

<aside class="notice">
Regarding the <code>passengers</code> attribute:
<ul>
  <li>The <code>passengers</code> array cannot be empty. <strong>At least one item is required</strong>.</li>
  <li>Even if you don't want to provide details regarding your passengers, each passenger has to be represented as an empty array to be considered by the system.</li>
</ul>
</aside>

## Create an itinerary search

This endpoint creates a new `itinerary` in our system. You can use the `id` of this itinerary to retrieve 
`propositions` that can be displayed to users.

```shell
curl -X "http://example.com/api/itineraries" \
-H "Authorization: secret" \
-H "accept: application/json" -H "Content-Type: application/json"\ 
-d '{\
  "origin_id": 42,\ 
  "destination_id": 43,\ 
  "departure_time": "2022-03-16T09:56:49Z",\
  "passengers": [{}]\
}'
```

> The above command returns JSON structured like this:

```json
{
  "id": "0b2c0e76-7ecc-42c8-9239-913a4e5f5544"
}
```

### HTTP Request

`POST http://example.com/itineraries/`

### Parameters

| Parameter      | Description                    | Required | Example                                                       |
|----------------|--------------------------------|----------|---------------------------------------------------------------|
| origin         | The ID of the departure node   | True     | Please refer to [The node object](#the-node-object)           |
| destination    | The ID of the destination node | True     | Please refer to [The node object](#the-node-object)           |
| departure_time | The desired departure time     | False    | `2022-03-16T09:56:49Z`                                        |
| arrival_time   | The desired arrival time       | False    | `2022-03-16T09:56:49Z`                                        |
| passengers     | An array of passengers         | True     | Please refer to [The passenger object](#the-passenger-object) |

<aside class="warning">None of the <code>departure_time</code> or the <code>arrival_time</code> is required but at least one must be provided.</aside>

## The proposition object

Each itinerary is related to few propositions (or none, which mean that we cannot serve the trip).  

### Attributes

| Name                     | Type    | Description                                                                                                                                                    |
|--------------------------|---------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id                       | `str`   | The id of the proposition (as a uuid).                                                                                                                         |
| itinerary                | `str`   | The id of the itinerary that is related to this proposition.                                                                                                   |
| departure_estimated_time | `str`   | The time at which we estimate that we can pick up the passenger(s).                                                                                            |
| arrival_estimated_time   | `str`   | The time at which we estimate that we can drop off the passenger(s).                                                                                           |
| estimated_price          | `float` | The price that we estimated the passengers will have to pay. This price may be reduced, at the payment step, if more details (such as discount car) are given. |

> Json representation of an object:

```json
{
  "id": "0b2c0e76-7ecc-42c8-9239-913a4e5f5544",
  "itinerary": "0b2c0e76-7ecc-42c8-9239-913a4e5f5545",
  "departure_estimated_time": "2022-03-16T09:56:49Z",
  "arrival_estimated_time": "2022-03-16T09:56:49Z",
  "estimated_price": 42.50
}
```

## Retrieve itinerary propositions

This endpoint creates a new `itinerary` in our system. You can use the `id` of this itinerary to retrieve 
`propositions` that can be displayed to users.

This endpoint allows to list all the `propositions` that are related to an `itinerary` search. You can list to users
those `propositions` and let him choose one. 

```shell
curl -X "http://example.com/api/itineraries/<ID>/propositions" \
-H "Authorization: secret"
```

> The above command returns JSON structured like this:

```json
{
  "count": 1,
  "next": "http://example.com",
  "previous": "http://example.com",
  "results": [{
    "id": "0b2c0e76-7ecc-42c8-9239-913a4e5f5544",
    "itinerary": "0b2c0e76-7ecc-42c8-9239-913a4e5f5545",
    "departure_estimated_time": "2022-03-16T09:56:49Z",
    "arrival_estimated_time": "2022-03-16T09:56:49Z"
  }]
}
```

### HTTP Request

`GET http://example.com/itineraries/<ID>/propositions`

## Validate a Proposition

### HTTP Request

`POST http://example.com/itineraries/<ID>/propositions/<ID>/validate`
