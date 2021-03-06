# RgToolsVS
Wrapper for RGVS, RGSF, and various median plugins as well as some functions that largely utilize them

#### Optimizations
- Replaces certain depreciated modes in RGVS/RGSF with core plugins
- Functions `sbr` and `MinBlur` have been completely rewritten to speed them up as much as possible

#### Enhancements
- Add `planes` parameters to functions and plugins that previously didn't have them. This will override any kind of mode or radius-type parameter
- Allow `radius` parameter to be set per-plane (sbr and MinBlur)
- Support float input (CTMF, sbr and MinBlur)

#### Dependencies
- [RGSF](https://github.com/IFeelBloated/RGSF)
- [vsutil](https://github.com/Irrational-Encoding-Wizardry/vsutil/blob/master/py)
- [CTMF](https://github.com/HomeOfVapourSynthEvolution/VapourSynth-CTMF)
- [vs-average](https://github.com/End-of-Eternity/vs-average)


## RGVS/RGSF
#### Repair
- use std.Minimum, std.Maximum and std.Expr to perform mode 1 in RGSF (commentable)

#### RemoveGrain
- use std.Convolution and std.Median for modes 4, 11, 12, 19 and 20 (commentable)

#### Clense
- use std.Expr instead of RGVS (commentable)

See Vapoursynth website for more information on RGVS - http://www.vapoursynth.com/doc/plugins/rgvs.html


## Median 
Wraps std.Median, ctmf.CTMF, rgvs.VerticalMedian and average.Median into one function
```
Median(clip clip[, int[] radius=1, int[] planes=[0, 1, 2], str[] mode='s', int vcmode=1, bint range_in=color_family==vs.RGB, int memsize=1048576, int opt=0])
```
- "vcmode" allows you to change the VerticalMedian mode when using horizontal or vertical processing and a radius of 1
- "range_in" is for mixed float/int processing when using CTMF
- "memsize" and "opt" parameters from CTMF


## RemoveGrainM
Special helper function for iterative RemoveGrain usage
```
RemoveGrainM(clip clip[, int[] mode=2, int[] modeu=mode, int[] modev=modeu, iter=max length of modes, planes=[0, 1, 2] ])
```
```
RemoveGrainM(clip, mode=1, modeu=2, modev=3, iter=[1,2,3])
# is equal to
clip.rgvs.RemoveGrain([1,2,3]).rgvs.RemoveGrain([0,2,3]).rgvs.RemoveGrain([0,0,3])
```


## More functions
### MinBlur
Pixelwise median and blur admixture. If the differences are homologous to the input, the weaker result of the two are taken, else the source pixel is passed
```
MinBlur(clip clip[, int[] radius=1, int[] planes=[0, 1, 2], str[] mode='s', str[] blur='gauss', bint range_in=color_family==vs.RGB, int memsize=1048576, int opt=0])
```
### sbr
Pixelwise blur operation. Takes a blur's difference and performs a blurring of the difference. If both differences are homologous to the input, the weaker result is taken, else the source pixel is passed
```
sbr(clip clip[, int[] radius=1, int[] planes=[0, 1, 2], str[] mode='s', str[] blur='gauss' ])
```
### Blur
No relation to AviSynth's internal plugin, use std.Convolution or muvsfunc's `Blur` for that.
Like RemoveGrainM, this was mainly written as a helper for other functions.
```
Blur(clip clip[, int[] radius=1, int[] planes=[0, 1, 2], str[] mode='s', str[] blur='gauss' ])
```
- Included modes:
  - "box" - average blurring (same as RemoveGrain(20), std.Convolution([1]\*9) and std.BoxBlur)
  - "gauss" - pseudo-gaussian blur technique made popular on a certain forum using an iteration of removegrain, starting with one call to mode 11 and all subsequent calls to mode 20
- parameter "radius" is iterative when mode="gauss"
- parameter "blur" toggles between std.BoxBlur for blur="box" and removegrain(11)\[.removegrain(20)...] for blur="gauss"
