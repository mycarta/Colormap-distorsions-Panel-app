## Colormap distorsions Panel app ##

The notebook in this repo served two goals:

1. As a playground for continue learning Panel (following the awesome PyData tutorial by James Bednar, [Panel: Dashboards for PyData (Austin 2019)](https://youtu.be/AXpjbJUVeb4))
2. To share an app demonstrating the effect of colormaps on perception (and on the ability to see fault edges on a seismic horizon)

The first version of the app was presented as a lightning talk at the [Transform 2020 virtual conference](https://transform2020.sched.com/) organized by [Software Underground](https://softwareunderground.org/); you can watch a [video recording of the presentation here](https://www.youtube.com/watch?v=rUbvueIF5f8&t=510s).

<p align="center">
<img src="https://github.com/mycarta/Colormap-distorsions-Panel-app/blob/master/for%20readme/new_gif.gif" width="600">
</p>

### Conda setup ####
To create the conda environment for this tutorial run:

```
conda env create -f environment.yml
```

### To run the notebook interactively with Binder click on the button below ####
[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/mycarta/Colormap-distorsions-Panel-app/HEAD)

### To launch the app directly from server, use the link below ####
[Launch app](https://mybinder.org/v2/gh/mycarta/Colormap-distorsions-Panel-app/master?urlpath=%2Fpanel%2FDemonstrate_colormap_distortions_interactive_Panel)


### How to use the app ####
If you would like some background, please read [Crameri et al., 2020,  The misuse of colour in science communication. Nat Commun 11](https://www.nature.com/articles/s41467-020-19160-7), and my Society of SEG tutorial [Evaluate and compare colormaps](https://github.com/seg/tutorials-2014/blob/master/README.md#august-2014).
The idea is to compare colormaps to a good perceptual benchmark, in this case, grayscale:
1. In the top row, I chose grayscale as reference perceptually uniform colormap. Not all grayscale colormaps are actually truly, 100% perceptually uniform, when you plot Lightness, but this is a decent approximation.

### License ###
<a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/4.0/"> CC BY Creative Commons License</a>, with the exception of the data used (a seismic horizon from the [Penobscot 3D](https://terranubis.com/datainfo/Penobscot) which is covered by a [CC BY-SA Creative Commons License](https://creativecommons.org/licenses/by-sa/3.0/)).

