---
layout: post
title: Form widgets for many2one fields in Odoo
categories: [odoo]
---
## Index
1. [`many2one` widget (default)](#many2one-widget-default)
1. [`many2onebutton` widget](#many2onebutton-widget)

## `many2one` widget (default)
![many2one widget](/assets/images/many2one_widget.png)

**Options**

* `no_quick_create` - remove the `Create and edit...` option.
* `no_create_edit` - remove the `Create "foo"` option.
  ![many2many widget](/assets/images/many2one_widget2.png)
* `no_create` - `no_quick_create` and `no_create_edit` combined.
* `no_open` - in read mode: do not render as a link.

**Example**
{% highlight xml %}
<field name="field_name" options="{'no_create': True}"/>
{% endhighlight %}

---

## `many2onebutton` widget
This widget seems rather pointless. It's just a clickable circle.

* It's red if the field is empty. Clicking on the red circle opens a form for creating a new related item (in a modal window).
![many2manybutton widget](/assets/images/many2onebutton_widget2.png)

* It's green if the field is not empty. Clicking on the green circle opens a form for editing the related item (in a modal window).
![many2manybutton widget](/assets/images/many2onebutton_widget.png)

There doesn't seem to be a way to use this widget to select a preexisting item.

**Example**
{% highlight xml %}
<field name="field_name" widget="many2onebutton"/>
{% endhighlight %}
