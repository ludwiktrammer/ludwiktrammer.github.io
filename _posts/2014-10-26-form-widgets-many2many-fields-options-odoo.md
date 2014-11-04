---
layout: post
title: Form widgets for many2many fields in Odoo
categories: [odoo]
---
## Index
1. [`many2many` widget (default)](#many2many-widget-default)
1. [`many2many_tags` widget](#many2manytags-widget)
1. [`many2many_checkboxes` widget](#many2manycheckboxes-widget)
1. [`many2many_kanban` widget](#many2manykanban-widget)
1. [`x2many_counter` widget](#x2manycounter-widget)
1. [`many2many_binary` widget](#many2manybinary-widget)

## `many2many` widget (default)
The `many2many` widget uses the default list view for the related model, to display a list of related objects.
![many2many widget](/assets/images/many2many_widget.png)

**Options**

* `no_create` - remove the "Create" button.

**Example**
{% highlight xml %}
<field name="field_name" options="{'no_create': True}"/>
{% endhighlight %}

## `many2many_tags` widget
Facebook-like multi item selection.
![many2many_tags widget](/assets/images/many2many_tags_widget.png)

**Options**

* `no_quick_create` - remove the `Create and edit...` option.
* `no_create_edit` - remove the `Create "foo"` option.
  ![many2many_tags widget](/assets/images/many2many_tags_widget2.png)
* `no_create` - `no_quick_create` and `no_create_edit` combined.

**Example**
{% highlight xml %}
<field name="field_name"
    widget="many2many_tags"
    options="{'no_create_edit': True}"/>
{% endhighlight %}

---

## `many2many_checkboxes` widget
According to a comment in Odoo's source code:

> This type of field display a list of checkboxes. It works only with m2ms. This field will display one checkbox for each record existing in the model targeted by the relation, according to the given domain if one is specified. Checked records will be added to the relation.

There's no way to use this widget to create new items.

![many2many_tags widget](/assets/images/many2many_checkboxes_widget.png)

**Example**
{% highlight xml %}
<field name="field_name" widget="many2many_checkboxes"/>
{% endhighlight %}

---

## `many2many_kanban` widget
The `many2many_kanban` widget uses a Kanban View to display a list of related objects.

This widget can have widely different look and feel, depending on the Kanban View it uses. Here's a screenshot from the `project` module:

![many2many_kanban widget](/assets/images/many2many_kanban_widget.png)

**Example**
{% highlight xml %}
<field name="field_name" widget="many2many_kanban">
    <kanban>
        <field name="name"/>
        <templates>
            <t t-name="kanban-box">
                <field name="name"/>
            </t>
        </templates>
    </kanban>
</field>
{% endhighlight %}
---

## `x2many_counter` widget

A simple, read only widget displaying a link with an information about the number of related items. The link's target view can be configured with the `views` option.

Also useful with `one2many` fields.

![x2many_counter widget](/assets/images/x2many_counter_widget.png)

**Options**

*   `views` - According to a comment in Odoo's source code:

    > The views to display in the act_window action. Must be a list of tuple whose first element is the id of the view to display (or `False` to take the default one) and the second element is the type of the view. Defaults to `[[false, "tree"], [false, "form"]]`.

**Example**
{% highlight xml %}
<field name="field_name" widget="x2many_counter" string="things"/>
{% endhighlight %}

---

## `many2many_binary` widget

According to a comment in Odoo's source code:

> Widget for (many2many field) to upload one or more file in same time and display in list. The user can delete his files.

This widget works **exclusively** on `many2many` fields associated with the `ir.attachment` model.

![many2many_binary kanban](/assets/images/many2many_binary_widget.png)

**Example**
{% highlight xml %}
<field name="field_name" widget="many2many_binary" string="Attach a file"/>
{% endhighlight %}
