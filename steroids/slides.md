layout: false
class: center, middle, inverse

# odoo testing on steroids

<!--
Todo:
-->

---
class: inverse
# About me

## Leonardo Pistone
* Developer @ Camptocamp
* OCA committer & delegate member
* @lepistone

---
# unittest (isolated)

```python
class TestUnitCheck(TransactionCase):

    def test_over_budget(self):
        order = self.env['sale.order'].new({
            'total_budget': 80.0,
            'amount_total': 100.0,
        })
        self.assertTrue(order.over_budget())
```
---
# unittest (integration)

```python
class TestItBlocks(TransactionCase):
    def test_it_can_block(self):
        self.order.order_line.budget_tot_price = 80.0
        self.order.order_line.price_unit = 100.0

        result = self.order.action_button_confirm()

        self.assertEqual('draft', self.order.state)
        exceptions = self.wizard_model.browse(result['res_id']).exception_ids
        self.assertEqual(2, len(exceptions))
        self.assertIn("is over the total budget", exceptions[0].description)
        self.assertIn("has not validated", exceptions[1].description)
```

---

# YAML

```YAML
-
  I create a quotation with a dropshipping line.
-
  !record {model: sale.order, id: so_4}:
    partner_id: base.res_partner_3
    order_line:
      - product_id: product.product_product_7
        product_uom_qty: 8
        route_id: route_drop_shipping
-
  I confirm the sale order, run the scheduler, and check that the address of
  the sale order has been propagated to the automatically generated purchase
  order.
-
  !python {model: sale.order, id: so_4}: |
    from nose.tools import *

    self.action_button_confirm()
    self.env['procurement.order'].run_scheduler()
    proc = self.order_line[0].procurement_ids
    assert_equal(
        proc.purchase_id.dest_address_id.id,
        ref('base.res_partner_3'),
    )
```
---
# OERPScenario

```cucumber
Feature: Invoice workflow

  Scenario: Validation of an invoice
    Given I entered a supplier invoice for 1000 EUR
    When I validate the invoice
    Then the state of the invoice is "open"
```
