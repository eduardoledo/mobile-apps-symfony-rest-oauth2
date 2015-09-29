# Creating the service layer

## Prerequisites
We will use [Symfony](http://symfony.com) as our base for the service layer and [Composer](http://getcomposer.org). This guide asumes you have basic knowledge about both.

## First steps
### Step 1 - Creating the project

First we need to create a new symfony project:

```
$ symfony new rest-oauth
```
This will give an output like:
```
Downloading Symfony...

   5.14 MB/5.14 MB ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  100%

Preparing project...

✔  Symfony 2.7.5 was successfully installed. Now you can:

   * Change your current directory to /private/tmp/rest-oauth

   * Configure your application in app/config/parameters.yml file.

   * Run your application:
       1. Execute the php app/console server:run command.
       2. Browse to the http://localhost:8000 URL.

   * Read the documentation at http://symfony.com/doc

```
And that's it.

### Step 2 - Configuring the REST layer
We need to install [FOSRestBundle](https://github.com/FriendsOfSymfony/FOSRestBundle)

```
composer install friendsofsymfony/rest-bundle

```
Then we enable the bundle in AppKernel.php
```php
// app/AppKernel.php
<?php
// app/AppKernel.php

public function registerBundles()
{
    $bundles = array(
        // ...
        new FOS\RestBundle\FOSRestBundle(),
    );
}
```

And configure it
```yaml
# app/config/config.yml
fos_rest:
    param_fetcher_listener: true
    body_listener: true
    format_listener: true
    routing_loader:
        default_format: json
    view:
        view_response_listener: force
```

## Step 3 - Adding the user layer
For the user layer we'll [install FOSUserBundle](install_fosuser.md)

## Step 4 - Adding OAuth2
We'll use [FOSOAuthServerBundle](https://github.com/FriendsOfSymfony/FOSOAuthServerBundle)
```
$ composer install friendsofsymfony/oauth-server-bundle

```
Then we enable the bundle in AppKernel.php
```php
<?php
// app/AppKernel.php

public function registerBundles()
{
    $bundles = array(
        // ...
        new FOS\OAuthServerBundle\FOSOAuthServerBundle(),
    );
}
```
And create the doctrine classes needed by the bundle:
```php
<?php
// src/Acme/ApiBundle/Entity/Client.php

namespace Acme\ApiBundle\Entity;

use FOS\OAuthServerBundle\Entity\Client as BaseClient;
use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Entity
 */
class Client extends BaseClient
{
    /**
     * @ORM\Id
     * @ORM\Column(type="integer")
     * @ORM\GeneratedValue(strategy="AUTO")
     */
    protected $id;

    public function __construct()
    {
        parent::__construct();
        // your own logic
    }
}

```
```php
<?php
// src/Acme/ApiBundle/Entity/AccessToken.php

namespace Acme\ApiBundle\Entity;

use FOS\OAuthServerBundle\Entity\AccessToken as BaseAccessToken;
use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Entity
 */
class AccessToken extends BaseAccessToken
{
    /**
     * @ORM\Id
     * @ORM\Column(type="integer")
     * @ORM\GeneratedValue(strategy="AUTO")
     */
    protected $id;

    /**
     * @ORM\ManyToOne(targetEntity="Client")
     * @ORM\JoinColumn(nullable=false)
     */
    protected $client;

    /**
     * @ORM\ManyToOne(targetEntity="Your\Own\Entity\User")
     */
    protected $user;
}

```
```php
<?php
// src/Acme/ApiBundle/Entity/RefreshToken.php

namespace Acme\ApiBundle\Entity;

use FOS\OAuthServerBundle\Entity\RefreshToken as BaseRefreshToken;
use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Entity
 */
class RefreshToken extends BaseRefreshToken
{
    /**
     * @ORM\Id
     * @ORM\Column(type="integer")
     * @ORM\GeneratedValue(strategy="AUTO")
     */
    protected $id;

    /**
     * @ORM\ManyToOne(targetEntity="Client")
     * @ORM\JoinColumn(nullable=false)
     */
    protected $client;

    /**
     * @ORM\ManyToOne(targetEntity="Your\Own\Entity\User")
     */
    protected $user;
}

```
```php
<?php
// src/Acme/ApiBundle/Entity/AuthCode.php

namespace Acme\ApiBundle\Entity;

use FOS\OAuthServerBundle\Entity\AuthCode as BaseAuthCode;
use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Entity
 */
class AuthCode extends BaseAuthCode
{
    /**
     * @ORM\Id
     * @ORM\Column(type="integer")
     * @ORM\GeneratedValue(strategy="AUTO")
     */
    protected $id;

    /**
     * @ORM\ManyToOne(targetEntity="Client")
     * @ORM\JoinColumn(nullable=false)
     */
    protected $client;

    /**
     * @ORM\ManyToOne(targetEntity="Your\Own\Entity\User")
     */
    protected $user;
}

```
Update our database with the new entities:
```bash
$ app/console doctrine:schema:update --force

```

__Note__: If you don't have `auto_mapping` activated in your doctrine configuration you need to add
`FOSOAuthServerBundle` to your mappings in `config.yml`.

Now we configure the security.yml
```yaml
# app/config/security.yml
security:
    firewalls:
        oauth_token:
            pattern:    ^/oauth/v2/token
            security:   false

        oauth_authorize:
            pattern:    ^/oauth/v2/auth
            # Add your favorite authentication process here

        api:
            pattern:    ^/api
            fos_oauth:  true
            stateless:  true
            anonymous:  false # can be omitted as its default value

    access_control:
        - { path: ^/api, roles: [ IS_AUTHENTICATED_FULLY ] }
```
Import the routing.yml configuration file in app/config/routing.yml:

```yaml
# app/config/routing.yml
fos_oauth_server_token:
    resource: "@FOSOAuthServerBundle/Resources/config/routing/token.xml"

fos_oauth_server_authorize:
    resource: "@FOSOAuthServerBundle/Resources/config/routing/authorize.xml"


```
Add FOSOAuthServerBundle settings in app/config/config.yml:
```yaml

# app/config/config.yml
fos_oauth_server:
    db_driver: orm       # Driver availables: orm, mongodb, or propel
    client_class:        Acme\ApiBundle\Entity\Client
    access_token_class:  Acme\ApiBundle\Entity\AccessToken
    refresh_token_class: Acme\ApiBundle\Entity\RefreshToken
    auth_code_class:     Acme\ApiBundle\Entity\AuthCode

    service:    # We use FOSUserBundle for the authentication
        user_provider: fos_user.user_manager
```
And last, we create a client
```php
<?php
$clientManager = $this->getContainer()->get('fos_oauth_server.client_manager.default');
$client = $clientManager->createClient();
$client->setAllowedGrantTypes(array('token', 'authorization_code', 'password'));
$clientManager->updateClient($client);

```
We could also add a login with the [facebook user token](facebook_user_token_auth.md)
