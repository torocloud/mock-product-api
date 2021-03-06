# Mock Product API
A simple product api with CRUD operations

### Prerequisites

  - Apache Maven 3+
  - [Martini Desktop](https://www.torocloud.com/martini/download)

### Building the Martini Package

```
$ mvn clean package
```
This will create a ZIP file named `mock-product-api-bin.zip` containing all the files (services, configurations, etc.) needed under the `target` folder. This ZIP file is what we call a [Martini Package](https://docs.torocloud.com/martini/latest/developing/package/) which then you can import in Martini Desktop to get started. You can learn more how to import a Martini Package by visiting our [documentation](https://docs.torocloud.com/martini/latest/developing/package/importing/)

### Getting the Martini Package from TORO Marketplace

You can also get this package via TORO Marketplace. See our documentation on [How to import a Martini Package from Marketplace](https://docs.torocloud.com/martini/latest/developing/package/importing/#from-the-marketplace).

### Usage
This package exposes operations for a simple product REST API that allows you to do CRUD operations. You can find the [Gloop REST API](https://docs.torocloud.com/martini/latest/developing/gloop/api/rest/) file at `io/toro/mock/product/api/Product` under the `code` folder after importing the package to your Martini Desktop application.

### Setup
This Martini Package automatically sets up the required resources for you. During the package startup, Martini will execute the configured _Startup Service_ that initializes the database to be used for storing the record that will be creating for using this mock api.

This package uses HSQL as the default datastore but you can change this to MySQL by updating the [package properties](https://docs.torocloud.com/martini/latest/developing/package/properties/) located at [conf](https://docs.torocloud.com/martini/latest/developing/package/directory/) folder. Below is what the contents of the properties file look like

```
# Flag used for testing
debug.enabled=true

# The database type to be used
database.type=hsql

# HSQL database configuration to be used for testing environment
hsql.driver=org.hsqldb.jdbc.JDBCDriver
hsql.connection.url=jdbc:hsqldb:file:${toroesb.home}data/hsql/mock_product_api.db;hsqldb.tx=MVCC;sql.syntax_mys=true
hsql.username=sa
hsql.password=

# MySQL database configuration to be used in production environment
mysql.driver=com.mysql.cj.jdbc.Driver
mysql.connection.url=jdbc:mysql://<host>/<database-to-use>?allowMultiQueries=true&sessionVariables=sql_mode='ANSI'
mysql.username=root
mysql.password=
```
* `debug.enabled` when set to `true` initializes the database with dummy data when the package starts. The initialized data are also removed when the package stops or is unloaded. Set the value of this property to `false` if you want to keep your data when doing a restart. You can also set this property to `true` when the package starts and then set to `false` afterwards so that you'll have the data initialized on startup, and keep the data when the package or the Martini instance do a restart
* `database.type` sets the database provider the Martini package will use. If you will use the default hsql config, you don't need to change anything in the hsql configuration. **Note**: If you will use a different hsql database, make sure that you add `sql.syntax_mys=true` in the connection properties. This ensures that the SQL query from the SQL Services in this package will be compatible with hsql.

### Custom Fields

The Product API supports custom fields but note that you can only add and edit custom fields as of this time.

### Sending Authenticated Requests

You can use your ECC account to send authenticated request to the API. Your ECC credentials must be sent in the `Authorization` header in the HTTP request

#### To authenticate a request with basic authentication

1. Combine your email and password with a colon (`:`). e.g. `jdoe@mailinator.com:pa$$w0rd`
2. Encode the resulting string in Base64
3. Include an Authorization header in the HTTP request containing the base64-encoded string. Example: ```
Authorization: Basic amRvZUBtYWlsaW5hdG9yLmNvbTpwYSQkdzByZA==```

### Operations

The base url is `<host>/api/mock-product-api` where `host` is the location where the Martini instance is deployed. By default, it's `localhost:8080`.

#### Products

`GET /products`

Returns a list of available products

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-product-api/products \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully fetched products.",
    "products": [
        {
            "productCode": "S10_1678",
            "productName": "1969 Harley Davidson Ultimate Chopper",
            "productLine": "Motorcycles",
            "productScale": "01:10",
            "productVendor": "Min Lin Diecast",
            "productDescription": "This replica features working kickstand, front suspension, gear-shift lever, footbrake lever, drive chain, wheels and steering. All parts are particularly delicate due to their precise scale and require special care and attention.",
            "quantityInStock": 7933,
            "buyPrice": 48.81,
            "msrp": 95.70,
            "customField": {

            }
        },
        {
            "productCode": "S10_1949",
            "productName": "1952 Alpine Renault 1300",
            "productLine": "Classic Cars",
            "productScale": "01:10",
            "productVendor": "Classic Metal Creations",
            "productDescription": "Turnable front wheels; steering function; detailed interior; detailed engine; opening hood; opening trunk; opening doors; and detailed chassis.",
            "quantityInStock": 7305,
            "buyPrice": 98.58,
            "msrp": 214.30,
            "customField": {

            }
        },
        {
            "productCode": "S72_3212",
            "productName": "Pont Yacht",
            "productLine": "Ships",
            "productScale": "02:12",
            "productVendor": "Unimax Art Galleries",
            "productDescription": "Measures 38 inches Long x 33 3/4 inches High. Includes a stand.Many extras including rigging, long boats, pilot house, anchors, etc. Comes with 2 masts, all square-rigged",
            "quantityInStock": 414,
            "buyPrice": 33.30,
            "msrp": 54.60,
            "customField": {

            }
        }
    ]
}
```

`POST /products`

Create a new product record

**Sample Request**

**curl**

**Creating a new product record with custom field**
```
curl -X POST \
  http://localhost:8080/api/mock-product-api/products \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "productCode": "PRD_001",
    "productName": "F21 - Fighter Jet",
    "productLine": "Planes",
    "productScale": "1:100",
    "productVendor": "Highway 66 Mini Classics",
    "productDescription": "Die cast metal with lots of gimmicks",
    "quantityInStock": "100",
    "buyPrice": "15.55",
    "msrp": "16.99",
    "customField": {
        "testField": "field value"
    }
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `201` with the response payload similar below.
```
{
    "result": "SUCCESS",
    "message": "Successfully created product with product code: S700_1691.",
    "product": {
        "productCode": "PRD_001",
        "productName": "F21 - Fighter Jet",
        "productLine": "Planes",
        "productScale": "1:100",
        "productVendor": "Highway 66 Mini Classics",
        "productDescription": "Die cast metal with lots of gimmicks",
        "quantityInStock": "100",
        "buyPrice": "15.55",
        "msrp": "16.99",
        "customField": {
            "testField": "field value"
        }
    }
}
```

`PATCH /products`

Updates a product. `productCode` must be included in the request body. All properties except `productCode` can be updated. Refer to the service input used in the Gloop Service that is bound to this endpoint for other properties that can be updated.

Alternatively, if you just want to update the product price, or quantity, you can use `POST /products/<productCode>/price` or `POST /products/<productCode>/quantity`. Both endpoints accept form data for `price` and `quantity` parameters.

**Sample Request with custom field**

**curl**
```
curl -X PATCH \
  http://localhost:8080/api/mock-product-api/products/ \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "productCode": "PRD_001",
    "productDescription": "Die cast metal with lots of gimmicks. With foldable wings",
    "quantityInStock": "10",
    "buyPrice": "16.55",
    "msrp": "19.99",
    "customField": {
        "testField": "updated field value"
    }
}'
```

**Sample Response** 

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "result": "Updated",
    "message": "Successfully updated product with product code: PRD_001."
    "product": {
        "productCode": "PRD_001",
        "productDescription": "Die cast metal with lots of gimmicks. With foldable wings",
        "quantityInStock": "10",
        "buyPrice": "16.55",
        "msrp": "19.99",
        "customField": {
            "testField": "updated field value"
        }
}
```

`GET /products/<productCode>`

Returns a single product record that matches the given `productId`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-product-api/products/S700_1691 \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "result": "SUCCESS",
    "message": "Successfully fetched product with code: S700_1691",
    "product": {
        "productCode": "S700_1691",
        "productName": "American Airlines: B767-300",
        "productLine": "Planes",
        "productScale": "12:40",
        "productVendor": "Min Lin Diecast",
        "productDescription": "Exact replia with official logos and insignias and retractable wheels",
        "quantityInStock": 5841,
        "buyPrice": 51.15,
        "msrp": 91.34,
        "customField": {

        }
    }
}
```

`DELETE /products/<productId>`

Deletes a product record that matches the `productId`

**Sample Request**

**curl**
```
curl -X DELETE \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  http://localhost:8080/api/mock-product-api/products/PRD_001
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200`.
```
{
    "result": "SUCCESS",
    "message": "Successfully deleted product with code: PRD_001"
}
```


#### Product Lines

`GET /product-lines`

Retrieves a list of all product lines

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-product-api/product-lines \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "result": "SUCCESS",
    "message": "Succesfully fetched all product lines.",
    "productLines": [
        {
            "productLine": "Classic Cars",
            "textDescription": "Attention car enthusiasts: Make your wildest car ownership dreams come true. Whether you are looking for classic muscle cars, dream sports cars or movie-inspired miniatures, you will find great choices in this category. These replicas feature superb attention to detail and craftsmanship and offer features such as working steering system, opening forward compartment, opening rear trunk with removable spare wheel, 4-wheel independent spring suspension, and so on. The models range in size from 1:10 to 1:24 scale and include numerous limited edition and several out-of-production vehicles. All models include a certificate of authenticity from their manufacturers and come fully assembled and ready for display in the home or office.",
            "customField": {

            }
        },
        ...
        {
            "productLine": "Vintage Cars",
            "textDescription": "Our Vintage Car models realistically portray automobiles produced from the early 1900s through the 1940s. Materials used include Bakelite, diecast, plastic and wood. Most of the replicas are in the 1:18 and 1:24 scale sizes, which provide the optimum in detail and accuracy. Prices range from $30.00 up to $180.00 for some special limited edition replicas. All models include a certificate of authenticity from their manufacturers and come fully assembled and ready for display in the home or office.",
            "customField": {

            }
        }
    ]
}
```

`POST /product-lines`

Creates a new product line.

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-producty-api/product-lines \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "productLine": "New Arrivals",
    "textDescription": "Items marked as new arrivals.",
    "customField": {
		"testField": "test value"
    }
}'
```

Aside from `textDescription`, you can also include an `htmlDescription` in the request. This property accepts an html string equivalent of the `textDescription`.

**Sample Response**

If the request is successful, it will return an HTTP status code `201`, with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully updated product line.",
    "productLine": {
        "productLine": "New Arrivals",
        "textDescription": "Items marked as new arrivals.",
        "customField": {
            "testField": "test value"
        }
    }
}
```

`PATCH /product-lines/`

Updates information about a product line. Providing the `productLine` is required.

**Sample Request**

**curl**
```
curl -X PATCH \
  http://localhost:8080/api/mock-product-api/products/ \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "productLine": "New Arrivals",
    "textDescription": "Items marked as new arrivals. Updated description",
    "customField": {
		"testField": "Updated custom field value"
    }
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200`, with response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully updated product line.",
    "productLine": {
        "productLine": "New Arrivals",
        "textDescription": "Items marked as new arrivals. Updated description",
        "customField": {
            "testField": "Updated custom field value"
        }
    }
}
```

`GET /product-lines/<productLineId>`

Returns a single product line record that matches the given `productLineId`.

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-product-api/product-lines/New%20Arrivals \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP response code `200`, with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully fetched product line.",
    "productLine": {
        "productLine": "New Arrivals",
        "textDescription": "Items marked as new arrivals. Updated description",
        "customField": {
            "testField": "Updated custom field value"
        }
    }
}
```

`DELETE /product-lines/<productLineId>`

Deletes a product line record that matches the given `productLineId`

**Sample Request**

**curl**
```
curl -X DELETE \
  http://localhost:8080/api/mock-product-api/product-lines/New%20Arrivals \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200`.
```
{
    "result": "SUCCESS",
    "message": "Successfully deleted product line."
}
```