# OpenAPI definitions

This example API definition can be used to provide an OpenAPI 3.0 definition for an implementation of _OGC API - Coverages_.
The API definition can be visualized with [SwaggerUI](https://petstore.swagger.io/?url=https://raw.githubusercontent.com/opengeospatial/ogcapi-coverages/master/standard/openapi/ogcapi-coverages-1.bundled.json).

The lists of collections and tile matrix sets in the `/api` sub-directory should be tailored to the implementation & deployment, or those `/api/*` paths can be implemented dynamically by the server instead.
The list of supported paths should be ajusted in `ogcapi-coverages-1.yaml`.

The `ogcapi-coverages-1.bundled.json` was generated with `swagger-cli bundle` from `ogcapi-coverages-1.yaml` and its dependencies included from the components sub-directories.

See also [SwaggerCLI](https://apitools.dev/swagger-cli/) and its [GitHub repository](https://github.com/APIDevTools/swagger-cli).
