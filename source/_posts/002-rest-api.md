---
title: Building RESTful web services
date: 2020-07-24 20:37:27
tags:
---

In this post we are going to learn how to build RESTful web services using Quarkus. Let's start using the project we created in the [last post.](../../../07/23/001-scaffold/)

We are going to implement the following endpoints:

| HTTP METHOD | Path        |Description        |
| ----------- | ----------- |----------- |
| POST        | /products   | Creates a new product |
| PUT   | /products/{id} |Updates an existing product |
| GET   | /products   |Gets all the products |
| DELETE   | /products/{id} |Deletes an existing product |

Quarkus uses JAX-RS to define rest endpoints so the implementation is going to be trivial. What we will need are the following annotations:

1. `javax.ws.rs.Path` annotation to define the required path for the endpoints (`/products`)
2. `javax.ws.rs.HTTP_METHOD` annotation where `HTTP_METHOD` can be POST, PUT, GET, DELETE
3. `javax.ws.rs.PathParam` annotation to get the values in the URL. This is going to be needed for updating and removing products.

In order to allow Quarkus consume and produce information using a JSON format, we need to add an additional dependency as follows:

```xml
  <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-resteasy-jsonb</artifactId>
  </dependency>
```

The implementation is going to save the information in memory only so no databases needed for now. This is how our implementation will look like:

```java
@Path("/products")
public class ProductsResource {

    private List<Product> products;

    public ProductsResource(){
        products = new ArrayList();
        products.add(new Product("product-1", "notebook"));
        products.add(new Product("product-2", "mouse"));
    }

    @GET
    @Produces(MediaType.APPLICATION_JSON)
    public Response getAll() {
        return Response.status(Status.OK).entity(this.products).build();
    }

    @POST
    @Consumes(MediaType.APPLICATION_JSON)
    @Produces(MediaType.APPLICATION_JSON)
    public Response add(Product product) {
        this.products.add(product);
        return Response.status(Status.OK).entity(product).build();
    }

    @PUT
    @Path("/{id}")
    @Consumes(MediaType.APPLICATION_JSON)
    @Produces(MediaType.APPLICATION_JSON)
    public Response update(@PathParam("id") String id, Product product) {
        Optional<Product> productToUpdate = findProductById(id);
        if (productToUpdate.isPresent()) {
            products.remove(productToUpdate.get());
            products.add(product);
            return Response.status(Status.OK).entity(product).build();
        } else {
            return Response.status(Status.NOT_FOUND).build();
        }
    }

    @DELETE
    @Path("/{id}")
    public Response delete(@PathParam("id") String id) {
        Optional<Product> productToRemove = findProductById(id);
        if (productToRemove.isPresent()) {
            products.remove(productToRemove.get());
            return Response.status(Status.OK).build();
        } else {
            return Response.status(Status.NOT_FOUND).build();
        }
    }

    private Optional<Product> findProductById(String id) {
        return this.products.stream()
                .filter(product -> id.equals(product.getId()))
                .findFirst();
    }
}
```

All the above-used annotations are part of JAX-RS, it means you don't need to learn anything new. Quarkus uses the standard annotations that you already know.

## Testing our RESTful web services
Let's try the implemented web services using CURL commands:

### Creating a product
For this operation, we are going to use the POST HTTP method:
```sh 
curl --location --request POST 'http://localhost:8080/products' -d '{"id": "product-3", "name": "iPhone"}' --header 'Content-Type: application/json'
```
You will get the following response:
```sh
{"id":"product-3","name":"iPhone"}
status=200  size=34 time=0.112 content-type="application/json"
```

## Updating a product
For this operation, we are going to use the PUT HTTP method, notice how the URL contains the product id that is going to be updated (`product-3`):
```sh 
curl --location --request PUT 'http://localhost:8080/products/product-3' -d '{"id": "product-3", "name": "guitar"}' --header 'Content-Type: application/json'
```
You will get the following response:
```sh
{"id":"product-3","name":"guitar"}
status=200  size=34 time=0.020 content-type="application/json"
```

## Getting all the products stored
For this operation, we are going to use the GET HTTP method:
```sh 
curl --location --request GET 'http://localhost:8080/products'
```
You will get the following response:
```sh
[{"id":"product-1","name":"notebook"},{"id":"product-2","name":"mouse"},{"id":"product-3","name":"guitar"}]
status=200  size=107 time=0.029 content-type="application/json"
```

## Removing an existing product
For this operation, we are going to use the DELETE HTTP method, notice how the URL contains the product id that is going to be deleted (`product-1`):
```sh 
curl --location --request DELETE 'http://localhost:8080/products/product-1'
```
You will get the following response:
```sh
status=200  size=0 time=0.016
```

In this way, we already tested all the implemented functionality. That’s all folks!