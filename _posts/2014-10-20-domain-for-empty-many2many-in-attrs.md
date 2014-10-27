---
layout: post
title: Changing element's attrs based on an empty many2many relation
categories: [odoo]
---
By using the `attrs` attribute on a view element you can dynamically set its attributes based on another field's value. For example you could make a field `long_description` read-only provided that `short_description` is not filled out:

{% highlight xml %}
<field name="short_description"/>
<field name="long_description"
    attrs="{'readonly': [('short_description', '=', False)]}"/>
{% endhighlight %}

...or present the user with a special message based on field's value:

{% highlight xml %}
<field name="favourites" string="What do you like?"/>
<div attrs="{'invisible': [('favourites', '=', 'ponies')]}">
    I like ponies too!
</div>
{% endhighlight %}

As you may have noticed, `attrs` takes a dictionary mapping element's attributes to [odoo domains][domains]. What may not be initially obvious, is that although domains are regarded as part of the Odoo ORM, and are usually interpreted server-side in Python, in this particular case they are used client side - directly by JavaScript code in user's browser. There are subtle differences between how domains are interpreted in Python and JavaScript.

Recently I wanted to make an item invisible when many2many relation is empty:

{% highlight xml %}
<field name="pony_ids" string="List your favourite ponies"/> <!-- many2many -->
<div attrs="{'invisible': [('pony_ids', '=', ???)]}">
    I like those too!
</div>
{% endhighlight %}

"I like those too!" should appear only after at least one pony was added to the pony_ids many2many relation.

I wasn't sure about the exact form the domain should take. I begun by trying those:

* `[('pony_ids', '=', False)]`
* `[('pony_ids', '=', None)]`
* `[('pony_ids', '=', [])]`

To no avail.

At the end it turned out (thank you Chrome Developer Tools!) that the domain should be `[('pony_ids', '=', [(6, False, [])])]`:

{% highlight xml %}
<field name="pony_ids" string="List your favourite ponies"/> <!-- many2many -->
<div attrs="{'invisible': [('pony_ids', '=', [(6, False, [])])]}">
    I like those too!
</div>
{% endhighlight %}

This of course brings to mind [the three-element tuple commands][orm-write] (I also [described them here][orm-write-so]). Note however that you need to use the exact form I showed above. Looking at the commands documentation, you may think that version with `4` instead or `6` or `None` instead of `False` would be equally valid. Theoretically speaking this may be true, but the comparison performed in JavaScript is strict, so any other form than the one shown above won't work.

If you want a domain that matches a many2many relation with a particular set of objects you can, but remember that the order also have to match. Here's an example of a domain matching a relation with one pony with id `5`: `[('pony_ids', '=', [(6, False, [5])])]`.

[domains]: https://www.odoo.com/documentation/8.0/reference/orm.html#reference-orm-domains
[orm-write]: https://www.odoo.com/documentation/8.0/reference/orm.html#openerp.models.Model.write
[orm-write-so]: http://stackoverflow.com/a/26409747/262618
