# Proposed template changes

## Controller

Preamble: The term 'controller' and 'service' are generally the same thing **in our organization**. For the purposes of this RFC I'll be referencing controllers but they terms are interchangeable.

The controller layer will largely be unchanged, it will be responsable for three main activities:

1. Retrieving and marshalling necessary information 
2. Constructing correct objects based on product class
3. Initializing view components

### Retrieving Information

There will be two methods for retrieving and marshalling information in the template. You will either interact with a DAO (data access/abstraction object) or interact with an API. 

```php

$this->product = $this->service->getProduct();
$this->page = $this->service->getPageFromPath();

```

### Constructing objects

Because of the slot-in nature of EPSS functionality (meaning that depending on the product class you will get different visuals or evern different behaviour) another responsibility of the controller layer will be to construct objects based on product class. This is done in primarily two ways:

1. Abstract factory 

```php
// create common components here, ex. All products send mail
$this->mailService = \LTS\Core::getComponentFactory()->createComponent("MailService");
// create X component based on the product class, and use that to create a family of objects. 
$this->abstractFactory = \LTS\Core::getComponentFactory()->createComponent($this->product->getProductClass());
$this->dao = $this->abstractFactory->getDataAcessObject();
$this->accessControlService = $this->abstractFactory->getAccessControlService();
$this->ckeService = $this->abstractFactory->getCKEditorService();
$this->skeletonBuilder = $this->abstractFactory->getBuilder();

```

2. Builders (and possibly a director) for view components