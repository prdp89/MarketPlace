
# Marketplace

This is a java project build via Domain driven and Microservice architecture 
using [Spring Boot](https://spring.io/projects/spring-boot) and backed with [H2DB](https://github.com/h2database/h2database)

## BasicStructure:

1. This Project contains mainly 5 services :

	
        1. GatewayService - This service is responsible to redirect each microservice request to specific port number along with logging and filtering mechanism.
	
        2. EurekaService - This project contains Eureka Server that manages or maintain individual microservices. This component has been included for scalability purpose incase we are using ELB in future.
	
        3. InventoryService - This service is responsible to mangae the Marketplace inventories and keep track of the available quantity. Local host url: http://localhost:9092/swagger-ui.html
	
        4. MarketService - This service is responsible to manage Market operation along with the Selller or User of each market.
		Local host url:http://localhost:9091/swagger-ui.html
		
		5. ProductService - This service is responsible to manage a published product in the market that associated with SKU's. Each Product belongs to a specific category. Local host url: http://localhost:9090/swagger-ui.html
	
2. This project contains 2 batch files. The order and usage are:

		1. zBuildServer - This file contains a command 'mvn clean install' that build above 5 services.
	
		2. zRunServer - This file contains a command 'mvn spring-boot:run' that run above 5 services in localhost.

3. After performing step 2 each microservice can be accessed through localhost with embedded Swagger. Some sample data has been inserted in data.sql file to perform a basic operation.

## Assumptions:

Suppose we are building a Marketplace to buy and sell products (electronics or gadgets etc.). A Seller is selling a Product called OnePlus6T. Then following properties will hold:

		1. The Color and Size is considered as ProductSpecification.
	
		2. The Price and Storage capacity of a mobile is considered as ProductSKU. Additionaly ProductSKUSpecification is provided incase each ProductSKU have different properties associated. Example : ProductSKU.skuName = "Price" and ProductSKU.ProductSKUSpecification.specificationCode = "PRICE_BLACK".
	
		3. The Seller and Market has one-to-many relationship. A Market can have multiple Sellers linked.
	
## Marketplace Operations for Product:

		1. Find or Group by Brand :
				A. BaseUrl : /api/productsByBrand/v1
				B. Params : brand (typeOf String)
				C. Zuul EndPoint : http://localhost:8000/product/api/productsByBrand/v1?brand=ONE-PLUS
		
		2. Find or Group by Price (SKU) :
				A. BaseUrl : /api/productsByPrice/v1
				B. Params : price (typeOf Double)
				C. Zuul EndPoint : http://localhost:8000/product/api/productsByPrice/v1?price=21000
				
		3. Find or Group by Specs (Color or Size) :
				A. BaseUrl : /api/productsBySpecs/v1
				B. Params : code (typeOf String)
							colorOrSize (typeOf String)
				C. Zuul EndPoint : http://localhost:8000/product/api/productsBySpecs/v1?code=CODE_SPECS.COLOR&colorOrSize=BLACK
				
		4. Available number of products by seller :
				A. BaseUrl : /api/productsCount/v1
				B. Params : seller (typeOf Integer, a seller id)
				C. Zuul EndPoint : http://localhost:8000/product/api/productsCount/v1?seller=700
				
## Additional Requirements:

		1. Data refresh trigger on new data inserted : For this scenario two additional optional params has been provided on /api/productsByPrice/v1 are:
				Params : i. createdDateTime ii. updatedDateTime
				
		2. On Market Deletion associated Products and Inventories will be disabled:
				A. BaseURL : /api/marketWithProductAndInventory/v1/{marketId}
				B. EndPoint : http://localhost:9091/api/marketWithProductAndInventory/v1/600
				C. Messaging : The inter-Api communication has been performed using [FeignClient] (https://github.com/OpenFeign/feign)
				
		3. Customer to have faster search : To support this operation Redis cache has been used that is linked with ServiceImpl layer. On first time API call fetched data will be stored on cache and subsequent calls can reuse that cached storage.
				A. BaseURL : /api/products/v1
				B. Custom Property : redis.enable = false
				C. Requirements : Redis server will be required and this API need support for Nested entities.
				
		4. Pagination and basic CRUD operations has done for each Entity.

## Notes :

		1. The Facade pattern in combination with DTO layer can be implemented and will be taken care in future.
	
		2. Redis Cache implementation has a known issue while retreiving data for nested entities.
		

##### About DEV :
[Linkedin](https://www.linkedin.com/in/pardeep-sharma-dev/)