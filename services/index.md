# Creating the service layer

## Prerequisites
We will use [Symfony](http://symfony.com) as our base for the service layer and [Composer](http://getcomposer.org). This guide asumes you have basic knowledge about both.

## First steps
### Step 1 - Creating the project and installiing the bundles

First we need to create a new symfony project:

```
$ symfony new rest-oauth
```
This will output something like:
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
#### And install the needed bundles:

- [JMSSerializerBundle](1_installing_jms_serializer.md): Entity serialization/deserialization
- [FOSRestBundle](2_installing_fos_rest.md): Automatic routing, etc.
- [NelmioApiDocBundle](3_installing_nelmio_apidoc.md): API Automatic documentation
- [FOSUserBundle](4_installing_fos_user.md): User management
- [FOSOAuthServerBundle](5_installing_fos_oauthserver.md): OAuth2 authentication

### Step 2 - Creating the entities
### Step 3 - Creating the controllers
### Step 4 - Creating the routes
### Step 5 - Creating basic API documentation
