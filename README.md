# Zoom Provider for OAuth 2.0 Client
[![Latest Version](https://img.shields.io/github/release/vantezzen/oauth2-pinterest.svg?style=flat-square)](https://github.com/vantezzen/oauth2-pinterest/releases)
[![Total Downloads](https://img.shields.io/packagist/dt/vantezzen/oauth2-pinterest.svg?style=flat-square)](https://packagist.org/packages/vantezzen/oauth2-pinterest)

This package provides Zoom OAuth 2.0 support for the PHP League's [OAuth 2.0 Client](https://github.com/thephpleague/oauth2-client).

## Installation

To install, use composer:

```
composer require larmib2019/oauth2-zoom
```

## Usage

Usage is the same as The League's OAuth client, using `\League\OAuth2\Client\Provider\Zoom` as the provider.

### Authorization Code Flow

```php
$provider = new League\OAuth2\Client\Provider\Zoom([
    'clientId'          => '{zoom-client-id}',
    'clientSecret'      => '{zoom-client-secret}',
    'redirectUri'       => 'https://example.com/callback-url'
]);

if (!isset($_GET['code'])) {

    // If we don't have an authorization code then get one
    $authUrl = $provider->getAuthorizationUrl();
    $_SESSION['oauth2state'] = $provider->getState();
    header('Location: '.$authUrl);
    exit;

// Check given state against previously stored one to mitigate CSRF attack
} elseif (empty($_GET['state']) || ($_GET['state'] !== $_SESSION['oauth2state'])) {

    unset($_SESSION['oauth2state']);
    exit('Invalid state');

} else {

    // Try to get an access token (using the authorization code grant)
    $token = $provider->getAccessToken('authorization_code', [
        'code' => $_GET['code']
    ]);

    // Optional: Now you have a token you can look up a users profile data
    try {

        // We got an access token, let's now get the user's details
        $user = $provider->getResourceOwner($token);

        // Use these details to create a new profile
        printf('Hello %s!', $user->getName());

    } catch (Exception $e) {

        // Failed to get user details
        exit('Oh dear...');
    }

    // Use this to interact with an API on the users behalf
    echo $token->getToken();
}
```

### Managing Scopes

When creating your Pinterest authorization URL, you can specify the state and scopes your application may authorize.

```php
$options = [
    'state' => 'OPTIONAL_CUSTOM_CONFIGURED_STATE',
    'scope' => ['read', 'write'] // array or string
];

$authorizationUrl = $provider->getAuthorizationUrl($options);
```
If neither are defined, the provider will utilize internal defaults.

At the time of authoring this documentation, the [following scopes are available](https://zoom.us/).

- user:read

## Testing

``` bash
$ ./vendor/bin/phpunit
```

## Credits

- [vantezzen](https://github.com/vantezzen)
- [Steven Maguire](https://github.com/stevenmaguire)
- [ajibarra](https://github.com/ajibarra)
- [The PHP League](https://github.com/thephpleague)
- [All Contributors](https://github.com/vantezzen/oauth2-pinterest/contributors)


## License

The MIT License (MIT). Please see [License File](https://github.com/iflorespaz/oauth2-zoom/blob/master/LICENSE) for more information.
