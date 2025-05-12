To integrate this POST API in **Odoo Manufacturing**, store the returned data into models, and display it in views, you can follow this step-by-step approach:

---

### ✅ **Step 1: Create Custom Odoo Module**

Create a custom module, e.g., `planning_order`.

```bash
odoo-bin scaffold planning_order addons
```

---

### ✅ **Step 2: Define Models**

Define 3 models to store:

* `directListOrders`
* `validSolutions`
* `ordersNotHaveSolutions`

**`models/planning_order.py`**:

```python
from odoo import models, fields, api
import requests
import json

class DirectOrder(models.Model):
    _name = 'planning.direct.order'
    _description = 'Direct Planning Order'

    order_id = fields.Integer(string="Order ID")
    width = fields.Float()
    length = fields.Float()
    quantity = fields.Integer()
    ribbing_type = fields.Char()
    total_waste = fields.Float()
    bestItemPerSheets = fields.Float()
    bestCuts = fields.Float()
    best_size = fields.Integer()
    size_used = fields.Float()
    solution_weight_kg = fields.Float()
    best_waste_kg = fields.Float()


class ValidSolution(models.Model):
    _name = 'planning.valid.solution'
    _description = 'Valid Solution'

    order1_id = fields.Integer()
    order2_id = fields.Integer()
    produced_quantity_1 = fields.Integer()
    produced_quantity_2 = fields.Integer()
    size_used = fields.Float()
    waste_percentage = fields.Float()
    best_waste_kg = fields.Float()
    solution_weight_kg = fields.Float()


class OrderWithoutSolution(models.Model):
    _name = 'planning.order.without.solution'
    _description = 'Order Without Solution'

    order_id = fields.Integer()
    width = fields.Float()
    length = fields.Float()
    quantity = fields.Integer()
    ribbing_type = fields.Char()
    total_waste = fields.Float()
```

---

### ✅ **Step 3: Add Backend Logic to Call API**

Add a server action or wizard that makes the **POST** request and stores the results.

**Add this to a wizard** (`wizards/planning_fetch_wizard.py`):

```python
class PlanningFetchWizard(models.TransientModel):
    _name = 'planning.fetch.wizard'
    _description = 'Fetch Planning Data Wizard'

    def fetch_and_store_planning_orders(self):
        url = "https://your-api.com/api/planning-orders"
        headers = {
            "Content-Type": "application/json"
        }

        payload = {
            "orders": [
                {
                    "id": 10,
                    "client": "Client A",
                    "category": "Category 1",
                    "quantity": 440000,
                    "width": 28.8,
                    "length": 94.6,
                    "high_priority": True,
                    "finished": "false",
                    "starch": "processed",
                    "ribbing_type": "c",
                    "created_at": "2025-05-07T10:59:47.000000Z",
                    "updated_at": "2025-05-07T10:59:47.000000Z",
                    "materials": []
                }
            ],
            "planning_configuration": {
                "static-trim": 3.0,
                "max-solution-order-quantity": 25000,
                "min-waste-percentage": 0.03,
                "reel-sizes": [180, 185, 190, 195, 200, 205, 210],
                "maxItemPerSheet": 6.0
            }
        }

        response = requests.post(url, headers=headers, json=payload)

        if response.status_code == 201:
            data = response.json()

            # Clear old records
            self.env['planning.direct.order'].sudo().search([]).unlink()
            self.env['planning.valid.solution'].sudo().search([]).unlink()
            self.env['planning.order.without.solution'].sudo().search([]).unlink()

            for order in data.get("directListOrders", []):
                self.env['planning.direct.order'].create({
                    'order_id': order['id'],
                    'width': order['width'],
                    'length': order['length'],
                    'quantity': order['quantity'],
                    'ribbing_type': order['ribbing_type'],
                    'total_waste': order['total_waste'],
                    'bestItemPerSheets': order['bestItemPerSheets'],
                    'bestCuts': order['bestCuts'],
                    'best_size': order['best_size'],
                    'size_used': order['size_used'],
                    'solution_weight_kg': order['solution_weight_kg'],
                    'best_waste_kg': order['best_waste_kg']
                })

            for solution in data.get("validSolutions", []):
                self.env['planning.valid.solution'].create({
                    'order1_id': solution['order1_id'],
                    'order2_id': solution['order2_id'],
                    'produced_quantity_1': solution['produced_quantity_1'],
                    'produced_quantity_2': solution['produced_quantity_2'],
                    'size_used': solution['size_used'],
                    'waste_percentage': solution['waste_percentage'],
                    'best_waste_kg': solution['best_waste_kg'],
                    'solution_weight_kg': solution['solution_weight_kg']
                })

            for order in data.get("ordersNotHaveSolutions", []):
                self.env['planning.order.without.solution'].create({
                    'order_id': order['id'],
                    'width': order['width'],
                    'length': order['length'],
                    'quantity': order['quantity'],
                    'ribbing_type': order['ribbing_type'],
                    'total_waste': order['total_waste']
                })

        else:
            raise UserError(f"Failed to fetch data: {response.status_code} - {response.text}")
```

---

### ✅ **Step 4: Add Views (Tree & Form)**

**Add XML views** to show the data under the Manufacturing module menu.

```xml
<record id="view_planning_direct_order_tree" model="ir.ui.view">
    <field name="name">planning.direct.order.tree</field>
    <field name="model">planning.direct.order</field>
    <field name="arch" type="xml">
        <tree>
            <field name="order_id"/>
            <field name="width"/>
            <field name="length"/>
            <field name="quantity"/>
            <field name="total_waste"/>
        </tree>
    </field>
</record>

<record id="action_planning_direct_order" model="ir.actions.act_window">
    <field name="name">Direct Planning Orders</field>
    <field name="res_model">planning.direct.order</field>
    <field name="view_mode">tree,form</field>
</record>

<menuitem id="menu_planning_root" name="Planning Orders" parent="mrp.menu_mrp_root"/>
<menuitem id="menu_planning_direct_order" name="Direct Orders" parent="menu_planning_root" action="action_planning_direct_order"/>
```

---

### ✅ **Step 5: Add Button to Trigger Wizard**

You can add a button in the backend to run `fetch_and_store_planning_orders()` manually, or automate it via cron.

