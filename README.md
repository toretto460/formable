# Formable Symfony Bundle
![Build](https://travis-ci.org/PUGX/formable.svg) 
[![Latest Stable Version](https://poser.pugx.org/pugx/formable/v/stable)](https://packagist.org/packages/pugx/formable) [![Total Downloads](https://poser.pugx.org/pugx/formable/downloads)](https://packagist.org/packages/pugx/formable) [![Latest Unstable Version](https://poser.pugx.org/pugx/formable/v/unstable)](https://packagist.org/packages/pugx/formable) [![License](https://poser.pugx.org/pugx/formable/license)](https://packagist.org/packages/pugx/formable)

# Why?

Because the cleanest way to transfer data from a web request to the domain is by using DTOs. 
For simple DTOs Symfony forces you to create 2 classes, the `FormType` class and the `SomethingDTO` class.

# How?

This Bundle allows you to describe DTOs by the annotation `@Formable()`. Let's see an example. 

## Example

### The Data Transfer Object

```php

use Formable\Definition\Formable;
use Symfony\Component\Validator\Constraints as Assert;

class PublishPostCommand
{
    /**
     * @Formable(name="title", dataType="text")
     *
     * @Assert\Length(max=250)
     */
    public $title;

    /**
     * @Formable(name="content", dataType="text")
     */
    public $content;

    /**
     * @Formable(name="tags", dataType="collection", options={
     *   "type"="text",
     *   "allow_add"=true
     * })
     *
     * @Assert\Count(
     *   min = "2"
     * )
     *
     */
    public $tags;

    /**
     * @Formable(name="date", dataType="date", options={
     *   "widget"="single_text",
     *   "format"="yyyy-M-d"
     * })
     */
    public $date;
}

```

### Embedded DTOs

```php
    /**
     * @var
     *
     * @Formable(name="moneyDTO", class="Formable\Tests\Integration\DTOs\TestMoneyDTO")
     */
    public $moneyDTO;
```


### The Controller

```php

public function publishAction(Request $request)
{
    $publishCommand = new PublishPostCommand();
    $publishCommand->date = new \DateTime('now');
    
    $form = $this->get('pugx.formable')->generate($publishCommand);
    $form->submit($request->request->all(), false /* Do not clear missing data */);
    
    if ($form->isValid()) {
        ...
    }
}
```
## The annotation in depth

The `@Formable()` annotation follows the `Symfony\Component\Form\FormBuilderInterface` interface.


**ARGUMENTS**: 

- **name**: [_string_] the field name
- **dataType**: [_string_] the [FormType](http://symfony.com/doc/current/reference/forms/types.html)
- **options**: [_array_] the FormType options

```php
    /**
     * @Formable(name="date", dataType="date", options={
     *   "format"= "yyyy-MM-dd",
     *   "days" = {1,2,3,4}
     * })
     */
    public $date;
```

## Installation
 
`composer require pugx/formable`

```php
// Register the Bundle

class AppKernel extends Kernel
{
    public function registerBundles()
    {
        $bundles = array(
            ...
            new \Formable\Bundle\FormableBundle(),
        );

        return $bundles;
    }

}
```

## Run tests

`bin/test`
