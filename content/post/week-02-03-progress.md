+++
title = 'GSoC [Week 02-03] Progress'
date = 2024-06-23T12:49:36+05:30

+++

This blog post covers all the work done in the second and third week of Google Summer of Code.


In the past weeks, my focus has been on enhancing the serialization support of NDCube by extending its support for ExtraCoords and GlobalCoords. After establishing serialization support for the fundamental attributes of the NDCube object—such as data and wcs—in the ASDF file format, the next logical step was extending this capability to include `ExtraCoords` and `GlobalCoords` object.

## GlobalCoords and ExtraCoords

ExtraCoords: The ExtraCoords attribute expand NDCube's capabilities by incorporating additional spatial or spectral coordinates beyond those defined by the primary WCS. It accommodate complex datasets needing extra dimensions or coordinates, such as spectroscopic data with folded axes.

GlobalCoords: The GlobalCoords attribute are coordinates in an NDCube that provides universal context across the entire dataset and apply uniformly across all axes. It captures metadata such as observation times or global positional coordinates of the NDCube object.

## Extending the Serialization support

Now, `ExtraCoords` can be initialized by specifying a `BaseLowLevelWCS` object and a mapping, or by building it up using one or more lookup tables. To support the latter method, I had to ensure the `lookup_tables` were properly preserved during serialization. For this purpose, I designed the schema and wrote the converter class for the following objects:

- ndcube.extra_coords.extra_coords.ExtraCoords
- ndcube.extra_coords.table_coord.TimeTableCoordinate
- ndcube.extra_coords.table_coord.QuantityTableCoordinate
- ndcube.extra_coords.table_coord.SkyCoordTableCoordinate

The initialization of `GlobalCoords` is more straightforward, which required me to design the schema and write the converter class for the following object:

- ndcube.global_coords.GlobalCoords

> Here's an example of the `ExtraCoords` Schema 
```
%YAML 1.1
---
$schema: "http://stsci.edu/schemas/yaml-schema/draft-01"
id: "asdf://sunpy.org/ndcube/schemas/extra_coords-0.1.0"

title:
Represents the ndcube ExtraCoords object

description:
Represents the ndcube ExtraCoords object

type: object
properties:
wcs:
    tag: "tag:stsci.edu:gwcs/wcs-1.*"
mapping:
    type: array
lookup_tables:
    type: array
    items:
        type: array
        items:
            - oneOf:
            - type: number
            - type: array
            - oneOf:
            - tag: "tag:sunpy.org:ndcube/extra_coords/table_coord/quantitytablecoordinate-0.*"
            - tag: "tag:sunpy.org:ndcube/extra_coords/table_coord/skycoordtablecoordinate-0.*"
            - tag: "tag:sunpy.org:ndcube/extra_coords/table_coord/timetablecoordinate-0.*"
dropped_tables:
    type: array
ndcube:
    tag: "tag:sunpy.org:ndcube/ndcube/ndcube-0.*"

required: [ndcube]
additionalProperties: false
...
```

## What's new: Keeping Up with the Coords

![img](/images/ASDF-ser-new-type.png)

Now, when we save an NDCube object to an ASDF file, the file successfully stores the `ExtraCoords` and `GlobalCoords` information, which is preserved when we deserialize and generate a new NDCube object by reading the file. The only thing that is still not supported is initializing an ExtraCoords object using a mapping and an `astropy.wcs.WCS` object. This feature will be supported in the future when I implement serialization support for `astropy.wcs.WCS` in the asdf-astropy library.

#### Saving an NDCube with `GlobalCoords` and `ExtraCoords` attribute

    with asdf.open('ndcubeglobal_extra.asdf') as af:
        ndcube2 = af.tree['ndcube']

#### The relevant ASDF file block

    extra_coords: !<tag:sunpy.org:ndcube/extra_coords/extra_coords/extracoords-0.1.0>
        dropped_tables: []
        lookup_tables:
        - - 0
        - !<tag:sunpy.org:ndcube/extra_coords/table_coord/timetablecoordinate-0.1.0>
            mesh: false
            names: [time]
            reference_time: !time/time-1.1.0 {base_format: fits, value: '2000-01-01T00:00:00.000'}
            table: !time/time-1.1.0
            base_format: fits
            value: !core/ndarray-1.0.0
                byteorder: little
                datatype: [ucs4, 23]
                shape: [10]
                source: 5
        - - 0
        - !<tag:sunpy.org:ndcube/extra_coords/table_coord/skycoordtablecoordinate-0.1.0>
            mesh: false
            names: [lon, lat]
            table: !<tag:astropy.org:astropy/coordinates/skycoord-1.0.0>
            dec: !<tag:astropy.org:astropy/coordinates/latitude-1.0.0>
                unit: !unit/unit-1.0.0 deg
                value: !core/ndarray-1.0.0
                byteorder: little
                datatype: float64
                shape: [10]
                source: 7
            frame: icrs
            ra: !<tag:astropy.org:astropy/coordinates/longitude-1.0.0>
                unit: !unit/unit-1.0.0 deg
                value: !core/ndarray-1.0.0
                byteorder: little
                datatype: float64
                shape: [10]
                source: 6
                wrap_angle: !<tag:astropy.org:astropy/coordinates/angle-1.0.0> {datatype: float64,
                unit: !unit/unit-1.0.0 deg, value: 360.0}
            representation_type: spherical
        - - 1
        - !<tag:sunpy.org:ndcube/extra_coords/table_coord/quantitytablecoordinate-0.1.0>
            mesh: true
            names: [exposure_time]
            table:
            - !unit/quantity-1.1.0
            unit: !unit/unit-1.0.0 s
            value: !core/ndarray-1.0.0
                byteorder: little
                datatype: float64
                shape: [10]
                source: 8
            unit: !unit/unit-1.0.0 s
        ndcube: *id001
    global_coords: !<tag:sunpy.org:ndcube/global_coords/globalcoords-0.1.0>
        internal_coords:
        name1:
        - custom:physical_type1
        - !unit/quantity-1.1.0 {datatype: float64, unit: !unit/unit-1.0.0 m, value: 1.0}
        name2:
        - custom:physical_type2
        - !unit/quantity-1.1.0 {datatype: float64, unit: !unit/unit-1.0.0 s, value: 2.0}
        ndcube: *id001



During my coding period, my mentors have been incredibly helpful, and huge thanks to [@braingram](https://github.com/braingram) for explaining and ensuring I understood the entire ASDF serialization process thoroughly. I learned a lot about schema design and JSON schema. I was particularly impressed by the [CI workflows](https://docs.github.com/en/actions/using-workflows), when I set up a dedicated schema testing workflow. It showed me how automated testing can catch errors that manual testing might overlook. Although I implemented a straightforward workflow, it go me curious about learning more about CI workflows.

I am currently adding tests for the new serialization support in NDCube, which I plan to finish by the next coding week.

