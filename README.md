# REST-main
Version: 0.1

Main RESTful API for ascribe ownership web service. https://www.ascribe.io

## Table of Contents

- [API Documentation](#api-documentation)
    - [Overview](#overview)
    - [Authorization Flow](#authorization-flow)
    - [Register your Application or Marketplace](#register-your-application-or-marketplace)
- [Methods](#methods)
    - [Pieces](#pieces)
    - [Transfer](#transfer)
    
## API Documentation

### Overview
Integration has these actions:
1. Register your app/marketplace and request an access token. This is a one time action.
2. Register a piece.
3. Check the status of a piece.

### Authorization Flow
Let's call the marketplace Makx.

As authentication is used to connect transfer data between Makx and ascribe, we
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

## Methods

### Pieces

#### List all pieces

##### HTTP Request
`GET https://www.ascribe.io/api/pieces/?page={number}&page_size={number}&search={query}&ordering={order}`

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
  "count":27,
  "next":"https://www.ascribe.io/api/pieces/?page=2&page_size=10&search=Elmo&ordering=-edition_number",
  "previous":null,
  "pieces": [
    {
      "title": "Elmo",
      "artist_name": "New Artist",
      "edition_number": 20,
      "num_editions": 20,
      "bitcoin_ID_noPrefix": "1G5CZbpCtyWNf1QqudiHM9kvrWnfdHj2v9",
      "yearAndEdition_str": "2015, 20/20",
      "artistNameOrID": "New Artist",
      "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/test/makx/elmo/thumbnail/elmo.jpg.png",
      "availableActions": "Transfer/Consign/Loan",
      "isActiveInPrize": false,
      "ratingFromUser": null
    },
    ...
  ]
}
```

#### Retrieve a piece

##### HTTP Request
`GET https://www.ascribe.io/api/pieces/{bitcoin_ID}/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_ID | `<string>` The ID as the registration address of the artwork

##### Example Request
```shell
curl https://www.ascribe.io/api/pieces/1NwT94k4srVqXjBPEi7dfuhSHQdpC5g69X/
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```

##### Example Response
```json
{
  "piece": {
    "title": "Elmo",
    "artist_name": "New Artist",
    "edition_number": 1,
    "num_editions": 1,
    "bitcoin_ID_noPrefix": "146eRKTDYzMxisg8d8q1KsxsaskPm7PSK2",
    "yearAndEdition_str": "2015, 1/1",
    "artistNameOrID": "New Artist",
    "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/local/makx/elmo/thumbnail/elmo.jpg.png",
    "availableActions": "Transfer/Consign/Loan",
    "isActiveInPrize": false,
    "ratingFromUser": null,
    "hashAsAddress": "16NBKk6HjnzHC48qXxq15XnAgbzK56T7A2",
    "btcOwnerAddress_noPrefix": "146eRKTDYzMxisg8d8q1KsxsaskPm7PSK2",
    "owner": "makx",
    "ownershipHistory": [
      [
        "May. 12, 2015, 09:56:29",
        "Registered by makx"
      ]
    ],
    "loanHistory": [],
    "datetime_registered": "2015-05-12T09:56:29.766455Z",
    "date_created": "2015-05-12",
    "extra_data": {},
    "digital_work": {
      "url": "https://d1qjsxua1o9x03.cloudfront.net/local/makx/elmo/digitalwork/elmo.jpg",
      "url_safe": "https://d1qjsxua1o9x03.cloudfront.net/local%2Fmakx%2Felmo%2Fdigitalwork%2Felmo.jpg",
      "mime": "image",
      "hash": "03f4ef27b84947caac6e1293a2c86324",
      "encoding_urls": null,
      "isEncoding": 0
    },
    "coa": null,
    "other_data": null,
    "canEdit": true,
    "canTransfer": true,
    "canConsign": true,
    "canLoan": true,
    "canView": true,
    "canDownload": true,
    "canShare": true,
    "canAddToPieces": false,
    "canRemoveFromPieces": false,
    "canDelete": true,
    "consign_status": 0,
    "consign_status_str": "-",
    "consignee_name": null,
    "noteFromUser": null,
    "prizeDetails": null
  },
  "success": true
}
```
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

##### Example Request
```shell
curl -X POST http://www.ascribe.io/api/pieces/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
     -d file_url=https://ascribe0.s3.amazonaws.com/local/admin@makx.com/elmo/digitalworkfile/elmo.jpg 
     -d title='New Piece' 
     -d artist_name='New Artist'
```
##### Example Response
```json
{
  "notification": "You have successfully registered \"Elmo\" by New Artist, 1 editions.",
  "piece": {
    "title": "Elmo",
    "artist_name": "New Artist",
    "edition_number": 1,
    "num_editions": 1,
    "bitcoin_ID_noPrefix": "146eRKTDYzMxisg8d8q1KsxsaskPm7PSK2",
    "yearAndEdition_str": "2015, 1/1",
    "artistNameOrID": "New Artist",
    "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/local/makx/elmo/thumbnail/elmo.jpg.png",
    "availableActions": "Transfer/Consign/Loan",
    "isActiveInPrize": false,
    "ratingFromUser": null,
    "hashAsAddress": "16NBKk6HjnzHC48qXxq15XnAgbzK56T7A2",
    "btcOwnerAddress_noPrefix": "146eRKTDYzMxisg8d8q1KsxsaskPm7PSK2",
    "owner": "makx",
    "ownershipHistory": [
      [
        "May. 12, 2015, 09:56:29",
        "Registered by makx"
      ]
    ],
    "loanHistory": [],
    "datetime_registered": "2015-05-12T09:56:29.766455Z",
    "date_created": "2015-05-12",
    "extra_data": {},
    "digital_work": {
      "url": "https://d1qjsxua1o9x03.cloudfront.net/local/makx/elmo/digitalwork/elmo.jpg",
      "url_safe": "https://d1qjsxua1o9x03.cloudfront.net/local%2Fmakx%2Felmo%2Fdigitalwork%2Felmo.jpg",
      "mime": "image",
      "hash": "03f4ef27b84947caac6e1293a2c86324",
      "encoding_urls": null,
      "isEncoding": 0
    },
    "coa": null,
    "other_data": null,
    "canEdit": true,
    "canTransfer": true,
    "canConsign": true,
    "canLoan": true,
    "canView": true,
    "canDownload": true,
    "canShare": true,
    "canAddToPieces": false,
    "canRemoveFromPieces": false,
    "canDelete": true,
    "consign_status": 0,
    "consign_status_str": "-",
    "consignee_name": null,
    "noteFromUser": null,
    "prizeDetails": null
  },
  "success": true
}
```

#### Delete a piece

##### HTTP Request
`DELETE https://www.ascribe.io/api/pieces/{bitcoin_ID}/`

##### HTTP Headers
`Authorization: Bearer <access_token>`


##### Example Request
```shell
curl -X DELETE http://www.ascribe.io/api/pieces/{bitcoin_ID}/
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```
##### Example Response
```json
{
  "notification": "You have successfully deleted \"Elmo\" by New Artist, edition 11.",
  "success": true
}
```
### Registration

#### Register a piece

A piece is registered in the blockchain upon creation. When the number of editions is specified, 
each edition is only registered in the blockchain right before any ownership action (transfer, consign, loan, ...) has been performed.   

#### List the registrations

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/registrations/?page={number}&page_size={number}&search={query}&ordering={order}`

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
curl https://www.ascribe.io/api/ownership/registrations/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
     -d page_size=10 
     -d page=1 
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
      "id": 4423,
      "piece": {
        "title": "Elmo",
        "artist_name": "New Artist",
        "edition_number": 1,
        "num_editions": 1,
        "bitcoin_ID_noPrefix": "1NvfMSvFoEAjAvoURwWcTPXdpxVWffTWDe",
        "yearAndEdition_str": "2015, 1/1",
        "artistNameOrID": "New Artist",
        "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/local/makx/elmo-0/thumbnail/elmo-0.jpg.png",
        "availableActions": "Transfer/Consign/Loan",
        "isActiveInPrize": false,
        "ratingFromUser": null
      },
      "type": "OwnershipRegistration",
      "datetime": "2015-05-12T10:21:58.981849Z",
      "btc_tx": {
        "datetime": "2015-05-12T10:21:59.894655Z",
        "service": "<bitcoin.bitcoin_service.BitcoinDaemonMainnetService object at 0x7f92ca7c5890>",
        "user": {
          "id": 670,
          "email": "makx@mailinator.com",
          "username": "makx",
          "prize_role": {
            "type": "",
            "prize": {"name": ""},
            "round": null,
            "canVote": false,
            "canSubmit": false
          }
        },
        "from_address": "12udvE3zmbQLhtSGZUqqAvGWSKDUCpbgoq",
        "inputs": null,
        "outputs": "[{'value': 600, 'address': u'1EpeiZqdFqre79AJQ8Tn5aGQ6UrFBCgvED'}, {'value': 600, 'address': u'1NvfMSvFoEAjAvoURwWcTPXdpxVWffTWDe'}]",
        "mining_fee": null,
        "tx": null,
        "block_height": null,
        "status": 0,
        "spoolverb": "ASCRIBESPOOLREGISTER"
      },
      "new_owner": {
        "id": 670,
        "email": "makx@mailinator.com",
        "username": "makx",
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "prev_owner": {
        "id": 670,
        "email": "makx@mailinator.com",
        "username": "makx",
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "new_btc_address": "1EpeiZqdFqre79AJQ8Tn5aGQ6UrFBCgvED",
      "prev_btc_address": "12udvE3zmbQLhtSGZUqqAvGWSKDUCpbgoq",
      "status": null
    },
    ...
  ],
  "success": true
}
```
#### Retrieve a registration

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/registrations/{id}/`

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
    "id": 4424,
    "piece": {
      "title": "Elmo",
      "artist_name": "New Artist",
      "edition_number": 1,
      "num_editions": 1,
      "bitcoin_ID_noPrefix": "1KJ8NXUbCf2Ax64SPs89fCX2jYExw4tYVX",
      "yearAndEdition_str": "2015, 1/1",
      "artistNameOrID": "New Artist",
      "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/local/makx/elmo-1/thumbnail/elmo-1.jpg.png",
      "availableActions": "View",
      "isActiveInPrize": false,
      "ratingFromUser": null
    },
    "type": "OwnershipRegistration",
    "datetime": "2015-05-12T10:22:51.057945Z",
    "btc_tx": {
      "datetime": "2015-05-12T10:22:52.076892Z",
      "service": "<bitcoin.bitcoin_service.BitcoinDaemonMainnetService object at 0x7f92ca597790>",
      "user": {
        "id": 670,
        "email": "makx@mailinator.com",
        "username": "makx",
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "from_address": "12udvE3zmbQLhtSGZUqqAvGWSKDUCpbgoq",
      "inputs": null,
      "outputs": "[{'value': 600, 'address': u'1HWCTCibZgCLqEyGzzEJfx22YtENJe27DV'}, {'value': 600, 'address': u'1KJ8NXUbCf2Ax64SPs89fCX2jYExw4tYVX'}]",
      "mining_fee": null,
      "tx": null,
      "block_height": null,
      "status": 0,
      "spoolverb": "ASCRIBESPOOLREGISTER"
    },
    "new_owner": {
      "id": 670,
      "email": "makx@mailinator.com",
      "username": "makx",
      "prize_role": {
        "type": "",
        "prize": {"name": ""},
        "round": null,
        "canVote": false,
        "canSubmit": false
      }
    },
    "prev_owner": {
      "id": 670,
      "email": "makx@mailinator.com",
      "username": "makx",
      "prize_role": {
        "type": "",
        "prize": {"name": ""},
        "round": null,
        "canVote": false,
        "canSubmit": false
      }
    },
    "new_btc_address": "1HWCTCibZgCLqEyGzzEJfx22YtENJe27DV",
    "prev_btc_address": "12udvE3zmbQLhtSGZUqqAvGWSKDUCpbgoq",
    "status": null
  }
}
```

### Transfer

#### Transfer a piece

##### HTTP Request
`POST https://www.ascribe.io/api/ownership/transfers/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_ID | `<string>` The ID as the registration address of the artwork
transferee | `<email>` The email of the new owner
password | `<string>` Your ascribe password
transfer_message | `(optional) <string>` Additional message

##### Example Request
```shell
curl -X POST https://www.ascribe.io/api/ownership/transfers/ 
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' 
    -d bitcoin_ID=157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W 
    -d transferee=new_user@makx.com 
    -d password=mypassword 
    -d transfer_message='I would like to transfer this piece to you'
```
##### Example Response
```json
{
  "notification": "You have successfully transfered \"Elmo\" to foo.",
  "success": true
}
```
An error will occur when trying to transfer a piece that's not yours:
```json
{
  "errors": {"bitcoin_ID_noPrefix": ["You don't have the appropriate rights to transfer this piece"]},
  "success": false
}
```
#### List the transfers

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/transfers/?page={number}&page_size={number}&search={query}&ordering={order}`

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
curl https://www.ascribe.io/api/ownership/transfers/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
     -d page_size=10 
     -d page=1 
```

##### Example Response
```json
{
  "success": true,
  "count": 2,
  "next": null,
  "previous": null,
  "transfers": [
    {
      "id": 4422,
      "piece": {
        "title": "Elmo",
        "artist_name": "New Artist",
        "edition_number": 1,
        "num_editions": 1,
        "bitcoin_ID_noPrefix": "146eRKTDYzMxisg8d8q1KsxsaskPm7PSK2",
        "yearAndEdition_str": "2015, 1/1",
        "artistNameOrID": "New Artist",
        "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/local/makx/elmo/thumbnail/elmo.jpg.png",
        "availableActions": "View",
        "isActiveInPrize": false,
        "ratingFromUser": null
      },
      "type": "OwnershipTransfer",
      "datetime": "2015-05-12T10:00:14.127345Z",
      "btc_tx": null,
      "new_owner": {
        "id": 671,
        "email": "foo@mailinator.com",
        "username": "foo",
        "prize_role": {
          "type": "",
          "prize": {"name": ""},
          "round": null,
          "canVote": false,
          "canSubmit": false
        }
      },
      "prev_owner": {
        "id": 670,
        "email": "makx@mailinator.com",
        "username": "makx",
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
  ],
  "success": true
}
```
#### Retrieve a transfer

##### HTTP Request
`GET https://www.ascribe.io/api/ownership/transfers/{id}/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
id | `<int>` The ID of the transfer

##### Example Request
```shell
curl https://www.ascribe.io/api/ownership/transfers/4422/
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```
##### Example Response
```json
{
  "transfer": {
    "id": 4422,
    "piece": {
      "title": "Elmo",
      "artist_name": "New Artist",
      "edition_number": 1,
      "num_editions": 1,
      "bitcoin_ID_noPrefix": "146eRKTDYzMxisg8d8q1KsxsaskPm7PSK2",
      "yearAndEdition_str": "2015, 1/1",
      "artistNameOrID": "New Artist",
      "thumbnail": "https://d1qjsxua1o9x03.cloudfront.net/local/makx/elmo/thumbnail/elmo.jpg.png",
      "availableActions": "View",
      "isActiveInPrize": false,
      "ratingFromUser": null
    },
    "type": "OwnershipTransfer",
    "datetime": "2015-05-12T10:00:14.127345Z",
    "btc_tx": null,
    "new_owner": {
      "id": 671,
      "email": "foo@mailinator.com",
      "username": "foo",
      "prize_role": {
        "type": "",
        "prize": {"name": ""},
        "round": null,
        "canVote": false,
        "canSubmit": false
      }
    },
    "prev_owner": {
      "id": 670,
      "email": "makx@mailinator.com",
      "username": "makx",
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
