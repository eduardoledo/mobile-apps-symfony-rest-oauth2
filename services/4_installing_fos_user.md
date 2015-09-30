

## Installing [FOSUserBundle](https://github.com/nelmio/NelmioApiDocBundle)
(This is a summary from the [official documentation](https://symfony.com/doc/master/bundles/FOSUserBundle/index.html))
### Step 1: Install the bundle using composer

```
$ composer require friendsofsymfony/user-bundle

```

### Step 2: Enable the bundle in AppKernel.php

```php
<?php
// app/AppKernel.php

public function registerBundles()
{
    $bundles = array(
        // ...
        new FOS\UserBundle\FOSUserBundle(),
    );
}
```

### Step 3: Create the User class:

1. Extend the base User class (from the Model folder if you are using any of the doctrine variants, or Propel for propel 1.x)
2. Map the id field. It must be protected as it is inherited from the parent class.

```php
<?php
// src/AppBundle/Entity/User.php

namespace AppBundle\Entity;

use FOS\UserBundle\Model\User as BaseUser;
use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Entity
 * @ORM\Table(name="fos_user")
 */
class User extends BaseUser
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
### Step 4: Configure security.yml
```yaml
security:
    encoders:
        FOS\UserBundle\Model\UserInterface: bcrypt
    providers:
        fos_userbundle:
            id: fos_user.user_provider.username
```

### Step 5: Configure the bundle
```yaml
# app/config/config.yml
fos_user:
    db_driver: orm # other valid values are 'mongodb', 'couchdb' and 'propel'
    firewall_name: main
    user_class: AppBundle\Entity\User
```

### Step 6: Import routing files
```yaml
# app/config/routing.yml
fos_user:
    resource: "@FOSUserBundle/Resources/config/routing/all.xml"
```

### Step 7: Update database schema
```
$ app/console doctrine:schema:update --force
```

---

**Previous**: [Installing NelmioApiDocBundle](3_installing_nelmio_apidoc.md)

**Next**: [Installing FOSUserBundle](5_installing_fos_oauthserver.md)
