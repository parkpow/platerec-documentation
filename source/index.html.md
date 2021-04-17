---
title: ALPR API Documentation

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
  - <hr>
  - © 2021 ParkPow, Inc.
  - All Rights Reserved.
  - Documentation is governed by our
  - <a href="https://platerecognizer.com/terms-of-use-privacy-policy/">Terms</a>.

includes:
  - errors

search: true
---

# Introduction

Welcome to the Plate Recognizer Snapshot API!  You can use our API to access our API endpoints, which can read license plates from images. For detailed instructions on how to install the SDK, go [here](https://app.platerecognizer.com/sdk/?utm_source=docs&utm_medium=website).

We have multiple language bindings. You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.


# Authentication

Plate Recognizer Snapshot API is only available to registered users.  [Sign up](https://app.platerecognizer.com/accounts/signup/?utm_source=docs&utm_medium=website) for a Free Trial and get an **API key**. It has to be included in all API calls. The HTTP **headers** must contain:

`Authorization: Token API_TOKEN`

<aside class="notice">
You must replace <code>API_TOKEN</code> with your personal API key.
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
  req['Authorization'] = 'Token API_TOKEN'
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
    'regions' => 'fr' // Optional
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
    "Authorization: Token API_TOKEN"  //API KEY
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
regions = ['gb', 'it'] # Change to your country
with open('/path/to/car.jpg', 'rb') as fp:
    response = requests.post(
        'https://api.platerecognizer.com/v1/plate-reader/',
        data=dict(regions=regions),  # Optional
        files=dict(upload=fp),
        headers={'Authorization': 'Token API_TOKEN'})
pprint(response.json())

# Calling with a custom engine configuration
import json
with open('/path/to/car.jpg', 'rb') as fp:
    response = requests.post(
        'https://api.platerecognizer.com/v1/plate-reader/',
        data=dict(regions=['au'], config=json.dumps(dict(region="strict"))),  # Optional
        files=dict(upload=fp),
        headers={'Authorization': 'Token API_TOKEN'})
```

```shell
# Calling the API with 2 regions
curl -F 'upload=@/path/to/car.jpg' \
  -F regions=fr \
  -F regions=gb \
  -H 'Authorization: Token API_TOKEN' \
  https://api.platerecognizer.com/v1/plate-reader/

# Calling the API with a custom engine configuration
curl -F 'upload=@/path/to/car.jpg' \
  -F regions=fr -F config='{"region":"strict"}' \
  -H 'Authorization: Token API_TOKEN' \
  https://api.platerecognizer.com/v1/plate-reader/
```

```javascript
// Using Node-RED?
// Check https://github.com/parkpow/node-red-contrib-plate-recognizer

const fetch = require('node-fetch');
const FormData = require('form-data');
const fs = require('fs');

let image_path = '/path/to/car.jpg'
let body = new FormData();
body.append('upload', fs.createReadStream(image_path));
// Or body.append('upload', base64Image);
body.append('regions', 'gb'); // Change to your country
fetch("https://api.platerecognizer.com/v1/plate-reader/", {
        method: 'POST',
        headers: {
            "Authorization": "Token API_TOKEN"
        },
        body: body
    }).then(res => res.json())
    .then(json => console.log(json))
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

If you need to detect vehicles and decode license plates from a live camera or video feed, consider using [Plate Recognizer Stream](https://platerecognizer.com/stream/?utm_source=docs&utm_medium=website). [Contact us](https://platerecognizer.com/contact?utm_source=docs&utm_medium=website) to request a Free Trial of Stream.

If you need to blur license plates, consider using [Plate Recognizer Blur](https://platerecognizer.com/blur/?utm_source=docs&utm_medium=website). [Contact us](https://platerecognizer.com/contact?utm_source=docs&utm_medium=website) for more info.

### HTTP Request

`POST https://api.platerecognizer.com/v1/plate-reader/`

### POST Parameters

| Parameter | Required | Description                                                                                                                                                                                                                                                                                                                             |
| --------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| upload    | Yes      | The file to be uploaded. The parameter can either be the **file bytes** (using Content-Type multipart/form-data) OR a **base64** encoded image.                                                                                                                                                                                         |
| regions   | No       | Match the license plate pattern of a specific region or [regions](#countries). This parameter can be used **multiple times** to specify more than one region. *                                                                                                                                                                         |
| camera_id | No       | Unique camera identifier.                                                                                                                                                                                                                                                                                                               |
| timestamp | No       | [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) timestamp. For example, `2019-08-19T13:11:25`. The timestamp has to be in UTC.                                                                                                                                                                                                       |
| mmc       | No       | Predict vehicle make, model, orientation and color. This feature is available for an [additional fee](https://platerecognizer.com/pricing?utm_source=docs&utm_medium=website). Set parameter to true (mmc=true) if you have this feature enabled/purchased to get vehicle make, model and color. Possible values are `true` or `false`. |
| config    | No       | Additional engine configuration. See below.                                                                                                                                                                                                                                                                                             |

 \* The regions parameter is used as a guide and the template will be ignored if the prediction differs too much from it. It works this way because we want to still be able to read plates from foreign vehicles. The system may sometimes mistake a local vehicle for a foreign one.

**config** is a JSON value to change the engine configuration. It can include the following values:

- `{"region":"strict"}`: Only accept the results that exactly match the templates of the specified region. For example, if the license plate of a region is 3 letters and 3 numbers, the value abc1234 will be discarded. For regions with vanity license plates (e.g. in us-ca), we do not recommend the use of Strict Mode. Otherwise, the engine will discard the vanity plates.
- `{"threshold_d":0.2, "threshold_o":0.6}`: By default the engine will use those thresholds to filter the detection and OCR results. Anything below that will be discarded. You can set different values.
- `{"mode":"fast"}`: The number of detection steps is always 1. On average it gives a **30% speed-up**. May result in lower accuracy when using images with small vehicles.
- `{"mode":"redaction"}`: Used for license plate redaction. It includes more candidates during the plate detection step. This configuration will **miss fewer plates** but will increase the number of false positives (objects that are not license plates).


### JSON Response

The response is a list of all the license plates found in the image. Each license plate has the following elements:

| Attribute                       | Description                                                                                                         |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| results/plate                   | Text of the license plate.                                                                                          |
| results/box                     | Bounding box for the license plate. Coordinates in pixel of the top left and bottom right corners of the plate.     |
| results/dscore                  | Confidence level for plate **detection**.                                                                           |
| results/score                   | Confidence level for **reading** the license plate text.                                                            |
| results/vehicle/type            | Vehicle type: Big Truck, Bus, Motorcycle, Pickup Truck, Sedan, SUV, Van, Unknown.                                   |
| results/vehicle/score           | Confidence level for vehicle prediction.                                                                            |
| results/vehicle/box             | Vehicle bounding box.                                                                                               |
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

## Countries

Codes for the `regions` parameter. You can also provide a state to select more specific license plate patterns. States are only supported for some countries.

Don't see your country listed?   [Contact us](https://platerecognizer.com/contact?utm_source=docs&utm_medium=website) for more info.

The column **Prediction** indicates if the region is supported by our region classification predictor (field `results/region/code`).

| Country                                               | Region code | Prediction |
| ----------------------------------------------------- | ----------- | ---------- |
| Albania                                               | al          |            |
| Andorra                                               | ad          |            |
| Angola                                                | ao          |            |
| Argentina                                             | ar          | Yes        |
| Armenia                                               | am          | Yes        |
| [Australia](#australia)                               |             | Yes        |
| Austria                                               | at          | Yes        |
| Azerbaijan                                            | az          | Yes        |
| Belarus                                               | by          | Yes        |
| Belgium                                               | be          | Yes        |
| Belize                                                | bz          |            |
| Bolivia                                               | bo          |            |
| Bosnia and Herzegovina                                | ba          |            |
| Brazil                                                | br          | Yes        |
| Brunei                                                | bn          |            |
| Bulgaria                                              | bg          | Yes        |
| [Canada](#canada)                                     |             | Yes        |
| Chile                                                 | cl          | Yes        |
| Colombia                                              | co          | Yes        |
| Costa Rica                                            | cr          | Yes        |
| Croatia                                               | hr          | Yes        |
| Cyprus                                                | cy          |            |
| Czechia                                               | cz          | Yes        |
| Denmark                                               | dk          |            |
| Ecuador                                               | ec          |            |
| El Salvador                                           | sv          |            |
| Estonia                                               | ee          | Yes        |
| Finland                                               | fi          | Yes        |
| France                                                | fr          | Yes        |
| French Guiana                                         | gf          |            |
| Georgia                                               | ge          | Yes        |
| Germany                                               | de          | Yes        |
| Gibraltar                                             | gi          |            |
| Greece                                                | gr          | Yes        |
| Guatamala                                             | gp          |            |
| Guyana                                                | gy          |            |
| Holy See                                              | va          |            |
| Honduras                                              | hn          |            |
| Hungary                                               | hu          | Yes        |
| Iceland                                               | is          |            |
| Israel                                                | il          | Yes        |
| India                                                 | in          | Yes        |
| Indonesia                                             | id          | Yes        |
| Ireland                                               | ie          |            |
| Italy                                                 | it          | Yes        |
| Jordan                                                | jo          |            |
| Kazakhstan                                            | kz          | Yes        |
| Kenya                                                 | ke          |            |
| Korea, Republic of                                    | kr          |            |
| Latvia                                                | lv          | Yes        |
| Lebanon                                               | lb          |            |
| Liechtenstein                                         | li          |            |
| Lithuania                                             | lt          | Yes        |
| Luxembourg                                            | lu          | Yes        |
| Malaysia                                              | my          |            |
| Maldives                                              | mv          |            |
| Malta                                                 | mt          |            |
| Mexico                                                | mx          |            |
| Moldova, Republic of                                  | md          | Yes        |
| Monaco                                                | mc          | Yes        |
| Montenegro                                            | me          | Yes        |
| Nepal                                                 | np          |            |
| Netherlands                                           | nl          | Yes        |
| New Zealand                                           | nz          | Yes        |
| Nicaragua                                             | ni          |            |
| Nigeria                                               | ng          |            |
| North Macedonia                                       | mk          |            |
| Norway                                                | no          | Yes        |
| Panama                                                | pa          |            |
| Paraguay                                              | py          |            |
| Peru                                                  | pe          |            |
| Philippines                                           | ph          |            |
| Poland                                                | pl          | Yes        |
| Portugal                                              | pt          | Yes        |
| Qatar                                                 | qa          |            |
| Romania                                               | ro          | Yes        |
| Russian Federation                                    | ru          |            |
| San Marino                                            | sm          |            |
| Serbia                                                | rs          | Yes        |
| Singapore                                             | sg          |            |
| Slovakia                                              | sk          | Yes        |
| Slovenia                                              | si          |            |
| South Africa                                          | za          | Yes        |
| Spain                                                 | es          | Yes        |
| Suriname                                              | sr          |            |
| Sweden                                                | se          | Yes        |
| Switzerland                                           | ch          | Yes        |
| Tanzania                                              | tz          |            |
| [Thailand](#thailand)                                 |             | Yes        |
| Turkey                                                | tr          | Yes        |
| Ukraine                                               | ua          | Yes        |
| [United Arab Emirates](#united-arab-emirates)         |             | Yes        |
| United Kingdom of Great Britain                       | gb          | Yes        |
| [United States of America](#united-states-of-america) |             | Yes        |
| Uraguay                                               | uy          |            |
| Uzbekistan                                            | uz          | Yes        |
| Venezuela                                             | ve          |            |
| Vietnam                                               | vn          | Yes        |
| Zambia                                                | zm          |            |

### United States of America

| State                | Region code |
| -------------------- | ----------- |
| Alabama              | us-al       |
| Alaska               | us-ak       |
| Arizona              | us-az       |
| Arkansas             | us-ar       |
| California           | us-ca       |
| Colorado             | us-co       |
| Connecticut          | us-ct       |
| Delaware             | us-de       |
| District of Columbia | us-dc       |
| Florida              | us-fl       |
| Georgia              | us-ga       |
| Hawaii               | us-hi       |
| Idaho                | us-id       |
| Illinois             | us-il       |
| Indiana              | us-in       |
| Iowa                 | us-ia       |
| Kansas               | us-ks       |
| Kentucky             | us-ky       |
| Louisiana            | us-la       |
| Maine                | us-me       |
| Maryland             | us-md       |
| Massachusetts        | us-ma       |
| Michigan             | us-mi       |
| Minnesota            | us-mn       |
| Mississippi          | us-ms       |
| Missouri             | us-mo       |
| Montana              | us-mt       |
| Nebraska             | us-ne       |
| Nevada               | us-nv       |
| New Hampshire        | us-nh       |
| New Jersey           | us-nj       |
| New Mexico           | us-nm       |
| New York             | us-ny       |
| North Carolina       | us-nc       |
| North Dakota         | us-nd       |
| Ohio                 | us-oh       |
| Oklahoma             | us-ok       |
| Oregon               | us-or       |
| Pennsylvania         | us-pa       |
| Rhode Island         | us-ri       |
| South Carolina       | us-sc       |
| South Dakota         | us-sd       |
| Tennessee            | us-tn       |
| Texas                | us-tx       |
| Utah                 | us-ut       |
| Vermont              | us-vt       |
| Virginia             | us-va       |
| Washington           | us-wa       |
| West Virginia        | us-wv       |
| Wisconsin            | us-wi       |
| Wyoming              | us-wy       |

### Thailand

<aside class="warn">
This country is only available in Snapshot SDK.
</aside>

Thai characters are not supported on our Cloud API. You will need a custom Docker image.
Replace `platerecognizer/alpr` by `platerecognizer/alpr:thailand` in the [installation instructions](https://app.platerecognizer.com/sdk/).

| State               | Region code |
| ------------------- | ----------- |
| Amnat Charoen       | th-37       |
| Ang Thong           | th-15       |
| Ayutthaya           | th-14       |
| Bangkok             | th-10       |
| Bangkok             | th-10b      |
| Bangkok             | th-10c      |
| Batong              | th-99       |
| Bueng Kan           | th-38       |
| Buri Ram            | th-31       |
| Chachoengsao        | th-24       |
| Chai Nat            | th-18       |
| Chaiyaphum          | th-36       |
| Chanthaburi         | th-22       |
| Chiang Mai          | th-50       |
| Chiang Rai          | th-57       |
| Chon Buri           | th-20       |
| Chumphon            | th-86       |
| Kalasin             | th-46       |
| Kamphaeng Phet      | th-62       |
| Kanchanaburi        | th-71       |
| Khon Kaen           | th-40       |
| Krabi               | th-81       |
| Lampang             | th-52       |
| Lamphun             | th-51       |
| Loei                | th-42       |
| Lop Buri            | th-16       |
| Mae Hong Son        | th-58       |
| Maha Sarakham       | th-44       |
| Mukdahan            | th-49       |
| Nakhon Nayok        | th-26       |
| Nakhon Pathom       | th-73       |
| Nakhon Phanom       | th-48       |
| Nakhon Ratchasima   | th-30       |
| Nakhon Sawan        | th-60       |
| Nakhon Si Thammarat | th-80       |
| Nan                 | th-55       |
| Narathiwat          | th-96       |
| Nong Bua Lam Phu    | th-39       |
| Nong Khai           | th-43       |
| Nonthaburi          | th-12       |
| Pathum Thani        | th-13       |
| Pattani             | th-94       |
| Phangnga            | th-82       |
| Phatthalung         | th-93       |
| Phayao              | th-56       |
| Phetchabun          | th-67       |
| Phetchaburi         | th-76       |
| Phichit             | th-66       |
| Phitsanulok         | th-65       |
| Phrae               | th-54       |
| Phuket              | th-83       |
| Prachin Buri        | th-25       |
| Prachuap Khiri Khan | th-77       |
| Ranong              | th-85       |
| Ratchaburi          | th-70       |
| Rayong              | th-21       |
| Roi Et              | th-45       |
| Sa Kaeo             | th-27       |
| Sakon Nakhon        | th-47       |
| Samut Prakan        | th-11       |
| Samut Sakhon        | th-74       |
| Samut Songkhram     | th-75       |
| Saraburi            | th-19       |
| Satun               | th-91       |
| Sing Buri           | th-17       |
| Sisaket             | th-33       |
| Songkhla            | th-90       |
| Sukhothai           | th-64       |
| Suphan Buri         | th-72       |
| Surat Thani         | th-84       |
| Surin               | th-32       |
| Tak                 | th-63       |
| Trang               | th-92       |
| Trat                | th-23       |
| Ubon Ratchathani    | th-34       |
| Udon Thani          | th-41       |
| Uthai Thani         | th-61       |
| Uttaradit           | th-53       |
| Yala                | th-95       |
| Yasothon            | th-35       |

### Australia

| State             | Region code |
| ----------------- | ----------- |
| New South Wales   | au-nsw      |
| Queensland        | au-qld      |
| South Australia   | au-sa       |
| Tasmania          | au-tas      |
| Victoria          | au-vic      |
| Western Australia | au-wa       |

### Brazil

| State               | Region Code |
| ------------------- | ----------- |
| Acre                | br-ac       |
| Alagoas             | br-al       |
| Amapá               | br-ap       |
| Amazonas            | br-am       |
| Bahia               | br-ba       |
| Ceará               | br-ce       |
| Distrito Federal    | br-df       |
| Espírito Santo      | br-es       |
| Goiás               | br-go       |
| Manias Gerais       | br-mg       |
| Maranhão            | br-ma       |
| Mato Grosso do Sul  | br-ms       |
| Mato Grosso         | br-mt       |
| Pernambuco          | br-pe       |
| Pará                | br-pa       |
| Paraíba             | br-pb       |
| Paraná              | br-pr       |
| Piauí               | br-pi       |
| Rio de Janeiro      | br-rj       |
| Rio Grande do Norte | br-rn       |
| Rio Grande do Sul   | br-rs       |
| Rondônia            | br-ro       |
| Roraima             | br-rr       |
| São Paulo           | br-sp       |
| Sergipe             | br-se       |
| Santa Catarina      | br_sc       |
| Tocantins           | br-to       |


### Canada

| State                     | Region Code |
| ------------------------- | ----------- |
| Alberta                   | ca-ab       |
| British Columbia          | ca-bc       |
| Manitoba                  | ca-mb       |
| New Brunswick             | ca-nb       |
| Newfoundland and Labrador | ca-nl       |
| Northwest Territories     | ca-nt       |
| Nova Scotia               | ca-ns       |
| Nunavut                   | ca-nu       |
| Ontario                   | ca-on       |
| Prince Edward Islands     | ca-pe       |
| Quebec                    | ca-qc       |
| Saskatchewan              | ca-sk       |
| Yukon                     | ca-yt       |


### United Arab Emirates

| State                     | Region Code |
| ------------------------- | ----------- |
| Abu Dhabi                 | ae-az       |
| Ajman                     | ae-aj       |
| Dubai                     | ae-du       |
| Fujairah                  | ae-fu       |
| Ras Al Khaimah            | ae-rk       |
| Sharjah                   | ae-sh       |
| Umm Al Quwain             | ae-uq       |


# SDK

## Recognition API

```shell
# Calling the API with just the image
curl -F 'upload=@/path/to/car.jpg' \
  http://localhost:8080/v1/plate-reader/

# Calling with API with optional parameters config and mmc
curl -F 'upload=@/path/to/car.jpg' \
  -F regions=fr \
  -F regions=it \
  -F mmc=true \
  -F config='{"mode":"fast"}' \
  http://localhost:8080/v1/plate-reader/

```


> Return value

```json
{
  "usage":{
    "max_calls":1000,
    "calls":44
  },
  "results":[
    // Same as cloud API example above.
  ],
  "camera_id": "null",
  "timestamp": "2020-01-16 17:00:00",
  "filename":"car.jpg"
}
```

Our service is also available on-premises. [Get started](https://app.platerecognizer.com/sdk/) with the SDK. It has a **similar interface** as the [recognition API](#read-number-plates-from-an-image) and it is hosted locally. See differences below.

### HTTP Request

`POST http://localhost:8080/v1/plate-reader/`

### POST Parameters

| Parameter | Required | Description                                                                       |
| --------- | -------- | --------------------------------------------------------------------------------- |
| -         | -        | **All** the parameters from [recognition API](#read-number-plates-from-an-image). |


### JSON Response

Returns the same parameters as the [recognition API](#read-number-plates-from-an-image). In addition to that, it returns the **number of calls** used.


## SDK version

> Return value

```json
{
    "version": "1.3.8",
    "license_key": "XXX",
    "webhooks": []
}
```

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

Our service also supports webhooks. It allows you to receive a HTTP **POST** request to a target URL of your choosing. Go to [webhooks settings page](https://app.platerecognizer.com/accounts/webhooks/) to manage your webhooks or add a new webhook target. If you are using Stream, see those [instructions](https://docs.google.com/document/d/1vLwyx4gQvv3gF_kQUvB5sLHoY0IlxV5b3gYUqR2wN1U/edit#heading=h.564g7uuo44vp).

To quickly test out this feature, you can use [http://webhook.site](http://webhook.site). It generates a unique target URL and displays all the requests made to that URL.

The `target_url` should return a **valid HTTP status code** (200). If the `target_url` consistently returns an error code, the hook will be removed and an email will be sent to the account owner.

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

Get number of recognition calls done during the current month.

### HTTP Request
`GET https://api.platerecognizer.com/v1/statistics/`

```shell
curl -H 'Authorization: Token API_TOKEN' \
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
