# Proposed template changes

## Controller

**Preamble:** The term 'controller' and 'service' are generally the same thing *in our organization*. For the purposes of this RFC I'll be referencing controllers but they terms are interchangeable.

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

The point of the abstract factory is to create a family of objects needed for our application to run. It is created first and it has facotry methods inside which return objects based o nthe passed product class. More informationm about factory methods can be found here: [Factory Method](https://sourcemaking.com/design_patterns/factory_method). 

```php
// create common components here, ex. All products send mail
$this->mailService = \LTS\Core::getComponentFactory()->createComponent("MailService");
// create X component based on the product class, and use that to create a family of objects. 
$this->abstractFactory = new AbstractFactory($this->product->getProductClass());
$this->dao = $this->abstractFactory->getDataAcessObject();
$this->accessControlService = $this->abstractFactory->getAccessControlService();
$this->ckeService = $this->abstractFactory->getCKEditorService();
$this->skeletonBuilder = $this->abstractFactory->getSkeletonBuilder();

```

2. Builders (and possibly a director) for view components

The skeleton builder for the epss will take the given information (the product class) and use that to create a nested tree of components.

```php

class SkeleTonBuilder {

    /** constructor with product class property */

    public function buildSkeleton() 
    {
        $skeleton = new SkeletonView();
        $banner = new Banner();
        $menu = new Menu();
        ...
        $skeleton->addBanner($banner);
        $skeleton->addMenu($banner);
        ...
        //calling the render method will render all of its children
        $skeleton->render();
        
    }
}

```

Calling render will then call the render functions of all child properties in sequence. A simple example of a render function for an unordered list would be:

```php
    public function render()
    {
        ob_start();
        ?>
            <ul>
                <?php
                    foreach($listItems as $li):
                        $li->render();
                    endforeach;
                ?>
            </ul>
        <?php
        return ob_get_clean();
    }

```

What's happening here is that PHP's output buffer is being requested to start creating content, and this works recursively through the tree because each component has the ability to call render on its children. 
