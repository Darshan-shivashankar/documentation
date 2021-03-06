## Install Feature API
### Prerequisites
To start feature integration, overview and install the necessary features:

| Name | Version |
| --- | --- |
| Spryker Core | 2018.12.0 |
| Customer Account Management | 2018.12.0 |
| ProductImage | 2018.12.0 |
| ProductsRestApi | 2.2.3 |

## 1) Install the required modules

Run the following command to install the required modules:
`composer require spryker/product-image-sets-rest-api:"^1.0.3" --update-with-dependencies`

@(Info)(Verification)(Make sure that the following module is installed:)

| Module | Expected Directory |
| --- | --- |
| `ProductImageSetsRestApi` | `	vendor/spryker/product-image-sets-rest-api` |       

## 2) Set up Transfer objects

Run the following command to generate transfer changes:
`console transfer:generat`

@(Info)(Verification)(Make sure that the following changes are present in the transfer objects:)

| Transfer | Type | Event | Path |
| --- | --- | --- | --- |
| `RestProductImageSetsAttributesTransfer` | column | created | `src/Generated/Shared/Transfer/RestProductImageSetsAttributesTransfers` |
| `RestProductImageSetTransfer` | class | created | `src/Generated/Shared/Transfer/RestProductImageSetTransfer` |
| `RestImagesAttributesTransfer` | class | created | `src/Generated/Shared/Transfer/RestImagesAttributesTransfer` |

## 3) Set up behavior
**Implementation**
Activate the following plugins:

| Plugin | Specification | Prerequisites | Namespace |
| --- | --- | --- | --- |
| `AbstractProductImageSetsRoutePlugin` | Registers an abstract product image sets resource. | None | `Spryker\Glue\ProductImageSetsRestApi\Plugin` |
| `ConcreteProductImageSetsRoutePlugin` | Registers a concrete product image sets resource. | None | `Spryker\Glue\ProductImageSetsRestApi\Plugin` |
| `AbstractProductsProductImageSetsResourceRelationshipPlugin` | Adds an abstract product image sets resource as a relationship to an abstract product resource. | None | `Spryker\Glue\ProductImageSetsRestApi\Plugin` |
| `ConcreteProductsProductImageSetsResourceRelationshipPlugin` | Adds a concrete product image sets resource as a relationship to a concrete product resource. | None | `Spryker\Glue\ProductImageSetsRestApi\Plugin` |

**`src/Pyz/Glue/GlueApplication/GlueApplicationDependencyProvider.php`**
```php
<?php
 
namespace Pyz\Glue\GlueApplication;
 
use Spryker\Glue\GlueApplication\GlueApplicationDependencyProvider as SprykerGlueApplicationDependencyProvider;
use Spryker\Glue\ProductImageSetsRestApi\Plugin\AbstractProductImageSetsRoutePlugin;a
use Spryker\Glue\ProductImageSetsRestApi\Plugin\ConcreteProductImageSetsRoutePlugin;
use Spryker\Glue\ProductImageSetsRestApi\Plugin\Relationship\AbstractProductsProductImageSetsResourceRelationshipPlugin;
use Spryker\Glue\ProductImageSetsRestApi\Plugin\Relationship\ConcreteProductsProductImageSetsResourceRelationshipPlugin;
use Spryker\Glue\ProductsRestApi\ProductsRestApiConfig;
 
class GlueApplicationDependencyProvider extends SprykerGlueApplicationDependencyProvider
{
    /**
     * @return \Spryker\Glue\GlueApplicationExtension\Dependency\Plugin\ResourceRoutePluginInterface[]
     */
    protected function getResourceRoutePlugins(): array
    {
        return [
            new AbstractProductImageSetsRoutePlugin(),
            new ConcreteProductImageSetsRoutePlugin(),
        ];
    }
 
    /**
    * @param \Spryker\Glue\GlueApplicationExtension\Dependency\Plugin\ResourceRelationshipCollectionInterface $resourceRelationshipCollection
    *
    * @return \Spryker\Glue\GlueApplicationExtension\Dependency\Plugin\ResourceRelationshipCollectionInterface
    */
    protected function getResourceRelationshipPlugins(
        ResourceRelationshipCollectionInterface $resourceRelationshipCollection
    ): ResourceRelationshipCollectionInterface {
        $resourceRelationshipCollection->addRelationship(
            ProductsRestApiConfig::RESOURCE_ABSTRACT_PRODUCTS,
            new AbstractProductsProductImageSetsResourceRelationshipPlugin()
        );
        $resourceRelationshipCollection->addRelationship(
            ProductsRestApiConfig::RESOURCE_CONCRETE_PRODUCTS,
            new ConcreteProductsProductImageSetsResourceRelationshipPlugin()
        );
 
        return $resourceRelationshipCollection;
    }
}
```

**Verification**
@(Info)()(Make sure that the following endpoints are available:)

* `http://example.org//abstract-products/{{abstract_sku}}/abstract-product-image-sets` 
* `http://example.org/concrete-products/{{concrete_sku}}/concrete-product-image-sets` 

@(Info)()(Make the request to `http://example.org/abstract-products/{{abstract_sku}}?include=abstract-product-image-sets`. The abstract product with the given SKU should have at least one image set. Make sure that the response includes relationships to the `abstract-product-image-sets` resources. )
@(Info)()(Make the request to `http://example.org/concrete-products/{{concrete_sku}}?include=abstract-product-image-sets`. The concrete product with the given SKU should have at least one image set. Make sure that the response includes relationships to the `concrete-product-image-sets` resources.)

_Last review date: Feb 21, 2019_
