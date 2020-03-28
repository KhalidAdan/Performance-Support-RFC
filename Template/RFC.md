# Proposed template changes

# Controller

**Preamble:** The term 'controller' and 'service' are generally the same thing *in our organization*. For the purposes of this RFC I'll be referencing controllers but the terms are interchangeable.

The controller layer will largely be unchanged, it will be responsable for three main activities:

1. Retrieving and marshalling necessary information 
2. Constructing correct objects based on product class
3. Initializing/Rendering view components

## Retrieving Information

There will be two methods for retrieving and marshalling information in the template. You will either interact with a DAO (data access/abstraction object) or interact with an API. 

```php

$this->product = $this->service->getProduct();
$this->page = $this->service->getPageFromPath();

```

## Constructing objects

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
        //calling the render method wil lrender all of its children
        $skeleton->render();
    }
}

```
# Views

## Initializing/Rendering view components
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

What's happening here is that PHP's output buffer is being used to start adding content, and this works recursively through the tree because each component has the ability to call render on its children. The ul component is completed only when all listen items are finished being sent to the output buffer.

## Implications of View Components

*WIP*

Here are a few of the issues I see:

1. Depending on the level of abstraction, there might be a *lot* of files in the View Components file
2. Error handling might be a bit cumbersome, but I have yet to figure out a clean way to handle them. Possibly a specific error class that extends exception just for view components? Then we could send the classname where the error started and more verbose information about the error.

# Access Control 

*WIP*

The template will need a way to handle access control, for things like access control. I'll flesh this section out later, but I'm thinking that the template's current access control will need ot be overhauled and maybe even merged with Fusion's.

The reason why we'd merge with Fusion is so we could have one set of soltuions for things like access controls and moderation rights.