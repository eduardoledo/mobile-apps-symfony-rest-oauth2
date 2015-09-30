## Installing [JMSSerializerBundle](https://github.com/schmittjoh/JMSSerializerBundle)
### Step 1: Install the bundle using composer

```
$ composer require jms/serializer-bundle

```

### Step 2: Enable the bundle in AppKernel.php

```php
<?php
// app/AppKernel.php

public function registerBundles()
{
    $bundles = array(
        // ...
        new JMS\SerializerBundle\JMSSerializerBundle(),
    );
}
```
### Step 3: Configure the bundle
This bundle works right out of the box, without any special configuration, but we can tweak it a little. We'll override one of this bundle parameters so the serialized entities have exactly the same property names as the original ones.

```yaml
# app/config/parameters.yml
parameters:
    jms_serializer.camel_case_naming_strategy.class: JMS\Serializer\Naming\IdenticalPropertyNamingStrategy
```
---
**Next**: [Installing FOSRestBundle](2_installing_fos_rest.md)
