# adyen-magento-subscription module subscriptions next 6 months

This projects the orders and it's products for the next six months for the 
[Adyen Magento Subscription module](https://github.com/Adyen/adyen-magento-subscription)

## SQL
```mysql
SELECT
	DATE_FORMAT(`as`.`scheduled_at`,'%Y-%c-%d ') AS `date_scheduled`
	, `o`.`customer_email`
	, `as`.`entity_id` AS `subscription id`
	, `c`.`increment_id` AS `customer increment id`
	, `o`.`increment_id` AS `order incrementid`
	, (SELECT SUM(`asi`.`price` * `asi`.`qty`) FROM `adyen_subscription_item` AS `asi` WHERE `asi`.`subscription_id` = `as`.`entity_id`) AS `subtotal_ex_vat`
	, `s`.`code` AS `store`
	, GROUP_CONCAT(`oi`.`sku` SEPARATOR ',') AS `product SKUs`
FROM 
	`adyen_subscription` AS `as`
LEFT JOIN
	`sales_flat_order` AS `o`
ON
	`o`.`entity_id` = `as`.`order_id`
LEFT JOIN
	`adyen_subscription_item` AS `oi`
ON
	`as`.`entity_id` = `oi`.`subscription_id`
LEFT JOIN
	`customer_entity` AS `c`
ON
	`o`.`customer_id` = `c`.`entity_id`
LEFT JOIN
	`core_store` AS `s`
ON
	`as`.`store_id` = `s`.`store_id`
WHERE
	(
		`as`.`scheduled_at` >= now()
		AND `as`.`scheduled_at` <= DATE_ADD(now(), INTERVAL 6 MONTH)
	) 
-- 	AND `as`.`status` = 'active'
GROUP BY 
	`oi`.`subscription_id`
ORDER BY 
	`as`.`scheduled_at` ASC
```

## Output Type
Plain Table

## Chart Configuration
n/a

## Grid Configuration
n/a
