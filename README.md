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

![Authorization Flow PNG](images/ascribe_api_workflow.png)

### Register your Application or Marketplace
The developer (e.g. admin@makx.com) should have an [ascribe account](https://www.ascribe.io), 
login and go to settings>API settings to register
the application or marketplace with following fields:
- Name: `name of the marketplace`
- Client Type: `confidential`
- Authorization Grant Type: `Resource owner password-based`

The provided `client_id` and `client_secret` will now allow users of makx to
request an access token.

### Request Access Token
Once the application is registered and a `client_id` and `client_secret` are
available, a user can request a token, provided that he has an ascribe account.

#### HTTP Request
`POST https://www.ascribe.io/o/token/`

#### HTTP Headers 
`Authorization: Basic <client_id>:<client_secret>`

#### Arguments
Parameter | Description
----------|------------
grant_type | TODO
username | username of Makx
password | password of Makx

#### Example Request
```shell
curl -X POST http://localhost:8000/o/token/ \
-u 'AWp2Nl180u90DlIBRgZuysooDc2vBPNZOY!kJnKJ:E!NUgsjZAak.7@xTSkA:K1PA!y8ieFRw;;e?JqFj0cHL1e._L59Y7RmXif1;779h5jzVIqAbFN!Kl5RQfeZp6;iNGz-L916oyLxnkj=aVrVA@M4CBBy62q0Kd=YSgLnj' \
-d grant_type=password \
-d username=admin@makx.com \
-d password=mypassword \
-d scope='read write' 
```

#### Example Response
```json
{
    "access_token": "2GJT0yFOnHYKtp9sgNak4GURL9jpKD",
    "expires_in": 31536000,
    "refresh_token": "zG3DPnOUhjPy1sIo8DkSp43C0SH9f1",
    "scope": "read write",
    "token_type": "Bearer"
}
```

## Methods

### Pieces

#### List all pieces

##### HTTP Request
`GET https://www.ascribe.io/api/0.1/pieces/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Example Request
```shell
curl http://localhost:8000/api/0.1/pieces/ \
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
        },
        ...
            ],
    "success": true
}
```

#### Retrieve a piece

##### HTTP Request
`GET https://www.ascribe.io/api/0.1/pieces/{bitcoin_ID_noPrefix}/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
bitcoin_ID_noPrefix | TODO

##### Example Request
```shell
curl http://localhost:8000/api/0.1/pieces/1NwT94k4srVqXjBPEi7dfuhSHQdpC5g69X/ \
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
`POST https://www.ascribe.io/api/0.1/pieces/`

##### HTTP Headers
`Authorization: Bearer <access_token>`


##### Arguments
Parameter | Description
----------|------------
file_url | Required
title | Optional
artist_name | Optional
asc-hash-md5 | Optional


##### Example Request
```shell
curl -X POST http://localhost:8000/api/0.1/pieces/ \
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

### Users

#### List all users

##### HTTP Request
`GET https://www.ascribe.io/api/0.1/users/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Example Request
```shell
curl http://localhost:8000/api/0.1/users/ \
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```
##### Example Response
```json
{
    "success": true,
    "users": [
        {
            "email": "admin@makx.com",
            "id": 13,
            "prize_role": {
                "canSubmit": false,
                "canVote": false,
                "type": ""
            },
            "username": "admin@makx.com"
        }
    ]
}
```
#### Retrieve a user

##### HTTP Request
`GET https://www.ascribe.io/api/0.1/users/{username}/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
username | TODO

##### Example Request
```shell
curl http://localhost:8000/api/0.1/users/admin@makx.com/ \
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```
##### Example Response
```json
{
    "success": true,
    "users": [
        {
            "email": "admin@makx.com",
            "id": 13,
            "prize_role": {
                "canSubmit": false,
                "canVote": false,
                "type": ""
            },
            "username": "admin@makx.com"
        }
    ]
}
```
#### Create a user

##### HTTP Request
`POST https://www.ascribe.io/api/0.1/users/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
username | Required
password | Required
email | Required

##### Example Request
```shell
curl -X POST http://localhost:8000/api/0.1/users/ \
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD' \
    -d username=new_user \
    -d password=newuserpassword \
    -d email=new_user@makx.com
```
##### Example Response
```json
{
    "success": true,
    "user": {
        "email": "new_user",
        "id": 16,
        "prize_role": {
            "canSubmit": false,
            "canVote": false,
            "type": ""
        },
        "username": "new_user"
    }
}
```

### Jobs

#### List all jobs

##### HTTP Request
`GET https://www.ascribe.io/api/0.1/users/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Example Request
```shell
curl http://localhost:8000/api/0.1/jobs/ \
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```
##### Example Response
```json
{
    "jobs": [
        {
            "created": "2015-03-17T16:50:19.409Z",
            "description": "bitcoin tx",
            "id": 17,
            "object_id": "25",
            "percent_done": 0,
            "resource_id": "bfd912255530d35a588d20246d19f60e63bdf86df4df03d449a919bad9a99e11",
            "user": "admin@makx.com"
        },
        {
            "created": "2015-03-17T16:46:17.279Z",
            "description": "bitcoin tx",
            "id": 15,
            "object_id": "23",
            "percent_done": 55,
            "resource_id": "afe693aa653bd2349f40adf7d12b256c61259c89720699d6d1b15421d4f6a5df",
            "user": "admin@makx.com"
        },
        {
            "created": "2015-03-17T16:46:29.675Z",
            "description": "bitcoin tx",
            "id": 16,
            "object_id": "24",
            "percent_done": 55,
            "resource_id": "77aee8b105c28ee492214c9fa3094461a0215fb793dd08755a8bd37cb10663dc",
            "user": "admin@makx.com"
        },
        ...
   ],
    "success": true
}
```

#### Retrieve a job

##### HTTP Request
`GET https://www.ascribe.io/api/0.1/jobs/{id}/`

##### HTTP Headers
`Authorization: Bearer <access_token>`

##### Arguments
Parameter | Description
----------|------------
id | TODO

##### Example Request
```shell
curl http://localhost:8000/api/0.1/jobs/16/ \
    -H 'Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD'
```
##### Example Response
```json
{
    "jobs": [
        {
            "created": "2015-03-17T16:46:29.675Z",
            "description": "bitcoin tx",
            "id": 16,
            "object_id": "24",
            "percent_done": 55,
            "resource_id": "77aee8b105c28ee492214c9fa3094461a0215fb793dd08755a8bd37cb10663dc",
            "user": "admin@makx.com"
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


