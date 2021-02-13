# OGC API - Coverages

This GitHub repository contains [OGC](http://opengeospatial.org)'s
standard for accessing geospatial data resources on the web as a coverage, "OGC API - Coverages".

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

This [OGC API - Coverages](https://github.com/opengeospatial/ogcapi-coverages) specification establishes how to access coverages as defined by the [Coverage Implementation Schema (CIS) 1.1](http://docs.opengeospatial.org/is/09-146r6/09-146r6.html) .

It integrates with the OGC API family of standards through [OGC API - Common](https://github.com/opengeospatial/oapi_common) by:
- being accessible from an API landing page for a particular dataset,
- enabling the API to be described and documented using [OpenAPI](https://www.openapis.org/),
- defining conformance classes specific to coverages,
- providing access to an OGC API *collection* of geospatial data (as defined in OGC API - Common - Part 2: Geospatial Data), as a coverage.

### Common resources

#### Landing page (Common Part 1: Core)

Resource path: `{datasetAPI}/`

Relation type: `http://www.opengis.net/def/rel/ogc/1.0/dataset`

HTTP methods: `GET`

Recommended encodings: HTML, JSON

A landing page for the dataset being distributed.
The landing page at minimum should feature links to:
- the description of the API,
- the declaration of conformance classes,
- the list of coverages available.

#### Conformance declaration (Common Part 1: Core)

Resource path: `{datasetAPI}/conformance`

Relation type: `conformance`

HTTP methods: `GET`

Recommended encodings: HTML, JSON

The declaration of conformance classes supported by that API.

#### API description / documentation (Common Part 1: Core)

Resource path: `{datasetAPI}/api` NOTE: the path is not fixed, this resource may reside elsewhere

Relation type: `service-desc`, `service-doc`

HTTP methods: `GET`

Recommended encodings: JSON (OpenAPI), HTML

A description, and optionally documentation for the API.
To conform to the OpenAPI conformance class of OGC API - Common, an OpenAPI representation of the API description must be available.

#### List of available collections (Common Part 2: Geospatial Data)

Resource path: `{datasetAPI}/collections`

Relation type: `data`

HTTP methods: `GET`

Recommended encodings: JSON, HTML

Lists all collections of data available for this dataset API, some of which may support being accessed as a coverage.
A `collections` property is defined as a list of individual collection resources,
where each element describes basic information about that specific collection.
The content for each collection is also available individually at `{datasetAPI}/collections/{collectionId}`, and described in the next section.
Additionally, a `links` property can be included (e.g. linking back to the dataset landing page).

##### Query parameters (optional conformance classes)

**`bbox`**

A bounding box, expressed in WGS84 (westLong,southLat,eastLong,northLat) or WGS84h (westLong,southLat,minHeight,eastLong,northLat,maxHeight) CRS,
by which to filter out all collections whose spatial extent does not intersect with the bounding box.

Example: `?bbox=160.6,-55.95,-170,-25.89`

Lists the collections intersecting with the New Zealand economic zone (any time, any elevation, etc.).

#### Collection description resource (Common Part 2: Geospatial Data)

Resource path: `{datasetAPI}/collections/{collectionId}`

Relation type: `http://www.opengis.net/def/rel/ogc/1.0/collection`

HTTP methods: `GET`

Recommended encodings: JSON, HTML

Each collection resource describes basic information about the geospatial data collection, including:
- an identifier (`id`),
- a human readable `title`,
- a more detailed `description`,
- an `extent`, which can include a spatial extent expressed in WGS84, as well as a temporal extent. For a coverage this represents its envelope, and may cover additional dimensions.
- a list of `crs` in which the data is available,
- an (optional) `storageCRS`, representing the native CRS of the data (as defined in OGC API - Common - Part 3: CRS),
- `links` to resources specific to the collection, such as for retrieving it as a coverage, as tiles or as a map.
See the description of coverage resources below for the relation types to use to link to those resources.

TODO: Additional properties should be defined in OGC API - Common - Part 2: Geospatial Data:
- A way to determine the native representation (e.g. for link to the coverage data), either as a property of a link, or a property of a collection.
- The available resolution of the data

### Coverage resources (Coverages: Part 1: Core)

#### Coverage description resource

Resource path: `{datasetAPI}/collections/{collectionId}`

Relation type: `http://www.opengis.net/def/rel/ogc/1.0/collection`

HTTP methods: `GET`

Recommended encodings: JSON, HTML

If a collection is accessible as a coverage, this resource inherits all the properties defined by Common: Part 2,
but the OGC API - Coverages Part 1: Core standard further extends it with the following properties:

- a `RangeType` describing the data values semantics (their components and data type),
- a `DomainSet` describing the domain set (the detailed n-dimensional space covered by the data).

In a JSON representation of this resource, both `RangeType` and `DomainSet` properties are at least available encoded as [CIS JSON](https://docs.opengeospatial.org/is/09-146r6/09-146r6.html#46).

Both of these properties are required for conforming to coverage access for that collection, unless they are available as separate resources.

Whether they are embedded or a separate resources, links using their respective relation type (`http://www.opengis.net/def/rel/ogc/1.0/coverage-rangetype` or
`http://www.opengis.net/def/rel/ogc/1.0/coverage-domainset`) must be present within the `links` property.
If the properties are embedded, the links will be relative and point directly to the property using https://tools.ietf.org/html/rfc6901[RFC 6901 (JSON Pointer)] e.g.,
`#/DomainSet` and `#/RangeType`.

A good reason to define them as separate resources would be if they are complex and consist of a sizable amount of data.

See the [range type](#rangeTypeExample) and [domain set](#domainSetExample) resources below for examples encoding them as CIS JSON.

In addition, as described above the `extent` property must be a full description of the coverage's envelope according to the CIS model, including any extra dimensions
which do not map to either the `spatial` or `temporal` properties described by Common - Part 2.

#### The coverage (including all of its components)

Resource path: `{datasetAPI}/collections/{collectionId}/coverage`

NOTE: though this path should exist, a client should not rely on it as additional representations for this resource may reside elsewhere, e.g. /coverage.tiff or /coverage?f=tiff

Relation type: `http://www.opengis.net/def/rel/ogc/1.0/coverage`

HTTP methods: `GET`

Recommended encodings: CIS JSON, HTML, CIS RDF, CoverageJSON, netCDF, GeoTIFF, PNG

NOTE: The media type `application/json` is reserved for [CIS JSON](https://docs.opengeospatial.org/is/09-146r6/09-146r6.html#46).
Alternative JSON encodings such as CoverageJSON must be differentiated by using an alternative media type.

Returns the coverage, including all components as defined by the CIS model (domain set, range type, range set and metadata), to the extent that they are supported
by the selected representation. A specific representation is selected either via HTTP headers Accept-content, or separate link URLs with different media types.

##### Query parameters (optional conformance classes)

**`subset`**

Allows to retrieve only a subset of the coverage, with well-defined ranges for named axes.

The axis names are determined by the CRS, as defined in the <gml:axisAbbrev> tags.
For EPSG:4326 and CRS84, this should always be `Lat` and `Lon` (case sensitive).
These should also correspond to the domain set definition, and additional axes beyond temporal and spatial
should correspond to their names in the collection description extent definition.

Example: `?subset=Lat(40:50),Lon(10:20)`

Retrieve the subset of the coverage between 40 and 50 degrees North, 10 and 20 degrees East, for a coverage whose domain set and CRS define axes named `Lat` and `Lon`.

**`range-subset`**

Allows to retrieve only a subset of the bands available.

The band names are listed as in the `id` member of the RangeType DataRecord fields.

Example: `?range-subset=B02,B03,B04`

Retrieve only bands with IDs B02, B03 and B04.
A 0-based index can also be used instead, based on the position of the band in the field array of the RangeType DataRecord.
A `*` before or after a list of bands indicate all previous or subsequent bands, respectively.

**`scale-size`**, **`scale-factor`** and **`scale-axes`**

Allows to specify scaling factors to apply to the resolution of the coverage, e.g. to retrieve an overview of the coverage, using one of those three query parameters.

Example: `?scale-size=Lon(800),Lat(400)`

Specify that 800 values along the longitude axis, and 400 values along the latitude axis are desired in the output.

Example: `?scale-factor=2`

Specify that the resolution of the data should be half that of the original data.

Example: `?scale-axes=Lon(2)`

Specify that the resolution of the data along the longitude axis should be half that of the original data, while the resolution along the other axes
remains unchanged.

**`bbox`** (OGC API - Common - Part 2: Geospatial Data)

A bounding box, expressed in WGS84 (westLong,southLat,eastLong,northLat) or WGS84h (westLong,southLat,minHeight,eastLong,northLat,maxHeight) CRS, allowing to retrieve
only a subset of the coverage, with this Common bounding box sub-setting mechanism.

Example: `?bbox=10,40,20,50`

Retrieve the subset of the coverage between 40 and 50 degrees North, 10 and 20 degrees East, regardless of how the domain set is defined.

#### Range set (optional)

Resource path: `{datasetAPI}/collections/{collectionId}/coverage/rangeset`

NOTE: this path is not fixed and not required (follow the link)

Relation type: `http://www.opengis.net/def/rel/ogc/1.0/coverage-rangeset`

HTTP methods: `GET`

Recommended encodings: CIS JSON, CIS RDF, CoverageJSON, PNG

NOTE: The media type `application/json` is reserved for [CIS JSON](https://docs.opengeospatial.org/is/09-146r6/09-146r6.html#46).
Alternative JSON encodings such as CoverageJSON must be differentiated by using an alternative media type.

If provided, returns only the range set of the coverage (i.e. the data values), for representations that support describing the values by themselves
without any accompanying description or extra information.

TODO: How could one optionally describe the actual range of the values that one can possibly encounter within the coverage, assuming this is known and not the full extent of the data type?

TODO: How could one map the full range of a type with a linear transformation to such an arbitrary range, given that the raw values themselves could not directly map to a defined unit?
(e.g. as is done in the [GeoPackage tile gridded coverage extension](http://docs.opengeospatial.org/is/17-066r1/17-066r1.html); use cases: populating a GeoPackage from a Coverages API, or serving coverage data from a GeoPackage)

Example CIS JSON rangeSet encoding:

```json
{
   "type" : "RangeSetType",
   "dataBlock" : {
      "type" : "VDataBlockType",
      "values" : [1,2,3,4,5,6,7,8,9]
   }
}
```

#### Range type (optional as a separate resource)

Resource path: `{datasetAPI}/collections/{collectionId}/coverage/rangetype`

NOTE: this path is not fixed and may not exist (follow the link)

Relation type: `http://www.opengis.net/def/rel/ogc/1.0/coverage-rangetype`

HTTP methods: `GET`

Recommended encodings: CIS JSON (required), CIS RDF

NOTE: The media type `application/json` is reserved for [CIS JSON](https://docs.opengeospatial.org/is/09-146r6/09-146r6.html#46).
Alternative JSON encodings such as CoverageJSON must be differentiated by using an alternative media type.

If provided, returns only the range type of the coverage, i.e. the data values semantics (their components and data type).
A CIS JSON encoding of this resource is required, but may be embedded within the Coverage description resource as the value of the `RangeType` property.
In that case, a link will still exist pointing to `#RangeType`
A reason to make it a separate resource is if the range type is very complex and consists of a sizable amount of data.

<a name="rangeTypeExample"></a>
Example CIS JSON range type encoding:

```json
{
   "type" : "DataRecordType",
   "field" : [
      {
         "type" : "QuantityType",
         "definition" : "ogcType:unsignedInt",
         "uom" : {
            "type" : "UnitReference",
            "code" : "10^0"
         }
     }
  ]
}
```

#### Domain set (optional as a separate resource)

Resource path: `{datasetAPI}/collections/{collectionId}/coverage/domainset`

NOTE: this path is not fixed and may not exist (follow the link)

Relation type: `http://www.opengis.net/def/rel/ogc/1.0/coverage-domainset`

HTTP methods: `GET`

Recommended encodings: CIS JSON (required), CIS RDF

NOTE: The media type `application/json` is reserved for [CIS JSON](https://docs.opengeospatial.org/is/09-146r6/09-146r6.html#46).
Alternative JSON encodings such as CoverageJSON must be differentiated by using an alternative media type.

If provided, returns only the domain set of the coverage (the detailed n-dimensional space covered by the data).
A CIS JSON encoding of this resource is required, but may be embedded within the Coverage description resource as the value of the `DomainSet` property.
In that case, a link will still exist pointing to `#DomainSet`
A reason to make it a separate resource is if the domain set is very complex and consists of a sizable amount of data,
and optionally if the API supports subsetting the domain set itself.

<a name="domainSetExample"></a>
Example CIS JSON domain set encoding:

```json
{
   "type" : "DomainSetType",
   "generalGrid" : {
      "type": "GeneralGridCoverageType",
      "srsName" : "http://www.opengis.net/def/crs/OGC/0/Index2D",
      "axisLabels": [ "i", "j" ],
      "axis": [
         {
            "type": "IndexAxisType",
            "axisLabel": "i",
            "lowerBound": 0,
            "upperBound": 2
         },
         {
            "type": "IndexAxisType",
            "axisLabel": "j",
            "lowerBound": 0,
            "upperBound": 2
         }
      ]
   }
}
```

##### Query parameters (optional conformance classes)

**`subset`**

Allows to retrieve only a subset of the domain set, with well-defined ranges for named axes.

Example: `?subset=Lat(40:50),Lon(10:20)`

Retrieve the domain set of the coverage between 40 and 50 degrees North, 10 and 20 degrees East, for a coverage whose domain set and CRS define axes named `Lat` and `Lon`.

#### Coverage metadata (optional)

Resource path: `{datasetAPI}/collections/{collectionId}/coverage/metadata`

NOTE: this path is not fixed and may not exist

Relation type: `http://www.opengis.net/def/rel/ogc/1.0/coverage-metadata`

HTTP methods: `GET`

If provided, returns the coverage metadata associated with the coverage.
Note that this is *not* general metadata associated with the data, but rather coverage metadata which forms part of the coverage as defined by the CIS standard,
and may be domain specific.

## Using the standard

TODO: Link to published standard, OpenAPI definition, implementations, etc.

Those who want to just see the endpoints and responses can explore generic
OpenAPI definitions in this folder (please paste one of them in the Swagger Editor):

* [ogcapi-coverages/standard/openapi](https://github.com/opengeospatial/ogcapi-coverages/tree/master/standard/openapi)

Implementations of the draft standard are listed at the link below:

[Implementations of the draft specification / demo services](https://github.com/opengeospatial/ogcapi-coverages/blob/master/implementations.adoc)

## Communication

Join the [mailing list](https://lists.ogc.org/mailman/listinfo/coverages.swg) or [![chat at https://gitter.im/opengeospatial/ogcapi-coverages](https://badges.gitter.im/opengeospatial/ogcapi-coverages.svg)](https://gitter.im/opengeospatial/ogcapi-coverages?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Most of the work on the specification takes place in [GitHub issues](https://github.com/opengeospatial/ogcapi-coverages/issues),
so browse there to get a good idea of what is happening, as well as past decisions.

## Additional parts of OGC API - Coverages

* [Part 2: CRS](extensions/crs.adoc)
* [Part 3: Processing](extensions/processing.adoc)

## Contributing

The contributor understands that any contributions, if accepted by the OGC Membership, shall be incorporated into OGC API standards documents and that all copyright and intellectual property shall be vested to the OGC.

The Coverages API Standards Working Group (SWG) is the group at OGC responsible for the stewardship of the standard, but is working to do as much work in public as possible.

* [Coverages API Standards Working Group Charter](charter/OGC-Coverages-SWG-Charter.adoc)
* [Open issues](https://github.com/opengeospatial/ogcapi-coverages/issues)
* [Copy of License Language](https://raw.githubusercontent.com/opengeospatial/ogcapi-coverages/master/LICENSE)


Pull Requests from contributors are welcomed. However, please note that by sending a Pull Request or Commit to this GitHub repository, you are agreeing to the terms in the Observer Agreement https://portal.ogc.org/files/?artifact_id=92169
