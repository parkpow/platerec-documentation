---
title: ALPR Snapshot API Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - ruby
  - python
  - javascript
  - csharp
  - php
  - java
  - cpp

toc_footers:
  - <a href='https://app.platerecognizer.com/accounts/signup/?utm_source=docs&utm_medium=website'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/marcbelmont/deep-license-plate-recognition'>Code Examples</a>
  -
  - <hr>
  -
  - © 2021 ParkPow, Inc.
  - All Rights Reserved.
  - Documenton is governed by our
  - <a href="https://platerecognizer.com/terms-of-use-privacy-policy/">Terms</a>.

includes:
  - countries
  - errors

search: true
---

# Introduction

Welcome to the Plate Recognizer **Snapshot API**! You can use our API to access our API endpoints, which can read license plates from images. For detailed instructions on how to install the SDK, go [here](https://app.platerecognizer.com/sdk/?utm_source=docs&utm_medium=website).

We have multiple language bindings. You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

<aside class="notice">
If you are looking for the documentation for Stream, Parkpow or another product, check our <a href="https://guides.platerecognizer.com/">guides</a> instead.
</aside>

# Authentication

Plate Recognizer Snapshot API is only available to registered users. [Sign up](https://app.platerecognizer.com/accounts/signup/?utm_source=docs&utm_medium=website) for a Free Trial and get an **API key**. It has to be included in all API calls. The HTTP **headers** must contain:

`Authorization: Token my-token******`

<aside class="notice">
You must replace <code>my-token******</code> with your personal API key. For example, <code>Token 3a0effff73919f898b69ac65a32dc12347769564</code>
</aside>

# License Plate Recognition

## Read Number Plates from an Image

```ruby
# gem install multipart-post
require 'net/http/post/multipart'

url = URI.parse('https://api.platerecognizer.com/v1/plate-reader/')
path = '/path/to/car.jpg'
File.open(path) do |jpg|
  req = Net::HTTP::Post::Multipart.new url.path,
    "upload" => UploadIO.new(jpg, "image/jpeg", path)
  req['Authorization'] = 'Token my-token******'
  res = Net::HTTP.start(url.host, url.port, use_ssl: true) do |http|
    http.request(req)
  end
end
```

```php
<?php
// CREATE FILE READY TO UPLOAD WITH CURL
$file = realpath('example.jpg');
if (function_exists('curl_file_create')) { // php 5.5+
  $cFile = curl_file_create($file);
} else {
  $cFile = '@' . realpath($file);
}

//ADD PARAMETER IN REQUEST LIKE regions
$data = array(
    'upload' => $cFile,
    'regions' => 'us-ca' // Optional
);

// Prepare new cURL resource
$ch = curl_init('https://api.platerecognizer.com/v1/plate-reader/');
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLINFO_HEADER_OUT, true);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
curl_setopt($ch, CURLOPT_HTTP_VERSION, CURL_HTTP_VERSION_2TLS);

// Set HTTP Header for POST request
curl_setopt($ch, CURLOPT_HTTPHEADER, array(
    "Authorization: Token my-token******"  //API KEY
	)
);

// Submit the POST request and close cURL session handle
$result = curl_exec($ch);
print_r($result);exit;
curl_close($ch);
?>
```

```python
# pip install requests
import requests
from pprint import pprint
regions = ['mx', 'us-ca'] # Change to your country
with open('/path/to/car.jpg', 'rb') as fp:
    response = requests.post(
        'https://api.platerecognizer.com/v1/plate-reader/',
        data=dict(regions=regions),  # Optional
        files=dict(upload=fp),
        headers={'Authorization': 'Token my-token******'})
pprint(response.json())

# Calling with a custom engine configuration
import json
with open('/path/to/car.jpg', 'rb') as fp:
    response = requests.post(
        'https://api.platerecognizer.com/v1/plate-reader/',
        data=dict(regions=['us-ca'], config=json.dumps(dict(region="strict"))),  # Optional
        files=dict(upload=fp),
        headers={'Authorization': 'Token my-token******'})
```

```shell
# On Linux
# Calling the API with 2 regions (Mexico and California).
curl -o /tmp/car.jpg https://app.platerecognizer.com/static/demo.jpg # Get an image
curl -F "upload=@/tmp/car.jpg" \
  -F regions=mx \
  -F regions=us-ca \
  -H "Authorization: Token my-token******" \
  https://api.platerecognizer.com/v1/plate-reader/

# Calling the API with a custom engine configuration and region California.
curl -F "upload=@/path/to/car.jpg" \
  -F regions=us-ca \
  -F config="{\"region\":\"strict\"}" \
  -H "Authorization: Token my-token******" \
  https://api.platerecognizer.com/v1/plate-reader/
  
# Calling the API with an image URL.
curl -X POST -F upload_url="https://app.platerecognizer.com/static/demo.jpg" -H "Authorization: Token my-token*****" https://api.platerecognizer.com/v1/plate-reader
```

```shell
# On Windows
# Calling the API with 2 regions (Mexico and California).
curl -o car.jpg https://app.platerecognizer.com/static/demo.jpg
curl -F "upload=@car.jpg" ^
  -F regions=mx ^
  -F regions=us-ca ^
  -H "Authorization: Token my-token******" ^
  https://api.platerecognizer.com/v1/plate-reader/

# Calling the API with a custom engine configuration and region California.
curl -F "upload=@c:\path\to\car.jpg" ^
  -F regions=us-ca ^
  -F config="{\"region\":\"strict\"}" ^
  -H "Authorization: Token my-token******" ^
  https://api.platerecognizer.com/v1/plate-reader/

```

```javascript
// Using Node-RED?
// Check https://github.com/parkpow/node-red-contrib-plate-recognizer

const fetch = require("node-fetch");
const FormData = require("form-data");
const fs = require("fs");

let image_path = "/path/to/car.jpg";
let body = new FormData();
body.append("upload", fs.createReadStream(image_path));
// Or body.append('upload', base64Image);
body.append("regions", "us-ca"); // Change to your country
fetch("https://api.platerecognizer.com/v1/plate-reader/", {
  method: "POST",
  headers: {
    Authorization: "Token my-token******",
  },
  body: body,
})
  .then((res) => res.json())
  .then((json) => console.log(json))
  .catch((err) => {
    console.log(err);
  });
```

```csharp
// Complete Example:

// https://github.com/marcbelmont/deep-license-plate-recognition/tree/master/csharp/PlateRecognizer

// Function calling the API:

// https://github.com/marcbelmont/deep-license-plate-recognition/blob/master/csharp/PlateRecognizer/PlateReader.cs#L23

```

```java
// Complete Example:

// https://github.com/marcbelmont/deep-license-plate-recognition/tree/master/java/PlateRecognizer

// Function calling the API:

// https://github.com/marcbelmont/deep-license-plate-recognition/blob/master/java/PlateRecognizer/src/main/java/com/mycompany/numberplate/recognize.java#L17    

// Complete Android App Example:

// https://github.com/parkpow/alpr-anpr-android/
```

```cpp
// Complete Example (Windows or Linux):

// https://github.com/marcbelmont/deep-license-plate-recognition/tree/master/java/PlateRecognizer

// Function calling the API:

// https://github.com/marcbelmont/deep-license-plate-recognition/blob/master/cpp/linux/numberPlate.cpp#L25
// https://github.com/marcbelmont/deep-license-plate-recognition/blob/master/cpp/windows/ConsoleApplication2/ConsoleApplication2.cpp#L29
```

> Return value

```json
{
  "processing_time": 288.758,
  "results": [
    {
      "box": {
        "xmin": 143,
        "ymin": 481,
        "xmax": 282,
        "ymax": 575
      },
      "plate": "nhk552",
      "region": {
        "code": "gb",
        "score": 0.747
      },
      "vehicle": {
        "score": 0.798,
        "type": "Sedan",
        "box": {
          "xmin": 67,
          "ymin": 113,
          "xmax": 908,
          "ymax": 653
        }
      },
      "score": 0.904,
      "candidates": [
        {
          "score": 0.904,
          "plate": "nhk552"
        }
      ],
      "dscore": 0.99,
      // Make Model, Orientation and Color are only available if you set mmc=true
      "model_make": [
        {
          "make": "Riley",
          "model": "RMF",
          "score": 0.306
        }
      ],
      "color": [
        {
          "color": "black",
          "score": 0.937
        }
      ],
      "orientation": [
        {
          "orientation": "Front",
          "score": 0.937
        }
      ]
    }
  ],
  "filename": "1617_7M83K_car.jpg",
  "version": 1,
  "camera_id": null,
  "timestamp": "2020-10-12T16:17:27.574008Z"
}
```

This Snapshot API endpoint reads all license plates from an image.

To programmatically process a bunch of images in a folder, refer to our [Snapshot Guides](https://guides.platerecognizer.com/docs/snapshot/bulk-processing).

If you need to detect vehicles and decode license plates from a live camera or video feed, consider using [Plate Recognizer Stream](https://platerecognizer.com/stream/?utm_source=docs&utm_medium=website). [Contact us](https://platerecognizer.com/contact?utm_source=docs&utm_medium=website) to request a Free Trial of Stream.

If you need to blur license plates, consider using [Plate Recognizer Blur](https://platerecognizer.com/blur/?utm_source=docs&utm_medium=website). [Contact us](https://platerecognizer.com/contact?utm_source=docs&utm_medium=website) for more info.

### HTTP Request

`POST https://api.platerecognizer.com/v1/plate-reader/`

\* The CORS policy of this endpoint allows requests from all origins.

### POST Parameters

| Parameter                       | Required | Description                                                                                                                                                                                                                                                                                                                                                                                                               |
|---------------------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| upload                          | Yes      | The file to be uploaded. The parameter can either be the **file bytes** (using Content-Type multipart/form-data) OR a **base64** encoded image. This parameter becomes **optional if upload_url parameter is present.**                                                                                                                                                                                                   |
| upload_url                      | No       | The url of file to be uploaded. This parameter is to be used as **an alternative to upload parameter.** This parameter is available only for Snapshot Cloud.                                                                                                                                                                                                                                                                                                                   |
| regions                         | No       | Match the license plate pattern of a specific region or [regions](#countries). This parameter can be used **multiple times** to specify more than one region. It is **treated as a guide** and the template will be ignored if the prediction differs too much from it. That's because we want to still be able to read plates from foreign vehicles. The system may sometimes mistake a local vehicle for a foreign one. |
| camera_id                       | No       | Unique camera identifier.                                                                                                                                                                                                                                                                                                                                                                                                 |
| timestamp                       | No       | [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) timestamp. For example, `2019-08-19T13:11:25`. The timestamp has to be in UTC.                                                                                                                                                                                                                                                                                         |
| mmc                             | No       | Predict vehicle make, model, orientation and color. This feature is available for an [additional fee](https://platerecognizer.com/pricing?utm_source=docs&utm_medium=website). Set parameter to true (mmc=true) if you have this feature enabled/purchased to get vehicle make, model and color. Possible values are `true` or `false`.                                                                                   |
| [config](#engine-configuration) | No       | Additional engine configuration. See [details](#engine-configuration).                                                                                                                                                                                                                                                                                                                                                    |

### JSON Response

The response is a list of all the license plates found in the image. Each license plate has the following elements:

| Attribute                       | Description                                                                                                         |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| results/plate                   | Text of the license plate.                                                                                          |
| results/box                     | Bounding box for the license plate. Coordinates in pixel of the top left and bottom right corners of the plate.     |
| results/dscore                  | Confidence level for plate **detection**.                                                                           |
| results/score                   | Confidence level for **reading** the license plate text.                                                            |
| results/vehicle/type            | Vehicle type: Big Truck, Bus, Motorcycle, Pickup Truck, Sedan, SUV, Van, Unknown.                                   |
| results/vehicle/score           | Confidence level for vehicle type prediction. If we **cannot find a vehicle**, the score is set to 0.               |
| results/vehicle/box             | Vehicle bounding box. If we **cannot find a vehicle**, the coordinates are all 0.                                   |
| results/region/code             | Region of license plate. Returns a code from the [country list](#countries).                                        |
| results/region/score            | Confidence level for license plate region.                                                                          |
| results/candidates              | List of predictions for the license plate value. The first element is the top prediction (same as `results/plate`). |
| results/model_make/make         | Prediction of vehicle make.                                                                                         |
| results/model_make/model        | Prediction of vehicle model.                                                                                        |
| results/model_make/score        | Confidence level for vehicle make and model prediction.                                                             |
| results/color/color             | Vehicle color. One of `black`, `blue`, `brown`, `green`, `red`, `silver`, `white`, `yellow`, `unknown`.             |
| results/color/score             | Confidence level for vehicle color prediction.                                                                      |
| results/orientation/orientation | Vehicle orientation. One of `Front`, `Rear`, `Unknown`.                                                             |
| results/orientation/score       | Confidence level for vehicle orientation prediction.                                                                |

- Scores vary between 0 and 1. A prediction with a **score close to 1** indicates high confidence.
- The value **dscore** is dependent on the type of image we are processing. For example, if the images are coming from a highway camera you may have a threshold of X for good license plate detection. But if images are coming from a parking lot you may have a threshold of Y. So a good dscore has to be determined based on the images you are sending to us.

<aside class="notice">
View complete examples for <a href="https://github.com/marcbelmont/deep-license-plate-recognition">ALPR API integration</a>. Easily do batching and use the API on a video. Examples are written in multiple languages.
</aside>

### [Error Responses](#errors)

## Engine Configuration

A lookup request can accept the parameter **config**. It is a JSON value to change the engine configuration. It can include the following values:

- `{"detection_rule":"strict"}`: The license plates that are detected outside a vehicle will be discarded.
- `{"detection_mode":"vehicle"}`: The default detection mode (plate) only returns vehicles if a license plate is also detected. To get vehicles without plates, use the value **vehicle**. See the [response format](#vehicle-only-response).
- `{"region":"strict"}`: Only accept the results that exactly match the templates of the specified region. For example, if the license plate of a region is 3 letters and 3 numbers, the value `abc1234` will be discarded. For regions with vanity license plates (e.g. in us-ca), we do not recommend the use of Strict Mode. Otherwise, the engine will discard the vanity plates. A region must be specified in order for this parameter to work.
- `{"threshold_d":0.2, "threshold_o":0.6}`: By default the engine will use those thresholds to filter the detection and OCR results. Anything below that will be discarded. You can set different values.
- `{"mode":"fast"}`: The number of detection steps is always 1. On average it gives a **30% speed-up**. May result in lower accuracy when using images with small vehicles.
- `{"mode":"redaction"}`: Used for Plate Recognizer Blur. It includes more candidates during the plate detection step. This configuration will **miss fewer plates** but will increase the number of false positives (objects that are not license plates).
- `{"text_formats":["..."]}`: List of regular expressions. They are used to **guide** the engine's predictions. For example, if you know the output is generally 3 digits and 2 letters, you would use `{"text_formats":["[0-9][0-9][0-9][a-z][a-z]"]}`. [More information](https://www.programiz.com/python-programming/regex) about the format. The engine may not follow the format when the predictions are too different. If you want to **exactly match** the regexp, you should also use `"region":"strict"` (see above).

### Examples

Here are a couple of examples of how to use the `config` parameter (with Shell or Python).

```shell
# On Linux
# Custom threshold and fast mode:
curl -F "upload=@/path/to/car.jpg" \
  -F config="{\"mode\":\"fast\", \"threshold_d\":0.2, \"threshold_o\":0.6}" \
  -H "Authorization: Token my-token******" \
  https://api.platerecognizer.com/v1/plate-reader/

# Strict region matching:
curl -F "upload=@/path/to/car.jpg" \
  -F config="{\"region\":\"strict\"}" \
  -F region=us-ca \
  -H "Authorization: Token my-token******" \
  https://api.platerecognizer.com/v1/plate-reader/

# Prediction must include a vehicle:
curl -F "upload=@/path/to/car.jpg" \
  -F config="{\"detection_rule\":\"strict\"}" \
  -H "Authorization: Token my-token******" \
  https://api.platerecognizer.com/v1/plate-reader/
```

```shell
# On Windows
# Custom threshold and fast mode:
curl -F "upload=@c:\path\to\car.jpg" ^
  -F config="{\"mode\":\"fast\", \"threshold_d\":0.2, \"threshold_o\":0.6}" ^
  -H "Authorization: Token my-token******" ^
  https://api.platerecognizer.com/v1/plate-reader/

# Strict region matching:
curl -F "upload=@c:\path\to\car.jpg" ^
  -F config="{\"region\":\"strict\"}" ^
  -F region=us-ca ^
  -H "Authorization: Token my-token******" ^
  https://api.platerecognizer.com/v1/plate-reader/

# Prediction must include a vehicle:
curl -F "upload=@c:\path\to\car.jpg" ^
  -F config="{\"detection_rule\":\"strict\"}" ^
  -H "Authorization: Token my-token******" ^
  https://api.platerecognizer.com/v1/plate-reader/
```

```python
import json
with open('/path/to/car.jpg', 'rb') as fp:
    response = requests.post(
        'https://api.platerecognizer.com/v1/plate-reader/',
        data=dict(config=json.dumps(dict(region="strict"))),
        files=dict(upload=fp),
        headers={'Authorization': 'Token my-token******'})
```

## Detection Zones

[Detection Zones](https://app.platerecognizer.com/detection-zone/) exclude overlay texts, street signs or other objects.

## Vehicle Only Response

> Return value

```json
{
  "filename": "car.jpg",
  "timestamp": "2021-11-23 17:56:47.896406",
  "camera_id": "my-camera",
  "processing_time": 58.184,
  "results": [
    {
      "plate": {
        "type": "Plate",
        "score": 0.925,
        "box": {
          "xmin": 146,
          "ymin": 481,
          "xmax": 276,
          "ymax": 576
        },
        "props": {
          "plate": [
            {
              "value": "nhk552",
              "score": 0.838
            }
          ],
          "region": [
            {
              "value": "gb",
              "score": 0.931
            }
          ]
        }
      },
      "vehicle": {
        "type": "Sedan",
        "score": 0.834,
        "box": {
          "xmin": 68,
          "ymin": 103,
          "xmax": 914,
          "ymax": 643
        },
        "props": {
          "make_model": [
            {
              "make": "Riley",
              "model": "RMF",
              "score": 0.339
            }
          ],
          "orientation": [
            {
              "value": "Front",
              "score": 0.939
            }
          ],
          "color": [
            {
              "value": "black",
              "score": 0.846
            }
          ]
        }
      }
    }
  ]
}
```

When the [parameter](#engine-configuration) `detection_mode` is set to `vehicle`, the output includes vehicles without a license plate. The lookup response is as follows.

- Each element of the list is now an object with two optional elements: `plate` and `vehicle`.
- The license plate text is stored in `plate.props.plate` as a list. The first element is the top prediction.
- The license plate region is in `plate.props.region`.
- The vehicle properties (orientation, make model and color) are in `vehicle.props`. Each include the top 3 predictions.

# On-Premise SDK

Our service is also available on-premises. [Get started](https://app.platerecognizer.com/sdk/) with the SDK. It has a **similar interface** as the [recognition API](#read-number-plates-from-an-image) and it is hosted locally. See differences below.

<aside class="notice">
  Are you using the on-premise API from a different device? Make sure that your firewall allows it. For example, you can disable the Windows Defender firewall to check if it is blocking the API calls.
</aside>

## Recognition API

```shell
# On Linux
# Calling the API with just the image
curl -o car.jpg https://app.platerecognizer.com/static/demo.jpg # Get an image
curl -F "upload=@car.jpg" \
  http://localhost:8080/v1/plate-reader/

# Calling with API with optional parameters config and mmc
# The region is set to Mexico and California
curl -F "upload=@/path/to/car.jpg" \
  -F regions=mx \
  -F regions=us-ca \
  -F mmc=true \
  -F config="{\"mode\":\"fast\"}" \
  http://localhost:8080/v1/plate-reader/

```

```shell
# On Windows
# Calling the API with just the image
curl -o car.jpg https://app.platerecognizer.com/static/demo.jpg
curl -F "upload=@car.jpg" ^
  http://localhost:8080/v1/plate-reader/

# Calling with API with optional parameters config and mmc
# The region is set to Mexico and California
curl -F "upload=@c:\path\to\car.jpg" ^
  -F regions=mx ^
  -F regions=us-ca ^
  -F mmc=true ^
  -F config="{\"mode\":\"fast\"}" ^
  http://localhost:8080/v1/plate-reader/

```

> Return value

```json
{
  "usage": {
    "max_calls": 1000,
    "calls": 44
  },
  "results": [
    // Same as cloud API example above.
  ],
  "camera_id": "null",
  "timestamp": "2020-01-16 17:00:00",
  "filename": "car.jpg"
}
```

### HTTP Request

`POST http://localhost:8080/v1/plate-reader/`

\* The CORS policy of this endpoint allows requests from all origins.

### POST Parameters

| Parameter | Required | Description                                                                       |
| --------- | -------- | --------------------------------------------------------------------------------- |
| -         | -        | **All** the parameters from [recognition API](#read-number-plates-from-an-image). |

### JSON Response

Returns the same parameters as the [recognition API](#read-number-plates-from-an-image). In addition to that, it returns the **number of calls** used.

## SDK Version

> Return value

```json
{
  "version": "1.3.8",
  "license_key": "XXX",
  "total_calls": 2500,
  "usage": {"calls": 10},
  "webhooks": []
}
```

Show version, webhooks and usage information.

### HTTP Request

`GET http://localhost:8080/info/`

### JSON Response

Returns the SDK version, license key and webhooks.

# WebHooks

> Example of POST payload

```json
{
  "hook": {
    "target": "http://localhost:8081/",
    "id": 2,
    "event": "image.done"
  },
  "data": {
    "processing_time": 0.186,
    "timestamp": "2019-08-31T14:22:06.983Z",
    "results": [
      {
        "box": {
          "xmin": 563,
          "ymin": 530,
          "ymax": 579,
          "xmax": 702
        },
        "plate": "765410",
        "score": 0.9131534678711563,
        "dscore": 0.4084282568529709
      },
      {
        "box": {
          "xmin": 563,
          "ymin": 530,
          "ymax": 579,
          "xmax": 702
        },
        "plate": "830265",
        "score": 0.7700640306685624,
        "dscore": 0.8493143507578083
      }
    ],
    "filename": "14_22_test.jpeg",
    "version": 1,
    "camera_id": null
  }
}
```

Our service also supports webhooks. It allows you to receive a HTTP **POST** request to a target URL of your choosing. Go to [webhooks settings page](https://app.platerecognizer.com/accounts/webhooks/) to manage your webhooks or add a new webhook target. If you are using Stream, see those [instructions](https://guides.platerecognizer.com/docs/stream/configuration#webhook-parameters).

- The `target_url` should return a **valid HTTP status code** (200). If the `target_url` consistently returns an error code, the hook will be removed and an email will be sent to the account owner.
- To quickly test out this feature, you can use [http://webhook.site](http://webhook.site). It generates a unique target URL and displays all the requests made to that URL.
- A webhook is sent for each call to the recognition API even when no license plate is found.

<aside class="notice">
We have sample code for how to <a href="https://github.com/marcbelmont/deep-license-plate-recognition/tree/master/webhooks/">set up a webhook</a> on your own machine.
</aside>

## Data Only Webhook

### Snapshot: HTTP Request

`POST target_url`

The request body contains the payload above in JSON format.

### Stream: HTTP Request

`POST target_url`

The request body contains a `json` field with JSON encoded data. See the example above for the content of that field.

## Webhook With Image

### Snapshot & Stream: HTTP Request

`POST target_url`

The request body contains a `multipart/form-data` content. Receiving this data is like receiving the content of a form with an `input` of type file. It has two fields:

- `upload` contains the image binary content
- `json` is the JSON encoded data. See the example of the payload above.

<aside class="notice">
The bounding box of the license plate are calculated based on the image sent by webhook.
</aside>

# Statistics

Get number of recognition calls done during the current month. This API is for **Snapshot Cloud only**. For Snapshot SDK, use the [info API](#sdk-version).

### HTTP Request

`GET https://api.platerecognizer.com/v1/statistics/`

```shell
curl -H "Authorization: Token my-token******" \
  https://api.platerecognizer.com/v1/statistics/
```

> Return value

```json
{
  "usage": {
    "month": 1,
    "calls": 128,
    "year": 2019,
    "resets_on": "2019-01-14T00:00:00Z"
  },
  "total_calls": 2500
}
```

### JSON Response

| Attribute   | Description                                                                                                                   |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------- |
| calls       | Number of API calls made during the current period.                                                                           |
| month       | Month of the current period.                                                                                                  |
| year        | Year of the current period.                                                                                                   |
| resets_on   | Date when the counter will reset.                                                                                             |
| total_calls | Maximum number of API calls you can make during the period. Need more? [Upgrade](https://app.platerecognizer.com/subscribe/). |

# ─

# Doc for Other Products

The documentation for Stream, our Highly-accurate ALPR software that processes live camera or video feeds quickly & efficiently, is on a different page.

For Stream and other products, see [this documentation](https://app.platerecognizer.com/stream-docs).

# FAQ

Can't make it work?
Check our [step by step guides](https://guides.platerecognizer.com/) or [FAQ](https://platerecognizer.com/help/).
