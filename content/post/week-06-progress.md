+++
title = 'GSoC [Week 06-07] Progress'
date = 2024-07-28T15:44:09+05:30

+++
![img](/images/mid_term.png)
> This blog post covers all the work done in the sixth week of Google Summer of Code.

After having developed the serialization logic for NDCube with wcs as `gWCS`, the next step was to extend this support to handle the serialization of NDCube where the wcs attribute is `astropy.wcs.WCS`. This week my primary focus has been on enabling the serialization of `astropy.wcs.WCS` objects to ASDF. This task involves adding the necessary serialization logic to the asdf-astropy repository.

### Discussions on WCS Serialization

We had a discussion about the process of serializing WCS objects and covered the following points:

#### Complex WCS Types: 
WCS types like tabular and distortion ones are tricky because they involve data tables. Handling these types requires a more sophisticated approach.

#### Approach Discussion: 
We discussed serializing a HDUList object or using `WCS.to_hdu` for the complex WCS types but that seems pretty complex for a first attempt. We agreed on initially not supporting these complex WCS types and just using `WCS.to_header()` for now for supporting the serialization of the basic WCS objects. 

### What's new
So after my PR gets merged asdf-astropy would support the serialization of the `astropy.wcs.WCS` objects to ASDF.
Ideally we would want to detect tabular and distortion WCS types and throw an error if they come up. This way, we’re clear about what’s supported and we agreed on revisiting this part and in the future and to extend this to support the serialization of the complex WCS objects.

> Serialized simple `astropy.wcs.WCS` object to ASDF

    #ASDF 1.0.0
    #ASDF_STANDARD 1.5.0
    %YAML 1.1
    %TAG ! tag:stsci.edu:asdf/
    --- !core/asdf-1.1.0
    asdf_library: !core/software-1.0.0 {author: The ASDF Developers, homepage: 'http://github.com/asdf-format/asdf',
    name: asdf, version: 3.0.1}
    history:
    extensions:
    - !core/extension_metadata-1.0.0
        extension_class: asdf.extension._manifest.ManifestExtension
        extension_uri: asdf://asdf-format.org/core/extensions/core-1.5.0
        software: !core/software-1.0.0 {name: asdf, version: 3.0.1}
    - !core/extension_metadata-1.0.0
        extension_class: asdf.extension._manifest.ManifestExtension
        extension_uri: asdf://astropy.org/astropy/extensions/astropy-1.0.0
        software: !core/software-1.0.0 {name: asdf-astropy, version: 0.6.1.dev10+gdab5b4d.d20240723}
    fits: !<tag:astropy.org:astropy/fits/fitswcs-1.0.0>
    header: {CDELT1: 0.4, CDELT2: 2.0e-11, CDELT3: 0.0055555555555556, CDELT4: 0.0013888888888889,
        CRPIX1: 0.0, CRPIX2: 0.0, CRPIX3: 0.0, CRPIX4: 5.0, CRVAL1: 0.0, CRVAL2: 0.0,
        CRVAL3: 0.0, CRVAL4: 0.0, CTYPE1: TIME, CTYPE2: WAVE, CTYPE3: HPLT-TAN, CTYPE4: HPLN-TAN,
        CUNIT1: min, CUNIT2: m, CUNIT3: deg, CUNIT4: deg, DATEREF: '2020-01-01T00:00:00',
        LATPOLE: 0.0, LONPOLE: 180.0, MJDREF: 58849.0, WCSAXES: 4}
    ...

