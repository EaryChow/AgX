![AgX_vs_Filmic](https://user-images.githubusercontent.com/59176246/228416284-fe8e5a45-2dbb-4edf-bb36-52906c32a813.png)
AgX (Left) vs Filmic (Right)

**What?**


Eary's AgX is an OCIO v2 configuration made with the intention to replace Blender's default config, to give Blender a better color management and image formation for the upcoming Spectral Cycles.

The config was built more specifically for Blender, but other software that supports OCIO v2 should also be able to use it. 

The featuring image formation (view transform) is AgX, which is targeted as a replacement for "Filmic".

"AgX" the name is a pseudo-chemical notation of silver halide, commonly used in photographic film, therefore, AgX is an alias of Filmic.

`AgX` Image formation does two things
- It forms a [0.0, 1.0] closed domain image from the unbounded radiometric-like tristimulus data that modern 3D render engines like Cycles and Eevee produce. 
- It provides smooth chromatic attenuation in the image across challenging use cases including wider gamut rendering, real-camera-produced colorimetry etc.

This config also comes with a different colorspace naming scheme, but with backwards compatibility setup with OCIO v2 feature of aliases, so that texture colorspaces in old .blend files will get auto-converted to the new names. 

One of the frequently asked space names is:
- `Linear Rec.709`, this corresponds to the legacy `Linear`

**Why?**

Because the current Filmic has issues like the Notorious Six, meaning Filmic collapses all colors into six before attenuating to white. Filmic also doesn't have the capability to handle wider gamut render produced by wider working space, spectral rendering, real-camera-produced colorimetry etc. 

![AgtX_vs_Filmic Sweep](https://user-images.githubusercontent.com/59176246/228655449-0b9b5e7b-e962-400f-bfb5-56c104bc7cd9.png)
AgX (Left) vs Filmic (Right)

**How?**

1. Download [the latest version of Eary's AgX](https://codeload.github.com/EaryChow/AgX/zip/refs/heads/main), Replace your current OpenColorIO configuration in Blender with this version.

2. The Blender OpenColorIO configuration directory is located in:

  BLENDER/bin/VERSIONNUMBER/datafiles/colormanagement
  Move the existing colormanagement directory to a backup location, and place the contents of this repository into a new colormanagement directory.

3. From within the Color Management panel, change the View to AgX, and choose the artistic Look of your desire.

EDIT: This version of AgX has been merged to Blender 4.0 main.

**View Transforms**

The config includes the following view transform:
- `Standard` A.K.A `Display's Native` The display device's native colorspace as view transform. Included for compatibility reason.
- `AgX` The Filmic-like sigmoid based image formation with 16.5 stops of exposure range.
- `AgX - HDR 1000 nits` The AgX picture formation for 1000 nits peak display medium, with middle gray set to 18% of 100 nits. Exposure range is the same as regular AgX.
- `False Color` A heat-map-like imagery derived from `AgX`'s formed image. uses BT.2020's CIE 2012 luminance for luminance coefficients evaluation. 

**False Color ranges**

Different from False Color in Blender 3.6 and before, or the Filmic-Blender config, the false color here is a post-formation closed domain evaluation. Therefore, all values below will be linearized 0 to 1 value written in percentage.

[0.0, 1.0] Closed Domain Linear | Color 
---- | ---- 
Low Clip | Black 
0.0001% to 0.05% | Blue
0.05% to 0.5% | Blue-Cyan
0.5% to 5% | Cyan
5% to 16% | Green-Cyan
16% to 22% | Grey
22% to 35% | Green-Yellow
35% to 55% | Yellow
55% to 80% | Orange
80% to 97% | Red
High Clip | White

For exposure-stop range reference, here is the exposure sweep:
![ea6deefe57f43e8684b6be27e3964fae_d676374d-3b8b-4e65-b048-5a17fd083a7f](https://github.com/EaryChow/AgX/assets/59176246/da259308-5d6f-409b-bf5b-4bd4c8fd4ec3)


**Looks**
"Looks" are artistic adjustments to the image formation chain. 

- `Punchy` A contrast look that makes the image look more “punchy” by darkening the entire image.

- `Greyscale` Turn the image into greyscale. Luminance coefficients are BT.2020’s CIE 2012 values, evaluated in Linear state.

-  Seven Contrast Looks. Similar to Filmic’s contrast looks. All operates in AgX Log, with pivot set in 0.18 middle grey. All using OCIO v2’s `Grading Primary Transform` feature, meaning you can customize your contrast just by editing the values in the config.

**Colorimetric Information**

- `Reference` Every OCIO config has their own reference space, all other spaces are defined with how they transform “from” and/or “to” the reference space.  While Blender’s previous config has been using `Linear Rec.709` as reference, this config uses `1931 CIE XYZ E white point chromaticity` as reference. This is a sane decision, since CIE XYZ is the root for everything else color management related. FilmLight’s TCAMv2 config also has CIE XYZ as reference. 

- `AgX Base image formation space` The AgX in this config has one single image formed in the BT.2020 display medium, then the images for other mediums are produced from the formed image in BT.2020.

- Supported Image Display Mediums:

  - `sRGB`, Generic sRGB / REC.709 encoding with sRGB Piece-wise function
  - `Rec.1886`, Generic sRGB / REC.709 displays with 2.4 native power function
  - `Display P3` Display P3 encoding with with sRGB Piece-wise function. Examples of Display P3 devices include:
    Apple MacBook Pros from 2016 on.
    Apple iMac Pros.
    Apple iMac from late 2015 on.
  - `Rec.2020` BT.2020 displays with 2.4 native power function.

  It's very unlikely someone would use a BT.2020 2.4 display as of now, but since we have the image formed in BT.2020, supporting it is just a "why not?" thing to do.


 - `Colorspaces`
    This config supports the following colorspaces:
   - `Linear CIE-XYZ E` This is the standard 1931 CIE chromaticity standard used as reference.
   - `Linear CIE-XYZ D65` This is the chromatic-adapted to Illuminant D65 version of the XYZ chromaticity. Method used is `Bradford`
   - `Linear Rec.709` Open Domain Linear BT.709 Tristimulus with Illuminant D65 white point
   - `Linear DCI-P3 D65` Open Domain Linear P3 Tristimulus with Illuminant D65 white point
   - `Linear Rec.2020` Open Domain Linear BT.2020 Tristimulus with Illuminant D65 white point
   - `ACES2065-1` Open Domain AP0 Tristimulus with ACES white point
   - `ACEScg` Open Domain AP1 Tristimulus with ACES white point
   - `Linear FilmLight E-Gamut` Open Domain Linear E-Gamut Tristimulus with Illuminant D65 white point
   - `sRGB` sRGB piece-wise encoding for reference display
   - `Rec.1886` BT.1886 2.4 Exponent EOTF Display
   - `Display P3`Display P3 with sRGB piece-wise encoding for reference display
   - `Rec.2020` BT.2020 2.4 Exponent EOTF Display
   - `Rec.2100-HLG` Rec.2100-HLG 1000 nits peak display with reference white at 100 nits
   - `Rec.2100-PQ` Rec.2100-PQ 10000 nits peak display with reference white at 100 nits
   - `Non-Color` Generic data that is not color, will not apply any color transform
 - `AgX Log`
  Blender AgX Log has a log curve of pure log2, normalized to cover the exposure range from -10 stops to +15 stops.
  The primaries colorimetry of Blender AgX Log can be interpreted differently based on your use case:
   - If you are using Blender AgX Log for picture formation:

     In this case, please consider Blender AgX Log as having AgX's mechanism baked in, by assuming it to be in Rec.2020 primaries.
   - If you are using Blender AgX Log for technical conversion or footage storage, so you want to be able to completely undo Blender AgX Log:

     In this case, you can consider Blender AgX Log as having the below colorimetry specification:
     
      `R xy: [0.9838378199, 0.2394126467], G xy: [0.0970499811, 0.9900566739], B xy: [0.1081307578, -0.0323324412] White point D65: [0.3127, 0.3290]`

      Blender AgX Log XYZ to RGB matrix:
      ```
       [[ 1.40797 -0.15298 -0.17008]
       [-0.26999  1.17735  0.07278]
       [ 0.1552   0.04965  0.73719]]
      ```

      Blender AgX Log XYZ to RGB matrix = Blender AgX Inset Matrix * Rec.2020 XYZ to RGB matrix.
      This means the Inset matrix can be isolated by: Blender AgX Log XYZ to RGB matrix * Rec.2020 RGB to XYZ matrix:
      ```
       [[ 0.85663  0.09512  0.04825]
       [ 0.13732  0.76124  0.10144]
       [ 0.1119   0.0768   0.8113 ]]
      ```
      The two above matrices are written in row-major fashion.
