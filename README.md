# ascribe REST API


## SPOOL API

Our SPOOL API is now hosted on apiary at 
[docs.ascribe.apiary.io](http://docs.ascribe.apiary.io/)

### Contributing
If you wish to contribute to the SPOOL API blueprint, you're welcome to do so.

The SPOOL API blueprint is contained in [apiary.apib](apiary.apib).


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
