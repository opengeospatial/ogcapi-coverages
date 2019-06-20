1 Introduction
==============

1.1 General
-----------

This [OGC API Coverages](https://github.com/opengeospatial/ogc_api_coverages) specification establishes how to access coverages as defined by the [Coverage Implementation Schema (CIS) 1.1](http://docs.opengeospatial.org/is/09-146r6/09-146r6.html) through [OpenAPI](https://www.openapis.org/).

Current scope:

* Only gridded coverages are addressed, not MultiPoint/Curve/Surface/SolidCoverages. Reason is that gridded coverages receive most attention today.
* Only CIS 1.1 GeneralGridCoverage is addressed, other coverage types will follow later. Reason is to have a first version early which shows and allows to evaluate the principles.
* Only coverage extraction functionality is considered, not general processing (as is provided with Web Coverage Service (WCS) extensions such as the Processing Extension). Exceptions from this rule are subsetting including band subsetting, scaling, and CRS conversion and data format encoding, given their practical relevance.
* Subsetting is considered in the query component only for now. As typically all dimensions in a coverage are of same importance subsetting might not fit perfectly in the hierarchical nature of the path component. Further, subsetting may reference any axis and leave out any other, which makes positional parameters unsuitable. Nevertheless subsetting in the path component particularly limited to fixed subsets might be considered in a future version.

As such, the functionality provided below resembles [OGC Web Coverage Service (WCS) 2.1 Interface Standard - Core](http://docs.opengeospatial.org/is/17-089r1/17-089r1.html).

1.2 Background Information: WCS Service Model
-----------
OGC WCS 2 has an internal model of its storage organization based on which the classic operations GetCapabilities, DescribeCoverage, and GetCoverage can be explained naturally.
This model consists of a single CoverageOffering resembling the complete WCS data store. It holds some service metadata describing service qualities (such as WCS extensions, encodings, CRSs, and interpolations supported, etc.). At its heart, this offering holds any number of OfferedCoverages. These contain the coverage payload to be served, but in addition can hold coverage-specific service-related metadata (such as the coverage's Native CRS).

![WCS 2 internal storage model](http://external.opengeospatial.org/twiki_public/pub/CoveragesDWG/CoveragesBigPicture/WCS-internal-organization.png)

Discussion has shown that the OpenAPI functionality also assumes underlying service and object descriptions, so a convergence seems possible. In any case, it will be advantageous to have a similar "mental moel" of the server store organization on hand to explain the various functionalities introduced below.


2 Principles
============

[OpenAPI](https://www.openapis.org/) establishes URL-based access patterns, as defined by [RFC 3986 "Uniform Resource Identifier (URI): Generic Syntax"](https://tools.ietf.org/html/rfc3986), [RFC 3987 "Internationalized Resource Identifiers (IRIs)"](https://tools.ietf.org/html/rfc3987), and [RFC 6570 "URI Template"](https://tools.ietf.org/html/rfc6570) following a syntax like
        http://www.acme.com/path/to/resource/{id}{?query,parameters}
whereby

* `/path/to/resource/{id}` defines the local path to the resource to be retrieved on the node identified by the head part, http://www.acme.com.
* the `{?query,parameters}` represent key/value pairs with further parameters passed to the request.

As a general rule,

* accessing a coverage component is done in the path section,
* subsetting is done in the query parameter section
* format encoding is controlled via HTTP headers

As the coverage model normatively is given by the corresponding XML schema (JSON and RDF are built in sync with XML) specification of the [OpenAPI](https://www.openapis.org/) access paths follows this schema. Note, though, that OWS Common, while normatively
referenced in CIS, is not followed by [OpenAPI](https://www.openapis.org/), so here deviations will occur.

For path expressions abbreviations (i.e., aliases) may be defined for convenience.

3 [OGC API Coverages](https://github.com/opengeospatial/ogc_api_coverages) Requests
===================================================================================

This clause defines the basics of accessing OGC coverages via OpenAPI.

3.1 General
-----------

Requirement:
A service offering [OGC API Coverages](https://github.com/opengeospatial/ogc_api_coverages) access shall be accessible via an URL as service endpoint (subsequently referred to as Base URL).

Example:
        http://acme.com/oapi

Requirement:
Coverage access paths, as defined in the next clause, are concatenated to the Base URL via a "/" character, followed (optionally) by a query part.

Example:
        http://acme.com/oapi/collections/{collectionid}/coverages/{coverageid}/rangeset

A request may be denied for server-specific reasons, such as quota.

3.2 Coverage Access Paths
-------------------------

In this clause, the path component of [OGC API Coverages](https://github.com/opengeospatial/ogc_api_coverages) access is established.

Access paths follow the XML Schema of [CIS](http://docs.opengeospatial.org/is/09-146r6/09-146r6.html) in their structure.

To access coverage service constituents, such as formats supported, OGC 14-121 Web Query Service provides guidance, see https://github.com/opengeospatial/ogc_api_coverages/blob/master/CIS%2BWCS-standards/14-121_Web-Query-Service_2016-06-19.pdf .

3.3 Coverage Query Parameters
-----------------------------

In this clause, the query component of [OGC API Coverages](https://github.com/opengeospatial/ogc_api_coverages) access is established.

Subsetting parameters may be mixed in a query part, in no particular order.

3.3.1 Coverage Subsetting
-------------------------

Without any subsetting parameter the whole coverage extent is the target resource addressed. If a subsetting operation is provided then the coverage subset indicated is the target resource addressed.

Coverage subsetting is indicated through the SUBSET parameter name. The value following the "=" symbol is built as follows:

    SubsetSpec:            SUBSET = axisName ( intervalOrPoint )
    axisName:              {NCName}
    intervalOrPoint:       interval | point
    interval:              low ,  high
    low:                   point | *
    high:                  point | *
    point:                 {number} | " {text} "    //" = double quote = ASCII code 0x42

where {NCName} is an XML-style identifier not containing ":" (colon) characters, {number} is an integer or floating-point number, and {text} is some general ASCII text (such as a time and date notation in ISO 8601). A coverage access request may have any number of SUBSET parameters, however for each axis of the coverage at most one SUBSET parameter may be provided.

In case of an interval a trim operation is specified, with lower and upper bound. In case of a point a slicing operation is specified. For the detailed semantics of subsetting, trimming, and slicing see [OGC WCS](http://docs.opengeospatial.org/is/17-089r1/17-089r1.html).


3.4 Coverage Encoding
-----------------------

Without any format encoding HTTP header (Accept and Accept-Encoding) a representation of the coverage in its Native Format will be returned. If an encoding HTTP header is provided then a representation of the coverage in the format indicated will be returned.
The syntax for format encoding HTTP headers is defined in [RFC 7231 "Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content"](https://tools.ietf.org/html/rfc7231).

The format chosen must be able to represent the output of the request.

For the detailed semantics of format encoding see [OGC WCS](http://docs.opengeospatial.org/is/17-089r1/17-089r1.html).

4 Examples
==========

This section contains examples for coverage access, subsetting, and encoding. It assumes a service endpoint of http://acme.com/oapi/ .

4.1 Service Metadata
--------------------

The first part is about service metadata. Currently this is wild speculation as it will mainly be defined through [OGC API Common](https://github.com/opengeospatial/oapi_common).

* http://acme.com/oapi/servicedescription/formatssupported  -- returns a list of formats in which coverage representations can be requested

4.2 Coverage Finding
--------------------

* http://acme.com/oapi/collections  -- returns a list of all collection identifiers
* http://acme.com/oapi/collections/{collectionid}  -- returns the description of a specific collection
* http://acme.com/oapi/collections/{collectionid}/coverages  --  returns a list of all coverage identifiers included in a specific collection
* http://acme.com/oapi/collections/{collectionid}/coverages/{coverageid}  --  returns a specific coverage (see format encoding for ways to retrieve in specific formats)
* http://acme.com/oapi/collections/{collectionid}/coverages?bbox=160.6,-55.95,-170,-25.89  -- returns a list of all coverages intersecting in a specific collection that is in the New Zealand economic zone (any time, any elevation, etc.); the CRS in which the bbox parameters are expressed is WGS84, defined in Common

Notes:

* all list results include links (cf. Common)
* collections can be optional (use case: 1 coverage served)
  * http://acme.com/oapi/coverages -- returns the list of all coverage identifiers on this service 
  * http://acme.com/oapi/coverages/{coverageid} -- returns (complete) coverage with name {coverageid}
* "coverages" is a specialization of "items"; further names could be defined in future (RectifiedGridCoverage, features, ...)
* "description of collection" to be clarified (defined in Common?)
* bbox is Common syntax
  * in Core, no subsetting on vertical and time coordinates 
  * horizontal coordinates: all filtering is evaluated in WGS84 (may require bounding box translation before intersecting)
  * if filtering dimension is not present in coverage to be tested (due to above restriction not an issue currently): coverage will be discarded from result

4.3 Coverage Access
-------------------

The second part is about coverage access, which (as described earlier) is driven by the coverage structure and, hence, given:

* http://acme.com/oapi/collections/{collectionid}/coverages/{coverageid}/description -- whole coverage description consisting of rangetype, domainset, metadata
* http://acme.com/oapi/collections/{collectionid}/coverages/{coverageid}/domainset  -- returns the coverage's domain set definition
* http://acme.com/oapi/collections/{collectionid}/coverages/{coverageid}/rangetype  -- returns the coverage's range type information (i.e., a description of the data semantics)
* http://acme.com/oapi/collections/{collectionid}/coverages/{coverageid}/metadata  -- returns the coverage's metadata (may be empty)
* http://acme.com/oapi/collections/{collectionid}/coverages/{coverageid}/rangeset  -- returns the coverage's range set, i.e., the actual values in the coverage's Native Format

4.4 Coverage Subsetting
-----------------------

The third part is about query parameters:

* http://acme.com/oapi/collections/{collectionid}/coverages/{coverageid}?SUBSET=Lat(40,50)&SUBSET=Long(10,20)  -- returns a coverage cutout between (40,10) and (50,20), as multipart coverage
* http://acme.com/oapi/collections/{collectionid}/coverages/{coverageid}/rangeset?SUBSET=Lat(40,50)&SUBSET=Long(10,20)  -- returns a coverage cutout between (40,10) and (50,20), in the coverage's Native Format
* http://acme.com/oapi/collections/{collectionid}/coverages/{coverageid}?SUBSET=time("2019-03-27")  -- returns a coverage slice at the timestamp given (in case the coverage is Lat/Long/time the result will be a 2D image)


5 Open Issues
=============

* Establish service parameter access, based on [OGC API Common](https://github.com/opengeospatial/oapi_common)
* What is the output format of items typically returned as XML or JSON, such as DomainSet and RangeType? Should maybe FORMAT be applicable here as well? If so, should it be listed as a possible output format (which might be confusing)?
* [OGC 14-121 Web Query Service](https://github.com/opengeospatial/ogc_api_coverages/blob/master/CIS%2BWCS-standards/14-121_Web-Query-Service_2016-06-19.pdf) provides a definition of path syntax, but adds more functionality (such as selection predicates), all based on the XPath standard. Such extra functionality might come handy.
