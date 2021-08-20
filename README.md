jTags module
==================

This is a module for Jelix, allowing to manage tags on records/objects.

This module is for Jelix 1.7.2 and higher.

It was originally developed into the [Booster project](https://github.com/jelix/booster/)
and the [Havefnubb project](https://github.com/havefnubb/havefnubb/).

Setting up the module
=====================

The best method is to use Composer.

In a commande line inside your project, execute:

```
composer require "jelix/jtags-module"
```

Launch the configurator for your application to enable the module

```bash
php dev.php module:configure jtags
```

After configuring the module, you should launch the installer of your application
to activate the module:

```bash
php install/installer.php
```


How to use it
=============

Adding tags to an object
-------------------------

Add in your jforms:

```xml
<textarea ref="tags" rows="2" cols="60">
    <label>Type your tags</label>
</textarea>
```

Add in your controller if you want autocompletion:

```php
jClasses::getService("jtags~tags")->setResponsesHeaders();
```

Displaying tags cloud
---------------------

Into your controller:

```php
$tpl->assignZone("tagscloud", "jtags~tagscloud", array('destination'=>'module~ctrl:method', 'maxcount'=>30));
```

or in a template

```
{zone "jtags~tagscloud", array('destination'=>'module~ctrl:method', 'maxcount'=>20)}
```

- `destination` : the action which will display objects corresponding to a tag. it receive a "tag" parameter.
- `maxcount` : displays only the given number of tags. Set it to 0 or don't set this parameter to displays all tags.

 
Getting tags of an object
-------------------------

```php
$tags = $srv_tags->getTagsByObject($scope, $id);
```

Getting all objects marked with tags
------------------------------------

```php

$tags = $this->param("tag", false);
if ($tags) {
    $srv_tags = jClasses::getService("jtags~tags");
    $cond->addCondition('sn_id', '=', $srv_tags->getObjectsByTags($tags, "snippet"));
}
```
