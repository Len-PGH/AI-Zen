# Function Responses
----------------------


verify_customer
-----------------

`verify_customer` shows the contents of the record of the customer once the `account_number` and `cpni` are verified.

```json

Args:
{
  "account_number": "8005000",
  "cpni": "4455"
}
```

```json

Received:
"{\n   \"response\" : \"Account verified, proceed\",\n   \"action\" : [\n      {\n         \"set_meta_data\" : {\n            \"customer\" : {\n               \"email_address\" : \"ada.lovelace@livewirecalbe.com\",\n               \"modem_snr\" : 35,\n               \"active\" : 1,\n               \"first_name\" : \"Ada\",\n               \"modem_upstream_level\" : 0,\n               \"cpni\" : 4455,\n               \"modem_downstream_level\" : 55,\n               \"service_address\" : \"123 some st pittsburgh pa 15215\",\n               \"mac_address\" : \"0024688924aa\",\n               \"modem_speed_upload\" : 75,\n               \"modem_downstream_uncorrectables\" : 534,\n               \"phone_number\" : \"5551235555\",\n               \"last_name\" : \"Lovelace\",\n               \"modem_speed_download\" : 955,\n               \"billing_address\" : \"123 some st pittsburgh pa 15215\",\n               \"account_number\" : 8005000,\n               \"id\" : 1\n            }\n         }\n      }\n   ]\n}\n"
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

`modem_diagnostics` uses the `meta_data` from the `verified_customer` function to populate the response with `"modem_downstream_level": 55` , `"modem_upstream_level": 0` and `"modem_snr": 35`

```json

Received:
"{\n   \"response\" : \"Tell the user here are the test results. Downstream level: 55, Upstream level: 0, Modem SNR: 35\"\n}\n"
```

swap_modem
------------

`modem_swap` shows what the `"mac_address": "0012345678ff",` was before the swap and after the database was updated with the new `"mac_address": "0012345678BB"`

```json
Args:
{
  "mac_address": "0012345678BB"
}
```

```json
Received:
"{\n   \"response\" : \"Customers modem mac address updated, please plug in your modem and allow 1 minute for all systems to update your new modem\"\n}\n"

```




