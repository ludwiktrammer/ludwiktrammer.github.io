---
layout: post
title: Changing SQL constraints in inheriting models in Odoo 8
categories: [odoo]
---
In OpenERP 7 there was an established pattern that allowed creators of inheriting model classes to modify sql constrained defined in a parent class. You just needed to define a new constraint with the same name (the technique is for example [described in this forum thread][thread]).

In Odoo 8 this no longer works. If you define a constrain with the same name as the one in parent model, Odoo tries to use them both, but the parent's constraints are executed later, so child constraint is immediately overwritten by the parent constraint. I created [a ticket in Odoo's bug tracker about the issue][bug], but for now you can use a simple workaround.

The workaround consists of changing the list of SQL constraints in `_auto_init` method on model class. This will run after Odoo already performed its (faulty?) merge of constraints from all parents and children, but before it sends them to the database.

**Example 1**
{% highlight python %}
class Foo(models.Model):
    _inherit = 'my.foo'

    # ...

    def _auto_init(self, cr, context=None):
        self._sql_constraints = [
            ('name_uniq', 'unique(name)', 'Two foos with the same name? Impossible!')
        ]
        super(Foo, self)._auto_init(cr, context)
{% endhighlight %}

Alternatively you could try to use the pattern that worked in OpenERP 7 and just reverse the `_sql_constraints` list to fix the order in which they are sent to the database:

**Example 2**
{% highlight python %}
class Foo(models.Model):
    _inherit = 'my.foo'

    # ...

    _sql_constraints = [
        ('name_uniq', 'unique(name)', 'Two foos with the same name? Impossible!')
    ]

    def _auto_init(self, cr, context=None):
        self._sql_constraints = self._sql_constraints.reverse()
        super(Foo, self)._auto_init(cr, context)
{% endhighlight %}

Please note that those examples are not equivalent. In the first example parent's list of constraints will be *replaced* by the new list. In the second example those two list will be *merged*.

[thread]: https://www.odoo.com/forum/help-1/question/remove-sql-constraints-5431
[bug]: https://github.com/odoo/odoo/issues/3957
