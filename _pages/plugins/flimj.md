---
mediawiki: FLIMJ
title: FLIMJ
categories: [Uncategorized]
extensions: ["mathjax"]
---


{% capture maintainer%}
{% include person id='ctrueden' %}
{% endcapture %}

{% capture source%}
{% include github org='slim-curve' repo='slim-plugin' %}
{% endcapture %}
{% include info-box software='ImageJ' name='FLIMJ plugin' logo='<img src="/media/icons/slim-curve.png" width="64"/>' author=' [CRUK/MRC at University of Oxford](http://www.rob.ox.ac.uk/)  
[UW-Madison LOCI](http://loci.wisc.edu/)' maintainer=maintainer filename='flimlib.jar, flimlib-*arch*-*ver*.jar,  
flimj-ops-*ver*.jar' source=source latest-version='1.0.0' website='https://flimlib.github.io/' category='Analysis' %}

## Introduction

The FLIMJ plugin for ImageJ provides the ability to analyze FLIM data within ImageJ, using the [FLIMLib](https://flimlib.github.io/) library. The plugin can be installed into the [Fiji](/software/fiji) distribution of ImageJ simply by enabling the FLIMJ [update site](/update-sites). Features include:

-   Fit individual pixels, entire images per-pixel, or do global analysis on entire images, using FLIMLib's rapid lifetime determination (RLD), Levenberg-Marquardt (LMA) or global analysis (Global) fitting algorithms
-   Single, double and triple exponential fits
-   Gaussian, Poisson and Maximum Likelihood Estimation noise models
-   Produce one or several fitted images depending which parameters (A, τ, Z, χ²) are chosen for visualization
-   Full control over the start and end fit cutoffs known as "cursors"
-   Binning options for various kernel sizes to reduce noise and boost intensity when fitting per-pixel
-   Support for so-called "excitation" or "prompt" files containing a recorded system response function to be convolved with the exponential fit
-   Batch processing support for analyzing many lifetime images as part of a [scripting](/scripting) workflow

## Installation

The FLIMJ plugin is available from the "FLIMJ" [update site](/update-sites).

Once you have installed the FLIMJ plugin, it becomes available on the menu under {% include bc path='Analyze | Lifetime | FLIMJ'%}.

## Usage

### Startup

Open a dataset (such as {% include github org='flimlib' repo='flimj-ops' branch='master' path='test_files/test2.sdt' label='this one' %}) or select an existing image display in Fiji:

<figure><img src="/media/plugins/flimj-usage-open-dataset.png" title="FLIMJ_usage_open_dataset.png" width="300" alt="FLIMJ_usage_open_dataset.png" /><figcaption aria-hidden="true">FLIMJ_usage_open_dataset.png</figcaption></figure>

With the desired dataset window active, launch FLIMJ from the menu under {% include bc path='Analyze | Lifetime | FLIMJ'%} or search for "FLIMJ" in the search box:

<figure><img src="/media/plugins/flimj-first-launch.png" title="FLIMJ_first_launch.png" width="600" alt="FLIMJ_first_launch.png" /><figcaption aria-hidden="true">FLIMJ_first_launch.png</figcaption></figure>

{% include info-box img-src='"FLIMJ Lifetime Axis Not Detected.png" width="350"/&gt;<img src="/media/plugins/flimj-time-base-info-not-detected.png" width="200"/>

If the dataset comes with a (fourth) spectral dimension, the user has to choose the spectral channel to analyze as well:

<img src="/media/plugins/flimj-multiple-channel-detected.png" width="350"/>' %}

### Fit preview

Before fitting the entire dataset, the user may click on the intensity image or type in the coordinates in **Preview** panel to preview the fitted curve and parameters of the pixel under the cursor:

<img src="/media/plugins/flimj-preview-preview.png" width="200"/> <img src="/media/plugins/flimj-preview-plot.png" width="400"/> <img src="/media/plugins/flimj-preview-settings.png" width="200"/>

In the screenshots above,

-   **X** and **Y** are coordinates of the pixel cursor counting from the upper left corner.
-   **Red curve** denotes the fitted portion of decay between the start and end cursor.
-   **Orange dots** denote the photon counts in each time bin in Fit plot and denote the residual ($$y_{data}-y_{fit}$$) in Res plot.
-   **Photon Count** displays the total number of photons collected between the start and end cursor.
-   $$z, A, \tau$$ (or $$z_1, A_1, A_2, \tau_1, \tau_2$$ in two-component fit) are the background, initial intensity and lifetime parameters of the model.
-   $$\chi^2$$ shows the chi-squared measure of the fit (see [Noise models](#noise-models)).

### Fit settings

<figure><img src="/media/plugins/flimj-fit-settings.png" title="Options on the Settings pannel." width="200" alt="Options on the Settings pannel." /><figcaption aria-hidden="true">Options on the <strong>Settings</strong> pannel.</figcaption></figure>

Sometimes you may want to fine-tune the fitting configurations. The **Settings** panel and the **Plot** pannel. The configurations include:

-   **Intensity Thresh.**: The minimal pixel intensity to be fitted.  
    Any pixel with cumulative intensity (over all time bins) below this threshold will be skipped when the dataset is fitted. When selected for preview in the **Preview** panel, only the photon counts (orange dots) will be plotted. *For those pixels, all parameters are treated as 0 both when being previewed and when the whole dataset is fitted.*

<!-- -->

-   **Kernel Size**: The radius (in pixels, excluding the center) of the binning kernel.  
    FLIMJ plugin currently only supports the square kernel with size $$2r+1$$ and values all equal to 1 where $$r$$ is the radius). The binning of the dataset is performed by convolving the dataset with this kernel, which is equivalent to adding neighboring pixels into the center:

<figure><img src="/media/plugins/flimj-settings-binning.png" title="Left to right: Intensity of dataset after binning of radius 0, 1, and 2." width="600" alt="Left to right: Intensity of dataset after binning of radius 0, 1, and 2." /><figcaption aria-hidden="true">Left to right: Intensity of dataset after binning of radius 0, 1, and 2.</figcaption></figure>

-   **χ² Target**: The $$\chi^2$$ value below which the LM fitting will stop.  
    The LM algorithm checks at the end of each iteration whether the fit is good enough by comparing $$\chi^2$$ with this threshold. If the results are satisfactory, the LM algorithm will terminate.

<!-- -->

-   **Noise Model**: The noise model used for fitting (see [Noise models](#noise-models)).

<!-- -->

-   **Algorithm**: The algorithm used for fitting.  
    Currently supported algorithms are:
    -   **LMA**: The Levenberg-Marquardt algorithm;
    -   **Global**: The global LMA algorithm;
    -   **Bayes**: The Bayesian fitting algorithm.

<!-- -->

-   **Instrument Response**: The dataset that contains the instrument response. (see [Instrument response function (IRF/prompt)](prompt/prompt))).

<!-- -->

-   **\# of Components**: The number of exponential decay terms in the model.  
    FLIMJ plugin currently supports up to 3 components.

<!-- -->

-   **Start** and **End**: The ends (in ns, inclusive) of the interval during which the data is considered.  
    The cursors can be changed by dragging in the Fit plot, clicking on the up and down buttons, or typing in values (input will be rounded to the nearest time bin marking).

<figure><img src="/media/plugins/flimj-settings-start-end.png" title="Change of the cursors results in different fit results." width="700" alt="Change of the cursors results in different fit results." /><figcaption aria-hidden="true">Change of the cursors results in different fit results.</figcaption></figure>

#### Instrument response function (IRF/prompt)

FLIMJ plugin currently only supports the selection of IRF from a single pixel in an [acceptable dataset](#Startup) that is taken during a standard IRF measurement procedure (such as {% include github org='flimlib' repo='flimj-ui' branch='master' path='test_files/urea.sdt' label='this one using urea crystals' %}). The steps are as follows:

1.  Click on the drop-down menu, select *From file*; select the dataset file that contains the IRF.
2.  In **Preview** panel, select *IRF Intensity* for the "Show" option (you may also select *Grayscale* for the "as" option to deactivate pseudocoloring):<img src="/media/plugins/flimj-irf-show-irf.png" title="fig:Choose IRF Intensity to enter IRF picking mode." width="200" alt="Choose IRF Intensity to enter IRF picking mode." />
3.  Click on the image to select the candidate pixel and adjust the *Start* and *End* cursors in **Plot** pannel to crop out the section of interest (*IRF is plotted in green*):<img src="/media/plugins/flimj-irf-crop-irf.png" title="fig:Drag the cursors to crop IRF." width="350" alt="Drag the cursors to crop IRF." />
4.  Exit the IRF picking mode by selecting other options for the "Show" in **Preview** panel and adjust the *Start* cursor to get a better fit.

You may readjust the IRF by starting from step 2 above. You may also reset the IRF setting by selecting *None* for *Instrument Response* in **Settings** pannel or go back to a previously set IRF by choosing the corresponding filename there.

## Dataset fitting and results preview

When all settings are properly configured. Click on **Fit Dataset** button on the **Settings** panel to start dataset fitting. A "pending" progress indicator will appear on top of the **Fit Dataset** button.

After the "pending" state ends, new options will be available as "Show" options in the **Preview** panel. Click on the options to view the fit results:

<figure><img src="/media/plugins/flimj-fit-dataset-preview-results-show.png" title="Viewing the $$tau$$ (lifetime) parameter of the fitted dataset." width="220" alt="Viewing the $$tau$$ (lifetime) parameter of the fitted dataset." /><figcaption aria-hidden="true">Viewing the $$tau$$ (lifetime) parameter of the fitted dataset.</figcaption></figure>

You may also change the "as" option to specify how the results are rendered. Currently supported options are:

<figure><img src="/media/plugins/flimj-fit-dataset-preview-results-as.png" title="Left to right: The $$tau$$ image rendered with Grayscale, Color and Composite Color." width="600" alt="Left to right: The $$tau$$ image rendered with Grayscale, Color and Composite Color." /><figcaption aria-hidden="true">Left to right: The $$tau$$ image rendered with <strong>Grayscale</strong>, <strong>Color</strong> and <strong>Composite Color</strong>.</figcaption></figure>

-   **Grayscale**: Linearly maps the middle 80 percentile of the result image's values to grayscale colors from black to white. Values below the 10th percentile and above the 90th percentile are rendered black and white respectively.

<!-- -->

-   **Color**: Linearly maps the middle 80 percentile of the result image's values to colors from <span style="color:hsl(20,100%,50%)">`hsl(20, 100%, 50%)`</span> to <span style="color:hsl(220,100%,50%)">`hsl(220, 100%, 50%)`</span> (see color bar below). Out-of-range values follow the same rule as in **Grayscale**.

<!-- -->

-   **Composite Color**: Takes the color mapped to by **Color** but replace the brightness with the brightness of the intensity image (on the left of the **Preview** pane). This option makes the fitted image look more uniform by deemphasizing jittery low photon count pixels.

You can hover the mouse pointer on the rendered image to see the lookup table (LUT) color bar and the numerical value of the pixel:

<figure><img src="/media/plugins/flimj-fit-dataset-preview-results-lut.png" title="Hover on image to see the color bar, the pixel value, and its place in the range." width="200" alt="Hover on image to see the color bar, the pixel value, and its place in the range." /><figcaption aria-hidden="true">Hover on image to see the color bar, the pixel value, and its place in the range.</figcaption></figure>

## Image export

<figure><img src="/media/plugins/flimj-image-export.png" title="Options on the Export pannel." width="200" alt="Options on the Export pannel." /><figcaption aria-hidden="true">Options on the <strong>Export</strong> pannel.</figcaption></figure>

After dataset fitting, you can select images to export in the drop-down checklist on the **Export** pannel. Hit "Export" button to export the selected images to for further processing. If "With LUT" is checked, the exported image will be loaded with the LUT used to render the preview image on the **Preview** panel (value ranges are set for each image separately).

{% include notice icon="info" content='You may follow the following steps to reproduce the composite color image after export:

1.  Export "Intensity" with desired fitted images;
2.  Convert all images to RGB format by searching and executing the "RGB Color" command in Fiji;
3.  Search and execute the "compose rgb-stacks" script, make the intensity image the "source" and the fitted image the "target", and choose "Multiply" as "compose method". Hit "OK".' %}

## Advanced topics

### Noise models

Coming soon...

More documentation coming soon. For now, see the source code at:

{% include link-banner url='https://github.com/flimlib/flimj-ui' %}

And:

{% include link-banner url='https://github.com/flimlib/flimj-ops' %}
