# ascribe REST API

<pre>
  Title: ascribe REST API
  Description: Main RESTful API for ascribe ownership web service. https://www.ascribe.io
  First public release: 2015-04-26
  Current public release: 2015-07-01, version 0.1
</pre>

## Table of Contents

- [API Documentation](#api-documentation)
    - [Overview](#overview)
    - [Authorization Flow](#authorization-flow)
    - [Register your Application or Marketplace](#register-your-application-or-marketplace)
- [SPOOL API](#spool-api)
    - [Pieces](#pieces)
    - [Editions](#editions)
    - [Register](#register)
    - [Transfer](#transfer)
    - [Consign](#consign)
    - [Unconsign](#unconsign)
    - [Loan](#loan)
- [Similarity Search API](#similarity-search-api)
    - [Photos](#photos)
    
## API Documentation

### Overview
Integration has these actions:

1. Register your app/marketplace and request an access token. This is a one time action.
2. Register a piece.
3. Check the status of a piece.

### Authorization Flow
Let's call the marketplace Makx.

As authentication is used to connect and transfer data between Makx and ascribe, we
use an encrypted connection (HTTPS).

Ascribe authorizes applications via tokens. Each token is an alphanumeric that
encodes the following information:
- The ID of the application that was granted access.
- The ID of the user who granted access to personal data.
- A set of actions available to the application.

![Authorization Flow PNG](https://s3-us-west-2.amazonaws.com/ascribe0/public/rest_doc/ascribe_api_workflow.png)

### Register your Application or Marketplace
The developer (e.g. admin@makx.com) should have an [ascribe account](https://www.ascribe.io), 
login and go to settings>API settings to register
the application or marketplace with following fields:
- Name: `name of the marketplace`

The user can then create a token of type 'Bearer'.

## SPOOL API

SPOOL is the Secure Public Online Ownership Ledger. 
An in-depth specification of the protocol can be found on [https://github.com/ascribe/spool](https://github.com/ascribe/spool) 

### Pieces

#### Create a piece

When creating a piece with our API, the piece is automatically registered on the blockchain. For marketplaces that 
act as a middle-man for registering pieces on ascribe.io, it is possible to register the piece as a consignee (someone 
that has the right to sell the piece on the owner's behalf).

##### HTTP Request
`POST https://www.ascribe.io/api/pieces/`

##### HTTP Headers
`Authorization: Bearer <access_token>`


##### Arguments
Parameter | Description
----------|------------
file_url | `<url>` The url of the digital file
title | `<string>`  The title of the artwork
artist_name | `<string>` The artist name
date_created | `(optional) <YYYY-MM-DD>` The creation date
num_editions | `(optional) <int>` The number of editions (will create as many pieces)
consign | `(optional) <boolean>` Set to True if the marketplace acts as a consignee

If you don't specify `num_editions` there will be no editions associated with the piece. 
You can specify the number of editions for the piece later (see Create number of editions)

##### Example Request
```shell
curl -X POST https://www.ascribe.io/api/pieces/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
     -d file_url=https://ascribe0.s3.amazonaws.com/local/admin@makx.com/elmo/digitalworkfile/elmo.jpg 
     -d title='New Piece' 
     -d artist_name='New Artist'
```
##### Example Response
```json
{
  "notification": "You have successfully registered \"New Piece\" by New Artist, 1 editions.",
  "success": true,
  "edition": {
    "id": 8542,
    "title": "New Piece",
    "artist_name": "New Artist",
    "num_editions": 1,
    "user_registered": "foo2",
    "datetime_registered": "2015-07-01T13:40:28.635Z",
    "date_created": "2015-01-01",
    "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/local/foo2/ascribe_animated_medium/thumbnail/ascribe_animated_medium.gif.png",
    "license_type": {
      "name": "Attribution without restriction",
      "code": "default",
      "organization": "ascribe",
      "url": "https://www.ascribe.io/faq/#legals"
    },
    "edition_number": 0,
    "bitcoin_id": "13Ftd8yNMJPwRA9EaYpctd2TSjKgi9duoz",
    "acl": [
      "edit",
      "consign",
      "transfer",
      "loan",
      "share",
      "download",
      "view",
      "delete",
      "coa"
    ],
    "request_action": null,
    "parent": 8532
  }
}
```

#### List all pieces

##### HTTP Request
`GET https://www.ascribe.io/api/pieces/?page=<number>&page_size=<number>&search=<query>&ordering=<order>`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
page | `(optional) <int>` The pagination number
page_size | `(optional) <int>` Number of results per page
search | `(optional) <string>` Search fields are `title`, `artist_name`
ordering | `(optional) <string>` Ordering fields are `title` (reverse order with `-title`), `artist_name`, `datetime_created`, `edition_number`. 

##### Example Request
```shell
curl https://www.ascribe.io/api/pieces/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
     -d page_size=10 
     -d page=1 
     -d search=Elm
     -d ordering=-edition_number
```

##### Example Response


```json
{
  "success": true,
  "count": 3,
  "next": null,
  "previous": null,
  "pieces": [
    {
      "id": 8530,
      "title": "art1",
      "artist_name": "art1",
      "num_editions": 5,
      "user_registered": "foo2",
      "datetime_registered": "2015-07-01T13:33:38.390154Z",
      "date_created": "2015-01-01",
      "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png",
      "license_type": {
        "name": "Attribution without restriction",
        "code": "default",
        "organization": "ascribe",
        "url": "https://www.ascribe.io/faq/#legals"
      }
    },
    ...
  ]
}
```

#### Retrieve a piece

##### HTTP Request
`GET https://www.ascribe.io/api/pieces/<id>/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
id | `<integer>` The ID of the piece

##### Example Request
```shell
curl https://www.ascribe.io/api/pieces/8530/
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```

##### Example Response
```json
{
  "piece": {
    "id": 8530,
    "title": "art1",
    "artist_name": "art1",
    "num_editions": 5,
    "user_registered": "foo2",
    "datetime_registered": "2015-07-01T13:33:38.390154Z",
    "date_created": "2015-01-01",
    "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png",
    "license_type": {
      "name": "Attribution without restriction",
      "code": "default",
      "organization": "ascribe",
      "url": "https://www.ascribe.io/faq/#legals"
    },
    "extra_data": {},
    "digital_work": {
      "id": 1890,
      "url": "https://d1qjsxua1o9x03.cloudfront.net/local/foo2/images-5/digitalwork/images-5.jpeg",
      "url_safe": "https://d1qjsxua1o9x03.cloudfront.net/local%2Ffoo2%2Fimages-5%2Fdigitalwork%2Fimages-5.jpeg",
      "mime": "image",
      "hash": "39971523c58d1d605b6b3f0e96803c25",
      "encoding_urls": null,
      "isEncoding": 0
    },
    "other_data": null
  },
  "success": true
}
```

#### Retrieve all editions of a piece

##### HTTP Request
`GET https://www.ascribe.io/api/pieces/<id>/editions/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
id | `<integer>` The ID of the piece

##### Example Request
```shell
curl https://www.ascribe.io/api/pieces/8553/editions/
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```

##### Example Response
```json
  "success": true,
  "count": 50,
  "next": null,
  "previous": null,
  "editions": [
    {
      "id": 8530,
      "title": "art1",
      "artist_name": "art1",
      "num_editions": 50,
      "user_registered": "foo",
      "datetime_registered": "2015-07-01T09:48:51.037Z",
      "date_created": "2015-01-01",
      "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png",
      "edition_number": 0,
      "bitcoin_id": "1CAiGhr6dSmrABAJud47PaUtZ8qvn88Tc4",
      "acl": [
        "share",
        "download",
        "view"
      ],
      "request_action": null,
      "parent": 8530
    },
    ...
  ]
}    
```

### Editions

#### Create number of editions

##### HTTP Request
`POST https://www.ascribe.io/api/editions/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
piece_id | `<int>` The ID of the piece
num_editions | `<int>` Number of editions for the piece

##### Example Request
```shell
curl -X POST https://www.ascribe.io/api/editions/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
     -d piece_id=1071 
     -d num_editions=10 
```

##### Example Response
```json
{
  "success": true,
  "notification": "You successfully registered 10 editions for piece with ID 1ADJ5fYt1Hq4acL3e7gVr7st6SD8rZq51d."
}
```

#### List all editions

##### HTTP Request
`GET https://www.ascribe.io/api/editions/?page=<number>&page_size=<number>`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
page | `(optional) <int>` The pagination number
page_size | `(optional) <int>` Number of results per page

##### Example Request
```shell
curl -G https://www.ascribe.io/api/editions/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
     -d page_size=10 
     -d page=1 
```

##### Example Response
```json
{
  "success": true,
  "count": 62,
  "next": "https://www.ascribe.io/api/editions/?page=2&page_size=1",
  "previous": null,
  "editions": [
    {
      "id": 8530,
      "title": "art1",
      "artist_name": "art1",
      "num_editions": 50,
      "user_registered": "foo",
      "datetime_registered": "2015-07-01T09:48:51.037Z",
      "date_created": "2015-01-01",
      "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png",
      "edition_number": 0,
      "bitcoin_id": "1CAiGhr6dSmrABAJud47PaUtZ8qvn88Tc4",
      "acl": [
        "edit",
        "consign",
        "transfer",
        "loan",
        "share",
        "download",
        "view",
        "delete",
        "coa"
      ],
      "request_action": null,
      "parent": 8530
    },
    ...
  ]
}
```

#### Retrieve an edition

##### HTTP Request
`GET https://www.ascribe.io/api/editions/<bitcoin_id>`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_id | `<string>` The bitcoin ID of the edition

##### Example Request
```shell
curl https://www.ascribe.io/api/editions/1N1kaRd7A3vYR4d1sfysPHQMMURoHCtmWY/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```

##### Example Response
```json
{
  "edition": {
    "id": 8531,
    "title": "art1",
    "artist_name": "art1",
    "num_editions": 50,
    "user_registered": "foo",
    "datetime_registered": "2015-07-01T09:48:51.037Z",
    "date_created": "2015-01-01",
    "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png",
    "edition_number": 1,
    "bitcoin_id": "1N1kaRd7A3vYR4d1sfysPHQMMURoHCtmWY",
    "acl": [
      "edit",
      "consign",
      "transfer",
      "loan",
      "share",
      "download",
      "view",
      "delete",
      "coa"
    ],
    "request_action": null,
    "parent": 8530,
    "extra_data": {},
    "digital_work": {
      "id": 1890,
      "url": "https://d1qjsxua1o9x03.cloudfront.net/local/foo/images-18/digitalwork/images-18.jpeg",
      "url_safe": "https://d1qjsxua1o9x03.cloudfront.net/local%2Ffoo%2Fimages-18%2Fdigitalwork%2Fimages-18.jpeg",
      "mime": "image",
      "hash": "39971523c58d1d605b6b3f0e96803c25",
      "encoding_urls": null,
      "isEncoding": 0
    },
    "other_data": null,
    "hash_as_address": "14Ab6qQcg2CaCE8RBrJtSFqEjsiremApXC",
    "owner": "foo",
    "btc_owner_address_noprefix": "1N1kaRd7A3vYR4d1sfysPHQMMURoHCtmWY",
    "ownership_history": [
      [
        "Jul. 01, 2015, 09:48:51",
        "Registered by foo"
      ]
    ],
    "consign_history": [],
    "loan_history": [],
    "coa": null,
    "status": [],
    "pending_new_owner": null,
    "consignee": null,
    "note_from_user": null,
    "public_note": null,
    "license_type": {
      "name": "Attribution without restriction",
      "code": "default",
      "organization": "ascribe",
      "url": "https://www.ascribe.io/faq/#legals"
    }
  },
  "success": true
}
```

#### Delete an edition

##### HTTP Request
`DELETE https://www.ascribe.io/api/editions/<bitcoin_id>`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_id | `<string>` The bitcoin ID of the edition

##### Example Request
```shell
curl -X DELETE https://www.ascribe.io/api/editions/1N1kaRd7A3vYR4d1sfysPHQMMURoHCtmWY/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```

##### Example Response
```json
{
  "notification": "You have successfully deleted 1 editions.",
  "success": true
}
```

### Register

#### Register a piece

A piece is registered in the blockchain upon creation. When the number of editions is specified, 
each edition is only registered in the blockchain right before any ownership action (transfer, consign, loan, ...) has been performed.   

#### List the registrations

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/registrations/?page=<number>&page_size=<number>`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
page | `(optional) <int>` The pagination number
page_size | `(optional) <int>` Number of results per page

##### Example Request
```shell
curl https://www.ascribe.io/api/ownership/registrations/?page=1&page_size=10 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```

##### Example Response
```json
{
  "success": true,
  "count": 4,
  "next": null,
  "previous": null,
  "registrations": [
    {
      "id": 5443,
      "piece": {
        "id": 8530,
        "title": "art1",
        "artist_name": "art1",
        "num_editions": 50,
        "user_registered": "foo",
        "datetime_registered": "2015-07-01T09:48:51.037053Z",
        "date_created": "2015-01-01",
        "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png"
      },
      "type": "OwnershipRegistration",
      "datetime": "2015-07-01T09:48:51.497008Z",
      "btc_tx": {
        "datetime": "2015-07-01T09:48:51.587300Z",
        "service": "<bitcoin.bitcoin_service.BitcoinDaemonMainnetService object at 0x7fb776c20cd0>",
        "from_address": "12udvE3zmbQLhtSGZUqqAvGWSKDUCpbgoq",
        "inputs": "[{'output': 'c63b6cfcda31371d94e28a734dcc0acbcd30047c03c23340601d806851d711f6:3', 'value': 28200}]",
        "outputs": "[{'value': 600, 'address': u'1NHXUfW1MfKsU83yGY52xrTDVux8UwMXio'}, {'value': 600, 'address': u'1HCnLDnbHvgddPKGkkYXaQtLZzbc23P3cE'}, {'value': 600, 'address': u'1CAiGhr6dSmrABAJud47PaUtZ8qvn88Tc4'}]",
        "mining_fee": 10000,
        "tx": "ea382a621f0f91a1a2291b3a8382d56ad049938dac9a2d3da73253d03be4f536",
        "block_height": null,
        "status": 2,
        "spoolverb": "ASCRIBESPOOL01REGISTER0"
      },
      "new_owner": {
        "email": "foo@mailinator.com",
        "username": "foo",
        "id": 1349,
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "prev_owner": {
        "email": "foo@mailinator.com",
        "username": "foo",
        "id": 1349,
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "new_btc_address": null,
      "prev_btc_address": "12udvE3zmbQLhtSGZUqqAvGWSKDUCpbgoq",
      "status": null
    },
    ...
  ]
}
```
#### Retrieve a registration

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/registrations/<id>/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
id | `<int>` The ID of the registration

##### Example Request
```shell
curl https://www.ascribe.io/api/ownership/registrations/4424/
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```
##### Example Response
```json
{
  "success": true,
  "registration": {
    "id": 5443,
    "piece": {
      "id": 8530,
      "title": "art1",
      "artist_name": "art1",
      "num_editions": 50,
      "user_registered": "foo",
      "datetime_registered": "2015-07-01T09:48:51.037053Z",
      "date_created": "2015-01-01",
      "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png"
    },
    "type": "OwnershipRegistration",
    "datetime": "2015-07-01T09:48:51.497008Z",
    "btc_tx": {
      "datetime": "2015-07-01T09:48:51.587300Z",
      "service": "<bitcoin.bitcoin_service.BitcoinDaemonMainnetService object at 0x7fb776b9a090>",
      "from_address": "12udvE3zmbQLhtSGZUqqAvGWSKDUCpbgoq",
      "inputs": "[{'output': 'c63b6cfcda31371d94e28a734dcc0acbcd30047c03c23340601d806851d711f6:3', 'value': 28200}]",
      "outputs": "[{'value': 600, 'address': u'1NHXUfW1MfKsU83yGY52xrTDVux8UwMXio'}, {'value': 600, 'address': u'1HCnLDnbHvgddPKGkkYXaQtLZzbc23P3cE'}, {'value': 600, 'address': u'1CAiGhr6dSmrABAJud47PaUtZ8qvn88Tc4'}]",
      "mining_fee": 10000,
      "tx": "ea382a621f0f91a1a2291b3a8382d56ad049938dac9a2d3da73253d03be4f536",
      "block_height": null,
      "status": 2,
      "spoolverb": "ASCRIBESPOOL01REGISTER0"
    },
    "new_owner": {
      "email": "foo@mailinator.com",
      "username": "foo",
      "id": 1349,
      "prize_role": {
        "type": "",
        "prize": {"name": ""},
        "round": null,
        "canVote": false,
        "canSubmit": false
      }
    },
    "prev_owner": {
      "email": "foo@mailinator.com",
      "username": "foo",
      "id": 1349,
      "prize_role": {
        "type": "",
        "prize": {"name": ""},
        "round": null,
        "canVote": false,
        "canSubmit": false
      }
    },
    "new_btc_address": null,
    "prev_btc_address": "12udvE3zmbQLhtSGZUqqAvGWSKDUCpbgoq",
    "status": null
  }
}
```

### Transfer

#### Transfer an edition

##### HTTP Request
`POST https://www.ascribe.io/api/ownership/transfers/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_id | `<string>` The ID as the registration address of the edition
transferee | `<email>` The email of the new owner
password | `<string>` Your ascribe password
transfer_message | `(optional) <string>` Additional message

##### Example Request
```shell
curl -X POST https://www.ascribe.io/api/ownership/transfers/ 
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
    -d bitcoin_id=157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W 
    -d transferee=foo@mailinator.com 
    -d password=mypassword 
    -d transfer_message='I would like to transfer this piece to you'
```
##### Example Response
```json
{
  "notification": "You have successfully transfered 1 edition(s) to foo2@mailinator.com.",
  "success": true
}
```

An error will occur when trying to transfer a piece that's not yours:
```json
{
  "errors": {"bitcoin_id": ["You don't have the appropriate rights to transfer this piece"]},
  "success": false
}
```
#### List the transfers

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/transfers/?page=<number>&page_size=<number>`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
page | `(optional) <int>` The pagination number
page_size | `(optional) <int>` Number of results per page

##### Example Request
```shell
curl https://www.ascribe.io/api/ownership/transfers/?page=1&page_size=10 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```

##### Example Response
```json
{
  "success": true,
  "count": 1,
  "next": null,
  "previous": null,
  "transfers": [
    {
      "id": 5450,
      "piece": {
        "id": 8530,
        "title": "art1",
        "artist_name": "art1",
        "num_editions": 50,
        "user_registered": "foo",
        "datetime_registered": "2015-07-01T09:48:51.037053Z",
        "date_created": "2015-01-01",
        "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png"
      },
      "type": "OwnershipTransfer",
      "datetime": "2015-07-01T11:41:45.492059Z",
      "btc_tx": {
        "datetime": "2015-07-01T11:41:45.414098Z",
        "service": "BitcoinDaemonMainnet",
        "from_address": "2015/7/1/9/48/51/51076:1CAiGhr6dSmrABAJud47PaUtZ8qvn88Tc4",
        "inputs": null,
        "outputs": "[{'value': 600, 'address': u'1NHXUfW1MfKsU83yGY52xrTDVux8UwMXio'}, {'value': 600, 'address': u'1Mm6TXr8os2orjygmpbU9XTLKCBj1ZTwXj'}]",
        "mining_fee": null,
        "tx": null,
        "block_height": null,
        "status": 0,
        "spoolverb": "ASCRIBESPOOL01TRANSFER1"
      },
      "new_owner": {
        "email": "foo2@mailinator.com",
        "username": "foo2",
        "id": 1350,
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "prev_owner": {
        "email": "foo@mailinator.com",
        "username": "foo",
        "id": 1349,
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "new_btc_address": "2015/7/1/11/41/45/23019:1Mm6TXr8os2orjygmpbU9XTLKCBj1ZTwXj",
      "prev_btc_address": "2015/7/1/9/48/51/51076:1CAiGhr6dSmrABAJud47PaUtZ8qvn88Tc4",
      "status": 1
    }
  ]
}
```
#### Retrieve a transfer

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/transfers/<id>/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
id | `<int>` The ID of the transfer

##### Example Request
```shell
curl https://www.ascribe.io/api/ownership/transfers/4431/
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```
##### Example Response
```json
  "transfer": {
    "id": 5450,
    "piece": {
      "id": 8530,
      "title": "art1",
      "artist_name": "art1",
      "num_editions": 50,
      "user_registered": "foo",
      "datetime_registered": "2015-07-01T09:48:51.037053Z",
      "date_created": "2015-01-01",
      "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png"
    },
    "type": "OwnershipTransfer",
    "datetime": "2015-07-01T11:41:45.492059Z",
    "btc_tx": {
      "datetime": "2015-07-01T11:41:45.414098Z",
      "service": "BitcoinDaemonMainnetService",
      "from_address": "2015/7/1/9/48/51/51076:1CAiGhr6dSmrABAJud47PaUtZ8qvn88Tc4",
      "inputs": null,
      "outputs": "[{'value': 600, 'address': u'1NHXUfW1MfKsU83yGY52xrTDVux8UwMXio'}, {'value': 600, 'address': u'1Mm6TXr8os2orjygmpbU9XTLKCBj1ZTwXj'}]",
      "mining_fee": null,
      "tx": null,
      "block_height": null,
      "status": 0,
      "spoolverb": "ASCRIBESPOOL01TRANSFER1"
    },
    "new_owner": {
      "email": "foo2@mailinator.com",
      "username": "foo2",
      "id": 1350,
      "prize_role": {
        "type": "",
        "prize": {"name": ""},
        "round": null,
        "canVote": false,
        "canSubmit": false
      }
    },
    "prev_owner": {
      "email": "foo@mailinator.com",
      "username": "foo",
      "id": 1349,
      "prize_role": {
        "type": "",
        "prize": {"name": ""},
        "round": null,
        "canVote": false,
        "canSubmit": false
      }
    },
    "new_btc_address": "2015/7/1/11/41/45/23019:1Mm6TXr8os2orjygmpbU9XTLKCBj1ZTwXj",
    "prev_btc_address": "2015/7/1/9/48/51/51076:1CAiGhr6dSmrABAJud47PaUtZ8qvn88Tc4",
    "status": 1
  },
  "success": true
}
```

### Consign
When consigning a piece, you assign the right to the consignee to sell the piece on your behalve.
Hereto, one needs to send a request for consignment to the consignee. 
Once accepted by the consignee, the consignment will change state and is recorded on the blockchain. 

#### Consign an edition
Send a request for consignment to the consignee.

##### HTTP Request
`POST https://www.ascribe.io/api/ownership/consigns/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_id | `<string>` The ID as the registration address of the edition
consignee | `<email>` The email of the consignee
password | `<string>` Your ascribe password
consign_message | `(optional) <string>` Additional message

##### Example Request
```shell
curl -X POST https://www.ascribe.io/api/ownership/consigns/ 
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
    -d bitcoin_id=157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W 
    -d consignee=foo@mailinator.com 
    -d password=mypassword 
    -d consign_message='I consign this edition to you'
```
##### Example Response
```json
{
  "notification": "You have successfully consigned 1 edition(s), pending their confirmation(s).",
  "success": true
}
```
An error will occur when trying to consign a piece that's not yours:
```json
{
  "errors": {"bitcoin_id": ["You don't have the appropriate rights to consign this piece"]},
  "success": false
}
```

#### Confirm/deny a consignment
When someone consigns an edition to you, you receive a request for consignment to the consignee. 
You can confirm or deny the consignment. 

##### HTTP Request
`POST https://www.ascribe.io/api/ownership/consigns/confirm/`

`POST https://www.ascribe.io/api/ownership/consigns/deny/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_id | `<string>` The ID as the registration address of the edition


##### Example Confirm Request
```shell
curl -X POST https://www.ascribe.io/api/ownership/consigns/confirm/ 
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
    -d bitcoin_id=157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W
```
##### Example Confirm Response
```json
{
  "notification": "You have succesfully confirmed consignment  of art1 by art1, edition 3.",
  "success": true
}
```
Or upon an error:
```json
{
  "errors": {"bitcoin_id": ["This piece is not pending for actions"]},
  "success": false
}
```

##### Example Deny Request
```shell
curl -X POST https://www.ascribe.io/api/ownership/consigns/deny/ 
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
    -d bitcoin_id=157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W
```
##### Example Deny Response
```json
{
  "notification": "You have denied consignment of art1 by art1, edition 4.",
  "success": true
}
```

#### List the consignments

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/consigns/?page=<number>&page_size=<number>`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
page | `(optional) <int>` The pagination number
page_size | `(optional) <int>` Number of results per page

##### Example Request
```shell
curl https://www.ascribe.io/api/ownership/consigns/?page=1&page_size=10 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
```

##### Example Response
```json
{
  "success": true,
  "count": 1,
  "next": null,
  "previous": null,
  "consignments": [
    {
      "id": 5451,
      "piece": {
        "id": 8530,
        "title": "art1",
        "artist_name": "art1",
        "num_editions": 50,
        "user_registered": "foo",
        "datetime_registered": "2015-07-01T09:48:51.037053Z",
        "date_created": "2015-01-01",
        "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png"
      },
      "type": "Consignment",
      "datetime": "2015-07-01T11:58:54.867789Z",
      "btc_tx": {
        "datetime": "2015-07-01T12:03:40.882600Z",
        "service": "<bitcoin.bitcoin_service.BitcoinDaemonMainnetService object at 0x7fcdca46c210>",
        "from_address": "2015/7/1/9/48/52/311730:1BKtJSfHSdo4tQq7QyikdruYrqHKYk6X6k",
        "inputs": "[{'output': '8abbf6e2420d887e861ab83c7d213b509ebfbb4d0d44e0932040b9a06c6fa798:0', 'value': 11200}]",
        "outputs": "[{'value': 600, 'address': u'1NHXUfW1MfKsU83yGY52xrTDVux8UwMXio'}, {'value': 600, 'address': u'1AuHk3o3DBdcKSLbNKPoyM1CU6bwwCCNP8'}]",
        "mining_fee": 10000,
        "tx": "36d7fd724a2d8a3d4be99255edf44ab80264dfb63bcab9b5086706b11bd19e3a",
        "block_height": null,
        "status": 2,
        "spoolverb": "ASCRIBESPOOL01CONSIGN3"
      },
      "new_owner": {
        "email": "foo2@mailinator.com",
        "username": "foo2",
        "id": 1350,
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "prev_owner": {
        "email": "foo@mailinator.com",
        "username": "foo",
        "id": 1349,
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "new_btc_address": "2015/7/1/12/3/40/504996:1AuHk3o3DBdcKSLbNKPoyM1CU6bwwCCNP8",
      "prev_btc_address": "2015/7/1/9/48/52/311730:1BKtJSfHSdo4tQq7QyikdruYrqHKYk6X6k",
      "status": 1
    }
  ]
}
```

#### Retrieve a consignment

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/consigns/<id>/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
id | `<int>` The ID of the consignment

##### Example Request
```shell
curl https://www.ascribe.io/api/ownership/consigns/<id>/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
```

##### Example Response
```json
{
  "consignment": {
    "id": 5451,
    "piece": {
      "id": 8530,
      "title": "art1",
      "artist_name": "art1",
      "num_editions": 50,
      "user_registered": "foo",
      "datetime_registered": "2015-07-01T09:48:51.037053Z",
      "date_created": "2015-01-01",
      "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png"
    },
    "type": "Consignment",
    "datetime": "2015-07-01T11:58:54.867789Z",
    "btc_tx": {
      "datetime": "2015-07-01T12:03:40.882600Z",
      "service": "<bitcoin.bitcoin_service.BitcoinDaemonMainnetService object at 0x7fcdca470510>",
      "from_address": "2015/7/1/9/48/52/311730:1BKtJSfHSdo4tQq7QyikdruYrqHKYk6X6k",
      "inputs": "[{'output': '8abbf6e2420d887e861ab83c7d213b509ebfbb4d0d44e0932040b9a06c6fa798:0', 'value': 11200}]",
      "outputs": "[{'value': 600, 'address': u'1NHXUfW1MfKsU83yGY52xrTDVux8UwMXio'}, {'value': 600, 'address': u'1AuHk3o3DBdcKSLbNKPoyM1CU6bwwCCNP8'}]",
      "mining_fee": 10000,
      "tx": "36d7fd724a2d8a3d4be99255edf44ab80264dfb63bcab9b5086706b11bd19e3a",
      "block_height": null,
      "status": 2,
      "spoolverb": "ASCRIBESPOOL01CONSIGN3"
    },
    "new_owner": {
      "email": "foo2@mailinator.com",
      "username": "foo2",
      "id": 1350,
      "prize_role": {
        "type": "",
        "prize": {"name": ""},
        "round": null,
        "canVote": false,
        "canSubmit": false
      }
    },
    "prev_owner": {
      "email": "foo@mailinator.com",
      "username": "foo",
      "id": 1349,
      "prize_role": {
        "type": "",
        "prize": {"name": ""},
        "round": null,
        "canVote": false,
        "canSubmit": false
      }
    },
    "new_btc_address": "2015/7/1/12/3/40/504996:1AuHk3o3DBdcKSLbNKPoyM1CU6bwwCCNP8",
    "prev_btc_address": "2015/7/1/9/48/52/311730:1BKtJSfHSdo4tQq7QyikdruYrqHKYk6X6k",
    "status": 1
  },
  "success": true
}
```

### Unconsign
When the consignee or owner decide that there is no more need to manage the sales of the edition. 
The consignee can unconsign immediately, the owner needs to request the consignee for an unconsignment. 

#### Unconsign a piece (or confirm an unconsignment request)
Action to be taken by the consignee. He will no longer manage the sales of the edition.

##### HTTP Request
`POST https://www.ascribe.io/api/ownership/unconsigns/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_id | `<string>` The ID as the registration address of the edition
password | `<string>` Your ascribe password
unconsign_message | `(optional) <string>` Additional message

##### Example Request
```shell
curl -X POST https://www.ascribe.io/api/ownership/unconsigns/ 
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
    -d bitcoin_id=157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W 
    -d password=mypassword 
    -d unconsign_message='I unconsign this piece from you. This is, I no longer manage the sales of your piece'
```
##### Example Response
```json
{
  "notification": "You have successfully unconsigned 1 edition(s).",
  "success": true
}
```
An error will occur when trying to consign a piece that's not yours:
```json
{
  "errors": {"bitcoin_id": ["You don't have the appropriate rights to unconsign this piece"]},
  "success": false
}
```
#### Request an unconsignment
Action to be taken by the owner. He requests the consignee to no longer manage the sales of the edition.
The consignee needs to confirm or deny the unconsignment.

##### HTTP Request
`POST https://www.ascribe.io/api/ownership/unconsigns/request/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_id | `<string>` The ID as the registration address of the edition
unconsign_request_message | `(optional) <string>` Additional message

##### Example Request
```shell
curl -X POST https://www.ascribe.io/api/ownership/unconsigns/request/
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
    -d bitcoin_id=157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W 
    -d unconsign_request_message='I unconsign this piece from you. This is, you no longer manage the sales of my piece'
```
##### Example Response
```json
{
  "notification": "You have requested foo2@mailinator.com to unconsign \"art1\".",
  "success": true
}
```
An error will occur when trying to consign a piece that's not yours:
```json
{
  "errors": {"bitcoin_id": ["This piece is not consigned"]},
  "success": false
}
```

#### Deny a unconsignment
Action taken by the consignee when the owner requests to unconsign the edition. 
You can confirm or deny the unconsignment. The confirmation is treated above.

##### HTTP Request
`POST https://www.ascribe.io/api/ownership/unconsigns/deny/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_id | `<string>` The ID as the registration address of the edition


##### Example Request
```shell
curl -X POST https://www.ascribe.io/api/ownership/unconsigns/deny/ 
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
    -d bitcoin_id=157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W
```
##### Example Response
```json
{
  "notification": "You have denied unconsignment of art1 by art1, edition 5.",
  "success": true
}
```
#### List the unconsignments

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/unconsigns/?page=<number>&page_size=<number>`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
page | `(optional) <int>` The pagination number
page_size | `(optional) <int>` Number of results per page

##### Example Request
```shell
curl https://www.ascribe.io/api/ownership/unconsigns/?page=1&page_size=10 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
```

##### Example Response
```json
{
  "success": true,
  "count": 1,
  "next": null,
  "previous": null,
  "unconsignments": [
    {
      "id": 5454,
      "piece": {
        "id": 8530,
        "title": "art1",
        "artist_name": "art1",
        "num_editions": 50,
        "user_registered": "foo",
        "datetime_registered": "2015-07-01T09:48:51.037053Z",
        "date_created": "2015-01-01",
        "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png"
      },
      "type": "UnConsignment",
      "datetime": "2015-07-01T12:16:13.264491Z",
      "btc_tx": {
        "datetime": "2015-07-01T12:16:13.181577Z",
        "service": "<bitcoin.bitcoin_service.BitcoinDaemonMainnetService object at 0x7fcba6267650>",
        "from_address": "2015/7/1/12/3/40/504996:1AuHk3o3DBdcKSLbNKPoyM1CU6bwwCCNP8",
        "inputs": "[{'output': '85aa8cc86182869d77fb51546128a91cd3991a77bb6a4cc837fc659a1508b7a5:0', 'value': 11200}]",
        "outputs": "[{'value': 600, 'address': u'1NHXUfW1MfKsU83yGY52xrTDVux8UwMXio'}, {'value': 600, 'address': u'1BKtJSfHSdo4tQq7QyikdruYrqHKYk6X6k'}]",
        "mining_fee": 10000,
        "tx": "3f4fd20e80be4229a9ba2a418d9ca7783865a5665c15650444003368cf232b34",
        "block_height": null,
        "status": 1,
        "spoolverb": "ASCRIBESPOOL01UNCONSIGN3"
      },
      "new_owner": {
        "email": "foo@mailinator.com",
        "username": "foo",
        "id": 1349,
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "prev_owner": {
        "email": "foo2@mailinator.com",
        "username": "foo2",
        "id": 1350,
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "new_btc_address": "2015/7/1/9/48/52/311730:1BKtJSfHSdo4tQq7QyikdruYrqHKYk6X6k",
      "prev_btc_address": "2015/7/1/12/3/40/504996:1AuHk3o3DBdcKSLbNKPoyM1CU6bwwCCNP8",
      "status": 1
    }
  ]
}
```

#### Retrieve an unconsignment

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/unconsigns/<id>/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
id | `<int>` The ID of the unconsignment

##### Example Request
```shell
curl https://www.ascribe.io/api/ownership/unconsigns/5454/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
```

##### Example Response
```json
{
  "unconsignment": {
    "id": 5454,
    "piece": {
      "id": 8530,
      "title": "art1",
      "artist_name": "art1",
      "num_editions": 50,
      "user_registered": "foo",
      "datetime_registered": "2015-07-01T09:48:51.037053Z",
      "date_created": "2015-01-01",
      "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png"
    },
    "type": "UnConsignment",
    "datetime": "2015-07-01T12:16:13.264491Z",
    "btc_tx": {
      "datetime": "2015-07-01T12:16:13.181577Z",
      "service": "<bitcoin.bitcoin_service.BitcoinDaemonMainnetService object at 0x7fcba623c210>",
      "from_address": "2015/7/1/12/3/40/504996:1AuHk3o3DBdcKSLbNKPoyM1CU6bwwCCNP8",
      "inputs": "[{'output': '85aa8cc86182869d77fb51546128a91cd3991a77bb6a4cc837fc659a1508b7a5:0', 'value': 11200}]",
      "outputs": "[{'value': 600, 'address': u'1NHXUfW1MfKsU83yGY52xrTDVux8UwMXio'}, {'value': 600, 'address': u'1BKtJSfHSdo4tQq7QyikdruYrqHKYk6X6k'}]",
      "mining_fee": 10000,
      "tx": "3f4fd20e80be4229a9ba2a418d9ca7783865a5665c15650444003368cf232b34",
      "block_height": null,
      "status": 1,
      "spoolverb": "ASCRIBESPOOL01UNCONSIGN3"
    },
    "new_owner": {
      "email": "foo@mailinator.com",
      "username": "foo",
      "id": 1349,
      "prize_role": {
        "type": "",
        "prize": {"name": ""},
        "round": null,
        "canVote": false,
        "canSubmit": false
      }
    },
    "prev_owner": {
      "email": "foo2@mailinator.com",
      "username": "foo2",
      "id": 1350,
      "prize_role": {
        "type": "",
        "prize": {"name": ""},
        "round": null,
        "canVote": false,
        "canSubmit": false
      }
    },
    "new_btc_address": "2015/7/1/9/48/52/311730:1BKtJSfHSdo4tQq7QyikdruYrqHKYk6X6k",
    "prev_btc_address": "2015/7/1/12/3/40/504996:1AuHk3o3DBdcKSLbNKPoyM1CU6bwwCCNP8",
    "status": 1
  },
  "success": true
}
```

### Loan
When loaning an edition, you assign the right to the consignee to display the edition in public for a limited period of time. 
Once accepted by the loanee, the loan will change state and is recorded on the blockchain. 

#### Loan a piece
Action taken by the owner. Send a request to the loanee to accept the loan.

##### HTTP Request
`POST https://www.ascribe.io/api/ownership/loans/editions/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_id | `<string>` The ID as the registration address of the edition
loanee | `<email>` The email of the loanee
password | `<string>` Your ascribe password
startdate | `<YYYY-MM-DD>` The start date of the loan
enddate | `<YYYY-MM-DD>` The end date of the loan
loan_message | `(optional) <string>` Additional message
gallery_name | `(optional) <string>` Name of the gallery where the piece will be displayed

##### Example Request
```shell
curl -X POST https://www.ascribe.io/api/ownership/loans/ 
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
    -d bitcoin_id=157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W 
    -d loanee=foo@mailinator.com 
    -d password=mypassword 
    -d startdate=2015-05-29
    -d enddate=2015-06-01
    -d loan_message='I loan this piece to you'
```
##### Example Response
```json
{
  "notification": "You have successfully loaned 1 edition(s) to foo2@mailinator.com, pending their confirmation.",
  "success": true
}
```
An error will occur when trying to loan a piece to someone that is already loans the piece:
```json
{
  "errors": {"bitcoin_id": ["The loanee already has access to the piece"]},
  "success": false
}
```

#### Confirm/deny a loan
When someone loans a piece to you, you receive a loan request. 
You can confirm or deny the loan. 

##### HTTP Request
`POST https://www.ascribe.io/api/ownership/loans/confirm/`

`POST https://www.ascribe.io/api/ownership/loans/deny/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_id | `<string>` The ID as the registration address of the edition


##### Example Confirm Request
```shell
curl -X POST https://www.ascribe.io/api/ownership/loans/confirm/ 
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
    -d bitcoin_id=1JbdWghbB21CtQazxNu4LqNFJnw2uB7Fe4
```
##### Example Confirm Response
```json
{
  "notification": "You have succesfully confirmed the loan of art1 by art1, edition 8.",
  "success": true
}
```
Or upon an error:
```json
{
  "errors": {"bitcoin_id": ["Loan not found"]},
  "success": false
}
```

##### Example Deny Request
```shell
curl -X POST https://www.ascribe.io/api/ownership/loans/deny/ 
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
    -d bitcoin_id=1JbdWghbB21CtQazxNu4LqNFJnw2uB7Fe4
```
##### Example Deny Response
```json
{
  "notification": "You have denied the loan of art1 by art1, edition 10.",
  "success": true
}
```
Or upon an error:
```json
{
  "errors": {"bitcoin_id": ["Loan not found"]},
  "success": false
}
```

#### List the loans

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/loans/?page=<number>&page_size=<number>`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
page | `(optional) <int>` The pagination number
page_size | `(optional) <int>` Number of results per page

##### Example Request
```shell
curl https://www.ascribe.io/api/ownership/loans/?page=1&page_size=10 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
```

##### Example Response
```json
{
  "success": true,
  "count": 2,
  "next": null,
  "previous": null,
  "loans": [
    {
      "id": 5461,
      "piece": {
        "id": 8530,
        "title": "art1",
        "artist_name": "art1",
        "num_editions": 50,
        "user_registered": "foo",
        "datetime_registered": "2015-07-01T09:48:51.037053Z",
        "date_created": "2015-01-01",
        "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png"
      },
      "type": "Loan",
      "datetime": "2015-07-01T12:44:43.398400Z",
      "btc_tx": null,
      "new_owner": {
        "email": "foo2@mailinator.com",
        "username": "foo2",
        "id": 1350,
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "prev_owner": {
        "email": "foo@mailinator.com",
        "username": "foo",
        "id": 1349,
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "new_btc_address": null,
      "prev_btc_address": null,
      "status": null
    },
    ...
  ]
}    
```

#### Retrieve a loan

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/loans/<id>/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
id | `<int>` The ID of the loan

##### Example Request
```shell
curl https://www.ascribe.io/api/ownership/loans/5461/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
```

##### Example Response
```json
{
  "loan": {
    "id": 5461,
    "piece": {
      "id": 8530,
      "title": "art1",
      "artist_name": "art1",
      "num_editions": 50,
      "user_registered": "foo",
      "datetime_registered": "2015-07-01T09:48:51.037053Z",
      "date_created": "2015-01-01",
      "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png"
    },
    "type": "Loan",
    "datetime": "2015-07-01T12:44:43.398400Z",
    "btc_tx": null,
    "new_owner": {
      "email": "foo2@mailinator.com",
      "username": "foo2",
      "id": 1350,
      "prize_role": {
        "type": "",
        "prize": {"name": ""},
        "round": null,
        "canVote": false,
        "canSubmit": false
      }
    },
    "prev_owner": {
      "email": "foo@mailinator.com",
      "username": "foo",
      "id": 1349,
      "prize_role": {
        "type": "",
        "prize": {"name": ""},
        "round": null,
        "canVote": false,
        "canSubmit": false
      }
    },
    "new_btc_address": null,
    "prev_btc_address": null,
    "status": null
  },
  "success": true
}
```

## Similarity Search API

### Photos
Match images from the web against images in the photos marketplace.
Try it in the browser at [labs.ascribe.io/similarity/global](http://labs.ascribe.io/similarity/global/).

#### Version
0.1 Beta

#### Roadmap
Features on the roadmap:

 * Rotation
 * Mirroring
 * Inversion is implemented but not yet turned on
 * Cropping resilience will be improved

#### Basics
The ```dist``` field indicates how closely matched the images are. 
A perfect match has a ```dist``` value of ```0.0```.


##### HTTP Request
`GET http://labs.ascribe.io/similarity/global/api/search/?image_url=<url>`

##### Arguments
Parameter | Description
----------|------------
image_url | `<string>` The url of the image. Should be encoded.

##### Example Request
```shell
curl -X GET http://labs.ascribe.io/similarity/global/api/search/
    -d image_url=http%3A%2F%2Fcdn...com%2Fthumb%2F640%2F480%2....jpg
```
##### Example Response
Status code 200 and an array of ```{dist, url, id}``` image descriptions. In case of error, the returned JSON will contain the key ``error``

<pre>
{
    "result": [
        {
            "url": "http://www.photos.com/img1.jpg",
            "dist": 0,
            "id": 12
        },
        {
            "url": "http://www.photos.com/img2.jpg",
            "dist": 0.43739087511375,
            "id": 528492
        },
        {
            "url": "http://www.photos.com/img3.jpg",
            "dist": 0.4438784282276083,
            "id": 27482264
        },
        {
            "url": "http://www.photos.com/img4.jpg",
            "dist": 0.4500463733919593,
            "id": 27550153
        },
        {
            "url": "http://www.photos.com/img5.jpg",
            "dist": 0.46666245188890754,
            "id": 22654175
        },
        {
            "url": "http://www.photos.com/img6.jpg",
            "dist": 0.4687795429136797,
            "id": 30254571
        },
        {
            "url": "http://www.photos.com/img7.jpg",
            "dist": 0.4719155672668924,
            "id": 24884288
        },
        {
            "url": "http://www.photos.com/img8.jpg",
            "dist": 0.4821427959813869,
            "id": 14152888
        }
    ]
}
</pre>

If an error occurs, the JSON will contain the key ``error``.
<pre>
{
    "error": "Timeout downloading the image"
}
</pre>

## Background
This API & documentation was developed by ascribe GmbH as part of the overall ascribe technology stack. https://www.ascribe.io

## Copyright

This API & documentation is © 2015 ascribe GmbH.

This API & documentation is available for use under the Creative Commons CC-BY-SA 3.0 license.
