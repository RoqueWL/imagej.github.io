---
title: Python
---

You can use ImageJ from [Python](https://python.org/):

-   If you want to write ImageJ [scripts](/scripting) in the Python language, which run from inside ImageJ similar to other scripts, check out the [Jython Scripting](/scripting/jython) page.
    -   **Advantage:** Such scripts are able to take advantage of SciJava [script parameters](/scripting/parameters) and run within several tools that support [SciJava](/libs/scijava).
    -   **Disadvantage:** You will not be able to use many of the Python modules requiring native code such as numpy or scipy.
    -   **Disadvantage:** As of this writing, Jython implements only Python 2, not Python 3.

<!-- -->

-   If you want to embed an ImageJ inside of your Python code, such as within a [Jupyter notebook](https://jupyter.org/) using the Python kernel, check out the [pyimagej](https://pypi.org/project/pyimagej/) Python package. It allows you to create an ImageJ with full access to its API from Python. See the [ImageJ tutorial notebooks](/tutorials) for an introduction.
    -   **Advantage:** It is possible to combine ImageJ with other image analysis libraries like [scikit-image](https://scikit-image.org/), [ITK](/software/itk), [OpenCV](/software/opencv) and more in a single Python program.
    -   **Disadvantage:** Wrapping ImageJ in Python has some limitations and bugs, particularly surrounding use of [ImageJ 1.x](/software/imagej) features, compared to using ImageJ from Java-based kernels such as [BeakerX](https://beakerx.com/).


