# Function Responses
----------------------


verify_customer
-----------------

```json

Received:
{
  "account_number": "8005000",
  "cpni": "4455"
}
Received:
{
  "modem_downstream_uncorrectables": 534,
  "phone_number": "5551235555",
  "cpni": 4455,
  "mac_address": "0012345678ff",
  "modem_speed_download": 955,
  "first_name": "Ada",
  "service_address": "123 some st pittsburgh pa 15215",
  "active": 1,
  "modem_speed_upload": 75,
  "billing_address": "123 some st pittsburgh pa 15215",
  "id": 1,
  "email_address": "ada.lovelace@livewirecalbe.com",
  "modem_upstream_level": 0,
  "modem_downstream_level": 55,
  "account_number": 8005000,
  "modem_snr": 35,
  "last_name": "Lovelace"
}
```

speed_test
-----------------

```json

Received:
"{\n   \"response\" : \"Tell the user here are the test results. Download speed: 955 megabits, Upload speed: 75 megabits\"\n}\n"
```

modem_diagnostics
-------------------

```json

Received:
"{\n   \"response\" : \"Tell the user here are the test results. Downstream level: 55, Upstream level: 0, Modem SNR: 35\"\n}\n"
```

swap_modem
------------

```json


```




