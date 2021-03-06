# Catalog

Whatever could be useful for Mage Catalog and it's known childs can be found here.

## Product

### Return product attributes

```php
    <?php
    $_product->getThisattribute();
    $_product->getAttributeText('thisattribute');
    $_product->getResource()->getAttribute('thisattribute')->getFrontend()->getValue($_product);
    $_product->getData('thisattribute');

    // The following returns the option IDs for an attribute that is a multiple-select field:
    $_product->getData('color'); // i.e. 456,499

    // The following returns the attribute object, and instance of Mage_Catalog_Model_Resource_Eav_Attribute:
    $_product->getResource()->getAttribute('color'); // instance of Mage_Catalog_Model_Resource_Eav_Attribute

    // The following returns an array of the text values for the attribute:
    $_product->getAttributeText('color') // Array([0]=>'red', [1]=>'green')

    // The following returns the text for the attribute
    if ($attr = $_product->getResource()->getAttribute('color')):
        echo $attr->getFrontend()->getValue($_product); // will display: red, green
    endif;
?>
```


### Get configurable products child products

```php
    <?php

    // input is $_product and result is iterating child products
    $childProducts = Mage::getModel('catalog/product_type_configurable')->getUsedProducts(null, $product);

    ?>
```

### Get configurable products children (simple product) custom attributes

```php
    <?php

    // input is $_product and result is iterating child products
    $conf = Mage::getModel('catalog/product_type_configurable')->setProduct($_product);
    $col = $conf->getUsedProductCollection()->addAttributeToSelect('*')->addFilterByRequiredOptions();

    foreach($col as $simple_product)
    {
        var_dump($simple_product->getId());
    }

    ?>
```

### Get associated products

In /app/design/frontend/default/site/template/catalog/product/view/type/

``` php
    <?php $_helper = $this->helper('catalog/output'); ?>
    <?php $_associatedProducts = $this->getAllowProducts() ?>
    <?php //var_dump($_associatedProducts); ?>
    <br />
    <br />
    <?php if (count($_associatedProducts)): ?>
        <?php foreach ($_associatedProducts as $_item): ?>
            <a href="<?php echo $_item->getProductUrl() ?>"><?php echo $_helper->productAttribute($_item, $_item->getName(), 'name') ?> | <?php echo $_item->getName() ?> | <?php echo $_item->getPrice() ?></a>
            <br />
            <br />
        <?php endforeach; ?>
    <?php endif; ?>
```

### Get simple products of a configurable product

```php
    <?php

    if($_product->getTypeId() == "configurable") {
        $ids = $_product->getTypeInstance()->getUsedProductIds();
    ?>
    <ul>
        <?php
        foreach ($ids as $id) {
            $simpleproduct = Mage::getModel('catalog/product')->load($id);
        ?>
            <li>
                <?php
                echo $simpleproduct->getName() . " - " . (int)Mage::getModel('cataloginventory/stock_item')->loadByProduct($simpleproduct)->getQty();
                ?>
            </li>
        <?php
        }
        ?>
    </ul>
    <?php
    }

    ?>
```


### Getting Configurable Product from Simple Product ID in Magento 1.5+

```php
    <?php

    $simpleProductId = 465;
    $parentIds = Mage::getResourceSingleton('catalog/product_type_configurable')
        ->getParentIdsByChild($simpleProductId);
    $product = Mage::getModel('catalog/product')->load($parentIds[0]);
    echo $product->getId(); // ID = 462 (aka, Parent of 465)

    ?>
```

## Get the current product

```
<?php

$currentProduct = Mage::registry('current_product');

?>
```

### Is product purchasable?

```php
<?php if($_product->isSaleable()) { // do stuff } ?>
```

### Load Products by Category ID

```php
<?php

$_category = Mage::getModel('catalog/category')->load(47);
$_productCollection = $_category->getProductCollection();
if($_productCollection->count()) {
	foreach( $_productCollection as $_product ):
		echo $_product->getProductUrl();
		echo $this->getPriceHtml($_product, true);
		echo $this->htmlEscape($_product->getName());
	endforeach;
}

?>
```

### Get random product

```
<?php

    /* @var $collection Mage_Catalog_Model_Resource_Product_Collection */
    $collection = Mage::getModel('catalog/product')->getCollection();
    $collection->getSelect()->order('RAND()');
    $collection->load();

    foreach ($collection as $product)
    {
        /** @var Mage_Catalog_Model_Product $product */

        ###
    }

?>
```

### Delete all products programaticaly

Place this somewhere in `shell` Directory for example and set the correct `$storeId` and `$storeCode`:

```PHP
<?php

require_once '../app/Mage.php';

$storeId = 1;
$storeCode = "de";

Mage :: app($storeCode)->setCurrentStore( Mage_Core_Model_App :: ADMIN_STORE_ID );
$products = Mage :: getResourceModel('catalog/product_collection')->setStoreId($storeId)->getAllIds();

if(is_array($products))
{
	foreach ($products as $key => $pId)
	{
	    try
	    {
	        $product = Mage::getModel('catalog/product')->load($pId)->delete();
	        echo "successfully deleted product with ID: ". $pId ."\n";
	    }
	    catch (Exception $e)
	    {
	        echo "Could not delete product with ID: ". $pId ."\n";
	    }
	}
}

```

### Delete all products

```sql
TRUNCATE TABLE `catalog_product_bundle_option`;
TRUNCATE TABLE `catalog_product_bundle_option_value`;
TRUNCATE TABLE `catalog_product_bundle_selection`;
TRUNCATE TABLE `catalog_product_entity_datetime`;
TRUNCATE TABLE `catalog_product_entity_decimal`;
TRUNCATE TABLE `catalog_product_entity_gallery`;
TRUNCATE TABLE `catalog_product_entity_int`;
TRUNCATE TABLE `catalog_product_entity_media_gallery`;
TRUNCATE TABLE `catalog_product_entity_media_gallery_value`;
TRUNCATE TABLE `catalog_product_entity_text`;
TRUNCATE TABLE `catalog_product_entity_tier_price`;
TRUNCATE TABLE `catalog_product_entity_varchar`;
TRUNCATE TABLE `catalog_product_link`;
TRUNCATE TABLE `catalog_product_link_attribute_decimal`;
TRUNCATE TABLE `catalog_product_link_attribute_int`;
TRUNCATE TABLE `catalog_product_link_attribute_varchar`;
TRUNCATE TABLE `catalog_product_option`;
TRUNCATE TABLE `catalog_product_option_price`;
TRUNCATE TABLE `catalog_product_option_title`;
TRUNCATE TABLE `catalog_product_option_type_price`;
TRUNCATE TABLE `catalog_product_option_type_title`;
TRUNCATE TABLE `catalog_product_option_type_value`;
TRUNCATE TABLE `catalog_product_super_attribute`;
TRUNCATE TABLE `catalog_product_super_attribute_label`;
TRUNCATE TABLE `catalog_product_super_attribute_pricing`;
TRUNCATE TABLE `catalog_product_super_link`;
TRUNCATE TABLE `catalog_product_enabled_index`;
TRUNCATE TABLE `catalog_product_website`;
TRUNCATE TABLE `catalog_product_entity`;
TRUNCATE TABLE `cataloginventory_stock_item`;
TRUNCATE TABLE `cataloginventory_stock_status`;
```

### Magento: Mass Exclude/Unexclude Images

By default, Magento will check the 'Exclude' box for you on all imported images, making them not show up as a thumbnail under the main product image on the product view.

```sql
    # Mass Unexclude
    UPDATE`catalog_product_entity_media_gallery_value` SET `disabled` = '0' WHERE `disabled` = '1';
    # Mass Exclude
    UPDATE`catalog_product_entity_media_gallery_value` SET `disabled` = '1' WHERE `disabled` = '0';
```

## Category

### Load category by id

```php
<?php

$_category = Mage::getModel('catalog/category')->load(89);
$_category_url = $_category->getUrl();

?>
```

## Get the current category

```php
<?php

$currentCategory = Mage::registry('current_category');

?>
```

## Get The Root Category In Magento

```php
<?php
$rootCategoryId = Mage::app()->getStore()->getRootCategoryId();
$_category = Mage::getModel('catalog/category')->load($rootCategoryId);
// You can then get all of the top level categories using:
$_subcategories = $_category->getChildrenCategories();
?>
```

## Category Navigation Listings in Magento

Make sure the block that you’re working is of the type catalog/navigation. If you’re editing catalog/navigation/left.phtml then you should be okay.

```php
<div id="leftnav">
	<?php $helper = $this->helper('catalog/category') ?>
	<?php $categories = $this->getStoreCategories() ?>
	<?php if (count($categories) > 0): ?>
		<ul id="leftnav-tree" class="level0">
			<?php foreach($categories as $category): ?>
				<li class="level0<?php if ($this->isCategoryActive($category)): ?> active<?php endif; ?>">
					<a href="<?php echo $helper->getCategoryUrl($category) ?>"><span><?php echo $this->escapeHtml($category->getName()) ?></span></a>
					<?php if ($this->isCategoryActive($category)): ?>
						<?php $subcategories = $category->getChildren() ?>
						<?php if (count($subcategories) > 0): ?>
							<ul id="leftnav-tree-<?php echo $category->getId() ?>" class="level1">
								<?php foreach($subcategories as $subcategory): ?>
									<li class="level1<?php if ($this->isCategoryActive($subcategory)): ?> active<?php endif; ?>">
										<a href="<?php echo $helper->getCategoryUrl($subcategory) ?>"><?php echo $this->escapeHtml(trim($subcategory->getName(), '- ')) ?></a>
									</li>
								<?php endforeach; ?>
							</ul>
							<script type="text/javascript">decorateList('leftnav-tree-<?php echo $category->getId() ?>', 'recursive')</script>
						<?php endif; ?>
					<?php endif; ?>
				</li>
			<?php endforeach; ?>
		</ul>
		<script type="text/javascript">decorateList('leftnav-tree', 'recursive')</script>
	<?php endif; ?>
</div>
```

### Import category

```php
<?php

	/* @var $category Mage_Catalog_Model_Category */
        $category = Mage::getModel('catalog/category');

	$parentId = null;	// possible parent
	$storeId = 1;		// store where the categories should go
	
	$mapping = array(
	        'name'             => '',
	        //'path' => '',
	        'description'      => '',
	        'meta_title'       => '',
	        'meta_keywords'    => '',
	        'meta_description' => '',
	        'landing_page'     => '',
	        'display_mode'     => 'PRODUCTS_AND_PAGE',
	        'is_active'        => 1,
	        'is_anchor'        => 0,
	        'url_key'          => '',
	        'image'            => '',
	);

	$category->addData($mapping);
	$category->setStoreId($storeId);
	
	if ( $parentId != null )
	{ // magento category id given: use as parent
		$category->getParentCategory()->setId($parentId);
	}
	
	try
	{
		$category->save();
		verbose("Added category {$data->idCategory} as {$category->getId()} ..");
	} catch ( Exception $e )
	{
		echo '!! ERROR: ' . $e->getMessage();
		exit;
	}
	
```

### Delete all categories

Place this somewhere in the root of magento and execute the php.

```php

<?php

require_once dirname(__FILE__) . '/app/Mage.php';
Mage::app()->setCurrentStore(Mage::getModel('core/store')->load(Mage_Core_Model_App::ADMIN_STORE_ID));

$resource = Mage::getSingleton('core/resource');
$db_read = $resource->getConnection('core_read');

$categories = $db_read->fetchCol("
    SELECT entity_id
    FROM " . $resource->getTableName("catalog_category_entity") . "
    WHERE entity_id>1
    ORDER BY entity_id DESC
");

foreach ($categories as $category_id) {
	try {
		Mage::getModel("catalog/category")->load($category_id)->delete();
	} catch (Exception $e) {
		echo $e->getMessage() . "\n";
	}
}

```

Alternative:

```
$idSet = Mage::getModel('catalog/category')->getCollection()->getAllIds();

while ($categoryId = array_shift($idSet))
{

    try {
        $category = Mage::getModel("catalog/category")->load($categoryId);
        if (!$category->hasChildren())
        {
            echo "Delete $categoryId ...";
            $category->delete();
            echo PHP_EOL;
        }
        else
        {
            $idSet[] = $categoryId;
        }
    } catch (Exception $e) {
        echo $e->getMessage();
    }

    echo PHP_EOL;

}

echo PHP_EOL;
```
