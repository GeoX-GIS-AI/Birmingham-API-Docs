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

=> Single Location API with GET method
1. Create a new request.
2. In the Request URL field, paste your API's invoke URL which is https://api.birmingham-city-land-registry.com/single-search
3. Select the GET HTTP method
4. Setup authorization as mentioned above.
5. Now put your lat/lng or address in the Params section with `lat` and `lng`  and `address` keys.
6. Finally, hit the API.

=> Batch Location API with POST method
1. Create a new request.
2. In the Request URL field, paste your API's invoke URL which is https://api.birmingham-city-land-registry.com/single-search
3. Select the POST HTTP method
4. Setup authorization as mentioned above.
5. Now go to the Body section, select the raw radio button and select JSON from the dropdown.
6. Now put your lat/lng or address in the Body section with `lat` and `lng`  and `address` keys along with `correlationId` key.
7. You can add multiple locations in the body section. Please refer to API documentation for more details (link at the end of this document).
8. Finally, hit the API.

## Hitting API with Python
1. We will need following libraries to be installed
   - requests
   - aws_requests_auth

### Python code for both single and batch requests
```python
import json
import requests
from aws_requests_auth.aws_auth import AWSRequestsAuth


def birmingham_request_single_location(access_key, secret_key, lat=None, lng=None, address=None):
    """
    This function will hit the API with single location and return the response.
    :param access_key: AWS Access Key
    :param secret_key: AWS Secret Key
    :param lat: Latitude of the location
    :param lng: Longitude of the location
    :param address: Address of the location
    :return: Response of the API
    """
    assert (lat and lng) or address, "Either lat/lng or address is required."
    api_url = "https://api.birmingham-city-land-registry.com/single-search"
    aws_details = {
        'aws_access_key': access_key,
        'aws_secret_access_key': secret_key,
        'aws_host': "api.birmingham-city-land-registry.com",
        'aws_region': "us-east-1",
        'aws_service': "execute-api"
    }
    auth = AWSRequestsAuth(**aws_details)
    if (lat and lng):
        params = {
        "lat": lat,
        "lng": lng
    }
    else:
        params = {
            "address": address
        }
    res = requests.get(api_url, auth=auth, params=request_params)
    assert res.status_code == 200, f"Request failed with status: {res.status_code}"
    res_data = res.json()
    return res_data

def birmingham_locations_batch(access_key, secret_key, locations):
    api_url = "https://api.birmingham-city-land-registry.com/single-search"
    aws_details = {
        'aws_access_key': access_key,
        'aws_secret_access_key': secret_key,
        'aws_host': "api.birmingham-city-land-registry.com",
        'aws_region': "us-east-1",
        'aws_service': "execute-api"
    }
    auth = AWSRequestsAuth(**aws_details)
    request_body = {
        "locations": locations
    }
    res = requests.post(api_url, auth=auth, json=request_body)
    assert res.status_code == 200, f"Request failed with status: {res.status_code}"
    res_data = res.json()
    return res_data


if __name__ == '__main__':
    # Calling single location API with lat/lng
    birmingham_response = birmingham_request_single_location("YOUR_API_KEY", "YOUR_API_SECRET",
                                                             lat=45.6696163800542, lng=-122.5038830583887)
    print(json.dumps(birmingham_response, indent=2))

    # Calling single location API with address
    birmingham_response = birmingham_request_single_location("YOUR_API_KEY", "YOUR_API_SECRET",
                                                             address="123 Main St, Portland, OR 97204")
    print(json.dumps(birmingham_response, indent=2))

    # Calling batch location API
    locations = [ # Each location object should contain lat/lng or address along with correlationId
        {
            "lat": 52.465536191811154,
            "lng": -1.877542072485532,
            "correlationId": "123"
        },
        {
            "address": "89 Stratford Rd, Birmingham B11 1AN, UK",
            "correlationId": "789"
        }
    ]
    birmingham_response = birmingham_locations_batch(access_key, secret_key, locations)

```

## Hitting API with cURL
The API request needs to be signed with AWS Signature Version 4. Please follow this [link](https://docs.aws.amazon.com/general/latest/gr/sigv4-signed-request-examples.html) for more details.

=> Single Location API with GET method
```shell
curl --location --request GET 'https://api.birmingham-city-land-registry.com/single-search?lat=52.44257691961897&lng=-2.015120663792357' \
--header 'X-Amz-Date: 20221119T101449Z' \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFGWE6TFCSUHL/20221119/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=15d1851e01835c1ca5da79edef69f69613f3c3ce2544f940e7c73f4c06acb3bd'
```

=> Batch Location API with POST method
```shell
curl --location --request POST 'https://api.birmingham-city-land-registry.com/single-search' \
--header 'X-Amz-Content-Sha256: beaead3198f7da1e70d03ab969765e0821b24fc913697e929e726aeaebf0eba3' \
--header 'X-Amz-Date: 20221119T101603Z' \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFGWE6TFCSUHL/20221119/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-content-sha256;x-amz-date, Signature=90a168c63da9e07d7c2c56778c60039f30b36e085f7c9b858999570e3f04d11c' \
--header 'Content-Type: application/json' \
--data-raw '{
    "locations": [
        {
            "lat": 52.465536191811154,
            "lng": -1.877542072485532,
            "correlationId": "1e6a747f11f16540a27e"
        },
        {
            "correlationId": "123",
            "address": "89 Stratford Rd, Birmingham B11 1AN, UK"
        }
    ]
}'
```

## Hitting API with wget
=> Single Location API with GET method
```shell
wget --no-check-certificate --quiet \
  --method GET \
  --timeout=0 \
  --header 'X-Amz-Date: 20221119T101648Z' \
  --header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFGWE6TFCSUHL/20221119/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=6db6db32213806662504744186566385a99fa44676636838b42a7555eef071b7' \
   'https://api.birmingham-city-land-registry.com/single-search?lat=52.44257691961897&lng=-2.015120663792357'
```

=> Batch Location API with POST method
```shell
wget --no-check-certificate --quiet \
  --method POST \
  --timeout=0 \
  --header 'X-Amz-Content-Sha256: beaead3198f7da1e70d03ab969765e0821b24fc913697e929e726aeaebf0eba3' \
  --header 'X-Amz-Date: 20221119T101603Z' \
  --header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIA2TITFGWE6TFCSUHL/20221119/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-content-sha256;x-amz-date, Signature=90a168c63da9e07d7c2c56778c60039f30b36e085f7c9b858999570e3f04d11c' \
  --header 'Content-Type: application/json' \
  --body-data '{
    "locations": [
        {
            "lat": 52.465536191811154,
            "lng": -1.877542072485532,
            "correlationId": "1e6a747f11f16540a27e"
        },
        {
            "correlationId": "123",
            "address": "89 Stratford Rd, Birmingham B11 1AN, UK"
        }
    ]
}' \
   'https://api.birmingham-city-land-registry.com/single-search'
```

# Request and Response Samples
Here are the sample request and response

## Request URL
```shell
https://api.birmingham-city-land-registry.com/single-search
```

## Request Query params Sample (Single Location API)

=> With lat/lng
```shell
lat=52.44257691961897&lng=-2.015120663792357
```
=> With address
```shell
address=89 Stratford Rd, Birmingham B11 1AN, UK
```

## Request Body Sample (Batch Location API)
```json
{
    "locations": [
        {
            "lat": 52.465536191811154,
            "lng": -1.877542072485532,
            "correlationId": "123"
        },
        {
            "address": "89 Stratford Rd, Birmingham B11 1AN, UK",
            "correlationId": "789"
        }
    ]
}
```

## Response Sample

=> Single Loation API
```json
{
    "data": {
        "address": {
            "address": "8, Parkfield, Birmingham",
            "city": "West Midlands",
            "lat": "52.44257691961897",
            "lng": "-2.015120663792357",
            "state": "England",
            "zip": ""
        },
        "img_path": "https://xgeo-birm-images.s3.amazonaws.com/img/02152021132148277000bU7kGZjSm5RdHL1.png?AWSAccessKeyId=AKIAZJIYWTQ64TAOCRO7&Signature=FEfrbQuHpfT2szJ4kyHCMBQy6ZA%3D&Expires=1668853608",
        "records": [
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
            }
        ]
    },
    "msg": "OK",
    "status": 200
}
```


=> Batch Location API
```json
{
    "data": [
        {
            "address": {
                "address": "89, Stratford Road, Birmingham",
                "city": "West Midlands",
                "lat": "52.465536191811154",
                "lng": "-1.877542072485532",
                "state": "England",
                "zip": ""
            },
            "correlationId": "123",
            "img_path": "https://xgeo-birm-images.s3.amazonaws.com/img/02152021132141473000X6wqmjsSoLiIWel.png?AWSAccessKeyId=AKIAZJIYWTQ64TAOCRO7&Signature=1Lw%2BmdQn0xFVq2WYmcwv%2FU9phgE%3D&Expires=1668853868",
            "records": [
                {
                    "Air_Condit": 0.0,
                    "Asp": 65.327,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.90265554,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 283.199078277,
                    "Dis_Road": 4.54782144059,
                    "Dis_Tree": 11.8964503378,
                    "Dis_Vegeta": 38.4620920471,
                    "Dis_WaterB": 0.44227205502,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 65.275375356,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 65.327,
                    "Road_Name": "Erasmus Road",
                    "Road_Num": null,
                    "Roof_Area": 65.275375356,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 130.551,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": null,
                    "Volume": 463.088,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 50.221,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.881686298,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 291.428101721,
                    "Dis_Road": 5.67721811631,
                    "Dis_Tree": 1.00101189419,
                    "Dis_Vegeta": 23.8471123687,
                    "Dis_WaterB": 0.424344798508,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 50.1809354068,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 50.221,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 50.1809354068,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 100.362,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": null,
                    "Volume": 356.002,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 60.924,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.876361336,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 293.354056846,
                    "Dis_Road": 5.72601285354,
                    "Dis_Tree": 5.42347220013,
                    "Dis_Vegeta": 23.2006596942,
                    "Dis_WaterB": 0.42052438073,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 60.8751309053,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 60.924,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 60.8751309053,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 121.75,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": "10094291433",
                    "Volume": 431.871,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 85.216,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.886576311,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 287.915401804,
                    "Dis_Road": 5.57520323001,
                    "Dis_Tree": 0.0,
                    "Dis_Vegeta": 25.4625191962,
                    "Dis_WaterB": 0.427755285343,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 85.1484908703,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 1.66829306771,
                    "Poly_Area": 85.216,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 85.1484908703,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 170.297,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": "10094291434",
                    "Volume": 604.075,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 59.457,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.870726448,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 295.495518538,
                    "Dis_Road": 5.75991452501,
                    "Dis_Tree": 10.8622730579,
                    "Dis_Vegeta": 21.5040623097,
                    "Dis_WaterB": 0.416270840128,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 59.4101854465,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 59.457,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 59.4101854465,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 118.82,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": "10094291432",
                    "Volume": 421.478,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 66.128,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.844365094,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 307.171493399,
                    "Dis_Road": 5.92344024925,
                    "Dis_Tree": 12.6746062134,
                    "Dis_Vegeta": 20.2685046723,
                    "Dis_WaterB": 0.398342952823,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 66.0752788951,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 66.128,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 66.0752788951,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 132.151,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": "10094291429",
                    "Volume": 468.762,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 33.936,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "Yes",
                    "Conc": 0.0,
                    "Dis_Coast": 110.831066865,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 312.712000918,
                    "Dis_Road": 5.9906500886,
                    "Dis_Tree": 10.5088534475,
                    "Dis_Vegeta": 24.8962686043,
                    "Dis_WaterB": 0.38947098828,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 89.2570480236,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 89.328,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 89.2570480236,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 178.514,
                    "Story_Num": 2.0,
                    "Tile": 55.392,
                    "Type": "Flat",
                    "Uprn": "10023491909,10094291426,10094291427,100071417185",
                    "Volume": 633.223,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 216.725,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.85059146,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 297.978843532,
                    "Dis_Road": 5.8028316659,
                    "Dis_Tree": 16.2631351782,
                    "Dis_Vegeta": 19.6638183444,
                    "Dis_WaterB": 0.402210135857,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 216.552482094,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 216.725,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 216.552482094,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 433.105,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": "10094291430,10094291431",
                    "Volume": 1536.3,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 262.354,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "Yes",
                    "Conc": 0.0,
                    "Dis_Coast": 110.814063255,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 282.23676431,
                    "Dis_Road": 4.42744384408,
                    "Dis_Tree": 0.0,
                    "Dis_Vegeta": 18.5645696727,
                    "Dis_WaterB": 0.377411186755,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 489.44050454,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 1.37051770526,
                    "Poly_Area": 489.83,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 489.44050454,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 978.881,
                    "Story_Num": 2.0,
                    "Tile": 227.476,
                    "Type": "Flat",
                    "Uprn": "10023491907,10023491908,10023491910,10094291424,10094291428,10094291435,10094291436,100071417182",
                    "Volume": 3472.27,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                }
            ]
        },
        {
            "address": {
                "address": "89, Stratford Road, Birmingham",
                "city": "West Midlands",
                "lat": "52.4654898",
                "lng": "-1.877521",
                "state": "England",
                "zip": ""
            },
            "correlationId": "789",
            "img_path": "https://xgeo-birm-images.s3.amazonaws.com/img/02152021132141473000X6wqmjsSoLiIWel.png?AWSAccessKeyId=AKIAZJIYWTQ64TAOCRO7&Signature=1Lw%2BmdQn0xFVq2WYmcwv%2FU9phgE%3D&Expires=1668853868",
            "records": [
                {
                    "Air_Condit": 0.0,
                    "Asp": 65.327,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.90265554,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 283.199078277,
                    "Dis_Road": 4.54782144059,
                    "Dis_Tree": 11.8964503378,
                    "Dis_Vegeta": 38.4620920471,
                    "Dis_WaterB": 0.44227205502,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 65.275375356,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 65.327,
                    "Road_Name": "Erasmus Road",
                    "Road_Num": null,
                    "Roof_Area": 65.275375356,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 130.551,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": null,
                    "Volume": 463.088,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 50.221,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.881686298,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 291.428101721,
                    "Dis_Road": 5.67721811631,
                    "Dis_Tree": 1.00101189419,
                    "Dis_Vegeta": 23.8471123687,
                    "Dis_WaterB": 0.424344798508,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 50.1809354068,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 50.221,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 50.1809354068,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 100.362,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": null,
                    "Volume": 356.002,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 60.924,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.876361336,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 293.354056846,
                    "Dis_Road": 5.72601285354,
                    "Dis_Tree": 5.42347220013,
                    "Dis_Vegeta": 23.2006596942,
                    "Dis_WaterB": 0.42052438073,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 60.8751309053,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 60.924,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 60.8751309053,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 121.75,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": "10094291433",
                    "Volume": 431.871,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 85.216,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.886576311,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 287.915401804,
                    "Dis_Road": 5.57520323001,
                    "Dis_Tree": 0.0,
                    "Dis_Vegeta": 25.4625191962,
                    "Dis_WaterB": 0.427755285343,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 85.1484908703,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 1.66829306771,
                    "Poly_Area": 85.216,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 85.1484908703,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 170.297,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": "10094291434",
                    "Volume": 604.075,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 59.457,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.870726448,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 295.495518538,
                    "Dis_Road": 5.75991452501,
                    "Dis_Tree": 10.8622730579,
                    "Dis_Vegeta": 21.5040623097,
                    "Dis_WaterB": 0.416270840128,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 59.4101854465,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 59.457,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 59.4101854465,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 118.82,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": "10094291432",
                    "Volume": 421.478,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 66.128,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.844365094,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 307.171493399,
                    "Dis_Road": 5.92344024925,
                    "Dis_Tree": 12.6746062134,
                    "Dis_Vegeta": 20.2685046723,
                    "Dis_WaterB": 0.398342952823,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 66.0752788951,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 66.128,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 66.0752788951,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 132.151,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": "10094291429",
                    "Volume": 468.762,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 33.936,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "Yes",
                    "Conc": 0.0,
                    "Dis_Coast": 110.831066865,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 312.712000918,
                    "Dis_Road": 5.9906500886,
                    "Dis_Tree": 10.5088534475,
                    "Dis_Vegeta": 24.8962686043,
                    "Dis_WaterB": 0.38947098828,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 89.2570480236,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 89.328,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 89.2570480236,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 178.514,
                    "Story_Num": 2.0,
                    "Tile": 55.392,
                    "Type": "Flat",
                    "Uprn": "10023491909,10094291426,10094291427,100071417185",
                    "Volume": 633.223,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 216.725,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "No",
                    "Conc": 0.0,
                    "Dis_Coast": 110.85059146,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 297.978843532,
                    "Dis_Road": 5.8028316659,
                    "Dis_Tree": 16.2631351782,
                    "Dis_Vegeta": 19.6638183444,
                    "Dis_WaterB": 0.402210135857,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 216.552482094,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 0.0,
                    "Poly_Area": 216.725,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 216.552482094,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 433.105,
                    "Story_Num": 2.0,
                    "Tile": 0.0,
                    "Type": "Flat",
                    "Uprn": "10094291430,10094291431",
                    "Volume": 1536.3,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                },
                {
                    "Air_Condit": 0.0,
                    "Asp": 262.354,
                    "Azimuth_M": "Flat",
                    "Azimuth_P": "Flat",
                    "Building_H": 7.09437,
                    "Chimney": "Yes",
                    "Conc": 0.0,
                    "Dis_Coast": 110.814063255,
                    "Dis_Footpr": 0.0,
                    "Dis_GreenS": 282.23676431,
                    "Dis_Road": 4.42744384408,
                    "Dis_Tree": 0.0,
                    "Dis_Vegeta": 18.5645696727,
                    "Dis_WaterB": 0.377411186755,
                    "District": "BIRMINGHAM_DISTRICT_(B)",
                    "East": 0.0,
                    "Flat": 489.44050454,
                    "Function": "Public Park Or Garden",
                    "GeoxKey": "02152021132141473000X6wqmjsSoLiIWel",
                    "Glass": 0.0,
                    "Inspireid": 58720457.0,
                    "Metal": 0.0,
                    "North": 0.0,
                    "Other": 0.0,
                    "Overlay_Tr": 1.37051770526,
                    "Poly_Area": 489.83,
                    "Road_Name": "Stratford Road",
                    "Road_Num": "A41",
                    "Roof_Area": 489.44050454,
                    "Slope": 0.0,
                    "South": 0.0,
                    "Square_Foo": 978.881,
                    "Story_Num": 2.0,
                    "Tile": 227.476,
                    "Type": "Flat",
                    "Uprn": "10023491907,10023491908,10023491910,10094291424,10094291428,10094291435,10094291436,100071417182",
                    "Volume": 3472.27,
                    "Ward": "Sparkbrook Ward",
                    "West": 0.0,
                    "Zmax": 125.530296326,
                    "Zmedian": 124.527954102,
                    "sky": 0.0,
                    "solar": 0.0
                }
            ]
        }
    ],
    "msg": "OK",
    "status": 200
}
```
# API Reference
Please visit this [link](https://api.birmingham-city-land-registry.com/redoc) for more detailed description about API response attributes
