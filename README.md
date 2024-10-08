# Laravel HTTP Client Auth helper 🤝

An easy-to-use helper for Laravel HTTP Client to make manage API requests with a two-step auth flow. 
For example, OAuth2 or refresh tokens to get a new short-lived access token.\
This helper takes care of all the headaches and boilerplate code with a simple and easy-to-use interface.

#### Features:

- Automatically fetches new access tokens when needed.
- Automatically caches the access tokens for their lifetime.
- Extends the Laravel HTTP Client to make it straightforward to use. Se the [usage section](#usage) below for examples. 
- Supports common auth flows like OAuth2 and refresh tokens with most grant types.
- No configuration and no boilerplate code required. Just [require](#installation) and use with a simple and consise API.
- Supports [callbacks](#customize-with-callbacks) to customize the behaviour when needed.

#### Vision, roadmap & plans for the future

I want to support as many common auth flows as possible.\
If you have a use case that is not super obscure,
please [open an issue](https://github.com/pelmered/laravel-http-client-auth-helper/issues/new) where you provide as much detail as possible,
or [submit a PR](https://github.com/pelmered/laravel-http-client-auth-helper/pulls) with a working solution.

##### Plans for 1.1 - [Milestone](https://github.com/pelmered/laravel-http-client-auth-helper/milestone/2)

- Helper for automatically refresh tokens in the background before they expire.
- Add support for authorization_code and implicit OAuth2 grants.
- Tokens owned by a model. For example, tokens associated with a user model rather than the whole application.

[![Latest Stable Version](https://poser.pugx.org/pelmered/laravel-http-client-auth-helper/v/stable)](https://packagist.org/packages/pelmered/laravel-http-client-auth-helper)
[![Total Downloads](https://poser.pugx.org/pelmered/laravel-http-client-auth-helper/d/total)](//packagist.org/packages/pelmered/laravel-http-client-auth-helper)
[![Monthly Downloads](https://poser.pugx.org/pelmered/laravel-http-client-auth-helper/d/monthly)](//packagist.org/packages/pelmered/laravel-http-client-auth-helper)
[![License](https://poser.pugx.org/pelmered/laravel-http-client-auth-helper/license)](https://packagist.org/packages/pelmered/laravel-http-client-auth-helper)

[![Tests](https://github.com/pelmered/laravel-http-client-auth-helper/actions/workflows/tests.yml/badge.svg?branch=main)](https://github.com/pelmered/laravel-http-client-auth-helper/actions/workflows/tests.yml)
[![Build Status](https://scrutinizer-ci.com/g/pelmered/laravel-http-client-auth-helper/badges/build.png?b=main)](https://scrutinizer-ci.com/g/pelmered/laravel-http-client-auth-helper/build-status/main)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/pelmered/laravel-http-client-auth-helper/badges/quality-score.png?b=main)](https://scrutinizer-ci.com/g/pelmered/laravel-http-client-auth-helper/?branch=master)
[![Code Coverage](https://scrutinizer-ci.com/g/pelmered/laravel-http-client-auth-helper/badges/coverage.png?b=main)](https://scrutinizer-ci.com/g/pelmered/laravel-http-client-auth-helper/?branch=main)

[![Tested on PHP 8.2 to 8.4](https://img.shields.io/badge/Tested%20on%20PHP-8.2%20|%208.3%20|%208.4-brightgreen.svg?maxAge=2419200)](https://github.com/pelmered/filament-money-field/actions/workflows/tests.yml)
[![Tested on OS:es Linux, MacOS, Windows](https://img.shields.io/badge/Tested%20on%20lastest%20versions%20of-%20Ubuntu%20|%20MacOS%20|%20Windows-brightgreen.svg?maxAge=2419200)](https://github.com/pelmered/laravel-http-client-auth-helper/actions/workflows/tests.yml)

## Table of contents
<!-- toc -->

- [Requirements](#requirements)
- [Vision, roadmap & plans for the future](#vision-roadmap--plans-for-the-future)
- [Contributing](#contributing)
  * [Issues & Bug Reports](#issues--bug-reports)
- [Installation](#installation)
- [Options reference](#options-reference)
    + [scopes - `array`](#scopes---array)
    + [authType - `string`](#authtype---string)
    + [grantType - `string`](#granttype---string)
    + [tokenType - `string`](#tokentype---string)
    + [tokenName - `string`](#tokenname---string)
    + [expires - `int|string|Closure|Carbon`](#expires---intstringclosurecarbon)
    + [accessToken - `string|Closure`](#accesstoken---stringclosure)
    + [tokenTypeCustomCallback - `?Closure`](#tokentypecustomcallback---closure)
    + [cacheKey - `?string`](#cachekey---string)
    + [cacheDriver - `?string`](#cachedriver---string)
- [Usage](#usage)
    + [Minimal example:](#minimal-example)
    + [All parameters with default values:](#all-parameters-with-default-values)
    + [For full type safety, you can also provide objects instead of arrays:](#for-full-type-safety-you-can-also-provide-objects-instead-of-arrays)
    + [Customize with callbacks](#customize-with-callbacks)
  * [Integration tips](#integration-tips)

<!-- tocstop -->

## Requirements

- PHP 8.2 or higher
- Laravel 10

## Vision, roadmap & plans for the future

I want to support as many common auth flows as possible.\
If you have a use case that is not super obscure,
please [open an issue](https://github.com/pelmered/laravel-http-client-auth-helper/issues/new) where you provide as much detail as possible,
or [submit a PR](https://github.com/pelmered/laravel-http-client-auth-helper/pulls).

## Contributing

See [Contribution Guide](.github/CONTRIBUTING.md) before sending pull requests.

### Issues & Bug Reports

When you are submitting issues, I appreciate if you could provide a failing test case. That makes my job a lot easier.\
I will try to fix reported issues as soon as possible, but I do this in my spare time, so I might not be able to do it immediately.

## Installation

```bash
composer require pelmered/laravel-http-client-auth-helper
```

## Options reference

### scopes - `array`
Scopes to send when requesting an access token.
Typically only used for OAuth2 flows.\
**Possible options:** array with strings
**Default:** `[]`

### authType - `string`
The type of authorization for the refresh token request.\
**Possible options:** `Credentials::AUTH_TYPE_BEARER`, `Credentials::AUTH_TYPE_BODY`, `Credentials::AUTH_TYPE_QUERY`, `Credentials::AUTH_TYPE_BASIC`, `Credentials::AUTH_TYPE_CUSTOM`,\
**Default:** `Credentials::AUTH_TYPE_BEARER` (=`'Bearer'`)

### grantType - `string`
Grant type for OAuth2 flows.\
**Possible options:** `Credentials::GRANT_TYPE_CLIENT_CREDENTIALS`, `Credentials::GRANT_TYPE_PASSWORD_CREDENTIALS` (authorization_code and implicit grants are not yet supported. See [issue #3](https://github.com/pelmered/laravel-http-client-auth-helper/issues/3))\
**Default:** `Credentials::GRANT_TYPE_CLIENT_CREDENTIALS` (=`'client_credentials'`)

### tokenType - `string`
How the access token should be applied to all subsequent requests.\
**Possible options:** `AccessToken::TOKEN_TYPE_BEARER`, `AccessToken::TOKEN_TYPE_QUERY`, `AccessToken::TOKEN_TYPE_CUSTOM` \
**Default:** `AccessToken::TOKEN_TYPE_BEARER` (=`'Bearer'`)

### tokenName - `string`
The name of the token field. This only applies for when the token is applied as a query parameter or to the body of the request.\
**Possible options:** Any string\
**Default:** `'token'`

### expires - `int|string|Closure|Carbon`
This determines when the access token expires.\
**Possible options:** \
**integer** - for how long until expiry in seconds)\
**string**  - Can be key of the field in response that contains the expiry of the token. Can also be a string with a date. This is then parsed by Carbon::parse so any format that Carbon can parse is acceptable.\
**Closure** - A closure that receives the refresh response and can return any other acceptable value (integer, string or Carbon object).\
**Carbon**  - A Carbon object with the time of the expiry.\
**Default:** `3600`

### accessToken - `string|Closure`
This is where the access token can be found on the refresh response.\
**Possible options:**\
**string** - The key of the access token in the refresh response.\
**Closure** - A closure that receives the refresh response and should return the token as a string.\
**Default:** `'access_token'`

### tokenTypeCustomCallback - `?Closure`
A callback for giving dull control of how the authentication should be applied. 
The closure receives the Http client and should return a new Http Client where the auth information has been appended.\
**Possible options:**\ Any closure that returns a Http Client (`Illuminate\Http\Client\PendingRequest`).\
**Default:** `null`

### cacheKey - `?string`
The cache key that should be used to save the access tokens.
If left empty, it will be generated based on the refresh URL.\
**Possible options:**\
**Default:** `null`

### cacheDriver - `?string`
The cache driver/store that should be used for storing the access tokens.
If left empty, the Laravel default will be used.\
**Possible options:**\
**Default:** `null`

## Usage

It's really simple to use. Just add the `withRefreshToken` method to your HTTP request and provide the necessary parameters. No configuration needed.

#### Minimal example:
```php
$response = Http::withRefreshToken(
  'https://example.com/token.oauth2',
  [
    'client_id',
    'client_secret',
  ]
)->get(
  'https://example.com/api',
);
```

#### All parameters with default values:
```php
$response = Http::withRefreshToken(
  'https://example.com/token.oauth2',
  [
    'client_id',
    'client_secret',
  ],
  [
    'scopes' => [],
    'expires' => 'expires_in', // When token should be considered expired. A string key in the response JSON for the expiration. We try to parse different formats and then remove 1 minute to be on the safe side.
    'auth_type' => 'body', // 'body' or 'header'
    'access_token' => 'access_token', // Key for the access token in the response JSON
  ],
  'Bearer'
)->get(
  'https://example.com/api',
);
```

#### For full type safety, you can also provide objects instead of arrays:

```php
use Pelmered\LaravelHttpOAuthHelper\AccessToken;
use Pelmered\LaravelHttpOAuthHelper\Credentials;
use Pelmered\LaravelHttpOAuthHelper\Options;
use Pelmered\LaravelHttpOAuthHelper\RefreshToken;

$response = Http::withRefreshToken(
  'https://example.com/token.oauth2',
  new Credentials(
    clientId: 'client_id',
    clientSecret: 'client_secret',
  ),
  new Options(
    scopes: ['scope1', 'scope2'],
    expires: 3600,
    grantType: 'password_credentials',
    authType: Credentials::AUTH_TYPE_BODY,
    tokenType: AccessToken::TOKEN_TYPE_BEARER,
  ),
)->get(
  'https://example.com/api',
);
```

#### Customize with callbacks
You can also provide callbacks for `expires`, `auth_type`, and `access_token` to customize the behavior.
```php
$response = Http::withRefreshToken(
  'https://example.com/token.oauth2',
  [
    'client_id',
    'client_secret',
  ],
  [
    'expires' => fn($response) => $response->json()['expires_in'] - 300, // Should return the ttl in seconds that has been parsed from the response and can be manipulated as you want.
    'access_token' => fn($response) => $response->access_token, // Should return the access token that has been parsed from the response.
  ],
  'Bearer'
)->get(
  'https://example.com/api',
);
```

Custom auth for refreshing token:

```php
use Illuminate\Http\Client\PendingRequest;

$response = Http::withRefreshToken(
  'https://example.com/token.oauth2',
  [
    'client_id',
    'client_secret',
  ],
  [
    'expires' => fn($response) => $response->json()['expires_in'] - 300, // Should return the ttl in seconds that has been parsed from the response and can be manipulated as you want.
    'access_token' => fn($response) => $response->access_token, // Should return the access token that has been parsed from the response.
    'auth_type' => 'custom',
    'apply_auth_token' => fn(PendingRequest $httpClient) => $request->withHeader('Authorization', 'Bearer ' . $token),
)->get(
  'https://example.com/api',
);
```

For more examples, check out the [Macro tests](tests/Unit/MacroTest.php).

### Integration tips

If you use the same token in multiple places, you can create the client only once and save it. For example:
```php
$this->client = Http::withRefreshToken(
  'https://example.com/token.oauth2',
  [
    'client_id',
    'client_secret',
  ],
  [
    'scopes' => ['read:posts', 'write:posts', 'read:comments'],
  ]
)->baseUrl('https://example.com/api');
```
to use it later like this:
```php
$this->client->get('posts');

$this->client->get('comments');

$this->client->post('posts', [
  'title' => 'My post',
  'content' => 'My content',
]);
```

You can also resolve it in the container if you want.
In your service provider:
```php
$this->app->singleton('my-oauth-client', function ($app) {
  return Http::withRefreshToken(
    'https://example.com/token.oauth2',
    [
      'client_id',
      'client_secret',
    ],
    [
      'scopes' => ['read:posts', 'write:posts', 'read:comments'],
    ]
  )->baseUrl('https://example.com/api');
});
```
and then use it anywhere like this:
```php
app('my-oauth-client')->get('posts');
```
