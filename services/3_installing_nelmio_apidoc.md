## Installing [NelmioApiDocBundle](https://github.com/nelmio/NelmioApiDocBundle)
### Step 1: Install the bundle using composer

```
$ composer require nelmio/api-doc-bundle

```

### Step 2: Enable the bundle in AppKernel.php

```php
<?php
// app/AppKernel.php

public function registerBundles()
{
    $bundles = array(
        // ...
        new Nelmio\ApiDocBundle\NelmioApiDocBundle(),
    );
}
```

### Step 3: Import the routing definition in routing.yml:
```yaml
# app/config/routing.yml
NelmioApiDocBundle:
    resource: "@NelmioApiDocBundle/Resources/config/routing.yml"
    prefix:   /api/doc
```

### Step 4: Configure the bundle
```yaml
# app/config/config.yml
nelmio_api_doc: ~
```

The **NelmioApiDocBundle** requires Twig as a template engine so do not forget to enable it:
```yaml
# app/config/config.yml
framework:
    templating:
        engines: ['twig']
```

---

**Previous**: [Installing FOSRestBundle](2_installing_fos_rest.md)

**Next**: [Installing FOSUserBundle](4_installing_fos_user.md)
