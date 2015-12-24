# sales_by_shipment_date

Report shows the sales by shipment date as that is the moment used to book the order into the administration.
Sales are without tax, shipment cost but including discount

authors: 
Sander Mangel [sander@sandermangel.nl](mailto:sander@sandermangel.nl)

## SQL
```sql
SELECT 
DATE(`s`.`created_at`) AS `created_at`
, SUM(
	`i`.`base_grand_total`
	- IFNULL(`i`.`base_tax_amount`, 0)
	- IFNULL(`i`.`base_discount_amount`, 0)
	- IFNULL(`i`.`base_shipping_amount`, 0)
	+ IFNULL(`i`.`base_discount_amount`, 0)
) AS `total`
, COUNT(`o`.`entity_id`) AS `nr_of_orders`
, `o`.`store_id` AS `store_id`
, GROUP_CONCAT(`o`.`increment_id`) AS `orders`

FROM `sales_flat_order` AS `o`
LEFT JOIN `sales_flat_shipment` AS `s`
ON `s`.`order_id` = `o`.`entity_id`
LEFT JOIN `sales_flat_invoice` AS `i`
ON `i`.`order_id` = `o`.`entity_id`

GROUP BY YEAR(`s`.`created_at`)
, MONTH(`s`.`created_at`)
, DAY(`s`.`created_at`)
, `o`.`store_id`
ORDER BY `s`.`created_at` DESC
```

## Output Type
Plain Table

## Chart Configuration
n/a

## Grid Configuration
```javascript
{
"filterable": {
"created_at": "adminhtml/widget_grid_column_filter_date",
"store_id": "adminhtml/widget_grid_column_filter_store"
}
}
```
