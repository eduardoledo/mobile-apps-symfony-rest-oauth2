## Authenticating with Facebook user-token
If you want to authenticate using the user-token you got from Facebook in your phone, follow the next steps.

### Step 1: Install FacebookSDK using composer
```
$ composer require facebook/php-sdk-v4

```
### Step 2: Create a new GrantExtension
```php
<?php
// src/Acme/ApiBundle/GrantExtensions/FacebookGrantExtension

namespace Acme\ApiBundle\GrantExtensions;

use FOS\OAuthServerBundle\Storage\GrantExtensionInterface;
use OAuth2\Model\IOAuth2Client;
use FOS\UserBundle\Doctrine\UserManager;

class FacebookGrantExtension implements GrantExtensionInterface
{

    /**
     * @var UserManager
     */
    protected $userManager = null;

    /**
     * @var \Facebook\Facebook
     */
    protected $facebookSdk = null;

    public function __construct(UserManager $userManager, \Facebook\Facebook $facebookSdk)
    {
        $this->userManager = $userManager;
        $this->facebookSdk = $facebookSdk;
    }

    /**
     * @see OAuth2\IOAuth2GrantExtension::checkGrantExtension
     */
    public function checkGrantExtension(IOAuth2Client $client, array $inputData, array $authHeaders)
    {
        if (!isset($inputData['facebook_access_token'])) {
            return false;
        }

        $this->facebookSdk->setDefaultAccessToken($inputData['facebook_access_token']);
        try {
            // Try to get the user with the facebook token from Open Graph
            $fbData = $this->facebookSdk->get('/me?fields=email,id,first_name,last_name,name,name_format');


            if (!$fbData instanceof \Facebook\FacebookResponse) {
                return false;
            }

            // Check if a user match in database with the facebook id
            $user = $this->userManager->findUserBy([
                'facebookId' => $fbData->getDecodedBody()['id']
            ]);

            // If none found, try to match email
            if (null === $user && isset($fbData->getDecodedBody()['email'])) {
                $user = $this->userManager->findUserBy([
                    'email' => $fbData->getDecodedBody()['email']
                ]);
            }

            // If no user found, register a new user and grant token
            if (null === $user) {
                // TODO: Create new user
                return false;
            } else { // Else, return the access_token for the user
                // Associate user with facebookId
                $user->setFacebookId($fbData->getDecodedBody()['id']);
                $this->userManager->updateUser($user);

                return array(
                    'data' => $user
                );
            }
        } catch (\FacebookApiExceptionion $e) {
            return false;
        }
    }

}

```

### Step 3: Add your Facebook app config
```yaml
# app/config/config.yml
parameters:
    facebook: { app_id: your_app_id, app_secret: your_app_secret, default_graph_version: v2.4 }
```
### Step 4: Make the FacebookSDK and grant extension available as services
```yaml
# app/config/config.yml
services:
    facebook:
        class: Facebook\Facebook
        arguments:
            config: %facebook%

    my.oauth.facebook_extension:
        class: Friendball\Rest\ApiBundle\GrantExtensions\FacebookGrantExtension
        arguments:
            userManager: "@fos_user.user_manager"
            facebookSdk: "@facebook"
        tags:
            - { name: fos_oauth_server.grant_extension, uri: 'http://grants.api.your-site.net/facebook_access_token' }
```
**Note**: You have to add the new grant type (in this example `http://grants.api.your-site.net/facebook_access_token`) to your client o create a new one for authenticating with this extension.
