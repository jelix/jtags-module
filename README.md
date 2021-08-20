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

All tags are stored into a table. An other table contains the association between a tag, 
a scope and an id.

A scope is the type of records (a table name for example), or a domain.
An id is the id of a record inside the scope.

When you store some tags for an object, you give a scope and the id of the object.

For example, for news posts, the scope may be `news`, and the id, the id of a news.

Editing tags within a form
---------------------------

Add in your jforms:

```xml
<input ref="tags" rows="2" cols="60">
    <label>Type your tags</label>
</input>
```

When initializing the form, retrieve the list of tags and set it to the input:

```php
$form = jForms::create('news', $id_news);

// ...

$srvTags = jClasses::getService("jtags~tags");
$tags = implode(',', $srvTags->getTagsBySubject('news', $id_news));
$form->setData('tags', $tags);
```

If you want autocompletion, add the class on the widget, into your template:

```
{ctrl_control 'tags', ["class"=>"jtags-autocomplete"]}
```

And call `setResponseHeaders()` into your controller:

```php
$resp = $this->getResponse('html');
//...
jClasses::getService("jtags~tags")->setResponsesHeaders($resp);
```

Saving tags after submitting a form
-----------------------------------

In the action of submit, retrieve the list of tags, and call `saveTagsBySubject()`

```php
$id_news = $this->param('id_news');
$form = jForms::fill('news', $id_news);

// ...

$srvTags = jClasses::getService("jtags~tags");
$tags = explode(',', $form->getData('tags'));

$srvTags->saveTagsBySubject($tags, 'news', $id_news));

```


Displaying the list of tags
---------------------------

to display the list of tags of a record, call `getTagsBySubject`

```php
$srvTags = jClasses::getService("jtags~tags");
$tags = implode(', ', $srvTags->getTagsBySubject('news', $id_news));

$tpl->assign('tags', $tags);
```


Displaying tags cloud
---------------------

There is a zone to display a tags cloud.

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


Getting all objects marked with tags
------------------------------------

```php

$tag = $this->param("tag", false);
if ($tag) {
    $srv_tags = jClasses::getService("jtags~tags");
    $listOfNewsId = $srv_tags->getSubjectsByTags($tag, "news"));
}
```
