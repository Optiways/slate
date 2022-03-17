---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript

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

Welcome to the Padam API! You can use our API to access Padam API endpoints, *which can get information on various 
cats, kittens, and breeds in our database* (**TODO**).

We have language bindings in Shell, Java, Python, and JavaScript! You can view code examples in the dark area to the 
right, and you can switch the programming language of the examples with the tabs in the top right.

# Authentication

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('secret')
```

```python
import padam

api = padam.authorize('secret')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here" \
  -H "Authorization: secret"
```

```javascript
const padam = require('padam');

let api = padam.authorize('secret');
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

# Nodes

## Search among nodes

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```shell
curl "http://example.com/api/nodes/search" \
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
      "longitude": 2.466311814729767,
      "latitude": 49.192376994101174
    }]
}
```

This endpoint allows to search among all nodes.

### HTTP Request

`GET http://example.com/api/nodes/search`

### URL Parameters

Parameter | Description
--------- | -----------
value | Optional. If given, filter against node's names.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

# Itineraries

## Create an itinerary search

This endpoint creates a new itinerary search in our system. You can use the id of this itinerary to retrieve the 
propositions that you can display to your users.

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

Parameter | Description | Required | Example
--------- | ----------- |----------| -------
origin | The ID of the departure place | True     |
destination | The ID of the destination place | True     |
departure_time | The desired departure time | False    | `2022-03-16T09:56:49Z`
arrival_time | The desired arrival time | False    | `2022-03-16T09:56:49Z`
passengers | Informations regarding the passengers | False    |

<aside class="warning">None of the <code>departure_time</code> or the <code>arrival_time</code> is required but at least one must be provided.</aside>

## Retrieve itinerary propositions

### HTTP Request

`GET http://example.com/itineraries/<ID>/propositions`

## Validate a Proposition

### HTTP Request

`POST http://example.com/itineraries/<ID>/propositions/<ID>/validate`
