---
layout: post
title: Remove link from many2one field widget in Odoo
categories: [odoo]
---
In form view's read mode, many2many fields are rendered by default as links pointing to the referenced objects. Often times this is undesirable, especially when referencing simple models.

You can change this behavior and simply display referenced object's name, without linking to it, using widget option called `no_open`:

{% highlight xml %}
<field name="city" options="{'no_open': True}"/>
{% endhighlight %}

Very simple, but undocumented.
