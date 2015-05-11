# REST-main
Version: 0.1

Main RESTful API for ascribe ownership web service. https://www.ascribe.io

## Table of Contents

- [API Documentation](#api-documentation)
    - [Overview](#overview)
    - [Authorization Flow](#authorization-flow)
    - [Register your Application or Marketplace](#register-your-application-or-marketplace)
    - [Request Access Token](#request-access-token)
- [Methods](#methods)
    - [Pieces](#pieces)
    - [Users](#users)
    - [Jobs](#jobs)
    - [Transfer](#transfer)
    - [Consign](#consign)

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
`GET https://www.ascribe.io/api/pieces/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Example Request
```shell
curl https://www.ascribe.io/api/pieces/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```

##### Example Response


```json
{
    "pieces": [
        {
            "artistNameOrID": "Makx",
            "artist_name": "Makx",
            "availableActions": "Can Transfer/Consign",
            "bitcoin_ID_noPrefix": "1NwT94k4srVqXjBPEi7dfuhSHQdpC5g69X",
            "date_created": "2015-01-01",
            "edition_number": 1,
            "isActiveInPrize": false,
            "num_editions": 1,
            "ratingFromUser": null,
            "thumbnail": "https://ascribe0.s3.amazonaws.com/local/admin@makx.com/elmo/thumbnailfile/elmo.jpg.png",
            "title": "Makx Art",
            "yearAndEdition_str": "2015, 1/3"
        },
        ...
            ],
    "success": true
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
bitcoin_ID |  The ID as the registration address of the artwork

##### Example Request
```shell
curl https://www.ascribe.io/api/pieces/1NwT94k4srVqXjBPEi7dfuhSHQdpC5g69X/
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```

##### Example Response
```json
{
    "pieces": [
        {
            "artistNameOrID": "Makx",
            "artist_name": "Makx",
            "availableActions": "Can Transfer/Consign",
            "bitcoin_ID_noPrefix": "1NwT94k4srVqXjBPEi7dfuhSHQdpC5g69X",
            "btcOwnerAddress_noPrefix": "1NwT94k4srVqXjBPEi7dfuhSHQdpC5g69X",
            "canAddToPieces": false,
            "canConsign": true,
            "canDelete": true,
            "canEdit": true,
            "canRemoveFromPieces": false,
            "canShare": true,
            "canTransfer": true,
            "canView": true,
            "consign_status": 0,
            "consign_status_str": "-",
            "consignee_name": null,
            "date_created": "2015-01-01",
            "datetime": null,
            "datetime_registered": "2015-03-17T14:43:37.568Z",
            "digital_work": {
                "encoding_urls": null,
                "hash": "03f4ef27b84947caac6e1293a2c86324",
                "isEncoding": false,
                "mime": "image",
                "url": "https://ascribe0.s3.amazonaws.com/local/admin@makx.com/elmo/digitalworkfile/elmo.jpg",
                "url_safe": "https://ascribe0.s3.amazonaws.com/local%2Fadmin%40makx.com%2Felmo%2Fdigitalworkfile%2Felmo.jpg",
                "user": "admin@makx.com"
            },
            "edition_number": 1,
            "extra_data": {},
            "hashAsAddress": "1BoNDHXCNGXNnFMTKEDbL3YSigCD1741y",
            "id": 23,
            "isActiveInPrize": false,
            "noteFromUser": null,
            "num_editions": 3,
            "other_data": null,
            "owner": "admin@makx.com",
            "ownershipHistory": [
                [
                    "Mar. 17, 2015, 14:43:37",
                    "Registered by admin@makx.com"
                ]
            ],
            "prizeDetails": null,
            "ratingFromUser": null,
            "thumbnail": "https://ascribe0.s3.amazonaws.com/local/admin@makx.com/elmo/thumbnailfile/elmo.jpg.png",
            "title": "Makx Art",
            "yearAndEdition_str": "2015, 1/3"
        }
    ],
    "success": true
}
```
#### Create a piece

##### HTTP Request
`POST https://www.ascribe.io/api/pieces/`

##### HTTP Headers
`Authorization: Bearer <access_token>`


##### Arguments
Parameter | Description
----------|------------
file_url | Required
title | Required
artist_name | Required
asc-hash-md5 | Optional


##### Example Request
```shell
curl -X POST http://www.ascribe.io/api/pieces/ 
     -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' \
     -d file_url=https://ascribe0.s3.amazonaws.com/local/admin@makx.com/elmo/digitalworkfile/elmo.jpg \
     -d title='New Piece' \
     -d artist_name='New Artist'
```
##### Example Response
```json
{
    "piece": [
        {
            "artistNameOrID": "New Artist",
            "artist_name": "New Artist",
            "availableActions": "Can Transfer/Consign",
            "bitcoin_ID_noPrefix": "12R8YHC43HqKV3eAvvuweWymJbszedNaSc",
            "btcOwnerAddress_noPrefix": "12R8YHC43HqKV3eAvvuweWymJbszedNaSc",
            "canAddToPieces": false,
            "canConsign": true,
            "canDelete": true,
            "canEdit": true,
            "canRemoveFromPieces": false,
            "canShare": true,
            "canTransfer": true,
            "canView": true,
            "consign_status": 0,
            "consign_status_str": "-",
            "consignee_name": null,
            "date_created": "2015-03-18",
            "datetime": null,
            "datetime_registered": "2015-03-18T09:11:32.632Z",
            "digital_work": {
                "encoding_urls": null,
                "hash": "03f4ef27b84947caac6e1293a2c86324",
                "isEncoding": false,
                "mime": "image",
                "url": "https://ascribe0.s3.amazonaws.com/media/digital_works/admin@makx.com/elmo.jpg",
                "url_safe": "https://ascribe0.s3.amazonaws.com/media%2Fdigital_works%2Fadmin%40makx.com%2Felmo.jpg",
                "user": "admin@makx.com"
            },
            "edition_number": 1,
            "extra_data": {},
            "hashAsAddress": "14CLRw39vW2WbcK5WMSqpfgog2nxKiGWth",
            "id": 43,
            "isActiveInPrize": false,
            "noteFromUser": null,
            "num_editions": 1,
            "other_data": null,
            "owner": "admin@makx.com",
            "ownershipHistory": [
                [
                    "Mar. 18, 2015, 09:11:32",
                    "Registered by admin@makx.com"
                ]
            ],
            "prizeDetails": null,
            "ratingFromUser": null,
            "thumbnail": "https://ascribe0.s3.amazonaws.com/media/thumbnails/ascribe_spiral.png",
            "title": "New Piece",
            "yearAndEdition_str": "2015, 1/1"
        }
    ],
    "success": true
}
```


### Transfer

#### Transfer a piece

##### HTTP Request
`POST https://www.ascribe.io/api/0.1/transfer/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_ID_noPrefix | TODO
transferee_name | TODO
password | TODO
transfer_message | TODO

##### Example Request
```shell
curl -X POST http://localhost:8000/api/0.1/transfer/ \
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' \
    -d bitcoin_ID_noPrefix=157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W \
    -d transferee_name=new_user@makx.com \
    -d password=mypassword \
    -d transfer_message='I would like to transfer this piece to you'
```
##### Example Response
```json
{
    "notification": "You have successfully transfered \"New Piece\" to new_user@makx.com.",
    "success": true
}
```
### Consign

#### Consign a Piece

##### HTTP Request
`POST https://www.ascribe.io/api/0.1/consign/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_ID_noPrefix | TODO
consignee_name | TODO
password | TODO
consign_message | TODO

##### Example Request
```shell
curl -X POST http://localhost:8000/api/0.1/consign/ \
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' \
    -d bitcoin_ID_noPrefix=1FSHWN12K2VZBHJYaAWfcDa63zrBozqPKY \
    -d consignee_name=new_user@makx.com \
    -d password=mypassword \
    -d transfer_message='I would like to consign this piece to you'
```
##### Example Response
```json
{
    "notification": "You have successfully consigned \"New Piece\" to new_user@makx.com, pending their confirmation.",
    "success": true
}
```


