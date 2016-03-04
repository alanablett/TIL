# Magento 2

Notes from the Magento 2 developer guide book by Branko Ajzele [http://www.packtpub.com/web-development/magento-2-developers-guide](http://www.packtpub.com/web-development/magento-2-developers-guide)

---

## General concepts

### Architectural layers

**Presentation** - Consists of layouts, blocks, templates and controllers. It also includes presentation assets such as javascript and css.

**Service** - Bridges the ***presentation*** and ***domain***. Contains service contracts (think PHP interfaces).

**Domain** - Contains the business logic. Data objects and models that may also contain a reference to the resource models that sit in the ***persistence*** layer. 

**Persistence** - Resource models etc the may either be simple or EAV.

## Module development

### File/Directory structure

```
├── Block
├── Controller
├── Helper
├── Model
├── README.md
├── Test
├── composer.json
├── etc
│   ├── acl.xml
│   ├── adminhtml
│   │   └── system.xml
│   ├── config.xml
│   ├── email_templates.xml
│   ├── frontend
│   │   ├── di.xml
│   │   ├── page_types.xml
│   │   └── routes.xml
│   └── module.xml
├── i18n
├── registration.php
└── view
    ├── adminhtml
    └── frontend
```

`Block` view-related PHP classes.

`Controller` controllers repond to http requests.

`etc` module configuration.

`Helper` usually fetch store configuration values from getter methods.

`i18n` module translation CSV files.

`Model` entities, resource entities, collections, and other business classes.

`Test` module unit tests.

`composer.json` standard composer.json file. Details of the package and its dependencies.

`view` admin and storefront template files (.phtml and .html) and static files (.js and .css).

`registration.php` module registration.

### composer.json

Here is an example from the `Magento/Contact` module

```json
{
    "name": "magento/module-contact",
    "description": "N/A",
    "require": {
        "php": "~5.5.0|~5.6.0|~7.0.0",
        "magento/module-config": "100.0.*",
        "magento/module-store": "100.0.*",
        "magento/module-backend": "100.0.*",
        "magento/module-customer": "100.0.*",
        "magento/module-cms": "100.0.*",
        "magento/framework": "100.0.*"
    },
    "type": "magento2-module",
    "version": "100.0.2",
    "license": [
        "OSL-3.0",
        "AFL-3.0"
    ],
    "autoload": {
        "files": [
            "registration.php"
        ],
        "psr-4": {
            "Magento\\Contact\\": ""
        }
    }
}
```

Notice the use of an autoloader for the `registration.php` file and the psr-4 autoload definition.

### Service contracts

Comprise of **data interfaces** and **service interfaces**.

**Data interfaces** - validation, entity information, search related functions. Defined in `<module>/Api/Data`.

Below is the CMS Block Interface. The interface implementation can be found in `Model/Block.php`.

```php
namespace Magento\Cms\Api\Data;

interface BlockInterface
{
    const BLOCK_ID      = 'block_id';
    const IDENTIFIER    = 'identifier';
    const TITLE         = 'title';
    const CONTENT       = 'content';
    const CREATION_TIME = 'creation_time';
    const UPDATE_TIME   = 'update_time';
    const IS_ACTIVE     = 'is_active';

    public function getId();
    public function getIdentifier();
    public function getTitle();
    public function getContent();
    public function getCreationTime();
    public function getUpdateTime();
    public function isActive();
    public function setId($id);
    public function setIdentifier($identifier);
    public function setTitle($title);
    public function setContent($content);
    public function setCreationTime($creationTime);
    public function setUpdateTime($updateTime);
    public function setIsActive($isActive);
}
```

**Service interfaces** - repository, management and metadata. Defined in `<module>/Api`.

Below is the CMS Block Repository Interface. The interface implementation can be found in `Model/BlockRepository.php`.

```php
namespace Magento\Cms\Api;

use Magento\Framework\Api\SearchCriteriaInterface;

interface BlockRepositoryInterface
{
    public function save(Data\BlockInterface $block);
    public function getById($blockId);
    public function getList(SearchCriteriaInterface $searchCriteria);
    public function delete(Data\BlockInterface $block);
    public function deleteById($blockId);
}
```

## Code generation

When certain classes are called within magento 2, they are infact fetched from the `var/generation` directory. If they do not exist in there then they are generated automatically. These are **Factory**, **Proxy** and **Interceptor** classes.

**Factory** - Creates instances of other classes. Typically using the **object manager**. Business objects/logic should NOT use the the object manager.

**Proxy** - A wrapper for some base class. They provide better performance by only instantiating the base class when it is needed.

**Interceptor** - Related to plugins. Details to follow.

In order to regenerate the code run `magento setup:di:compile` on simple sites or `magento setup:di:compile-multi-tenant` on multi site.