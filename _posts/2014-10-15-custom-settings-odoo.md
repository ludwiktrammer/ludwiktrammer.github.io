---
layout: post
title: Defining custom settings in Odoo
categories: [odoo]
---

Unfortunately Odoo documentation doesn’t seem to include any information about adding new configuration options to Odoo. So let’s fill in the gaps.

# Defining a model

First of all, you need to define a new model inheriting from `res.config.settings`:

{% highlight python %}
class YourSettings(models.TransientModel):
    _inherit = 'res.config.settings'
    _name = 'your.config.settings'
{% endhighlight %}

It’s a `TransientModel`, also known as a wizard. Do not expect it to permanently hold the values. TransientModels inherently store values on a temporary basis only. You need other means to make them permanent.

Fortunately `res.config.settings` makes this easy. First of all, you need to add some fields to your `TransientModel` - one for every setting option you want to define. Odoo comes with built-in support for four different kinds of settings. It distinguishes between them based on the field names.

**“Default” settings**<br/>
The value of a field named `default_foo` will be set as a *default value* for a field named `foo` on a model given as a `default_model` argument.

{% highlight python %}
class YourSettings(models.TransientModel):
    _inherit = 'res.config.settings'
    _name = 'your.config.settings'

    default_name = fields.Char(default_model='your.other.model')
{% endhighlight %}

This will make the value of `default_name` field the global default value of a field `name` in model `your.other.model`.

**“Group” settings**<br/>
Boolean fields named `group_foo` take two arguments: `group` (defaults to `base.group_user`) and `implied_group`. If the value of such field is true, the group defined in `group` gain all `implied_group`'s permissions. This is exactly the same as adding a group to `implied_ids` field on another group's object (which as far as I know is also an undocumented feature). This is useful for controlling which groups of users have access to a feature.

{% highlight python %}
class YourSettings(models.TransientModel):
    _inherit = 'res.config.settings'
    _name = 'your.config.settings'

    group_kill = fields.Boolean(
        group='your.secret_agents',
        implied_group='your.licence_to_kill'
    )
{% endhighlight %}

**“Module” settings**<br/>
Boolean fields named `module_foo`, when enabled will trigger installation of a module named `foo`.

{% highlight python %}
class YourSettings(models.TransientModel):
    _inherit = 'res.config.settings'
    _name = 'your.config.settings'

    # if enabled will install "spies" module
    module_spies = fields.Boolean()
{% endhighlight %}

**Other settings**<br/>
By default the values of other fields will be discarded, but you change that by implementing your own means of saving them. Just define a method named `set_foo` (where `foo` is an arbitrary string). You can also set initial values of such fields using a `get_default_foo` method (the exact form of `foo` is once again irrelevant).

For example if you want to use settings to control the name and phone number of a company linked to the current user:

{% highlight python %}
class YourSettings(models.TransientModel):
    _inherit = 'res.config.settings'
    _name = 'your.config.settings'

    company_name = fields.Char()
    company_phone = fields.Char()

    @api.model
    def get_default_company_values(self, fields):
    """
    Method argument "fields" is a list of names
    of all available fields.
    """
        company = self.env.user.company_id
        return {
            'company_name': company.name,
            'company_phone': company.phone,
        }

    @api.one
    def set_company_values(self):
        company = self.env.user.company_id
        company.name = self.company_name
        company.phone = self.company_phone
{% endhighlight %}

# Defining a view
Then you just need to define a view for your settings. Let's use the previous example:

{% highlight xml %}
<record id="your_configuration" model="ir.ui.view">
    <field name="name">Your configuration</field>
    <field name="model">your.config.settings</field>
    <field name="arch" type="xml">
        <form string="Your configuration" class="oe_form_configuration">
            <header>
                <button string="Save" type="object"
                    name="execute" class="oe_highlight"/>
                or
                <button string="Cancel" type="object"
                    name="cancel" class="oe_link"/>
            </header>
            <group string="Company">
                <label for="id" string="Name &amp; Phone"/>
                <div>
                    <div>
                        <label for="company_name"/>
                        <field name="company_name"/>
                    </div>
                    <div>
                        <label for="company_phone"/>
                        <field name="company_phone"/>
                    </div>
                </div>
            </group>
        </form>
    </field>
</record>
{% endhighlight %}

{% highlight xml %}
<record id="your_settings_action" model="ir.actions.act_window">
    <field name="name">Your configuration</field>
    <field name="res_model">your.config.settings</field>
    <field name="view_id" ref="your_configuration"/>
    <field name="view_mode">form</field>
    <field name="target">inline</field>
</record>
{% endhighlight %}

...and of course don't forget to make a new entry in the settings menu:

{% highlight xml %}
<menuitem id="your_settings_menu" name="Your settings"
    parent="base.menu_config" action="your_settings_action"/>
{% endhighlight %}
