# OGC API - Coverages

Latest draft specifications: [HTML](https://docs.opengeospatial.org/DRAFTS/19-087.html) [PDF](https://docs.opengeospatial.org/DRAFTS/19-087.pdf)

Latest draft User's Guide: [HTML](https://docs.opengeospatial.org/DRAFTS/20-075.html) [PDF](https://docs.opengeospatial.org/DRAFTS/20-075.pdf)

See the latest [OpenAPI definition](https://petstore.swagger.io/?url=https://raw.githubusercontent.com/opengeospatial/ogcapi-coverages/master/standard/openapi/ogcapi-coverages-1.bundled.json) with SwaggerUI.

This GitHub repository contains [OGC](http://opengeospatial.org)'s
candidate standard for accessing geospatial data resources on the web as a coverage, _OGC API - Coverages_.

[OGC API standards](https://ogcapi.ogc.org) define modular API building blocks to spatially enable Web APIs
in a consistent way. [OpenAPI](http://openapis.org) is used to define the
reusable API building blocks with responses available in different representations,
such as JSON and HTML, as well as more domain specific formats (e.g. CIS RDF, netCDF, GeoTIFF).

The OGC API family of standards is organized by resource type. OGC API -
Coverages specifies the fundamental API building blocks for interacting with
coverages. The spatial data community uses the term 'coverage' for homogeneous
collections of values located in space/time, such as spatio-temporal sensor,
image, simulation, and statistics data.

If you are unfamiliar with the term 'coverage', the explanations on
the [Coverages DWG Wiki](http://myogc.org/go/coveragesDWG) provide more detail and links to educational material.
Additionally, [Coverages: describing properties that vary with location (and time)](https://www.w3.org/TR/sdw-bp/#coverages)
in the W3C/OGC Spatial Data on the Web Best Practice document may be considered.

Please note that this Readme holds an informative introduction, the
authoritative specification is contained in the folder `standard`. There is also
a [nightly build](http://docs.opengeospatial.org/DRAFTS/19-087.html).

## Overview

This [OGC API - Coverages](https://github.com/opengeospatial/ogcapi-coverages) standard establishes an access mechanism for coverages as defined by the
[OGC Abstract Specification Topic 6](https://portal.ogc.org/files/?artifact_id=19820) / [ISO 19123-1](https://www.iso.org/standard/40121.html) Schema for coverage geometry and functions through a Web API
which can be described by an API description language such as the [OpenAPI specification](https://www.openapis.org/).

It integrates with the OGC API family of standards through [OGC API - Common](https://github.com/opengeospatial/ogcapi-common) by:
- being accessible from an API landing page for a particular dataset,
- enabling the API to be described and documented using [OpenAPI](https://www.openapis.org/) as per [_OGC API - Common - Part 1: Core_](https://docs.ogc.org/is/19-072/19-072.html),
- defining conformance classes specific to coverages,
- providing access to an OGC API *collection* of geospatial data (as defined in [OGC API - Common - Part 2: Geospatial Data](https://docs.ogc.org/DRAFTS/20-024.html)), as a coverage.

### Resources

#### Defined in [**_OGC API - Common - Part 1_**](https://docs.ogc.org/is/19-072/19-072.html)

`{root}` The root of the API for the dataset.

`{root}/api` The API definition and documentation.

`{root}/conformance` The conformance declaration.

#### Extended from [**_OGC API - Common - Part 2_**](https://docs.ogc.org/DRAFTS/20-024.html) with additional properties

`{root}/collections`

The list of all collections available, some or all of which may be accessible using this _Coverages API_.
Each of these collections contains a minimal subset of the object collection resource object described immediately below.

`{root}/collections/{collectionId}`

Description for the collection with the unique identifier `{collectionId}`, which may be accessible as a coverage.
The resource includes elements such as an `id`, `title`, `description`, available `crs` and `extent`.
This [`extent`](https://github.com/opengeospatial/ogcapi-coverages/blob/master/standard/openapi/schemas/common-geodata/extent-uad.yaml) describes the domain of the coverage for each dimension, including the overall envelope, detailed sub-intervals where data is available, and/or a regular or irregular `grid`.
This object also includes links to resources pertaining to this collection. For coverages, a link to the record schema described below will be included.
This resource is comparable to a WCS *_DescribeCoverage_* response, with the exception that the schema, corresponding to Coverage Implementation Schema (CIS) _range type_, needs to be retrieved separately.

#### Defined in _Coverages - Part 1_

`{root}/collections/{collectionId}/schema`

Returns the [schema](https://github.com/opengeospatial/ogcapi-coverages/blob/master/standard/openapi/schemas/tms/propertiesSchema.yaml) for the coverage fields or properties of values available at each direct position.
At minimum, a _JSON Schema_ representation of this resource is available.
This resource is comparable to the CIS _range type_ portion of the WCS *_DescribeCoverage_* response,
and is retrieved separately from the collection description to accommodate more complex record schemas including several record fields and/or detailed semantic annotations.

`{root}/collections/{collectionId}/coverage`

Returns the coverage data, including any self-describing information (such as the _domain set_, _range type_ and _metadata_ components in addition to the _range set_ of CIS).
This resource is comparable to a WCS *_GetCoverage_* response.

#### Defined in _Tiles - Part 1_ (_Coverage Tiles_)

`{root}/collections/{collectionId}/coverage/tiles`

Returns the list of tilesets available for this coverage.

`{root}/collections/{collectionId}/coverage/tiles/{tileSetId}`

Returns an individual coverage [tileset](https://github.com/opengeospatial/ogcapi-coverages/blob/master/standard/openapi/schemas/tms/tileSet.yaml) for a particular 2D Tile Matrix Set

`{root}/collections/{collectionId}/coverage/tiles/{tileSetId}/{tileMatrix}/{tileRow}/{tileCol}`

Returns an individual coverage tile for a particular 2D Tile Matrix Set, tile matrix, tile row and tile column

#### Defined in _Coverages - Part 1 "Scenes"_ requirements class

`{root}/collections/{collectionId}/scenes`

Returns the list of scenes available for this coverage (for multi-scenes coverages, when the _Scenes_ requirement class is supported)

`{root}/collections/{collectionId}/scenes/{sceneId}`

Returns the scene metadata for an individual scene

`{root}/collections/{collectionId}/scenes/{sceneId}/coverage`

Returns the coverage data for an individual scene

### Examples

`GET /collections/myCoverage/coverage`

Retrieve the whole coverage (the response may be downsampled if [Scaling](https://docs.ogc.org/DRAFTS/19-087.html#rc-scaling) is supported by the server).

`GET /collections/myCoverage/coverage?scale-factor=1`

Retrieve the whole coverage at native resolution (the server will likely return a 400 error for large datasets).

`GET /collections/myCoverage/coverage?bbox=10,40,20,50`

Retrieve coverage spatial subsets between 40 and 50 degrees North, 10 and 20 degrees East, using the `bbox` query parameter.

`GET /collections/myCoverage/coverage?bbox=160.6,-55.95,-170,-25.89`

Retrieve coverage spatial subsets using the `bbox` query parameter, crossing the antimeridian.

`GET /collections/myCoverage/coverage?subset=Lat(40:50),Lon(10:20)`

Retrieve a coverage spatial subset using the `subset` query parameter.

`GET /collections/myCoverage/coverage?properties=B02,B03,B04`

Retrieve bands B02, B03 and B04 (field selection a.k.a. "range subsetting").

`GET /collections/myCoverage/coverage?scale-size=Lon(800),Lat(400)`

Retrieve a the coverage at a a resolution of 800 cells for the longitude axis and 400 cells for the latitude axis.

`GET /collections/myCoverage/coverage?scale-factor=2`

Retrieve a the coverage at a downsampled (2x) resolution.

`GET /collections/myCoverage/coverage?scale-axes=Lon(2)`

Retrieve a the coverage at a downsampled (2x) resolution for the longitude axis.


### Conformance Classes

#### Requirements classes defining resources

* [Core](https://docs.ogc.org/DRAFTS/19-087.html#rc-core)
* [Coverage Tiles](https://docs.ogc.org/DRAFTS/19-087.html#rc-coverage-tiles)
* [Coverage Scenes](https://docs.ogc.org/DRAFTS/19-087.html#rc-scenes)

The _Core_ Requirements Class is the minimal useful service interface for an OGC Coverages API. The requirements specified in this Requirements Class are mandatory for all implementations of _OGC API - Coverages_.

The _Coverage Tiles_ Requirements Class defines how to combine the _OGC API - Tiles_ building blocks with the Coverages API to request coverage tiles.

The _Scenes_ Requirements Class defines how to present separate components of a coverage as individual scenes, in addition to an overall coverage of the whole collection.

#### Requirements classes defining query parameters

* [Subsetting](https://docs.ogc.org/DRAFTS/19-087.html#rc-subsetting)
* [Scaling](https://docs.ogc.org/DRAFTS/19-087.html#rc-scaling)
* [Field Selection](https://docs.ogc.org/DRAFTS/19-087.html#rc-fieldselection)
* [Coordinate Reference System](https://docs.ogc.org/DRAFTS/19-087.html#rc-crs)

The _Subset_ Requirements Class defines the `subset`, `bbox` and `datetime` parameters to select a subset of a coverage of any coordinate reference system and any dimension.

The _Scaling_ Requirements Class defines the `scale-factor`, `scale-size` and `scale-axes` parameters for retrieving data from n-dimensional Range Sets at a resolution different than the original.

The _Field Selection_ (Range Subsetting) Requirements Class defines the `properties` parameter for selecting a subset of the bands (defined in the Range Type) to retrieve from Range Sets.

The _Coordinate Reference System_ Requirements Class defines the `crs` parameter to request coverage data in an alternate output coordinate reference system.

#### Requirements classes defining resource representations

* [HTML](https://docs.ogc.org/DRAFTS/19-087.html#rc-encoding-html)
* [GeoTIFF](https://docs.ogc.org/DRAFTS/19-087.html#rc-encoding-geotiff)
* [netCDF](https://docs.ogc.org/DRAFTS/19-087.html#_requirements_class_netcdf)
* [CIS JSON](https://docs.ogc.org/DRAFTS/19-087.html#rc-encoding-cisjson)
* [CoverageJSON](https://docs.ogc.org/DRAFTS/19-087.html#rc-encoding-coveragejson)
* [LAS](https://docs.ogc.org/DRAFTS/19-087.html#rc-encoding-las)
* [LASZip](https://docs.ogc.org/DRAFTS/19-087.html#rc-encoding-laszip)
* [PNG](https://docs.ogc.org/DRAFTS/19-087.html#rc-encoding-png)
* [JPEG XL](https://docs.ogc.org/DRAFTS/19-087.html#rc-encoding-jpegxl)
* [JPEG 2000](https://docs.ogc.org/DRAFTS/19-087.html#rc-encoding-jpeg2000)
* [(Geo)Zarr](https://docs.ogc.org/DRAFTS/19-087.html#rc-encoding-zarr)
* [OpenAPI 3.0](https://docs.ogc.org/DRAFTS/19-087.html#rc-oas30)

The Encoding Requirements Classes address support for formats commonly used for encoding coverage data.

The _OpenAPI 3.0_ Requirements Class defines additonal requirements in addition to those defined in _OGC API - Common - Part 1: Core_ to facilitate identifying coverage resources from an OpenAPI 3.0 API Definition.

## Using the standard

Those who want to just see the endpoints and responses can explore generic
OpenAPI definitions in this directory (or visualize the bundled definition with [SwaggerUI](https://petstore.swagger.io/?url=https://raw.githubusercontent.com/opengeospatial/ogcapi-coverages/master/standard/openapi/ogcapi-coverages-1.bundled.json):

* [ogcapi-coverages/standard/openapi](https://github.com/opengeospatial/ogcapi-coverages/tree/master/standard/openapi)

Implementations of the draft standard are listed at the link below:

[Implementations of the draft specification / demo services](https://github.com/opengeospatial/ogcapi-coverages/blob/master/implementations.adoc)

## Communication

Join the [mailing list](https://lists.ogc.org/mailman/listinfo/coverages.swg) or [![chat at https://gitter.im/opengeospatial/ogcapi-coverages](https://badges.gitter.im/opengeospatial/ogcapi-coverages.svg)](https://gitter.im/opengeospatial/ogcapi-coverages?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Most of the work on the specification takes place in [GitHub issues](https://github.com/opengeospatial/ogcapi-coverages/issues),
so browse there to get a good idea of what is happening, as well as past decisions.

## Additional parts of OGC API - Coverages

* [Part 2: Filtering and deriving fields](https://github.com/opengeospatial/ogcapi-coverages/issues/164)

See also [OGC API - Processes - Part 3: Workflows and Chaining](https://docs.ogc.org/DRAFTS/21-009.html), "Collection Input" and "Collection Output" requirements class in particular,
for how an _OGC API - Coverage_ coverage can be the input and/or the output of an _OGC API - Processes_ process.

## Contributing

The contributor understands that any contributions, if accepted by the OGC Membership, shall be incorporated into OGC API standards documents and that all copyright and intellectual property shall be vested to the OGC.

The Coverages API Standards Working Group (SWG) is the group at OGC responsible for the stewardship of the standard, but is working to do as much work in public as possible.

* [Coverages API Standards Working Group Charter](charter/OGC-Coverages-SWG-Charter.adoc)
* [Open issues](https://github.com/opengeospatial/ogcapi-coverages/issues)
* [Copy of License Language](https://raw.githubusercontent.com/opengeospatial/ogcapi-coverages/master/LICENSE)


Pull Requests from contributors are welcomed. However, please note that by sending a Pull Request or Commit to this GitHub repository, you are agreeing to the terms in the Observer Agreement https://portal.ogc.org/files/?artifact_id=92169
