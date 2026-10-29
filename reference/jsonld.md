# Working with JSON-LD

Wrappers for converting, expanding and compacting JSON-LD documents. All
parameters and return values must be JSON strings. Use
[jsonlite::toJSON](https://jeroen.r-universe.dev/jsonlite/reference/fromJSON.html)
and
[jsonlite::fromJSON](https://jeroen.r-universe.dev/jsonlite/reference/fromJSON.html)
to convert between R objects and JSON format. The
[readme](https://github.com/ropensci/jsonld#readme) has basic examples.

## Usage

``` r
jsonld_compact(doc, context, options = NULL)

jsonld_expand(compacted, options = NULL)

jsonld_flatten(doc, context = NULL, options = NULL)

jsonld_frame(doc, frame, options = NULL)

jsonld_from_rdf(rdf, options = list(format = "application/nquads"))

jsonld_to_rdf(doc, options = list(format = "application/nquads"))

jsonld_normalize(doc, options = list(algorithm = "URDNA2015", format =
  "application/nquads"))
```

## Arguments

- doc:

  a URL or literal string with JSON-LD document

- context:

  a URL or literal string with JSON-LD context

- options:

  named list with advanced options

- compacted:

  a URL or literal string with JSON message

- frame:

  a URL or literal string with JSON-LD frame

- rdf:

  string with RDF text

## Examples

``` r
# Example from https://github.com/digitalbazaar/jsonld.js#quick-examples
doc <- '{
  "http://schema.org/name": "Manu Sporny",
  "http://schema.org/url": {"@id": "http://manu.sporny.org/"},
  "http://schema.org/image": {"@id": "http://manu.sporny.org/images/manu.png"}
}'

context <- '{
  "name": "http://schema.org/name",
  "homepage": {"@id": "http://schema.org/url", "@type": "@id"},
  "image": {"@id": "http://schema.org/image", "@type": "@id"}
}'

# Compact and expand:
(out <- jsonld_compact(doc, context))
#> {
#>   "@context": {
#>     "name": "http://schema.org/name",
#>     "homepage": {
#>       "@id": "http://schema.org/url",
#>       "@type": "@id"
#>     },
#>     "image": {
#>       "@id": "http://schema.org/image",
#>       "@type": "@id"
#>     }
#>   },
#>   "image": "http://manu.sporny.org/images/manu.png",
#>   "name": "Manu Sporny",
#>   "homepage": "http://manu.sporny.org/"
#> } 
(expanded <- jsonld_expand(out))
#> [
#>   {
#>     "http://schema.org/url": [
#>       {
#>         "@id": "http://manu.sporny.org/"
#>       }
#>     ],
#>     "http://schema.org/image": [
#>       {
#>         "@id": "http://manu.sporny.org/images/manu.png"
#>       }
#>     ],
#>     "http://schema.org/name": [
#>       {
#>         "@value": "Manu Sporny"
#>       }
#>     ]
#>   }
#> ] 

# Convert between JSON and RDF:
cat(nquads <- jsonld_to_rdf(doc))
#> _:b0 <http://schema.org/image> <http://manu.sporny.org/images/manu.png> .
#> _:b0 <http://schema.org/name> "Manu Sporny" .
#> _:b0 <http://schema.org/url> <http://manu.sporny.org/> .
jsonld_from_rdf(nquads)
#> [
#>   {
#>     "@id": "_:b0",
#>     "http://schema.org/image": [
#>       {
#>         "@id": "http://manu.sporny.org/images/manu.png"
#>       }
#>     ],
#>     "http://schema.org/name": [
#>       {
#>         "@value": "Manu Sporny"
#>       }
#>     ],
#>     "http://schema.org/url": [
#>       {
#>         "@id": "http://manu.sporny.org/"
#>       }
#>     ]
#>   }
#> ] 

# Other utilities:
jsonld_flatten(doc)
#> [
#>   {
#>     "@id": "_:b0",
#>     "http://schema.org/image": [
#>       {
#>         "@id": "http://manu.sporny.org/images/manu.png"
#>       }
#>     ],
#>     "http://schema.org/name": [
#>       {
#>         "@value": "Manu Sporny"
#>       }
#>     ],
#>     "http://schema.org/url": [
#>       {
#>         "@id": "http://manu.sporny.org/"
#>       }
#>     ]
#>   }
#> ] 
cat(jsonld_normalize(doc))
#> _:c14n0 <http://schema.org/image> <http://manu.sporny.org/images/manu.png> .
#> _:c14n0 <http://schema.org/name> "Manu Sporny" .
#> _:c14n0 <http://schema.org/url> <http://manu.sporny.org/> .
```
