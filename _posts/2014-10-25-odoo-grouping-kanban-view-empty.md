---
layout: post
title: Displaying a full list of groups in Odoo's Kanban view
categories: [odoo]
---
Kanban view is probably the most flexible view in Odoo. It can be used for many different purposes. One of the most common ones is splitting items into distinct groups (represented by a row of columns) and allowing users to drag items between those groups. For example the `hr_reqruitment` module lets users to manage applications this way.

It's trivial and [well documented][docs] how to group objects in Kanban. Just add `default_group_by` attribute to the `<kanban>` element:

{% highlight xml %}
<kanban default_group_by="company_id">
{% endhighlight %}

## Including empty groups
There is however a potential problem. Columns representing groups without any items will not be included. This means users won't be able to move items to those absent groups, which is probably not what we intended.

Oddo has an answer for this ready - an optional model attribute called `_group_by_full`. It should be a dictionary, mapping field names (of the fields you use for grouping) to methods returning information about all available groups for those fields.

{% highlight python %}
class Store(models.Model):
    @api.model
    def company_groups(self, present_ids, domain, **kwargs):
        companies = self.env['res.company'].search([]).name_get()
        return companies, None

    _name = 'store'
    _group_by_full = {
        'company_id': company_groups,
    }

    name = fields.Char()
    company_id = fields.Many2many('res.company')
{% endhighlight %}

The code above ensures that when displaying `store` objects grouped by `company_id`, all available companies will be represented (and not only those already having stores).

Methods listed in `_group_by_full` need to return a two element tuple:

*   First element: a list of two element tuples, representing individual groups. Every tuple in the list need to include the particular group's value (in our example: id of a particular company) and a user friendly name for the group (in our example: company's name). That's why we can use the `name_get` method, since it returns a list of `(object id, object name)` tuples.

*   Second element: a dictionary mapping groups' values to a boolean value, indicating whether the group should be folded in Kanban view. Not including a group in this dictionary has the same meaning as mapping it to `False`.

    For example this version of `company_groups` method would make group representing a company with id `1` folded in Kanban view:

{% highlight python %}
@api.model
def company_groups(self, present_ids, domain, **kwargs):
    companies = self.env['res.company'].search([]).name_get()
    folded = {1: True}
    return companies, folded
{% endhighlight %}

## Grouping by fields other than `many2one`
There seem to be problem in Odoo 8.0 preventing use of `_group_by_full` with fields other than `many2one`. I got around the issue extending the `_read_group_fill_results` method. Here is an example of grouping by the `state` field:

{% highlight python %}
class Store(models.Model):
    _name = 'store'
    STATES = [
        ('good', 'Good Store'),
        ('bad', 'Bad Store'),
        ('ugly', 'Ugly Store'),
    ]

    # States that should be folded in Kanban view
    # used by the `state_groups` method
    FOLDED_STATES = [
        'ugly',
    ]

    @api.model
    def state_groups(self, present_ids, domain, **kwargs):
        folded = {key: (key in self.FOLDED_STATES) for key, _ in self.STATES}
        # Need to copy self.STATES list before returning it,
        # because odoo modifies the list it gets,
        # emptying it in the process. Bad odoo!
        return self.STATES[:], folded

    _group_by_full = {
        'state': state_groups
    }

    name = fields.Char()
    state = fields.Selection(STATES, default='good')

    def _read_group_fill_results(self, cr, uid, domain, groupby,
                                 remaining_groupbys, aggregated_fields,
                                 count_field, read_group_result,
                                 read_group_order=None, context=None):
        """
        The method seems to support grouping using m2o fields only,
        while we want to group by a simple status field.
        Hence the code below - it replaces simple status values
        with (value, name) tuples.
        """
        if groupby == 'state':
            STATES_DICT = dict(self.STATES)
            for result in read_group_result:
                state = result['state']
                result['state'] = (state, STATES_DICT.get(state))

        return super(Store, self)._read_group_fill_results(
            cr, uid, domain, groupby, remaining_groupbys, aggregated_fields,
            count_field, read_group_result, read_group_order, context
        )
{% endhighlight %}

[docs]: https://www.odoo.com/documentation/8.0/reference/views.html#kanban
