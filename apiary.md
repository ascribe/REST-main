FORMAT: 1A
HOST: https://www.ascribe.io/api

<!-- This file used to be named apiary.apib back when it was rendered online
by apiary.io. When we turned off apiary.io, we wanted it to render nicely
on GitHub, so we changed the filename to apiary.md so that GitHub would
treat it as a Markdown file. -->


# ascribe ownership REST API

This is a REST API for [ascribe](https://www.ascribe.io) ownership processing. 
It wraps the [SPOOL blockchain protocol](https://github.com/ascribe/spool). (If you prefer python, use [this](https://pypi.python.org/pypi/ascribe).)

## Overview

Here's a typical usage:
- Get an access token for your app, using the ascribe webapp. This is a one-time action
- [Register a piece (digital work)](#reference/ownership/pieces)
- [Register the number of editions for the piece](#reference/ownership/editions)
- [Transfer the ownership of an edition](#reference/ownership/transfers)
- Check the status of a piece, edition, etc

Other usage:
- [Consign](#reference/ownership/consignments) - give someone the right to sell a work on another's behalf (eg marketplace)
- [Loan](#reference/ownership/loans-for-pieces) - give public display rights to someone, for a fixed time period

Details on each step are below.

## Get an Access Token for Your App

- If needed, sign up an ascribe account: go to https://www.ascribe.io, click "Create a free account", then specify email and password
- Log into ascribe account
- Go to settings (top right) -> API integration
- Fill in the "Name" field. This should be the name of your app
- Copy the token for use. Note you can always retrieve this later


## Webhooks

Webhooks allow external services to receive notifications from ascribe.

For more details:

- Log into ascribe account
- Go to settings (top right) -> Webhooks


# Group Ownership

## Pieces [/pieces/]

### Register a Piece [POST]

When creating a piece with our API, the piece is automatically registered on the blockchain.

For marketplaces that act as a middle-man for registering pieces on ascribe.io, it is possible
to register the piece as a consignee (someone that has the right to sell the piece on the
owner's behalf).

**Note**: If you don't specify `num_editions` there will be no editions associated with the piece.
You can specify the number of editions for the piece later (see Register number of editions).
    
+ Request (application/json)

    + Attributes
        + file_url (string, required) - The url of the digital file
        + title (string, required) - The title of the artwork
        + artist_name (string, required) - The artist name
        + date_created (string, optional) - <YYYY> The year of the creation date
        + num_editions (number, optional) - The number of editions (will create as many pieces)
        + consign (boolean, optional) - Set to True if the marketplace acts as a consignee

    + Header
        
            Authorization: Bearer <your_api_token>
    + Body
    
            {
                "file_url": "https://ascribe0.s3.amazonaws.com/local/admin@makx.com/elmo/digitalworkfile/elmo.jpg",
                "title": "New Piece",
                "artist_name": "New Artist"
            }

+ Response 201 (application/json)

    + Attributes
        + notification (string) - A message describing the result of the request.
        + success (boolean) - A flag indicating whether the request succeeded or not
        + edition (object) - The registered piece/edition
        
    + Body
        
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


### List Pieces [GET]

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)

    + Body
        
            {
                "success": true,
                "count": 1,
                "unfiltered_count": 1,
                "next": null,
                "previous": null,
                "pieces": [      
                    {
                        "id": 12578,
                        "title": "elmo",
                        "artist_name": "the cranberry being",
                        "num_editions": 10,
                        "user_registered": "cranberry",
                        "datetime_registered": "2015-09-18T12:16:47.384245Z",
                        "date_created": "2015-01-01",
                        "thumbnail": {
                            "id": 3726,
                            "url": "https://d1qjsxua1o9x03.cloudfront.net/live/eb1e78328c46506b46a4ac4a1e378b91/elmo.jpg/thumbnail/100x100/thumbnail.png",
                            "url_safe": "https://d1qjsxua1o9x03.cloudfront.net/live%2Feb1e78328c46506b46a4ac4a1e378b91%2Felmo.jpg%2Fthumbnail%2F100x100%2Fthumbnail.png",
                            "mime": "image",
                            "thumbnail_sizes": {
                                "600x600": "https://d1qjsxua1o9x03.cloudfront.net/live%2Feb1e78328c46506b46a4ac4a1e378b91%2Felmo.jpg%2Fthumbnail%2F600x600%2Fthumbnail.png",
                                "300x300": "https://d1qjsxua1o9x03.cloudfront.net/live%2Feb1e78328c46506b46a4ac4a1e378b91%2Felmo.jpg%2Fthumbnail%2F300x300%2Fthumbnail.png",
                                "100x100": "https://d1qjsxua1o9x03.cloudfront.net/live%2Feb1e78328c46506b46a4ac4a1e378b91%2Felmo.jpg%2Fthumbnail%2F100x100%2Fthumbnail.png"
                            }
                        },
                        "license_type": {
                            "name": "Attribution without restriction",
                            "code": "default",
                            "organization": "ascribe",
                            "url": "https://www.ascribe.io/faq/#legals"
                        },
                        "bitcoin_id": "1EAuSbAGL64EwfJfPKJnZ7HPKJfbKgwsCx",
                        "acl": {
                            "acl_view_editions": true
                        },
                        "request_action": [],
                        "first_edition": {
                            "edition_number": 1,
                            "num_editions_available": 1,
                            "bitcoin_id": "1E2Ya9YT9nFphNSinz8TAkpDH5Fj32GHGi"
                        }
                    }
                ]
            } 
    

### Retrieve a Piece [GET /pieces/{piece_id}/]
+ Parameters
    + piece_id (integer) - The id of the Piece.

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)

    + Body
    
            {
                "success": true,
                "piece": {
                    "id": 12566,
                    "title": "elmo",
                    "artist_name": "alice",
                    "num_editions": -1,
                    "user_registered": "alice",
                    "datetime_registered": "2015-09-18T08:15:11.853945Z",
                    "date_created": "2015-01-01",
                    "thumbnail": {
                        "id": 3714,
                        "url": "https://d1qjsxua1o9x03.cloudfront.net/live/d56b9fc4b0f1be8871f5e1c40c0067e7/elmo.jpg/thumbnail/100x100/thumbnail.png",
                        "url_safe": "https://d1qjsxua1o9x03.cloudfront.net/live%2Fd56b9fc4b0f1be8871f5e1c40c0067e7%2Felmo.jpg%2Fthumbnail%2F100x100%2Fthumbnail.png",
                        "mime": "image",
                        "thumbnail_sizes": {
                            "100x100": "https://d1qjsxua1o9x03.cloudfront.net/live%2Fd56b9fc4b0f1be8871f5e1c40c0067e7%2Felmo.jpg%2Fthumbnail%2F100x100%2Fthumbnail.png",
                            "300x300": "https://d1qjsxua1o9x03.cloudfront.net/live%2Fd56b9fc4b0f1be8871f5e1c40c0067e7%2Felmo.jpg%2Fthumbnail%2F300x300%2Fthumbnail.png",
                            "600x600": "https://d1qjsxua1o9x03.cloudfront.net/live%2Fd56b9fc4b0f1be8871f5e1c40c0067e7%2Felmo.jpg%2Fthumbnail%2F600x600%2Fthumbnail.png"
                        }
                    },
                    "license_type": {
                        "name": "Attribution without restriction",
                        "code": "default",
                        "organization": "ascribe",
                        "url": "https://www.ascribe.io/faq/#legals"
                    },
                    "bitcoin_id": "1EDinmq4SNQUafkLrriSU3B3zFqSrREMpy",
                    "acl": {
                        "piece": 12566,
                        "edition": null,
                        "acl_view": true,
                        "acl_edit": true,
                        "acl_download": true,
                        "acl_delete": true,
                        "acl_create_editions": true,
                        "acl_view_editions": true,
                        "acl_share": true,
                        "acl_unshare": false,
                        "acl_transfer": false,
                        "acl_withdraw_transfer": false,
                        "acl_consign": false,
                        "acl_withdraw_consign": false,
                        "acl_unconsign": false,
                        "acl_request_unconsign": false,
                        "acl_loan": true,
                        "acl_loan_request": false,
                        "acl_coa": false
                    },
                    "request_action": [],
                    "extra_data": {},
                    "digital_work": {
                        "id": 4249,
                        "url": "https://d1qjsxua1o9x03.cloudfront.net/live/d56b9fc4b0f1be8871f5e1c40c0067e7/elmo.jpg/digitalwork/elmo.jpg",
                        "url_safe": "https://d1qjsxua1o9x03.cloudfront.net/live%2Fd56b9fc4b0f1be8871f5e1c40c0067e7%2Felmo.jpg%2Fdigitalwork%2Felmo.jpg",
                        "mime": "image",
                        "hash": "03f4ef27b84947caac6e1293a2c86324",
                        "encoding_urls": null,
                        "isEncoding": 0
                    },
                    "other_data": [],
                    "loan_history": [],
                    "private_note": null,
                    "public_note": null
                }
            }    

### Retrieve all Editions of Piece [GET /pieces/{piece_id}/editions/]

+ Parameters
    + piece_id (integer) - The id of the Piece.

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)



### Delete a Piece [DELETE /pieces/{piece_id}/]
+ Parameters
    + piece_id (integer) - The id of the Piece.

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)

        {
            "notification": "You have successfully deleted <your_piece> by <yourself>.",
            "success": true
        }
        

## Editions [/editions/]

### Register a Number of Editions [POST]

+ Request (application/json)

    + Attributes
        + piece_id (number) - The ID of the piece
        + num_editions (number) - Number of editions for the piece
    
    + Header
        
            Authorization: Bearer <your_api_token>
    
    + Body
    
            {
                "piece_id": 1071,
                "num_editions": 10
            } 

+ Response 201 (application/json)

        {
            "success": true,
            "notification": "You successfully registered 10 editions for piece with ID 1ADJ5fYt1Hq4acL3e7gVr7st6SD8rZq51d."
        }

### List Editions [GET]

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)

    + Body
            
            {
                "success": true,
                "count": 10,
                "unfiltered_count": 10,
                "next": null,
                "previous": null,
                "editions": [
                    {
                        {
                            "id": 10700,
                            "title": "soleil",
                            "artist_name": "The Photon Machine",
                            "num_editions": 5,
                            "user_registered": "photon",
                            "datetime_registered": "2015-07-14T13:58:15.060Z",
                            "date_created": "2013-01-01",
                            "thumbnail": {
                                "id": 1861,
                                "url": "https://d1qjsxua1o9x03.cloudfront.net/live/sbellem/soleil-maison-hiver/thumbnail/soleil-maison-hiver.jpg.png",
                                "url_safe": "https://d1qjsxua1o9x03.cloudfront.net/live%2Fsbellem%2Fsoleil-maison-hiver%2Fthumbnail%2Fsoleil-maison-hiver.jpg.png",
                                "mime": "image",
                                "thumbnail_sizes": null
                            },
                            "license_type": {
                                "name": "Attribution without restriction",
                                "code": "default",
                                "organization": "ascribe",
                                "url": "https://www.ascribe.io/faq/#legals"
                            },
                            "bitcoin_id": "1DtFReB1ev4vpsLyxp4ktgVV8eeK1UJDbC",
                            "acl": {
                                "piece": 10698,
                                "edition": 10700,
                                "acl_view": true,
                                "acl_edit": true,
                                "acl_download": true,
                                "acl_delete": true,
                                "acl_create_editions": false,
                                "acl_view_editions": true,
                                "acl_share": true,
                                "acl_unshare": false,
                                "acl_transfer": true,
                                "acl_withdraw_transfer": false,
                                "acl_consign": true,
                                "acl_withdraw_consign": false,
                                "acl_unconsign": false,
                                "acl_request_unconsign": false,
                                "acl_loan": true,
                                "acl_loan_request": false,
                                "acl_coa": true
                            },
                            "request_action": [],
                            "edition_number": 3,
                            "parent": 10698
                        }
                    ]
                }    


### Retrieve an Edition [GET /editions/{bitcoin_id}/]

+ Parameters
    + bitcoin_id (string) - The bitcoin ID of the edition

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)

    + Body
    
            {
                "success": true,
                "edition": {
                    "id": 10700,
                    "title": "soleil",
                    "artist_name": "The Photon Machine",
                    "num_editions": 5,
                    "user_registered": "photon",
                    "datetime_registered": "2015-07-14T13:58:15.060Z",
                    "date_created": "2013-01-01",
                    "thumbnail": {
                        "id": 1861,
                        "url": "https://d1qjsxua1o9x03.cloudfront.net/live/sbellem/soleil-maison-hiver/thumbnail/soleil-maison-hiver.jpg.png",
                        "url_safe": "https://d1qjsxua1o9x03.cloudfront.net/live%2Fsbellem%2Fsoleil-maison-hiver%2Fthumbnail%2Fsoleil-maison-hiver.jpg.png",
                        "mime": "image",
                        "thumbnail_sizes": null
                    },
                    "license_type": {
                        "name": "Attribution without restriction",
                        "code": "default",
                        "organization": "ascribe",
                        "url": "https://www.ascribe.io/faq/#legals"
                    },
                    "bitcoin_id": "1DtFReB1ev4vpsLyxp4ktgVV8eeK1UJDbC",
                    "acl": {
                        "piece": 10698,
                        "edition": 10700,
                        "acl_view": true,
                        "acl_edit": true,
                        "acl_download": true,
                        "acl_delete": true,
                        "acl_create_editions": false,
                        "acl_view_editions": true,
                        "acl_share": true,
                        "acl_unshare": false,
                        "acl_transfer": true,
                        "acl_withdraw_transfer": false,
                        "acl_consign": true,
                        "acl_withdraw_consign": false,
                        "acl_unconsign": false,
                        "acl_request_unconsign": false,
                        "acl_loan": true,
                        "acl_loan_request": false,
                        "acl_coa": true
                    },
                    "request_action": [],
                    "edition_number": 3,
                    "parent": 10698,
                    "extra_data": {},
                    "digital_work": {
                        "id": 2115,
                        "url": "https://d1qjsxua1o9x03.cloudfront.net/live/sbellem/soleil-maison-hiver/digitalwork/soleil-maison-hiver.jpg",
                        "url_safe": "https://d1qjsxua1o9x03.cloudfront.net/live%2Fsbellem%2Fsoleil-maison-hiver%2Fdigitalwork%2Fsoleil-maison-hiver.jpg",
                        "mime": "image",
                        "hash": "e87f25b707d32e983a46da9a23aae7a2",
                        "encoding_urls": null,
                        "isEncoding": 0
                    },
                    "other_data": [],
                    "loan_history": [],
                    "private_note": null,
                    "public_note": null,
                    "hash_as_address": "128QA4dnN4pVX1U5dKNmReQMXpY7X2Mz9X",
                    "owner": "photon",
                    "btc_owner_address_noprefix": "1DtFReB1ev4vpsLyxp4ktgVV8eeK1UJDbC",
                    "ownership_history": [
                        [
                            "Jul. 14, 2015, 13:58:15",
                            "Registered by photon"
                        ]
                    ],
                    "consign_history": [],
                    "coa": null,
                    "status": [],
                    "pending_new_owner": null,
                    "consignee": null
                }
            }    


### Delete an Edition [DELETE /editions/{bitcoin_id}/]

+ Parameters
    + bitcoin_id (string) - The bitcoin ID of the edition

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)

        {
            "notification": "You have successfully deleted 1 editions.",
            "success": true
        }


## Registrations [/ownership/registrations/]

A piece is registered in the blockchain upon creation. When the number of editions
is specified, each edition is only registered in the blockchain right before any
ownership action (transfer, consign, loan, ...) has been performed.


### List all Registrations [GET]

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)


### Retrieve a Registration [GET /ownership/registrations/{id}/]

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Parameters
    + id (integer) - The ID of the registration.

+ Response 200 (application/json)


## Transfers [/ownership/transfers/]

### Initiate a Transfer of Ownership [POST]

+ Attributes
    + bitcoin_id (required, string) - The ID as the registration address of the edition
    + transferee (required, string) - The email of the new owner
    + password (required, string) - Your ascribe password
    + transfer_message (optional, string) - Additional message

+ Request (application/json)

    + Header
        
            Authorization: Bearer <your_api_token>

    + Body
     
            {
                "bitcoin_id": "157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W",
                "transferee": "foo@mailinator.com",
                "password": "mypassword",
                "transfer_message": "I would like to transfer this piece to you"
            }
        

+ Response 201 (application/json)

        {
            "notification": "You have successfully transfered 1 edition(s) to foo2@mailinator.com.",
            "success": true
        }


### List all Transfers [GET]

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)


### Retrieve a Transfer [GET /ownership/transfers/{id}/]

+ Parameters
    + id (integer) - The ID of the transfer.

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)


## Consignments [/ownership/consigns/]

When consigning a piece, you assign the right to the consignee to sell the piece on your
behalve. Hereto, one needs to send a request for consignment to the consignee. Once
accepted by the consignee, the consignment will change state and is recorded on the blockchain.


### Consign an Edition [POST]

Send a request for consignment to the consignee.

+ Attributes
    + bitcoin_id (required, string) - The ID as the registration address of the edition
    + consignee (required, string) - The email of the consignee
    + password (required, string) - Your ascribe password
    + consign_message (optional, string) - Additional message
    
+ Request (application/json)

    + Header
        
            Authorization: Bearer <your_api_token>
            
    + Body
    
            {
                "bitcoin_id": "157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W",
                "consignee": "foo@mailinator.com",
                "password": "mypassword",
                "consign_message": "I consign this edition to you"
            }

+ Response 201 (application/json)

        {
            "notification": "You have successfully consigned 1 edition(s), pending their confirmation(s).",
            "success": true
        }
        
+ Response 400 (application/json)

        {
            "errors": {"bitcoin_id": ["You don't have the appropriate rights to consign this piece"]},
            "success": false
        }


### List all consignments [GET]

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)


### Confirm a Consignment [POST /ownership/consigns/confirm/]

Allows a consignee to confirm a request for consignment.

+ Attributes
    + bitcoin_id (required, string) - The bitcoin id of the edition for which consignment is being confirmed.

+ Request (application/json)

    + Header
        
            Authorization: Bearer <your_api_token>
            
    + Body
    
            {
                "bitcoin_id": "157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W",
            }

+ Response 201 (application/json)
        
    + Body

            {
                "notification": "You have succesfully confirmed consignment of art1 by art1, edition 3.",
                "success": true
            }
            
+ Response 400 (application/json)

    + Body
    
            {
                "errors": {"bitcoin_id": ["This piece is not pending for actions"]},
                "success": false
            }


### Deny a Consignment [POST /ownership/consigns/deny/]

Allows a consignee to deny a request for consignment.

+ Attributes
    + bitcoin_id (required, string) - The bitcoin id of the edition for which consignment is being denied.

+ Request (application/json)

    + Header
        
            Authorization: Bearer <your_api_token>
            
    + Body
    
            {
                "bitcoin_id": "157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W",
            }

+ Response 201 (application/json)
        
    + Body

            {
                "notification": "You have denied consignment of art1 by art1, edition 4.",
                "success": true
            }

+ Response 400 (application/json)

    + Body
    
            {
                "errors": {"bitcoin_id": ["This piece is not pending for actions"]},
                "success": false
            }


### Retrieve a consignment [GET /ownership/consigns/{id}/]

+ Parameters
    + id (integer) - The ID of the consignment

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)


## Unconsignments [/ownership/unconsigns/]

When the consignee or owner decide that there is no more need to manage the sales of the
edition. The consignee can unconsign immediately, the owner needs to request the consignee
for an unconsignment.


### Unconsign an Edition [POST]

(Or confirm an unconsignment request.)

+ Attributes
    + bitcoin_id (required, string) - The ID as the registration address of the edition
    + password (required, string) - Your ascribe password
    + unconsign_message (optional, string) - Additional message

+ Request (application/json)

    + Header
        
            Authorization: Bearer <your_api_token>
            
    + Body
        
            {
                "bitcoin_id": "157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W",
                "password": "mypassword",
                "unconsign_message": "I unconsign this piece from you. This is, I no longer manage the sales of your piece"
            }

+ Response 201 (application/json)

    + Body
 
            {
                "notification": "You have successfully unconsigned 1 edition(s).",
                "success": true
            }

+ Response 400 (application/json)

    + Body
 
            {
                "errors": {"bitcoin_id": ["You don't have the appropriate rights to unconsign this piece"]},
                "success": false
            }


### List all Unconsignments [GET]

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)


### Request an unconsignment [POST /ownership/unconsigns/request/]

Action to be taken by the owner. He requests the consignee to no longer manage the sales
of the edition. The consignee needs to confirm or deny the unconsignment.

+ Attributes
    + bitcoin_id (required, string) - The ID as the registration address of the edition
    + unconsign_request_message (optional, string) - Additional message

+ Request (application/json)

    + Header

            Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD
            
    + Body
        
            {
                "bitcoin_id": "157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W",
                "unconsign_request_message": "I unconsign this piece from you. This is, you no longer manage the sales of my piece"
            }                

+ Response 201 (application/json)

    + Body
            
            {       
                "notification": "You have requested foo2@mailinator.com to unconsign \"art1\".",
                "success": true
            }

+ Response 400 (application/json)

    An error will occur when trying to consign a piece that's not yours:
    
    + Body

            {
                "errors": {"bitcoin_id": ["This piece is not consigned"]},
                "success": false
            }

### Deny an unconsignment [POST /ownership/unconsigns/deny/]

Action taken by the consignee when the owner requests to unconsign the edition.
You can confirm or deny the unconsignment. The confirmation is treated above.

+ Attributes
    + bitcoin_id (required, string) - The ID as the registration address of the edition

+ Request (application/json)

    + Header

            Authorization: Bearer 2GJT0yFOnHYKtp9sgNak4GURL9jpKD
            
    + Body
        
            {
                "bitcoin_id": "157od1WGsmh7ctYXEstTbsA7pzx6BoWU9W"
            }

+ Response 201 (application/json)

    + Body
            
            {
                "notification": "You have denied unconsignment of art1 by art1, edition 5.",
                "success": true
            }


### Retrieve an Unconsignment [GET /ownership/unconsigns/{id}/]

+ Request

    + Header
        
            Authorization: Bearer <your_api_token>

+ Parameters
    + id (integer) - The ID of the unconsignment

+ Response 200 (application/json)


## Loans for Pieces [/ownership/loans/pieces/]

### Loan a Piece [POST]

+ Request (application/json)
    
    + Attributes
        + piece_id (required, number) - The small integer id of the piece
        + loanee (required, string) - The email of the loanee
        + password (required, string) - Your ascribe password
        + startdate (required, string) - <YYYY-MM-DD> The start date of the loan
        + enddate (required, string) - <YYYY-MM-DD> The end date of the loan
        + terms (required, boolean) - Whether to accept the terms and conditions
        + loan_message (optional, string) - Additional message
        + gallery (optional, string) - Name of the gallery where the piece will be displayed
        + bitcoin_id (optional, string) - The ID as the registration address of the piece
        + contract_key (optional, string) - Identifier of the contract

    + Header
        
            Authorization: Bearer <your_api_token>
    
    + Body
    
            {
                "piece_id": 3,
                "loanee": "alice@wonderland.xyz",
                "password": "secret",
                "startdate": "2015-09-19",
                "enddate": "2015-09-20",
                "terms": true,
                "loan_message": "I loan this piece to you"
            }

+ Response 201 (application/json)

        {
            "loan": {
                "btc_tx": null,
                    "datetime": "2016-03-24T13:47:23.864767Z",
                    "id": 2,
                    "new_btc_address": null,
                    "new_owner": "alice@wonderland.xyz",
                    "piece": {
                        "artist_name": "",
                        "bitcoin_id": "mohBA3msbDtsrxACnDwmmjwHyyarABTjCJ",
                        "id": 1,
                        "thumbnail": {
                            "id": 1,
                            "mime": "image",
                            "thumbnail_sizes": null,
                            "url": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png",
                            "url_safe": "https://d1qjsxua1o9x03.cloudfront.net/media%2Fthumbnails%2Fascribe_spiral.png"
                        },
                        "title": "wonderalice"
                    },
                    "prev_btc_address": "2016/3/24/13/47/23/309355:mohBA3msbDtsrxACnDwmmjwHyyarABTjCJ",
                    "prev_owner": "alice@test.com",
                    "status": null,
                    "type": "LoanPiece"
            },
                "notification": "You have successfully loaned a piece to bob@test.com.",
                "success": true
        }


### List Loans for Pieces [GET]

+ Request
    
    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)


### Retrieve a Loan for a Piece [GET /ownership/loans/piece/{id}/]

+ Parameters
    + id (integer) - The ID of the loan

+ Request
    
    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)


### Confirm a Loan for a Piece [POST /ownership/loans/pieces/confirm/]

+ Request (application/json)
    
    + Attributes
        + piece_id (required, number) - The small integer id of the piece
        
    + Header
        
            Authorization: Bearer <your_api_token>
    
    + Body
    
            {
                "piece_id": 3
            }

+ Response 200 (application/json)
    + Attributes
        + success (boolean)
        + notification (string)
        
+ Response 400 (application/json)
    + Attributes
        + success (boolean)
        + notification (string)



### Deny a Loan for a Piece [POST /ownership/loans/pieces/deny/]
+ Request (application/json)
    
    + Attributes
        + piece_id (required, number) - The small integer id of the piece
        
    + Header
        
            Authorization: Bearer <your_api_token>
    
    + Body
    
            {
                "piece_id": 3
            }

+ Response 200 (application/json)
    + Attributes
        + success (boolean)
        + notification (string)
        
+ Response 400 (application/json)
    + Attributes
        + success (boolean)
        + notification (string)


## Loans for Editions [/ownership/loans/editions/]

When loaning an edition, you assign the right to the consignee to display the edition in public
for a limited period of time. Once accepted by the loanee, the loan will change state and is
recorded on the blockchain.


### Loan an Edition [POST]

+ Request (application/json)
    
    + Attributes
        + bitcoin_id (string) - The ID as the registration address of the edition
        + loanee (string) - The email of the loanee
        + password (string) - Your ascribe password
        + enddate (string) - <YYYY-MM-DD> The end date of the loan
        + loan_message (string, optional) - Additional message
        + gallery_name (string, optional) - Name of the gallery where the piece will be displayed        

    + Header
        
            Authorization: Bearer <your_api_token>
    
    + Body
    
            {
                "bitcoin_id": "abc",
                "loanee": "alice@wonderland.xyz",
                "password": "secret",
                "startdate": "2015-09-19",
                "enddate": "2015-09-20",
                "loan_message": "I loan this piece to you",
                "terms": true
            }

+ Response 201 (application/json)

        {
            "loan": {
                "btc_tx": null,
                    "datetime": "2016-03-24T11:00:21.020439Z",
                    "edition": {
                        "artist_name": "",
                        "bitcoin_id": "abc",
                        "edition_number": 1,
                        "id": 3,
                        "parent": 2,
                        "thumbnail": {
                            "id": 2,
                            "mime": "image",
                            "thumbnail_sizes": null,
                            "url": "https://d1qjsxua1o9x03.cloudfront.net/media/thumbnails/ascribe_spiral.png",
                            "url_safe": "https://d1qjsxua1o9x03.cloudfront.net/media%2Fthumbnails%2Fascribe_spiral.png"
                        },
                        "title": "wonderalice"
                    },
                    "id": 5,
                    "new_btc_address": null,
                    "new_owner": "bob@test.com",
                    "prev_btc_address": null,
                    "prev_owner": "alice@wonderland.xyz",
                    "status": null,
                    "type": "Loan"
            },
                "notification": "You have successfully loaned 1 edition(s) to bob@test.com.",
                "success": true
        }


### List Loans for Editions [GET]

+ Request
    
    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    

### Retrieve a Loan for an Edition [GET /ownership/loans/editions/{id}/]

+ Parameters
    + id (integer) - The ID of the loan

+ Request
    
    + Header
        
            Authorization: Bearer <your_api_token>

+ Response 200 (application/json)
    
    + Attributes
        + loan (object) - The retreived loan object
        + success (boolean) - Whether the request was successful or not
    
    + body
    
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
                            "prize": {
                                "name": ""
                            },
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
                            "prize": {
                                "name": ""
                            },
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


### Confirm a Loan for an Edition [POST /ownership/loans/editions/confirm/]

+ Request (application/json)
    
    + Attributes
        + bitcoin_id (string) - The ID as the registration address of the edition

    + Header
        
            Authorization: Bearer <your_api_token>
    
    + Body
    
            {
                "bitcoin_id": "abc"
            }

+ Response 201 (application/json)

        {
            "notification": "You have succesfully confirmed the loan of art1 by art1, edition 8.",
            "success": true
        }


### Deny a Loan for an Edition [POST /ownership/loans/editions/deny/]

+ Request (application/json)
    
    + Attributes
        + bitcoin_id (string) - The ID as the registration address of the edition

    + Header
        
            Authorization: Bearer <your_api_token>
    
    + Body
    
            {
                "bitcoin_id": "abc"
            }

+ Response 201 (application/json)

        {
            "notification": "You have denied the loan of art1 by art1, edition 10.",
            "success": true
        }


# Group Appendix

## Authorization Flow Details
Let's call the marketplace Makx.

As authentication is used to connect and transfer data between Makx and ascribe, we
use an encrypted connection (HTTPS).

Ascribe authorizes applications via tokens. Each token is an alphanumeric that
encodes the following information:

- The ID of the application that was granted access.
- The ID of the user who granted access to personal data.
- A set of actions available to the application.

![Authorization Flow PNG](https://s3-us-west-2.amazonaws.com/ascribe0/public/rest_doc/ascribe_api_workflow.png)


## Copyright

SPOOL, this API & documentation are © 2015 ascribe GmbH.

SPOOL, this API & documentation are available for use under the 
[Creative Commons CC-BY-SA 3.0 license](https://creativecommons.org/licenses/by-sa/3.0/). 
