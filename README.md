# XRPHP - XRP Ledger Library

This is a PHP 7.1+ wrapper for communication with the XRP Ledger.

It is recommended to use this library in conjuction with the 
[API Documentation](https://developers.ripple.com/rippled-api.html)
in the [Ripple Developer Portal](https://developers.ripple.com/).

Websocket support may come later, but until then, it should be
assumed you're using the `JSON-RPC` [request formatting](https://developers.ripple.com/request-formatting.html)
unless otherwise stated.

Note: When `The API` is mentioned in this documentation, it refers to
the [rippled/XRP Ledger API](https://developers.ripple.com/rippled-api.html).

## Dependencies

This project uses [HTTPLUG](http://docs.php-http.org/en/latest/index.html) which builds
on top of [PSR-7](https://www.php-fig.org/psr/psr-7/) so the developer can select the
[HTTP Client](http://docs.php-http.org/en/latest/clients.html) best suited for their
architecture.

If you do not have a preference, simply run the command below and proceed to the
installation section.

```
composer require php-http/guzzle6-adapter php-http/message
```

## Installation

```
composer require mikemilano/xrphp
```

## Instantiating With Connection Data

You can either instantiate the client with a string, or an array.

Create a `Client` object with a uri:
```
$client = new \XRPHP\Connection('https://s1.ripple.com:51234');
```

Create a `Client` object with an array:
```
$client = new \XRPHP\Connection([
    'scheme' => 'https',
    'host' => 's1.ripple.com',
    'port' => 51234
]);
```

## Sending a Request (Incomplete)

Note: Only the Account methods are complete. For now, you can call any method
by following the `Direct Posting to the API` documentation later in this file. 

The [API documentation]([rippled api](https://developers.ripple.com/rippled-api.html))
clearly defines `method` and `params` for each method, along with `JSON-RPC` examples 
and an explanation for each parameter.

Simply pass the `method`, followed by an associative array of 
`params` into the client.

```
$res = $client->method('account_info', [
    'account' => 'rG1QQv2nh2gr7RCZ1P8YYcBUKCCN633jCn'
])->execute();
```

Exceptions will be thrown when calling a method with invalid, or missing
parameters are included in a request, at the time the method
is instantiated.

## Response Object

The API provides responses in a JSON format. The `result` property
of the of the JSON object contains the data you are looking for.

XRPHP takes care of the mundane by validating the response received
 from the API, decoding the JSON, and arranging the data in a friendlier
 format.
 
It does this with the `MethodResponse` object, which is returned
when you call `->execute()` on a client method.

### $res->getResult(): 

The `result` property of a response contains the data returned
in in the `result` property of the JSON.

### $res->isSuccess():

The API provides a `status` property which indicates a successful
call when the value is `success`. While it is accessible in
`$res->result['status']`, XRPHP makes this available as a property.

You can check if the response was successful with: `$res->isSuccess()`.

## Direct Posting to the API

You can use the following method to access the API. It simply wraps the API without validating
parameters or normalizing the response.

```
// Instantiate the client.
$client = new \XRPHP\Client('https://s1.ripple.com:51234');

// Retrieve the info.
$res = $client->post('account_info', ['account' => 'rG1QQv2nh2gr7RCZ1P8YYcBUKCCN633jCn']);
```

Response data is the result of `json_decode($json, true)`, so the entire structure
has been converted to an associative array.

Output of `print_r($res)`:
```
(
    [result] => Array
        (
            [account_data] => Array
                (
                    [Account] => rG1QQv2nh2gr7RCZ1P8YYcBUKCCN633jCn
                    [Balance] => 6
                    [Flags] => 65536
                    [LedgerEntryType] => AccountRoot
                    [OwnerCount] => 0
                    [PreviousTxnID] => E9A2BAA7B310BA3C52FDFBDEC404D1339E7547E8CD769D6CD40AD0EFABF337F8
                    [PreviousTxnLgrSeq] => 36405347
                    [RegularKey] => rU4DpLWAzs3ECf8SVkeJGeLt9KBRGhxpQg
                    [Sequence] => 192218
                    [index] => 92FA6A9FC8EA6018D5D16532D7795C91BFB0831355BDFDA177E86C8BF997985F
                )

            [ledger_current_index] => 38726789
            [queue_data] => Array
                (
                    [txn_count] => 0
                )

            [status] => success
            [validated] => 
        )
)
```

## Development Plan

- [x] Phase 1: Provide an API wrapper which returns the raw data. (`$client->post('method', $params)`)
- [ ] Phase 2: Extend `Method` for each public API method, providing validation, normalization of output, and tests.
- [ ] Phase 3: Repeat phase 2 for the admin API.

## Phase 2 Development Status

Note: This just applies to Phase 2 architecture. You can call any method with 
`$res = $client->method('method_name', $params)->execute()`.

- account
  - [x] account_channels
  - [x] account_currencies
  - [x] account_account_info
  - [x] account_lines
  - [x] account_objects
  - [x] account_offers
  - [x] account_tx
  - [x] gateway_balances
  - [x] noripple_check
- ledger
  - [ ] ledger
  - [ ] ledger_closed
  - [ ] ledger_current
  - [ ] ledger_data
  - [ ] ledger_entry
- transaction
  - [ ] sign
  - [ ] signFor
  - [ ] submit
  - [ ] submitMultisigned
  - [ ] entry
  - [ ] tx
  - [ ] txHistory
- path
  - [ ] find
  - [ ] rippleFind
- book
  - [ ] offers
- channel
  - [ ] authorize
  - [ ] verify
- subscription
  - [ ] subscribe
  - [ ] unsubscribe
- server
  - [ ] fee
  - [ ] info
  - [ ] state
- util
  - [ ] json
  - [ ] ping
  - [ ] random

## Testing

As of now, only unit tests exists which mock the API so requests are not actually made.

Run the test suite:
```
make test
```

Run test coverage:
```
make cov
```

Once you run the coverage command, open `coverage/index.html` to view the report.

## Contribute

PRs, New Issues, and Tips are all welcome!

XRP address: `rwSZu5vAgPEdoDpYx9qZtqtHRDcFwCooqw`
