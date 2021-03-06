DIBS Payment Window Module for Magento
==

Implementation of the DIBS Payment Window gateway &amp; API solution.

Features
--

* HMAC security calculation
* Direct payment capture
* DIBS Payment Type selection
* Callback URL locking to prevent recording parallel payments
* Follows Magento's transaction API, and supports:
	* Per-transaction debug information
	* Partial captures of authorizations
	* Partial refunds of captured invoices
	* Payment void
	* Re-authorization of previously expired authorizations

Migrating from another DIBS module
--

This covers the easiest way to migrate from another DIBS module to this one. The migration doesn't cover the API cases that newer modules include, meaning pending authorizations made with another module needs to be captured from within the DIBS administration interface after deployment of this module.

Example migration script:

```php
$this->startSetup();

$connection = $this->getConnection();
$connection->delete($this->getTable('core_config_data'), 'path LIKE "payment/dibs%"');

$connection->update($this->getTable('sales_flat_order_payment'), array(
    'method' => 'made_dibs_gateway'
), "method LIKE 'dibs%'");

$this->endSetup();

Mage::getConfig()->reinit();
Mage::app()->reinitStores();
```

After the above script has run it should be safe to remove/deactivate the other module.

**CAUTION!** When you run this migration script you risk losing references to old DIBS transactions that other modules might use. The real solution to this problem is creating a legacy model that handles the old data, or simply not running this script, keeping the old module and disabling it from the Magento admin interface.

Known Limitations
--
* Direct bank payments sometimes return with status "PENDING". The final status of these payments can't be determined via the Payment Window API but must be manually checked in the DIBS administration interface.
* The way Magento calculates and rounds numbers differs from the way DIBS does it. Also, Payment Window only supports two decimals in amounts as well as VAT percent, meaning there is no reliable way to leave the total calculation to either part. Because of this, the implementation doesn't send the complete order information to DIBS if the Magento grand total differs from the individual order row information, allowing all orders to be passed through without rounding issues.

License
--
This project is licensed under the 4-clause BSD License, see [LICENSE](https://github.com/madepeople/Made_Dibs/blob/master/LICENSE)
