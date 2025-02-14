---
mediawiki: How_to_write_your_own_spot_feature_analyzer_algorithm_for_TrackMate
title: How to write your own spot feature analyzer algorithm for TrackMate
nav-links:
- title: Edge Feature Analyzers
  url: /plugins/trackmate/custom-edge-feature-analyzer-algorithms
- title: Track Feature Analyzers
  url: /plugins/trackmate/custom-track-feature-analyzer-algorithms
- title: Spot Feature Analyzers
  url: /plugins/trackmate/custom-spot-feature-analyzer-algorithms
- title: Viewers
  url: /plugins/trackmate/custom-viewers
- title: Actions
  url: /plugins/trackmate/custom-actions
- title: Detection Algorithms
  url: /plugins/trackmate/custom-detection-algorithms
- title: Particle-Linking Algorithms
  url: /plugins/trackmate/custom-particle-linking-algorithms
---

## Introduction

This third article in the series dedicated to extending [TrackMate](/plugins/trackmate) deals with spot feature analyzer. This is the last of the three kind of feature analyzers you can create, and it focuses on spots, or detections.

Spot features are typically calculated from the spot location and the image data. For instance, there is a spot feature that reports the mean intensity within the spot radius. You need to have the spot location, its radius and the image data to compute it.

In this tutorial, we will generate an analyzer that is not directly calculated from the image data. This will enable us to skip over introducing [ImgLib2](/libs/imglib2) API, which would have considerably augmented the length of this series. But this choice does not only aim at nurturing my laziness: We will make our feature **depend on other features** which will allow us to introduce **analyzers priority**.

But before this, let's visit the spot feature analyzers specificities.

## Spot analyzers and spot analyzer factories

In the two previous articles we dealt with [edge](/plugins/trackmate/custom-edge-feature-analyzer-algorithms) and [track](/plugins/trackmate/custom-track-feature-analyzer-algorithms) analyzers. We could make them in a single class, and this class embedded both the code for

-   TrackMate integration (feature names, dimensions, declaration, etc...);
-   and actual feature calculation.

For spot analyzer, the two are separated.

You must first create a {% include github org='fiji' repo='TrackMate' branch='master' source='fiji/plugin/trackmate/features/spot/SpotAnalyzerFactory.java' label='SpotAnalyzerFactory' %}. This factory will be in charge of the TrackMate integration. The interface extends both the {% include github org='fiji' repo='TrackMate' branch='master' source='fiji/plugin/trackmate/plugins/trackmateModule.java' label='TrackMateModule' %} and the {% include github org='fiji' repo='TrackMate' branch='master' source='fiji/plugin/trackmate/features/FeatureAnalyzer.java' label='FeatureAnalyzer' %} interfaces. It is the class you will need to annotate with a [SciJava](/libs/scijava) annotation for automatic discovery.

But it is also in charge of instantiating {% include github org='fiji' repo='TrackMate' branch='master' source='fiji/plugin/trackmate/features/spot/SpotAnalyzer.java' label='SpotAnalyzer' %}s. As you can see, this interface just extends ImgLib2 {% include github repo='imglib' branch='master' path='algorithms/core/src/main/java/net/imglib2/algorithm/Algorithm.java' label='Algorithm' %}, so all parameters will have to be passed in the constructor, which can be what you want thanks to the factory. We do not need a return value method, because results are stored directly inside the spot objects. But we will see this later.

Let's get started with our example.

## The spot analyzer factory

We want to generate an analyzer that will compute for each spot, its intensity relative to the mean intensity of all spots in the same frame. So you get for this feature a value of 1 if its intensity is equal to the mean, etc... We could have our analyzer actually compute the pixel intensity for each spot, take the mean over a frame, then normalize, etc... But, there is an analyzer that already computes the spot intensity and we can re-use it. Check the {% include github org='fiji' repo='TrackMate' branch='master' source='fiji/plugin/trackmate/features/spot/SpotIntensityAnalyzerFactory.java' label='SpotIntensityAnalyzerFactory' %}.

It is a good idea to reuse this value in our computations, both for the quickness of development and runtime performance. But if we do so, we must ensure that the feature we depend on is available when our new analyzer runs. There is a way to do that, thanks to the notion of **priority**, which we will deal with later.

Right now, let's focus on the factory class itself. There is not much to say: its content resembles all the feature analyzers we saw so far. So I am going to skip over the details and point you to the full source code {% include github org='fiji' repo='TrackMate-examples' branch='master' source='plugin/trackmate/examples/spotanalyzer/RelativeIntensitySpotAnalyzerFactory.java' label='here' %}.

The one interesting part is the factory method in charge of instantiating the `SpotAnalyzer`:

    @Override
        public SpotAnalyzer< T > getAnalyzer( final Model model, final ImgPlus< T > img, final int frame, final int channel )
        {
            return new RelativeIntensitySpotAnalyzer< T >( model, frame );
        }

Since we want to build a feature that does not need the image data, the constructor just skips the image reference. And that's it. We must now move on to the analyzer itself to implement the feature calculation logic.

## The spot analyzer

As you noted in the above method, each analyzer is meant to operate only on one frame. It can access the whole model, but it is supposed to compute the values for all the spots of a single frame. This permits multithreading: The factory will be asked to generate as many analyzer as there is threads available, and they will run concurrently. And we, as we build our analyzer - do not have to worry about concurrent issues.

A little word about the expected execution context: The TrackMate GUI operates in steps, as you have noted. First the detection step generates spots, then they are filtered, then they are tracked, etc... Therefore, when I said earlier that the whole model is available for calculation, this is not entirely true. When using the GUI, spot numerical features are used to filter spots after they have been detected. So that this stage, there is no tracks yet. There is not even filtered spots. A spot feature cannot depend on these objects, and this is a built-in limitation of TrackMate. So be cautious on what your numerical feature depends.

Before we go into the code, here is quick recap on the TrackMate model API. After the detection step, the spots are stored in a {% include github org='fiji' repo='TrackMate' branch='master' source='fiji/plugin/trackmate/SpotCollection.java' label='SpotCollection' %} object. It gathers all the spots, and can deal with their filtering visibility, etc... Spot analyzers are meant to operate only on one frame, so we will need to require the spot of this frame. The target frame is specified at construction time, by the factory.

The {% include github org='fiji' repo='TrackMate' branch='master' source='fiji/plugin/trackmate/features/spot/SpotAnalyzer.java' label='SpotAnalyzer' %} interface is pretty naked. There is nothing specific, and all the logic has to go in the `process()` method. There is no need to have a method to return the results of the computation, for spot objects can store their own feature values, thanks to the `Spot.putFeature(feature, value)` method.

Here is what the `process()` method of the analyzer looks like:

        @Override
        public boolean process()
        {
            /*
             * Collect all the spots from the target frame. In a SpotAnalyzer, you
             * cannot interrogate only visible spots, because spot features are
             * typically used to determine whether spots are going to be visible or
             * not. This happens in the GUI at the spot filtering stage: We are
             * actually building a feature on which a filter can be applied. So the
             * spot features must be calculated over ALL the spots.
             */

            /*
             * The spots are stored in a SpotCollection before they are tracked. The
             * SpotCollection is the product of the detection step.
             */
            final SpotCollection sc = model.getSpots();
            // 'false' means 'not only the visible spots, but all spots'.
            Iterator< Spot > spotIt = sc.iterator( frame, false );

            /*
             * Compute the mean intensity for all these spots.
             */

            double sum = 0;
            int n = 0;
            while ( spotIt.hasNext() )
            {
                final Spot spot = spotIt.next();
                // Collect the mean intensity in the spot radius.
                final double val = spot.getFeature( SpotIntensityAnalyzerFactory.MEAN_INTENSITY );
                sum += val;
                n++;
            }

            if ( n == 0 )
            {
                // Nothing to do here.
                return true;
            }

            final double mean = sum / n;

            /*
             * Make a second pass to set the relative intensity of these spots with
             * respect to the mean we just calculated.
             */

            spotIt = sc.iterator( frame, false );
            while ( spotIt.hasNext() )
            {
                final Spot spot = spotIt.next();
                final double val = spot.getFeature( SpotIntensityAnalyzerFactory.MEAN_INTENSITY );
                final double relMean = val / mean;
                // Store the new feature in the spot
                spot.putFeature( RELATIVE_INTENSITY, Double.valueOf( relMean ) );
            }

            return true;
        }

The code for the whole class is {% include github org='fiji' repo='TrackMate-examples' branch='master' source='plugin/trackmate/examples/spotanalyzer/RelativeIntensitySpotAnalyzer.java' label='here' %}.

## Using SciJava priority to determine order of execution

Now it's time to discuss the delicate subject of dependency.

As stated above, our new analyzer depends on some other features to compute. Therefore, the analyzers that calculate these other features need to run *before* our analyzer. Or else you will bet `NullPointerException`s randomly.

TrackMate does not offer a real in-depth module dependency management. It simply offers to **determine** the order of analyzer execution thanks to the [SciJava](/libs/scijava) plugin **priority parameter**.

For instance, if you check the annotation part of the spot analyzer factory, you can see that there is an extra parameter, `priority`:

    @Plugin( type = SpotAnalyzerFactory.class, priority = 1d )

This priority parameter accepts a `double` as value and this value determines the order of execution. Careful, the rule is the opposite of what would make sense for a priority:

{% include notice icon="info" content='Feature analyzers are executed in order according to **increasing priority**. This means that analyzers with the greatest priority are executed last.' %}

By convention, if your feature analyzer depends on the features calculated by N other analyzers, you take the larger priority of these analyzers, and add 1. In our case, we depend on the {% include github org='fiji' repo='TrackMate' branch='master' source='fiji/plugin/trackmate/features/spot/SpotIntensityAnalyzerFactory.java' label='SpotIntensityAnalyzerFactory' %}, which as a priority of 0 (the default if the parameter is unspecified). So quite logically, we set the priority of our analyzer to be 1. This ensures the proper execution order.

## Wrapping up

Apart from the discussion on the priority and execution order, there is not much to say. It works!

<figure><img src="/media/plugins/trackmate/trackmate-customspotanalyzer-01.png" title="TrackMate_CustomSpotAnalyzer_01.png" width="600" alt="TrackMate_CustomSpotAnalyzer_01.png" /><figcaption aria-hidden="true">TrackMate_CustomSpotAnalyzer_01.png</figcaption></figure>

{% include person id='tinevez' %} ([talk](User_talk_JeanYvesTinevez)) 07:32, 11 March 2014 (CDT)


