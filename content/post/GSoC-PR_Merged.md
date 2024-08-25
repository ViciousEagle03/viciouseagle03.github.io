+++
title = 'GSoC 2024 @OpenAstronomy: Overview of Merged and Pending PRs'
date = 2024-08-24T16:47:02+05:30

+++

![img](/images/PR-Merged.png)

> This blogpost deals with all the PRs that were merged/opened in NDCube/ SunPy / Astropy for completing the project.
---
> The PRs were filed in accordance with the tasks recorded in the GitHub [task-tab](https://github.com/orgs/sunpy/projects/12).

## NDCube 

#### PR #708: [Asdf-Support](https://github.com/sunpy/ndcube/pull/708) 
#### (Merged)
 - PR for supporting the serialization of basic `ndcube.NDCube` objects with the serialization logic written for the below classes
 NDCube, GlobalCoords, ExtraCoords.

#### PR #751: [Add support for the serialization of the ndcube WCS wrappers](https://github.com/sunpy/ndcube/pull/751) 
#### (Yet to be Merged)
 - This PR introduces serialization support for `ndcube` WCS wrappers, including `CompoundLowLevelWCS`, `ResampledLowLevelWCS` and `ReorderedLowLevelWCS`, allowing them to be saved in ASDF format.

#### PR #756: [Add serialization logic for the NDCubeSequence and NDCollection](https://github.com/sunpy/ndcube/pull/756) 
#### (Yet to be Merged)
 - This PR introduces serialization support for `NDCubeSequence` and `NDCollection` objects, enabling their conversion to and from ASDF format.

## Astropy

#### PR #237: [Remove astropy version check](https://github.com/astropy/asdf-astropy/pull/237)
#### (Merged)
 - This PR removes the `astropy` version check, which was previously set to version 5.1, from the codebase. The minimum required version is updated to 5.2, and associated conditional logic in `test_transform.py` is removed.

#### PR #235: [Support serialization of astropy.wcs.WCS objects to ASDF](https://github.com/astropy/asdf-astropy/pull/235)
#### (Yet to be Merged)
 - This PR adds the support for serializing `astropy.wcs.WCS` and `astropy.wcs.wcsapi.SlicedLowLevelWCS` objects to ASDF. This would enable any `ndcube.NDCube` objects to be serialized to ASDF with the underlying wcs as `astropy.wcs.WCS` and any sliced `ndcube.NDCube` objects be serialized to ASDF with proper wcs preservation. 

#### PR #239: [Add serialization logic for uncertainty objects](https://github.com/astropy/asdf-astropy/pull/239)
#### (Yet to be Merged)
 - This PR adds serialization logic for `astropy.nddata.StdDevUncertainty` and `astropy.nddata.UnknownUncertainty` objects. This update enables the serialization of these uncertainty types, ensuring that the uncertainty attribute of the `ndcube.NDCube` object is properly preserved and restored.

## SunPy

#### PR #7686: [ASDF schema update: minor change](https://github.com/sunpy/sunpy/pull/7686)
#### (Merged)
 - This PR updates the ASDF schema by removing incorrect usage of the unsupported `allowAdditionalProperties` validator. This minor change ensures that the schema files for generic_map (versions `1.0.0`, `1.1.0`, and `1.2.0`) are correctly formatted.
