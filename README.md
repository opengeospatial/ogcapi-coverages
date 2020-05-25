# OGC API - Coverages

This GitHub repository contains [OGC](http://opengeospatial.org)'s
standard for querying geospatial information on the web, "OGC API - Coverages".

OGC API standards define modular API building blocks to spatially enable Web
APIs in a consistent way. [OpenAPI](http://openapis.org) is used to define the
reusable API building blocks with responses in JSON and HTML.

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

This [OGC API - Coverages](https://github.com/opengeospatial/ogc_api_coverages) specification establishes how to access coverages as defined by the [Coverage Implementation Schema (CIS) 1.1](http://docs.opengeospatial.org/is/09-146r6/09-146r6.html) through [OpenAPI](https://www.openapis.org/).


OGC API - Coverages provides access to collections of geospatial data as coverages.

```
GET /collections
```

Lists the collections of data or coverages on the server that can be queried,
and each describes basic information about the geospatial data collection,
like its id and description, as well as the spatial and temporal extents of all
the data contained.

```
GET /collections?bbox=160.6,-55.95,-170,-25.89
```

Lists the collections intersecting with the New Zealand economic zone (any
time, any elevation, etc.). The CRS in which the `bbox` parameters are
expressed is WGS84, defined in OGC API - Common and likely in a future OGC
API - Catalog.

```
GET /collections/{coverageid}
```

Returns the description of a specific collection including links to available
sub-paths like /coverage, /tiles, or /map as defined in OGC API - Common.

```
GET /collections/{coverageid}/coverage
```

Returns a general description of the coverage represented by `coverageid`
containing the coverage's envelope (the full domainset might be huge and
thus is omitted here), rangetype, and service metadata like the coverage's
native format

```
GET /collections/{coverageid}/coverage/domainset
GET /collections/{coverageid}/coverage/rangetype
GET /collections/{coverageid}/coverage/metadata
GET /collections/{coverageid}/coverage/rangeset
GET /collections/{coverageid}/coverage/description
GET /collections/{coverageid}/coverage/all
```

These are the coverage paths returning the respective component as described
by CIS. `/description` returns domainset, rangetype, and metadata (but not the
rangeset) whereas `/all` returns all four components.

```
GET /collections/{coverageid}/coverage/rangeset?subset=Lat(40,50)&subset=Long(10,20)
```

The `subset` parameter may be applied on various of the paths. The example
above returns a coverage cutout between (40,10) and (50,20), in the
coverage's Native Format.

## Using the standard

TODO: Link to published standard, OpenAPI definition, implementations, etc.

## Communication

TODO: links to mailing lists, issues, etc.

## Additional parts of OGC API - Coverages

TODO

## [Contributing](CONTRIBUTING.md)

TODO
