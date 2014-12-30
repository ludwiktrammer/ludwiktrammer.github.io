---
layout: post
title: Showing a menu badge in Odoo
categories: [odoo]
---

Odoo allows programmers to display badges with numeric values next to menu items:

![menu badge](/assets/images/menu_badge.png)

To use this functionality, you need to configure your model. Odoo provides an `ir.needaction_mixin` for you to inherit from:

{% highlight python %}
_inherit = ['ir.needaction_mixin']
{% endhighlight %}

Then you need to define a method called `_needaction_domain_get`. It should return [a domain][domains] selecting those objects that should be counted towards the number on the badge.

{% highlight python %}
@api.model
def _needaction_domain_get(self):
    return [('state', '=', 'new')]
{% endhighlight %}

## Complete example
{% highlight python %}
class Horse(models.Model):
    _name = 'horse'
    _inherit = ['ir.needaction_mixin']

    STATES = [
        ('healthy', "Healthy horse"),
        ('sick', "Sick horse"),
    ]

    name = fields.Char(required=True)
    state = fields.Selection(STATES, default='healthy')

    @api.model
    def _needaction_domain_get(self):
        """
        Show a count of sick horses on the menu badge.
        An exception: don't show the count to Bob,
        because he worries too much!
        """
        if self.env.user.name == "Bob":
            return False  # don't show to Bob!
        return [('state', '=', 'sick')]
{% endhighlight %}

## More control
Instead of defining `_needaction_domain_get` method you could alternatively define a `_needaction_count` method and return a number of your choice directly:

{% highlight python %}
@api.model
def _needaction_count(self, domain=None):
    return 12
{% endhighlight %}

Another example, making use of the `domain` argument:

{% highlight python %}
@api.model
def _needaction_count(self, domain=None):
    """
    Count all objects in a view, deducting a dozen
    before displaying on the badge
    (we don't want to alarm people with big numbers)
    """
    return self.search_count(domain or []) - 12
{% endhighlight %}


[domains]: https://www.odoo.com/documentation/8.0/reference/orm.html#domains
