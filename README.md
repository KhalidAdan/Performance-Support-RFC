# EPSS Environment Refactor / Request for Comments

## Abstract

Recreate Fusion and the template to make use of design patterns to allow for easier development in the future and to fix inconsistenices and errors in Fusion. 

## Stakeholders

- KH
- Integras
- Technical Learning
- LTS

## Problem

The current environment is not sufficient for maintaining and publishing courses. The biggest problem currently is dealing with product classes.

Product classes were an afterthought while building the template and Fusion and supporting a growing number of use cases is becoming harder as every new type of instance comes with its own sets of validation rules, view logic, loading different content filters and making differnet service layer calls i.e. different social features, comment features, e.t.c.

## Template Solution

The proposed solution for the template is to graduate to using more design patterns to handle the creation fo classes and the adding of behaviour to those classes. For specifics, see the link below but here is an overview of how it will work:

The EPSS template should employ an Abstract Factory pattern after retrieving the product information. This will allow the template to construct the necessary classes to handle all product behaviour.

An example of this would be:

URL -> KnowHow/topics/default.htm

Template queries product information with KH's product id (118) and then the controller layer creates the product object and passes the product class to the abstract factory. 

Once that is complete the view will start to be assembled via product class specific builders and returned to the skeleton.php file where a render function will be called.

[RFC for the epss template](Template/RFC.md) *WIP*

## Management Solution

*WIP*

[RFC for Fusion](Fusion/RFC.md) 

## Data Solution

Another possible area for cleanup is our database solution. After almost 3 years of development, the model has grown and things have been added over time and I'd like a fresh take on how things work. 

Entities:

Pages
Topics
Products
Menu
Product classes


## Note to readers

This document is the culmination of a couple weeks of introspection and research. Everything in here I learned very recently, and if this comes off as confusing that is completely understandable. I recommend looking at the resources section for a really good description of these topics and maybe even give a couple of examples a try. 

I'm trying to find a good piece of software for creating UML diagrams to give more context about what classes we'll be building but I have yet to fond a great one. Any help here would be awesome.

Also, I'm available for questions whenever.

## Resources

https://sourcemaking.com/design_patterns/abstract_factory

https://sourcemaking.com/design_patterns/chain_of_responsibility 

https://medium.com/@devDeeJay/getting-started-with-design-patterns-for-beginners-d0032241b760 

https://dev.to/thayseonofrio/how-to-get-started-with-design-patterns-iai 

