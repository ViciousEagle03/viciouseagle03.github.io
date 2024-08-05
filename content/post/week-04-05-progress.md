+++
title = 'GSoC [Week 04-05] Progress'
date = 2024-07-15T11:19:00+05:30

+++

![img](/images/test_img.png)

> This blog post covers all the work done in the fourth and fifth week of Google Summer of Code.

#### Mid term evaluations are nearing!

This week, my focus was on extending the test suite to include examples of loading an `NDCube` backed by various `gwcs.WCS` objects and testing the roundtrip serialization of the NDCube. I needed to verify that the ExtraCoords and GlobalCoords objects are correctly serialized and deserialized. Adding the gWCS test suite presented some challenges, but seeing it function correctly was incredibly rewarding. Completing these tasks was essential to ensure everything was in place for the mid-term evaluation 🚀.

### Adding gWCS Test Suite

This was the most challenging part for me this week. To check the roundtrip serialization of `gwcs.WCS` objects (i.e., ensuring all attributes of the `wcs` attribute of the NDCube are preserved when read and loaded through an ASDF file), I had to write a test suite for NDCube backed by different `gwcs.WCS` objects as the wcs attribute for the NDCube objects which would then be loaded into an NDCube and checked for roundtrip serialization.

> One of the `gwcs.WCS` test object which would be loaded in an NDCube and checked for roundtrip serialization.

    def gwcs_4d_t_l_lt_ln():
        """
        Creates a 4D GWCS object with time, wavelength, and celestial coordinates.

        - Time: Axis 0
        - Wavelength: Axis 1
        - Sky: Axes 2 and 3

        Returns:
            wcs.WCS: 4D GWCS object.
        """

        time_model = models.Identity(1)
        time_frame = cf.TemporalFrame(axes_order=(0, ), unit=u.s,
                                        reference_frame=Time("2000-01-01T00:00:00"))

        wave_frame = cf.SpectralFrame(axes_order=(1, ), unit=u.m, axes_names=('wavelength',))
        wave_model = models.Scale(0.2)

        shift  = models.Shift(-5) & models.Shift(0)
        scale  = models.Scale(5) & models.Scale(20)
        tan = models.Pix2Sky_TAN()
        celestial_rotation = models.RotateNative2Celestial(0, 0, 180)
        cel_model = shift | scale | tan | celestial_rotation
        sky_frame = cf.CelestialFrame(axes_order=(2, 3), name='icrs',
                                        reference_frame=coord.ICRS(),
                                        axes_names=("longitude", "latitude"))

        transform = time_model & wave_model & cel_model

        frame = cf.CompositeFrame([time_frame, wave_frame, sky_frame])
        detector_frame = cf.CoordinateFrame(name="detector", naxes=4,
                                            axes_order=(0, 1, 2, 3),
                                            axes_type=("pixel", "pixel", "pixel", "pixel"),
                                            unit=(u.pix, u.pix, u.pix, u.pix))

        return (wcs.WCS(forward_transform=transform, output_frame=frame, input_frame=detector_frame))


### Checking the ExtraCoords and GlobalCoords

I had to make sure the converters written for these objects preserved all the required parameters when saved to an ASDF file and read from an ASDF file. Doing this was straightforward as my mentors helped me during the process of writing the tests and ensuring good coverage.

### Handling Unsupported Attributes

It is as important to test the supported API as it is to throw a warning to the user when a particular attribute serialization is unsupported. I added warnings for attributes that are not yet supported (such as a sliced `NDCube` and `wcs` attribute of the `NDCube` as the `astropy.wcs.WCS` object), which I plan to support in the future.
