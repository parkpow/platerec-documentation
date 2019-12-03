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
  - <a href='https://platerecognizer.com/accounts/signup/?from-doc'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/marcbelmont/deep-license-plate-recognition'>Code Examples</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Plate Recognizer API! You can use our API to access our API endpoints, which can read license plates from images. For detailed instructions
on how to install the SDK, go [here](https://app.platerecognizer.com/sdk/).

We have multiple language bindings. You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.


# Authentication

Plate Recognizer API is only available to registered users. You first have to register and **[get an API key](https://platerecognizer.com/)**. It has to be included in all API calls. The HTTP **headers** must contain:

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
regions = ['fr', 'it']
with open('/path/to/car.jpg', 'rb') as fp:
    response = requests.post(
        'https://api.platerecognizer.com/v1/plate-reader/',
        data=dict(regions=regions),  # Optional
        files=dict(upload=fp),
        headers={'Authorization': 'Token API_TOKEN'})
pprint(response.json())

```

```shell
curl -F 'upload=@/path/to/car.jpg' \
  -H 'Authorization: Token API_TOKEN' \
  https://api.platerecognizer.com/v1/plate-reader/

# You may also include regions
curl -F 'upload=@/path/to/car.jpg' \
  -F regions=fr -F regions=us-ca \
  -H 'Authorization: Token API_TOKEN' \
  https://api.platerecognizer.com/v1/plate-reader/

```

```javascript
const fetch = require('node-fetch');
const FormData = require('form-data');
const fs = require('fs');

let image_path = '/path/to/car.jpg'
let body = new FormData();
body.append('upload', fs.createReadStream(image_path));

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
// View the complete example here:
// https://github.com/marcbelmont/deep-license-plate-recognition/tree/master/csharp/PlateRecognizer

HttpWebRequest request = WebRequest.Create(postUrl) as HttpWebRequest;

// Set up the request properties.
request.Method = "POST";
request.ContentType = contentType;
request.UserAgent = ".NET Framework CSharp Client";
request.CookieContainer = new CookieContainer();
request.ContentLength = formData.Length;
request.Headers.Add("Authorization", "Token " + token);

// Send the form data to the request.
using (Stream requestStream = request.GetRequestStream())
{
    requestStream.Write(formData, 0, formData.Length);
    requestStream.Close();
}

HttpWebResponse response = (HttpWebResponse)request.GetResponse();

```

```java
// View the complete example here:
// https://github.com/marcbelmont/deep-license-plate-recognition/tree/master/java/PlateRecognizer

String token = "MY_API_KEY";
String file = "C:\\assets\\demo.jpg";
        
try{
  HttpResponse<String> response = Unirest.post("https://api.platerecognizer.com/v1/plate-reader/")
  .header("Authorization", "Token "+token)
  .field("upload", new File(file))
  .asString();
  System.out.println("Recognize:");
  System.out.println(response.getBody().toString());
}
catch(Exception e){
  System.out.println(e);
}
        
        
    
```

```cpp
// View the complete example here:
// https://github.com/marcbelmont/deep-license-plate-recognition/tree/master/java/PlateRecognizer
CURL *hnd = curl_easy_init();

//curl_easy_setopt(hnd, CURLOPT_CUSTOMREQUEST, "POST");
curl_easy_setopt(hnd, CURLOPT_URL, "https://api.platerecognizer.com/v1/plate-reader/");

form = curl_mime_init(hnd); //initialize form fields

/* Fill in the file upload field */
field = curl_mime_addpart(form);
curl_mime_name(field, "upload");
curl_mime_filedata(field, fileName.c_str());
curl_easy_setopt(hnd, CURLOPT_MIMEPOST, form);
```



> Return value

```json
{
   "processing_time":78.569,
   "results":[
      {
         "box":{
            "ymin":478,
            "xmin":148,
            "ymax":579,
            "xmax":288
         },
         "plate":"nhk552",
         "region":{
            "score":0.948,
            "code":"gb"
         },
         "vehicle":{
            "score":0.842,
            "type":"Car"
         },
         "score":0.896,
         "candidates":[
            {
               "score":0.896,
               "plate":"nhk552"
            }
         ],
         "dscore":0.74
      }
   ],
   "filename":"15_54_bcar_586jvjP.jpg",
   "version":1,
   "camera_id":null,
   "timestamp":"2019-12-03T15:54:49.093210Z"
}
```

This endpoint reads all license plates from an image.

### HTTP Request

`POST https://api.platerecognizer.com/v1/plate-reader/`

### POST Parameters

| Parameter | Required | Description                                                                                                                                                 |
| --------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| upload    | Yes      | The file to be uploaded                                                                                                                                     |
| regions   | No       | Match the license plate pattern of specific [regions](#regions-supported). This parameter can be used **multiple times** to specify more than one region. * |
| camera_id | No       | Unique camera identifier.                                                                                                                                   |
| timestamp | No       | [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) timestamp. For example, 2019-08-19T13:11:25. The timestamp has to be in UTC.                             |

 \* The regions parameter is used as a guide and the template will be ignored if the prediction differs too much from it. It works this way because we want to still be able to read plates from foreign vehicles. The system may sometimes mistake a local vehicle for a foreign one.

### JSON Response

The response is a list of all the license plates found in the image. Each license plate has the following elements:

| Attribute            | Description                                                                                                     |
| -------------------- | --------------------------------------------------------------------------------------------------------------- |
| results/plate        | Text of the license plate.                                                                                      |
| results/box          | Bounding box for the license plate. Coordinates in pixel of the top left and bottom right corners of the plate. |
| results/dscore       | Score for plate detection. Range [0, 1].                                                                        |
| results/score        | Score for reading the license plate text. Range [0, 1].                                                         |
| results/vehicle/type | Vehicle type: Ambulance, Bus, Car, Limousine, Motorcycle, Taxi, Truck, Van, Unknown.                            |
| results/region/code  | Region of license plate. Returns a code from the [country list](#regions-supported).                            |


The value **dscore** is dependent on the type of image we are processing. For example, if the images are coming from a highway camera you may have a threshold of X for good license plate detection. But if images are coming from a parking lot you may have a threshold of Y. So a good dscore has to be determined based on the images you are sending to us.

<aside class="notice">
View complete examples for <a href="https://github.com/marcbelmont/deep-license-plate-recognition">ALPR API integration</a>. Easily do batching and use the API on a video. Examples are written in multiple languages.
</aside>

<!--
## Camera and Location Support
```shell
curl -F 'upload=@/path/to/car.jpg' \
  -F regions=fr -F regions=us-ca \
  -F camera_id=123 \
  -F latitude=1.23366 \
  -F longitude=-12.44556 \
  -H 'Authorization: Token API_TOKEN' \
  https://api.platerecognizer.com/v1/plate-reader-dashboard/

```

This endpoint supports additional camera and location parameters. <a href="https://platerecognizer.com/contact/">Contact</a> us is you are interested in using this feature.

### HTTP Request

`POST https://api.platerecognizer.com/v1/plate-reader-dashboard/`

### POST Parameters

| Parameter | Required | Description                                                                                                           |
| --------- | -------- | --------------------------------------------------------------------------------------------------------------------- |
| upload    | Yes      | The file to be uploaded                                                                                               |
| regions   | No       | Match the license plate pattern of specific [regions](#regions-supported). This parameter can be used multiple times. |
| camera_id | Yes      | unique ID for camera                                                                                                  |
| latitude  | No       | Latitude location of the camera                                                                                       |
| longitude | No       | Logitude location of the camera                                                                                       |

<aside class="notice">
Support for camera and location is currently in beta testing and is only available on invite-only basis.
</aside>
 -->

## Regions supported

The regions parameter can take one of the following codes. In addition to country, you can also provide a [states](#states) to select more specific license plate patterns. States are only supported for USA and Australia.

| Country                                              | Region code |
| ---------------------------------------------------- | ----------- |
| Albania                                              | al          |
| Andorra                                              | ad          |
| Angola                                               | ao          |
| Argentina                                            | ar          |
| Armenia                                              | am          |
| Australia                                            | au          |
| Austria                                              | at          |
| Azerbaijan                                           | az          |
| Belarus                                              | by          |
| Belgium                                              | be          |
| Belize                                               | bz          |
| Bolivia                                              | bo          |
| Bosnia and Herzegovina                               | ba          |
| Brazil                                               | br          |
| Brunei                                               | bn          |
| Bulgaria                                             | bg          |
| Canada                                               | ca          |
| Chile                                                | cl          |
| Columbia                                             | co          |
| Costa Rica                                           | cr          |
| Croatia                                              | hr          |
| Cyprus                                               | cy          |
| Czechia                                              | cz          |
| Denmark                                              | dk          |
| Ecuador                                              | ec          |
| El Salvador                                          | sv          |
| Estonia                                              | ee          |
| Finland                                              | fi          |
| France                                               | fr          |
| French Guiana                                        | gf          |
| Georgia                                              | ge          |
| Germany                                              | de          |
| Gibraltar                                            | gi          |
| Greece                                               | gr          |
| Guatamala                                            | gp          |
| Guyana                                               | gy          |
| Holy See                                             | va          |
| Honduras                                             | hn          |
| Hungary                                              | hu          |
| Iceland                                              | is          |
| Israel                                               | il          |
| India                                                | in          |
| Indonesia                                            | id          |
| Ireland                                              | ie          |
| Italy                                                | it          |
| Jordan                                               | jo          |
| Kazakhstan                                           | kz          |
| Kenya                                                | ke          |
| Korea, Republic of                                   | kr          |
| Latvia                                               | lv          |
| Lebanon                                              | lb          |
| Liechtenstein                                        | li          |
| Lithuania                                            | lt          |
| Luxembourg                                           | lu          |
| Malaysia                                             | my          |
| Maldives                                             | mv          |
| Malta                                                | mt          |
| Mexico                                               | mx          |
| Moldova, Republic of                                 | md          |
| Monaco                                               | mc          |
| Montenegro                                           | me          |
| Nepal                                                | np          |
| Netherlands                                          | nl          |
| Nicaragua                                            | ni          |
| Nigeria                                              | ng          |
| North Macedonia                                      | mk          |
| Norway                                               | no          |
| Panama                                               | pa          |
| Paraguay                                             | py          |
| Peru                                                 | pe          |
| Philippines                                          | ph          |
| Poland                                               | pl          |
| Portugal                                             | pt          |
| Qatar                                                | qa          |
| Romania                                              | ro          |
| Russian Federation                                   | ru          |
| San Marino                                           | sm          |
| Serbia                                               | rs          |
| Singapore                                            | sg          |
| Slovakia                                             | sk          |
| Slovenia                                             | si          |
| South Africa                                         | za          |
| Spain                                                | es          |
| Suriname                                             | sr          |
| Sweden                                               | se          |
| Switzerland                                          | ch          |
| Tanzania                                             | tz          |
| Turkey                                               | tr          |
| Ukraine                                              | ua          |
| United Arab Emirates                                 | ae          |
| United Kingdom of Great Britain and Northern Ireland | gb          |
| United States of America                             | us          |
| Uraguay                                              | uy          |
| Venezuela                                            | ve          |
| Vietnam                                              | vn          |
| Zambia                                               | zm          |

### States

Australian states:

| State             | Region code |
| ----------------- | ----------- |
| New South Wales   | au-nsw      |
| Queensland        | au-qld      |
| South Australia   | au-sa       |
| Tasmania          | au-tas      |
| Victoria          | au-vic      |
| Western Australia | au-wa       |

United States of America states:

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

Brazilian states:

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



# SDK

> Return value

```json
{
  "usage":{
    "max_calls":1000,
    "calls":44
  },
  "results":[
    ...
  ],
  "filename":"car.jpg"
}
```

Our service is also available on-premises. [Get started](https://app.platerecognizer.com/sdk/) with the SDK. It has the **same interface** as the [recognition API](#read-number-plates-from-an-image). Note that it is hosted locally.

### HTTP Request

`POST http://localhost:8080/alpr`

Accepts the same parameters as the [recognition API](#read-number-plates-from-an-image).

<aside class="notice">
SDK API does not support fields <code>camera_id</code> and <code>timestamp</code>.
</aside>

### JSON Response

Returns the same parameters as the [recognition API](#read-number-plates-from-an-image). In addition to that, it returns the **number of calls** used.

#WebHooks

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

Our service also have a webhook service that allows you to receive a HTTP **POST** request to a target URL of your choosing. To configure webhooks, go to [webhooks settings page](https://app.platerecognizer.com/accounts/webhooks/) to manage your webhooks or add a new webhook target.

To quickly test out this feature, you can use the URL from a [http://webhook.site](webhook tester).





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
    "year": 2019
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
| total_calls | Maximum number of API calls you can make during the period. Need more? [Upgrade](https://app.platerecognizer.com/subscribe/). |
