# Overview
This API allows you to search properties from our vast range of footprints data. 

## Endpoint URL
The base URL for the API is
```bash
https://api.birmingham-city-land-registry.com
```

# Autorization
The API requires to be signed with Version 4 signing process of AWS. Here is how we can do it in different ways:

## Postman
In the Authorization tab of Postman app, do the following:
- For Type, choose AWS Signature.
- For AccessKey and SecretKey, enter your IAM access key ID and secret access key.

## Python 
We can install and use the AWS Requests Auth library from [here](https://pypi.org/project/aws-requests-auth/)

## Manual
To manually sign your requests using another tool or environment,
use the [Signature Version 4 signing process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html).
For more information, see [Signing requests](https://docs.aws.amazon.com/apigateway/api-reference/signing-requests/).


# HTTP response codes
- 200 — Success.
- 400 — Bad Request There was a validation error with the input. Most probably you missed to provide lat/lng or correlationId in the request body. 
- 401 — You Signging process was failed. Probably due to incorrect AWS Access Key/Secret Key. 
- 403 — Forbidden. 
- 404 — Not Found One or more of the required resources was not found, please make sure you entered correct endpoint URL. 
- 500 — Internal Server Error This is an issue with our servers processing your request. In most cases we are notified so that we can investigate the issue.

# Getting Started

## Postman
Postman is an amazing application. It's a tool that allows developers to make HTTP requests extremely easy, save examples, work under different environments, we LOVE it!

Postman is downloadable for free use from here and supported with lots of developer docs here.We are providing you the full collection of this API for your usage with Postman, this collection includes examples of different requests & responses, the endpoints, documentation to immensely ease your interaction with our API.

After downloading Postman follow the instructions below to get started.
1. Create a new request.
2. In the Request URL field, paste your API's invoke URL which is https://api.birmingham-city-land-registry.com/single-search
3. Select the GET HTTP method
4. Setup authorization as mentioned above.
5. Now put your lat/lng in the Params section with `lat` and `lng` keys.
6. Finally, hit the API.

## Hitting API with Python
1. We will need following libraries to be installed
   - requests
   - aws_requests_auth
### Python code for API Call with Post Method
```python
import json
import requests
from aws_requests_auth.aws_auth import AWSRequestsAuth


def birmingham_request(access_key, secret_key, lat, lng):
    api_url = "https://api.birmingham-city-land-registry.com/single-search"
    aws_details = {
        'aws_access_key': access_key,
        'aws_secret_access_key': secret_key,
        'aws_host': "api.birmingham-city-land-registry.com",
        'aws_region': "us-east-1",
        'aws_service': "execute-api"
    }
    auth = AWSRequestsAuth(**aws_details)
    request_params = {
        "lat": lat,
        "lng": lng
    }
    res = requests.get(api_url, auth=auth, params=request_params)
    assert res.status_code == 200, f"Request failed with status: {res.status_code}"
    res_data = res.json()
    return res_data


if __name__ == '__main__':
    m2m_response = birmingham_request("YOUR_API_KEY", "YOUR_API_SECRET",
                               52.44257691961897, -2.015120663792357)
    print(json.dumps(m2m_response, indent=2))
```

## Hitting API with cURL
The API request needs to be signed with AWS Signature Version 4. Please follow this [link](https://docs.aws.amazon.com/general/latest/gr/sigv4-signed-request-examples.html) for more details. 

```shell
curl --location --request GET 'https://api.birmingham-city-land-registry.com/single-search?lat=52.44257691961897&lng=-2.015120663792357' \
--header 'X-Amz-Date: 20221110T065928Z' \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFGWE6TFCSUHL/20221110/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=0078cf993d25980a940bba7efe96be56de143912bf177f7179f23db76698794a'
```

## Hitting API with wget
```shell
wget --no-check-certificate --quiet \
  --method GET \
  --timeout=0 \
  --header 'X-Amz-Date: 20221110T065928Z' \
  --header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFGWE6TFCSUHL/20221110/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=0078cf993d25980a940bba7efe96be56de143912bf177f7179f23db76698794a' \
   'https://api.birmingham-city-land-registry.com/single-search?lat=52.44257691961897&lng=-2.015120663792357'
```

# Request and Response Samples
Here are the sample request and response

## Request URL
```shell
https://api.birmingham-city-land-registry.com/single-search
```

## Request Query params Sample
```shell
lat=52.44257691961897&lng=-2.015120663792357
```

## Response Sample
```json
{
    "data": {
        "address": {
            "address": "8, Parkfield, Birmingham",
            "city": "West Midlands",
            "last_updated": null,
            "lat": "52.44257691961897",
            "lng": "-2.015120663792357",
            "state": "England",
            "zip": ""
        },
        "img_path": "https://xgeo-birm-images.s3.amazonaws.com/img/02152021132148277000bU7kGZjSm5RdHL1.png?AWSAccessKeyId=AKIAZJIYWTQ64TAOCRO7&Signature=SMQO8NPTAOMj%2BABDGZG9T9YESVg%3D&Expires=1668064168",
        "records": [
            {
                "Air_Condit": 0.0,
                "Asp": 0.0,
                "Azimuth_M": "Flat",
                "Azimuth_P": "Flat",
                "Building_H": 1.36564,
                "Chimney": "No",
                "Conc": 0.0,
                "Dis_Coast": 108.25481403,
                "Dis_Footpr": 7.17708053434,
                "Dis_GreenS": 262.121936298,
                "Dis_Road": 28.7883392748,
                "Dis_Tree": 0.483889491769,
                "Dis_Vegeta": 0.0,
                "Dis_WaterB": 1.22435475899,
                "District": "BIRMINGHAM_DISTRICT_(B)",
                "East": 0.0,
                "Flat": 6.2217867998,
                "Function": "Public Park Or Garden",
                "GeoxKey": "02152021132148277000bU7kGZjSm5RdHL1",
                "Glass": 0.0,
                "Inspireid": 43241743.0,
                "Metal": 0.0,
                "North": 0.0,
                "Other": 0.0,
                "Overlay_Tr": 0.0,
                "Poly_Area": 5.559,
                "Road_Name": "Lye Avenue",
                "Road_Num": null,
                "Roof_Area": 6.2217867998,
                "Slope": 9.46,
                "South": 0.0,
                "Square_Foo": 5.33296,
                "Story_Num": 1.0,
                "Tile": 5.559,
                "Type": "Flat",
                "Uprn": null,
                "Volume": 15.9191,
                "Ward": "Bartley Green Ward",
                "West": 0.0,
                "Zmax": 191.289009094,
                "Zmedian": 191.230239868,
                "sky": 0.0,
                "solar": 0.0
            },
            {
                "Air_Condit": 0.0,
                "Asp": 0.0,
                "Azimuth_M": "South",
                "Azimuth_P": "{'East': 12, 'South': 87}",
                "Building_H": 6.15839,
                "Chimney": "No",
                "Conc": 0.0,
                "Dis_Coast": 108.25450081,
                "Dis_Footpr": 0.0,
                "Dis_GreenS": 256.69730494,
                "Dis_Road": 23.8748132268,
                "Dis_Tree": 0.0,
                "Dis_Vegeta": 0.0,
                "Dis_WaterB": 1.22095931599,
                "District": "BIRMINGHAM_DISTRICT_(B)",
                "East": 6.48733041007,
                "Flat": 0.0,
                "Function": "Public Park Or Garden",
                "GeoxKey": "02152021132148277000bU7kGZjSm5RdHL1",
                "Glass": 0.0,
                "Inspireid": 43241743.0,
                "Metal": 0.0,
                "North": 0.0,
                "Other": 0.0,
                "Overlay_Tr": 12.0368562669,
                "Poly_Area": 54.104,
                "Road_Name": "Woodgate Gardens",
                "Road_Num": null,
                "Roof_Area": 68.1169692965,
                "Slope": 22.62,
                "South": 47.033145473,
                "Square_Foo": 103.797,
                "Story_Num": 2.0,
                "Tile": 54.104,
                "Type": "Gable",
                "Uprn": "100070476208",
                "Volume": 414.021,
                "Ward": "Bartley Green Ward",
                "West": 0.0,
                "Zmax": 195.719924927,
                "Zmedian": 195.59552002,
                "sky": 0.0,
                "solar": 0.0
            },
            {
                "Air_Condit": 0.0,
                "Asp": 0.0,
                "Azimuth_M": "South",
                "Azimuth_P": "{'North': 33, 'East': 16, 'South': 50}",
                "Building_H": 6.15839,
                "Chimney": "No",
                "Conc": 0.0,
                "Dis_Coast": 108.246726631,
                "Dis_Footpr": 0.0,
                "Dis_GreenS": 264.851907344,
                "Dis_Road": 31.4626482847,
                "Dis_Tree": 0.0,
                "Dis_Vegeta": 0.0,
                "Dis_WaterB": 1.21258342732,
                "District": "BIRMINGHAM_DISTRICT_(B)",
                "East": 8.83486281724,
                "Flat": 0.0,
                "Function": "Public Park Or Garden",
                "GeoxKey": "02152021132148277000bU7kGZjSm5RdHL1",
                "Glass": 0.0,
                "Inspireid": 43241743.0,
                "Metal": 0.0,
                "North": 18.2219045606,
                "Other": 0.0,
                "Overlay_Tr": 3.46232372562,
                "Poly_Area": 55.262,
                "Road_Name": "Woodgate Gardens",
                "Road_Num": null,
                "Roof_Area": 69.5745446862,
                "Slope": 22.62,
                "South": 27.6089463039,
                "Square_Foo": 106.018,
                "Story_Num": 2.0,
                "Tile": 55.262,
                "Type": "Gable",
                "Uprn": "100070476207",
                "Volume": 422.88,
                "Ward": "Bartley Green Ward",
                "West": 0.0,
                "Zmax": 195.719924927,
                "Zmedian": 195.59552002,
                "sky": 0.0,
                "solar": 0.0
            }
        ]
    },
    "msg": "OK",
    "status": 200
}
```
