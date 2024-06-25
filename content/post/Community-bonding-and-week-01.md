+++
title = 'Community Bonding and Week 01 Progress'
date = 2024-06-08T02:15:27+05:30

+++

This post documents the planning of the project, my learnings during the community bonding period, and the tasks I completed in my first week.

## Community Bonding Period
After the euphoria of being accepted into GSoC faded, it was time for setting up my workspace and diving back into the codebase after a month. Planning out the project was key, and meeting with my mentors was a big part of that. I was excited for this chat because the mentors had been super helpful and easy to reach during the proposal stage.

## Setting the Stage: Mentor Meet-Up and Project Planning

During the meet-up, we discussed about the best approach to kick-start the development process. It was mostly the mentors discussing about the possible roadmap and as they outlined the project roadmap, I listened carefully, taking notes on important discussions. One of the key decision was to set up clear communication channels and workflows to make sure we all stay on the same page and to ensure I do not vary off of the project or get stuck in a deadend.

Additionally, we agreed to use GitHub projects for project management, making it easier to keep track of tasks and monitor progress. You can check out the GitHub project here: [**Github Project**](https://github.com/orgs/sunpy/projects/12/views/1)

Apart from discussing the project proceedings, I was also tasked with setting up a blog website to document my GSoC journey and share updates with the community. I decided to use Hugo to create the blog website and deployed it through GitHub pages. Additionally, I was asked to share the RSS feed of my blog to ensure that project updates are easily accessible to everyone.

![](/images/tasks.png)

-------------------------------------------------

# Week [01]: First Coding Week

## A brief overview about my project

My projects aims to enable the storage and retrieval of NDCube objects using the ASDF file format. Currently, ndcube lacks native support for saving NDCube objects to files and loading them back, limiting its usability. To address this, the project will implement convertors and schema definitions for NDCube objects and related classes ensuring robust serialization and deserialiazation. The first phase of my project requires the complete imlementation of serialization of an **ndcube.NDCube** object when `.data` is a numpy array and `.wcs` is a **gwcs.WCS object**.

## The Prototype

In our meeting, we discussed the initial steps,  which centered around laying the groundwork for serialization support for the NDCube object, particularly focusing on establishing basic functionality. This included implementing the convertor class, registering the convertors as ASDF extensions, and ensuring they were readily available via entry points. Getting a good grasp of the whole serialization process took some dedicated reading through the ASDF docs 🧐.

   - Convertor Class: I implemented the convertor class, including the `to_yaml_tree` and `from_yaml_tree` methods. The former converts NDCube object attributes into a single node object suitable for serialization. Now, here's the super cool part the `.wcs` attribute of the NDCube object, being a complex object, is accommodated within the node, as the node is permitted to contain nested complex objects. 
   _Viola!_, so I was able to leverage the existing convertor for GWCS objects which made the process somewhat less complicated. The latter method is exactly the opposite, it accepts a single node object from parsed YAML and returns the corresponding NDCube object.

   - Schema and Manifests: The Schema was designed to validate the presence, and the datatype of the validator properties for the NDCube object. The manifest was implemented for acheiving the resource mapping of the schema.

   - ASDF Integration: In entry_point.py, I've implemented get_resource_mapping to retrieve mappings for schema and manifest files, required for ASDF file validation and identifying NDCube objects and the get_extension is implemented to retrieve extensions , here the extension through which the ndcube convertor was registered

## Code Demonstration

### NDCube initialised with gwcs.WCS object as its '.wcs' attribute
>   
    import numpy as np
    import asdf
    from astropy.time import Time
    from ndcube import NDCube
    from ndcube.extra_coords import QuantityTableCoordinate, TimeTableCoordinate
    import astropy.units as u


    energy = np.arange(10) * u.keV
    time = Time('2020-01-01 00:00:00') + np.arange(10) * u.s
    energy_coord = QuantityTableCoordinate(energy, names='energy', physical_types='em.energy')
    time_coord = TimeTableCoordinate(time, names='time', physical_types='time')

    # Initialize the GWCS object
    wcs = (time_coord & energy_coord).wcs
    data = np.random.rand(len(time), len(energy))

    # Initialize the NDCube
    ndcube = NDCube(data, wcs=wcs)

    with asdf.AsdfFile() as af:
        af.tree['ndcube'] = ndcube
        af.write_to('ndcube.asdf')

### Serialized ASDF File

``` 
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
    extension_uri: asdf://sunpy.org/extensions/ndcube-0.1.0
    software: !core/software-1.0.0 {name: ndcube, version: 2.3.dev224+g04409b5.d20240605}
  - !core/extension_metadata-1.0.0
    extension_class: asdf_astropy._manifest.CompoundManifestExtension
    extension_uri: asdf://astropy.org/core/extensions/core-1.5.0
    software: !core/software-1.0.0 {name: asdf-astropy, version: 0.5.0}
  - !core/extension_metadata-1.0.0
    extension_class: asdf.extension._manifest.ManifestExtension
    extension_uri: asdf://asdf-format.org/transform/extensions/transform-1.5.0
    software: !core/software-1.0.0 {name: asdf-astropy, version: 0.5.0}
  - !core/extension_metadata-1.0.0
    extension_class: asdf.extension._manifest.ManifestExtension
    extension_uri: asdf://asdf-format.org/astronomy/gwcs/extensions/gwcs-1.2.0
    software: !core/software-1.0.0 {name: gwcs, version: 0.20.0}
ndcube: &id001 !<tag:sunpy.org:ndcube/ndcube/NDCube-0.1.0>
  data: !core/ndarray-1.0.0
    source: 0
    datatype: float64
    byteorder: little
    shape: [10, 10]
  extra_coords: !<tag:sunpy.org:ndcube/extra_coords/extra_coords/ExtraCoords-0.1.0>
    dropped_tables: []
    ndcube: *id001
  wcs: !<tag:stsci.edu:gwcs/wcs-1.2.0>
    name: ''
    pixel_shape: null
    steps:
    - !<tag:stsci.edu:gwcs/step-1.1.0>
      frame: !<tag:stsci.edu:gwcs/frame-1.0.0>
        axes_names: ['', '']
        axes_order: [0, 1]
        axes_type: [PIXEL, PIXEL]
        axis_physical_types: ['custom:PIXEL', 'custom:PIXEL']
        name: PixelFrame
        naxes: 2
        unit: [!unit/unit-1.0.0 pixel, !unit/unit-1.0.0 pixel]
      transform: !transform/concatenate-1.2.0
        forward:
        - !transform/tabular-1.2.0
          bounds_error: false
          fill_value: .nan
          inputs: [x]
          lookup_table: !unit/quantity-1.1.0
            unit: !unit/unit-1.0.0 s
            value: !core/ndarray-1.0.0
              source: 1
              datatype: float64
              byteorder: little
              shape: [10]
          method: linear
          outputs: [y]
          points:
          - !unit/quantity-1.1.0
            unit: !unit/unit-1.0.0 pixel
            value: !core/ndarray-1.0.0
              source: 2
              datatype: float64
              byteorder: little
              shape: [10]
        - !transform/tabular-1.2.0
          bounds_error: false
          fill_value: .nan
          inputs: [x]
          lookup_table: !unit/quantity-1.1.0
            unit: !unit/unit-1.0.0 keV
            value: !core/ndarray-1.0.0
              source: 3
              datatype: float64
              byteorder: little
              shape: [10]
          method: linear
          outputs: [y]
          points:
          - !unit/quantity-1.1.0
            unit: !unit/unit-1.0.0 pixel
            value: !core/ndarray-1.0.0
              source: 4
              datatype: float64
              byteorder: little
              shape: [10]
        inputs: [x0, x1]
        outputs: [y0, y1]
    - !<tag:stsci.edu:gwcs/step-1.1.0>
      frame: !<tag:stsci.edu:gwcs/composite_frame-1.0.0>
        frames:
        - !<tag:stsci.edu:gwcs/temporal_frame-1.0.0>
          axes_names: [time]
          axes_order: [0]
          axis_physical_types: [time]
          name: TemporalFrame
          reference_frame: !time/time-1.1.0 2020-01-01 00:00:00.000
          unit: [!unit/unit-1.0.0 s]
        - !<tag:stsci.edu:gwcs/frame-1.0.0>
          axes_names: [energy]
          axes_order: [1]
          axes_type: [CUSTOM]
          axis_physical_types: [em.energy]
          name: CoordinateFrame
          naxes: 1
          unit: [!unit/unit-1.0.0 keV]
        name: CompositeFrame
      transform: null
...
�BLK 0                             [TY*ߝ��R�4p}Q����?��#���?ȩȏ��?���#��?��ҩ��?*B��y�?ʱ�tv��?������?�fEp�X�?k1^}��?=���A�?�]����?
�֜���?���H�?��)9�?{juoPW�?p�:����?0�T9��?w�A��?pWݫ	%�?�K#)cB�?�uv����?V����?���^�.�?��2���?���eQ�?^߁}���?�$6mwp�?�&�"��?N�;�[�?4�؅J3�?
�� ��? =>��ܨ?��#%�?J6F=s��?E��}.�?°���
�?����Qa�?;��0�?vM���?q��ǖ'�?�<5��? G]�X�?��.���?��ɐ?�?j��G�L�?<��&��? ��q];I?�̱���?X�Dt�v�?Pҧ���?0�=�E��?����? O>>tGj?D.���#�?p�N����?,$�V)��?j��xƦ�?�v�h�?�"�
�p�?7S�0�?���ۢ`�?8YI�yְ?"E��o��?J�G�8�?�W�����?����ـ�?t���o�?sg���?h�Ȝ���?�)�=g�?e/B/�?�2�;�?�~�xT��?s�S���?t�|1.��?�n��ʳ?��/�؛�?jd����?�oB���?Lz09��?�����?��ga��?`������?���kW��?���P7�?�|�F@�?Oq���?d���$�?>�e�~�?�t�(�?�Ԓ���?<�P����?���a�?�Q΢���?����?Ѳ�#[[�?� �^�X�?jC�����?�mJA��?�BLK 0               P       P       P?�N��{I�?N"F98        �    �?`������?     @�	    @h�����@     @������@x�����@    "@�BLK 0               P       P       P�����Z
�η~�ZH              �?       @      @      @      @      @      @       @      "@�BLK 0               P       P       P�����Z
�η~�ZH              �?       @      @      @      @      @      @       @      "@�BLK 0               P       P       P�����Z
�η~�ZH              �?       @      @      @      @      @      @       @      "@#ASDF BLOCK INDEX
%YAML 1.1
---
- 4304
- 5158
- 5292
- 5426
- 5560
...

```

## GSoC Week One: Reflections
My first week with GSoC has been a thrilling ride, full of learning and growth. The support from the community has been incredible. Looking forward to what lies ahead!🚀

> Perseverance is not a long race; it is many short races one after the other.