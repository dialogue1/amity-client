# amity API Client

This package contains a Guzzle3-based API client for the amity API (v2). Use
this if you want to connect your project with an amity installation and manage
contacts, lists etc.

## Requirements

* PHP 5.3
* Composer to install the package and its dependencies

## Installation

Using Composer, installing is as simple as doing

    php composer.phar require dialogue1/amity-client:~1.0

In your PHP code, all you need is to include the autoloader generated by
Composer:

```php
require 'vendor/autoload.php';

$apiClient = dialogue1\amity\API\Client::create(...);
```

## Usage

In general, you need four pieces of information to use the amity API:

* the hostname of the installation (e.g. ``news.mydomain.com``)
* whether or not to use SSL
* the amity client ID (e.g. ``gh26bb81``)
* your amity API key (e.g. ``34hguj13fggj1kjh24j5hg45j1h3g2g42``)

You can either instantiate a fresh API client, which sets up all the dependencies
as you need it, or use a plain Guzzle object and inject the amity API logic into
it.

### Using the API client

```php
// This will setup a new Guzzle client and prepare it for the amity API
$apiClient = dialogue1\amity\API\Client::create(
   'news.mydomain.com',
   true,
   'gh26bb81',
   '34hguj13fggj1kjh24j5hg45j1h3g2g42'
);
```

The API client offers wrappers around the provided API endpoints and takes care
of handling errors for you. The code to handle the resources like contacts is
available in *services*.

```php
$apiClient      = dialogue1\amity\API\Client::create(...);
$contactService = $apiClient->getContactService();

// both $contacts and $females will be a PHP array of contacts
$contacts = $contactService->getMany();
$females  = $contactService->getMany(null, null, null, null, 'f');

$newbie = $contactService->create(array(
	'email' => 'some@one.org'
));
```

If the existing wrappers are not sufficient (e.g. if you want to use resource
embedding), you can use the ``requestData()`` helper method.

```php
$apiClient = dialogue1\amity\API\Client::create(...);
$contacts  = $apiClient->requestData('GET', '/contacts', array('embed' => 'lists'));
```

### Using an existing Guzzle HTTP client

Alternatively, just use the ``RequestSigner`` plugin to add the signing
functionality to any Guzzle instance - in this case, you need to take care
of setting the hostname and SSL yourself:

```php
$httpClient = getGuzzleClientFromMyExistingFramework();
$signer     = new dialogue1\amity\API\RequestSigner('<client ID>', '<api key>');

$httpClient->addSubscriber($signer);
```

With this setup, you can perform freeform requests as you see fit. Note that you
have to take care of decoding JSON, handlings errors etc. yourself!

```php
// [snip]

$contacts = $httpClient->get('/contacts');
```

License
-------

This code is licensed under the MIT license.
