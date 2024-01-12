# Function Responses
----------------------


verify_customer
-----------------

`verify_customer` shows the contents of the record of the customer once the `account_number` and `cpni` are verified.

```json

Received:
{
  "account_number": "8005000",
  "cpni": "4455"
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

`modem_diagnostics` uses the `meta_data` from the `verified_customer` function to populate the response with `"modem_downstream_level": 55` , `"modem_upstream_level": 0` and `"modem_snr": 35`

```json

Received:
"{\n   \"response\" : \"Tell the user here are the test results. Downstream level: 55, Upstream level: 0, Modem SNR: 35\"\n}\n"
```

swap_modem
------------

`modem_swap` shows what the `"mac_address": "0012345678ff",` was before the swap and after the database was updated with the new `"mac_address": "0012345678BB"`

```json
Received:
{
  "mac_address": "0012345678BB"
}


```




