---
layout: post
title: Catching SQL constraints violations in Odoo controllers
categories: [odoo]
---

It's easy to add an SQL constraint to Odoo model - just use the [`_sql_constraints`][doc] list. Odoo Forms handle those constraints automatically. When you write your own controllers, you need to handle them by hand.

Let's say with got this simple model:
{% highlight python %}
class FooBar(models.Model):
    _name = 'my.foobar'

    # ...

    _sql_constraints = [
        ('foo_bar_uniq', 'unique("foo", "bar")', 'You could not step twice into the same foobar!')
    ]
{% endhighlight %}

When the constraint is violated, a `psycopg2.IntegrityError` exception is thrown. You need to catch the exception and handle the problem. And here comes the tricky part - you will most likely encounter an `InternalError`:

    InternalError: current transaction is aborted, commands ignored until end of transaction block.

This happens because after an error PostgreSQL will not allow subsequent queries in the same transaction. You need to end the transaction first, by either committing or rolling back.

Here's a full example:

{% highlight python %}
class FooBarController(http.Controller):
    @http.route('/foobar/create/', auth='public', website=True)
    def create(self, foo, bar):
        try:
            http.request.env['my.foobar'].create({
                'foo': foo,
                'bar': bar,
            })
            return http.request.render('my.thank_you_page')
        except IntegrityError:
            # can't use the usual `http.request.env.cr` style,
            # because `env` queries db and everything explodes
            http.request._cr.rollback()
            return http.request.render('my.error_page')
{% endhighlight %}

[doc]: https://www.odoo.com/documentation/8.0/reference/orm.html#openerp.models.Model._sql_constraints
