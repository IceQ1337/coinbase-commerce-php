# Coinbase Commerce
Fork of the official PHP library for the [Coinbase Commerce API](https://docs.cdp.coinbase.com/commerce-onchain/docs/welcome/).  
**Note: The official repository is not actively maintained.**  

**I strongly recommend to access the few endpoints that are left directly via a client of your choice and advise against using this library! Also, the old API could be discontinued at any time.**  

If the official repository gets updated, this fork will be deleted or updated accordingly. You'll notice when updating packages using ``composer`` results in an error. In this case, the official repository has probably been updated and this fork has been removed. However, this will probably never happen 🙂  

**The information in this documentation may no longer be correct! In addition, this library provides functions that no longer exist, but I couldn't be bothered to remove them yet.**  

# Table of contents

<!--ts-->
   * [PHP Versions](#php-versions)
   * [Installation](#installation)
   * [Usage](#usage)
      * [Checkouts](#checkouts)
      * [Charges](#charges)
      * [Events](#events)
      * [Webhooks](#webhooks)
      * [Warnings](#warnings)
   * [Testing and Contributing](#testing-and-contributing)
<!--te-->

## PHP versions
PHP version 8.1 and above are supported.

## Installation

If you already have a `composer.json` file in your project, you can skip **Step 3** by pre-editing the `composer.json` file **(Step 2)** and then finish with **Step 1**.

### Step 1: Install the official package with ``composer``
``` sh
composer require coinbase/coinbase-commerce
```

### Step 2: Add this fork to the version control system (VCS)

The `composer.json` file should require the package like this:
``` json
"require": {
    "coinbase/coinbase-commerce": "^1.0"
}
```

Edit `composer.json` to require the package like this instead:
``` json
"require": {
    "coinbase/coinbase-commerce": "dev-master"
},
"repositories": [
    {
        "type": "vcs",
        "url": "git@github.com:IceQ1337/coinbase-commerce-php"
    }
]
```

### Step 3: Update the source files with ``composer``
``` sh
composer update coinbase/coinbase-commerce
```

## Usage
For more details visit the [Coinbase Commerce API documentation](https://docs.cdp.coinbase.com/commerce-onchain/docs/welcome/).

To start using this library register an account on [Coinbase Commerce](https://commerce.coinbase.com/signup).  
To find your `API_KEY`, navigate to the User Settings section.

Next initialize a ``Client`` for interacting with the API. The only required parameter to initialize a client is ``apiKey``, however, you can also pass in ``baseUrl``, ``apiVersion``  and ``timeout``. You can also set your own `HTTP Client`, e.g. to use proxy settings.  

Parameters can be also be set post-initialization:
``` php
use CoinbaseCommerce\ApiClient;

// Make sure you don't store your API Key in your source code!
$apiClientObj = ApiClient::init(<API_KEY>);
$apiClientObj->setHttpClient(HttpFactory::makeClient());
$apiClientObj->setTimeout(3);
```

### Disable SSL Check
``` php
$apiClientObj->verifySsl(false);
```

The API resource class provides the following static methods: ``list, all, create, retrieve, updateById, deleteById``.  Additionally, the API resource class also provides the following instance methods: ``save, delete, insert, update``.

Each API method returns an ``ApiResource`` which represents the JSON response from the API.
When the response data is parsed into objects, the appropriate ``ApiResource`` subclass will automatically be used.

Client supports the handling of common API errors and warnings.
All errors that occur during any interaction with the API will be raised as exceptions.


| Error                        | Status Code |
|------------------------------|-------------|
| APIException                 |      *      |   
| InvalidRequestException      |     400     |   
| ParamRequiredException       |     400     |  
| ValidationException          |     400     |  
| AuthenticationException      |     401     |  
| ResourceNotFoundException    |     404     |
| RateLimitExceededException   |     429     |
| InternalServerException      |     500     |
| ServiceUnavailableException  |     503     |

## Checkouts 
[Checkouts API docs](https://docs.cdp.coinbase.com/commerce-onchain/reference/creates-a-new-checkout/)  

More examples on how to use checkouts can be found in the [`examples/Resources/CheckoutExample.php`](examples/Resources/CheckoutExample.php) file.

### Load checkout resource class
``` php
use CoinbaseCommerce\Resources\Checkout;
```
### Retrieve
``` php
$checkoutObj = Checkout::retrieve(<checkout_id>);
```
### Create
``` php
$checkoutData = [
    'name' => 'The Sovereign Individual',
    'description' => 'Mastering the Transition to the Information Age',
    'pricing_type' => 'fixed_price',
    'local_price' => [
        'amount' => '100.00',
        'currency' => 'USD'
    ],
    'requested_info' => ['name', 'email']
];
$newCheckoutObj = Checkout::create($checkoutData);

// or

$newCheckoutObj = new Checkout();

$newCheckoutObj->name = 'The Sovereign Individual';
$newCheckoutObj->description = 'Mastering the Transition to the Information Age';
$newCheckoutObj->pricing_type = 'fixed_price';
$newCheckoutObj->local_price = [
    'amount' => '100.00',
    'currency' => 'USD'
];
checkoutObj->requested_info = ['name', 'email'];

checkoutObj->save();
```

### List
List method returns ApiResourceList object.  

``` php
$params = [
    'limit' => 2,
    'order' => 'desc'
];

$list = Checkout::getList($params);

foreach($list as $checkout) {
    var_dump($checkout);
}

// Get number of items in list
$count = $list->count();

// or
$count = count($list);

// Get number of all checkouts
$countAll = $list->countAll();

// Get pagination
$pagination = $list->getPagination();

// To load next page with previous setted params(in this case limit, order)
if ($list->hasNext()) {
    $list->loadNext();
    
    foreach($list as $checkout) {
        var_dump($checkout);
    }
}

```
### Get all checkouts
``` php
$params = [
    'order' => 'desc'  
];

$allCheckouts = Checkout::getAll($params);

```
## Charges
[Charges API docs](https://docs.cdp.coinbase.com/commerce-onchain/reference/creates-a-charge/)  

More examples on how to use charges can be found in the [`examples/Resources/ChargeExample.php`](examples/Resources/ChargeExample.php) file.

### Load charge resource class
``` php
use CoinbaseCommerce\Resources\Charge;
```
### Retrieve
``` php
$chargeObj = Charge::retrieve(<charge_id>);
```
### Create
``` php
$chargeData = [
    'name' => 'The Sovereign Individual',
    'description' => 'Mastering the Transition to the Information Age',
    'local_price' => [
        'amount' => '100.00',
        'currency' => 'USD'
    ],
    'pricing_type' => 'fixed_price'
];
Charge::create($chargeData);

// or
$chargeObj = new Charge();

$chargeObj->name = 'The Sovereign Individual';
$chargeObj->description = 'Mastering the Transition to the Information Age';
$chargeObj->local_price = [
    'amount' => '100.00',
    'currency' => 'USD'
];
$chargeObj->pricing_type = 'fixed_price';
$chargeObj->save();
```
### List
``` php
$list = Charge::getList();

foreach($list as $charge) {
    var_dump($list);
}

$pagination = $list->getPagination();
```
### Get all charges
``` php
$allCharges = Charge::getAll();
```

### Cancel a charge
Cancels a charge that has been previously created.  

Note: Only new charges can be successfully canceled. Once payment is detected, charge can no longer be canceled.

```
$chargeObj = Charge::retrieve(<charge_id>);

if ($chargeObj) {
    $chargeObj->cancel();
}
```

## Events
[Events API docs](https://docs.cdp.coinbase.com/commerce-onchain/reference/getevents/)  

More examples on how to use events can be found in the [`examples/Resources/EventExample.php`](examples/Resources/EventExample.php) file.

### Load event resource class
``` php
use CoinbaseCommerce\Resources\Event;
```
### Retrieve
``` php
$eventObj = Event::retrieve(<event_id>);
```
### List
``` php
$listEvent = Event::getList();

foreach($listEvent as $event) {
    var_dump($event);
}

$pagination = $listEvent->getPagination();
```
### Get all events
``` php
$allEvents = Event::getAll();
```

## Warnings
It's prudent to be conscious of warnings. The library will log all warnings to a standard PSR-3 logger if one is configured.
``` php
use CoinbaseCommerce\ApiClient;

// Make sure you don't store your API Key in your source code!
$apiClientObj = ApiClient::init(<API_KEY>);
$apiClientObj->setLogger($logger);
```

## Webhooks
Coinbase Commerce signs the webhook events it sends to your endpoint, allowing you to validate and verify that they weren't sent by someone else. You can find a simple example of how to use this in the [`examples/Webhook`](examples/Webhook) folder.

### Verify Signature header
``` php
use CoinbaseCommerce\Webhook;

try {
    Webhook::verifySignature($body, $signature, $sharedSecret);
    echo 'Successfully verified';
} catch (\Exception $exception) {
    echo $exception->getMessage();
    echo 'Failed';
}
```

### Testing and Contributing
Any and all contributions are welcome! The process is simple: fork this repo, make your changes, run the test suite, and submit a pull request. To run the tests, clone the repository and run the following commands:

``` sh
composer install
composer test
```

License
----

Apache-2.0
