# Specification for LayeredGeoJSON 

__Authors__
- Eduard POROSNICU, European Organisation for the Safety of Air Navigation (EUROCONTROL)

__Abstract__

LayeredGEOJson is an extension to GeoJSON that provides a vertical layer extent to all standard GeoJSON geometries. In addition, it also provides the possibility to specify a horizontal "circle" extent for a Point geometry.

__Contents__
- [Introduction](#introduction)
  * [Scope and purpose](#scope-and-purpose)
  * [Example](#example)
  * [Definitions](#definitions)
- [LayeredGEOJson objets](#LayeredGEOJson-objets)
  * [Point with (vertical) layer](#Point-with-layer)
  * [Point with horizontal (circle) extent](#Point-with-radius-Circle)
  * [LineString with (vertical) layer](#LineString-with-layer)
  * [Polygon with (vertical) layer](#Polygon-with-layer)
  * [MultiPoint with (vertical) layer](#MultiPoint-with-layer)
  * [MultiLineString with (vertical) layer](#MultiLineString-with-layer)
  * [MultiPolygon with (vertical) layer](#MultiPolygon-with-layer)
  * [GeometryCollection with (vertical) layers](#GeometryCollection-with-layers)
- [Compatibility aspects](#compatibility-aspects)
<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>
        
# Introduction
## Scope and purpose
The purpose of this extension is to enable the use of GeoJSON for coding common airspace structures defined in the aeronautical data domain. In particular, it is intended to be used for the coding of Unmanned Airspace System (UAS) GeoZones.
The GeoJSON standard [RFC 7946](https://tools.ietf.org/html/rfc7946) geometries (Point, LineString, Polygon, etc.) allow the coding of horizontal projections, but does not allow to specify a vertical extent of these geometrical features. While it is possible to declare the vertical extent as feature properties, that would have two limitations:
- it would not allow to specify a different vertical extent for each member of a GeometryCollection. This is needed for airspace structures that are composed of multiple volumes, for example in the form of an "inverted wedding cake"; this may be a common airspace structure for GeoZones;
- it would not facilitate the rendering of such areas in a 3D view, as the naming and location of the vertical layer would be at the discretion of each user community.
Thus, this extension of the GeoJSON geometries is proposed, enabling the coding of upper/lower limits for each GeoJSON geometry. The scope of this extension includes all GeoJSON geometries: Point, LineString, Polygon, MultiPoint, ... and GeometryCollection. However, the most common use is expected to include Polygon, MultiPolygon and GeometryCollection.
In addition, for the Point geometries, this extension enables the provision of a radius value. This re-uses the [Microsoft Azure Maps GeoJSON extension](https://learn.microsoft.com/en-us/azure/azure-maps/extend-geojson) solution for Circle.
## Example
```JSON
...
{
  "type": "Polygon",
  "coordinates": [
    [
      [2.585866,49.029301],
      [2.610414,48.983358],
      [2.731263,48.987301],
      [2.704141,49.044704],
      [2.585866,49.029301]
    ]
  ],
  "layer": {
    "upper": 150,
    "upperReference": "AMSL",
    "lower": 50,
    "lowerReference": "AMSL"
    "uom": "m",
   }
}
...
```
## Definitions
JavaScript Object Notation (JSON), and the terms “object”, “name”, “value”, “array”, and “number”, are defined in [IETF RTC 4627](https://tools.ietf.org/html/rfc4627).

# LayeredGeoJSON objets
An additional complex __"layer"__ property is added to all standard GeoJSON geometry types (Point, LineString, Polygon, MultiPoint, MultiLineString, MultiPolygon and GeometryCollection).

## Vertical layer object
The __layer__ object has the following properties, in this order:
- __upper__ = _The value of the upper limit of the airspace layer expressed in metres (m) or feet (ft), in relation with the vertical datum specified in the  __upperReference__ property. The __upper__ value is a double. A positive value is interpreted as meaning "above" the reference surface._
- __upperReference__ = _A code indicating a vertical reference system. Allowed values_
  * ___AGL___ = _Above ground level (or above water surface, as applicable)_
  * ___AMSL___ = _Above Mean Sea Level_
  * ___WGS84___ = _Above the surface of the WGS-84 ellipsoid (Ellipsoidal height)_
- __lower__ = _The value of the lower limit of the airspace layer expressed in metres (m) or feet (ft), in relation with the vertical datum specified in the  __lowerReference__ property. The __lower__ value is a double. A positive value is interpreted as meaning "above" the reference surface._
- __lowerReference__ = _A code indicating a vertical reference system. The same values as for __upperReferemnce__ may be used_
- __uom__ = _The unit of measurement in which the __upper__ and __lower__ values are expressed. Allowable values:_
  * ___m___ = _metres_
  * ___ft___ = _feet_

## Point with layer
In a GeoJSON __geometry__ of type "Point", the __layer__ property appears immediately after the "coordinates" position:
```JSON
...
{
    "type": "Point",
    "coordinates": [100.0, 0.0],
     "layer": {
        "upper": 150,
        "upperReference": "AMSL",
        "lower": 50,
        "lowerReference": "AMSL"
   }
}
...
```

## Point with radius (Circle)
In a GeoJSON __geometry__ of type "Point", it is also possible to add an __extent__ property, as a sibling of the "coordinates" property and it must have two sub-properties:
- "subType" with fixed value "Circle"
- "radius" expressed in metres
```JSON
...
{
    "type": "Point",
    "coordinates": [100.0, 0.0],
     "layer": {
        "upper": 150,
        "upperReference": "AMSL",
        "lower": 50,
        "lowerReference": "AMSL"
   },
   "extent": {
      "subType": "Circle",
      "radius": 10000.0
   }
}
...
```

## LineString with layer
In a GeoJSON __geometry__ of type "LineString", the __layer__ property appears immediately after the "coordinates" property:
```JSON
...
{
      "type": "LineString",
      "coordinates": [
           [-170, 10],
           [170, 11]
     ],
     "layer": {
        "upper": 150,
        "upperReference": "AMSL",
        "lower": 50,
        "lowerReference": "AMSL"
   }
}
...
```


## Polygon with layer
In a GeoJSON __geometry__ of type "Polygon", the __layer__ property appears immediately after the "coordinates" array:
```JSON
...
{
    "type": "Polygon",
    "coordinates": [[100.0, 0.0],[0, 100.0], [100.0, 100.0]]
     "layer": {
        "upper": 150,
        "upperReference": "AMSL",
        "lower": 50,
        "lowerReference": "AMSL"
   }
}
...
```

## MultiPoint with layer
In a GeoJSON __geometry__ of type "MultiPoint", the __layer__ property appears immediately after the "coordinates" array:
```JSON
...
{
    "type": "MultiPoint",
    "coordinates": [[100.0, 0.0],[0, 100.0], [100.0, 100.0]],
    "layer": {
        "upper": 150,
        "upperReference": "AMSL",
        "lower": 50,
        "lowerReference": "AMSL"
   }
}
...
```

## MultiLineString with layer
In a GeoJSON __geometry__ of type "MultiLineString", the __layer__ property appears immediately after the "coordinates" property:
```JSON
...
{
      "type": "MultiLineString",
      "coordinates": [
          [
              [100.0, 0.0],
              [101.0, 1.0]
          ],
          [
              [102.0, 2.0],
              [103.0, 3.0]
          ]
      ],
     "layer": {
        "upper": 150,
        "upperReference": "AMSL",
        "lower": 50,
        "lowerReference": "AMSL"
   }
}
...
```

## MultiPolygon with layer
In a GeoJSON __geometry__ of type "MultiPolygon", the __layer__ property appears immediately after the "coordinates" array:
```JSON
...
{
    "type": "MultiPolygon",
    "coordinates": [
       [
           [
               [180.0, 40.0], [180.0, 50.0], [170.0, 50.0],
               [170.0, 40.0], [180.0, 40.0]
           ]
       ],
       [
           [
               [-170.0, 40.0], [-170.0, 50.0], [-180.0, 50.0],
               [-180.0, 40.0], [-170.0, 40.0]
           ]
       ]
     ],
     "layer": {
        "upper": 150,
        "upperReference": "AMSL",
        "lower": 50,
        "lowerReference": "AMSL"
   }
}
...
```

## GeometryCollection with layers
In a GeoJSON __geometry__ of type "GeometryCollection", the __layer__ property appears in each of the individual geometry objects that are part of the collection, after the "geometries" array:
```JSON
...
{
  "type": "GeometryCollection",
  "geometries": [{
      "type": "Point",
      "coordinates": [100.0, 0.0],
      "layer": {
        "upper": 150,
        "upperReference": "AMSL",
        "lower": 50,
        "lowerReference": "AMSL"
        }
      }, {
      "type": "LineString",
      "coordinates": [
          [101.0, 0.0],
          [102.0, 1.0]
      ],
      "layer": {
        "upper": 50,
        "upperReference": "AMSL",
        "lower": 0,
        "lowerReference": "AMSL"
        }
   }], 
}
...
```



# Compatibility aspects
The vertical layer data is coded as additional properties in the geometry features, after "coordinates". This is a non-compliance with the GeoJSON [RFC 7946](https://tools.ietf.org/html/rfc7946), which explicitly indicates "__7. GeoJSON Types Are Not Extensible__". However, many GeoJSON implementations are tolerant with such extensions if the additional elements appear after "coordinates". Thus, it is expected that GeoJSON implementations will graciously ignore the layer information and will simply render the horizontal projection of the AeroGeoJSON object.
