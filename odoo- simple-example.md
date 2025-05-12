Here’s a **simple example** for an **Odoo beginner developer** to integrate the DummyJSON Quotes API (`https://dummyjson.com/quotes`) and display the quotes in a custom model and view. Follow these steps:

---

### ✅ Step 1: Create a Custom Module

Let’s call the module: `quotes_api`

#### File Structure:

```
quotes_api/
├── __init__.py
├── __manifest__.py
├── models/
│   ├── __init__.py
│   └── quotes.py
├── views/
│   └── quotes_views.xml
```

---

### ✅ Step 2: `__manifest__.py`

```python
{
    'name': 'Quotes API Integration',
    'version': '1.0',
    'depends': ['base'],
    'author': 'Your Name',
    'category': 'Tools',
    'description': 'Fetch and display quotes from external API',
    'installable': True,
    'auto_install': False,
    'data': [
        'views/quotes_views.xml',
    ],
}
```

---

### ✅ Step 3: Create the Model

`models/__init__.py`:

```python
from . import quotes
```

`models/quotes.py`:

```python
from odoo import models, fields, api
import requests

class QuotesAPI(models.Model):
    _name = 'quotes.api'
    _description = 'Quotes from external API'

    quote_id = fields.Integer('Quote ID')
    quote = fields.Text('Quote')
    author = fields.Char('Author')

    @api.model
    def fetch_quotes(self):
        url = 'https://dummyjson.com/quotes'
        response = requests.get(url)
        if response.status_code == 200:
            data = response.json()
            for item in data.get('quotes', []):
                # Avoid duplicates
                existing = self.search([('quote_id', '=', item['id'])])
                if not existing:
                    self.create({
                        'quote_id': item['id'],
                        'quote': item['quote'],
                        'author': item['author'],
                    })
```

---

### ✅ Step 4: Create the View

`views/quotes_views.xml`:

```xml
<odoo>
    <record id="view_quotes_tree" model="ir.ui.view">
        <field name="name">quotes.api.tree</field>
        <field name="model">quotes.api</field>
        <field name="arch" type="xml">
            <tree>
                <field name="quote_id"/>
                <field name="author"/>
                <field name="quote"/>
            </tree>
        </field>
    </record>

    <record id="view_quotes_action" model="ir.actions.act_window">
        <field name="name">Quotes</field>
        <field name="res_model">quotes.api</field>
        <field name="view_mode">tree</field>
    </record>

    <menuitem id="menu_quotes_root" name="Quotes API"/>
    <menuitem id="menu_quotes_main" name="Quotes" parent="menu_quotes_root" action="view_quotes_action"/>
</odoo>
```

---

### ✅ Step 5: Install the Module

1. Zip or place the `quotes_api` folder inside your Odoo `addons` directory.
2. Restart the Odoo server.
3. Activate **Developer Mode**.
4. Go to **Apps** → Update Apps List → Search for "Quotes API" → Install it.

---

### ✅ Step 6: Fetch Quotes

* Open Odoo > Go to the Quotes menu you created.
* Quotes won’t show automatically until you call `fetch_quotes()` method.

#### You can run it manually from the Odoo shell:

```bash
$ odoo shell
>>> env['quotes.api'].fetch_quotes()
```

Let's complete the example by adding a **button in the UI** that will allow you to fetch quotes from the external API with a single click.

---

## ✅ Step 7: Update Your Model to Add a Button Method

In `models/quotes.py`, update the model like this:

```python
from odoo import models, fields, api
import requests

class QuotesAPI(models.Model):
    _name = 'quotes.api'
    _description = 'Quotes from external API'

    quote_id = fields.Integer('Quote ID')
    quote = fields.Text('Quote')
    author = fields.Char('Author')

    @api.model
    def fetch_quotes(self):
        url = 'https://dummyjson.com/quotes'
        response = requests.get(url)
        if response.status_code == 200:
            data = response.json()
            for item in data.get('quotes', []):
                existing = self.search([('quote_id', '=', item['id'])])
                if not existing:
                    self.create({
                        'quote_id': item['id'],
                        'quote': item['quote'],
                        'author': item['author'],
                    })

    def button_fetch_quotes(self):
        self.fetch_quotes()
        return {
            'type': 'ir.actions.client',
            'tag': 'display_notification',
            'params': {
                'title': 'Quotes Fetched',
                'message': 'Successfully fetched quotes from API.',
                'sticky': False,
            }
        }
```

---

## ✅ Step 8: Add a Form View and the Fetch Button

Update your `views/quotes_views.xml` file:

```xml
<odoo>
    <!-- Tree View -->
    <record id="view_quotes_tree" model="ir.ui.view">
        <field name="name">quotes.api.tree</field>
        <field name="model">quotes.api</field>
        <field name="arch" type="xml">
            <tree>
                <field name="quote_id"/>
                <field name="author"/>
                <field name="quote"/>
            </tree>
        </field>
    </record>

    <!-- Form View (to allow button access) -->
    <record id="view_quotes_form" model="ir.ui.view">
        <field name="name">quotes.api.form</field>
        <field name="model">quotes.api</field>
        <field name="arch" type="xml">
            <form>
                <header>
                    <button name="button_fetch_quotes" type="object" string="Fetch Quotes" class="btn-primary"/>
                </header>
                <sheet>
                    <group>
                        <field name="quote_id"/>
                        <field name="author"/>
                        <field name="quote"/>
                    </group>
                </sheet>
            </form>
        </field>
    </record>

    <!-- Action -->
    <record id="view_quotes_action" model="ir.actions.act_window">
        <field name="name">Quotes</field>
        <field name="res_model">quotes.api</field>
        <field name="view_mode">tree,form</field>
    </record>

    <!-- Menus -->
    <menuitem id="menu_quotes_root" name="Quotes API"/>
    <menuitem id="menu_quotes_main" name="Quotes" parent="menu_quotes_root" action="view_quotes_action"/>
</odoo>
```

---

## ✅ Step 9: Restart Odoo and Upgrade the Module

Run these commands:

```bash
$ ./odoo-bin -u quotes_api -d your_database_name
```

Or from the Odoo UI:

* Enable Developer Mode
* Go to **Apps**
* Search for "Quotes API"
* Click **Upgrade**

---

## ✅ Step 10: Test It in the UI

1. Open the Quotes API menu from the top menu bar.
2. Open any record or create a new one.
3. Click the **“Fetch Quotes”** button at the top.
4. You should see a popup notification and the list view will populate with quotes from the external API.

---


