## Installing [FOSRestBundle](https://github.com/FriendsOfSymfony/FOSRestBundle)
### Step 1: Install the bundle using composer

```
$ composer require friendsofsymfony/rest-bundle

```

### Step 2: Enable the bundle in AppKernel.php

```php
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
### Step 3: Configure the bundle
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
---

**Previous**: [Installing JMSSerializerBundle](1_installing_jms_serializer.md)

**Next**: [Installing NelmioApiDocBundle](3_installing_nelmio_apidoc.md)
