# Function Responses
----------------------


verify_customer
-----------------

verify_customer shows the contents of the record of the customer once the account_number and cpni are verified.

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

`speed_test` uses the `meta_data` from the `verified_customer` function to populate the response with `"modem_speed_download": 955` and `"modem_speed_upload": 75`

```json

Received:
"{\n   \"response\" : \"Tell the user here are the test results. Download speed: 955 megabits, Upload speed: 75 megabits\"\n}\n"
```

modem_diagnostics
-------------------

modem_diagnostics uses the `meta_data` from the `verified_customer` function to populate the response with `"modem_downstream_level": 55` , `"modem_upstream_level": 0` and `"modem_snr": 35`

```json

Received:
"{\n   \"response\" : \"Tell the user here are the test results. Downstream level: 55, Upstream level: 0, Modem SNR: 35\"\n}\n"
```

swap_modem
------------

modem_swap shows what the `"mac_address": "0012345678ff",` was before the swap and after the database was updated with the new `"mac_address": "0012345678BB"`

```json
Received:
{
  "mac_address": "0012345678BB"
}
Received:
{
  "modem_downstream_level": 55,
  "modem_snr": 35,
  "account_number": 8005000,
  "last_name": "Lovelace",
  "active": 1,
  "service_address": "123 some st pittsburgh pa 15215",
  "modem_speed_upload": 75,
  "id": 1,
  "billing_address": "123 some st pittsburgh pa 15215",
  "email_address": "ada.lovelace@livewirecalbe.com",
  "modem_upstream_level": 0,
  "cpni": 4455,
  "modem_speed_download": 955,
  "first_name": "Ada",
  "mac_address": "0012345678ff",
  "modem_downstream_uncorrectables": 534,
  "phone_number": "5551235555"
}
Received:
{
  "modem_downstream_uncorrectables": 534,
  "phone_number": "5551235555",
  "modem_speed_download": 955,
  "mac_address": "0012345678BB",
  "first_name": "Ada",
  "cpni": 4455,
  "service_address": "123 some st pittsburgh pa 15215",
  "active": 1,
  "modem_upstream_level": 0,
  "email_address": "ada.lovelace@livewirecalbe.com",
  "billing_address": "123 some st pittsburgh pa 15215",
  "modem_speed_upload": 75,
  "id": 1,
  "modem_downstream_level": 55,
  "last_name": "Lovelace",
  "account_number": 8005000,
  "modem_snr": 35
}

```




