---
title: 2009-10-14 - Fiji Plzen is out
---

It is a great pleasure to announce [a new Fiji version](/downloads) nick-named Plzen. It was named after the city in which we had a very successful image processing school, where we tested a few of the shiny new components which are the goodies of this release. The two most prominent new features are

-   the new and shiny [Fiji Updater](/plugins/updater). The updater knows whether your files are up-to-date, obsolete or locally modified. It will check at startup whether there are updates available, and if so, offer you to run the updater. You can also choose to be reminded later, or not ever again.

<!-- -->

-   the [Script Editor](/scripting/script-editor). Just call {% include bc path='File | New | Script'%}, select a language and start writing a script. The editor has syntax highlighting and you can run the script without even saving it.

For an introduction to the power of scripting, see e.g. [our page on Javascript](/scripting/javascript).

Other changes include:

-   lots of bug fixes (thanks to all!)
-   many, many improvements in the Register Virtual Stack Slices plugin (thanks to Ignacio Arganda Carreras and Albert Cardona)
-   a metric ton of improvements in TrakEM2 (thanks Stephan Saalfeld and Albert)
-   substantial work has been performed on the Level Sets plugin (thanks to Erwin Frise and Albert)
-   on Windows, a possible infinite loop upon startup was fixed
-   the Sync Win plugin was added
-   we ship a new Clojure version now, and support for this language was enhanced (thanks to Albert)
-   our Stack Manipulation collection now has Bob Dougherty's Stack\_Sorter (thanks to Bob, Jean-Yves Tinevez and Christophe Leterrier), Interleave and Deinterleave (thanks to Tony Collins and Jean-Yves), and Grouped ZProjector (thanks Jean-Yves)
-   we have a circle fitter which can recognize a single circle, even if there are fluctuations in the signal
-   a lot of encodings were fixed (you should no longer get funny little squares in place of non-ASCII characters)
-   for better integration with the Swing library, we have a JImagePanel that can show a single ImagePlus (thanks to the FocalPoint developers!)
-   we can read/write .xpm and .lss16 files now
-   you can download a LiveCD image and an image you can put on a USB stick now
-   lots and lots of improvements in the AnalyzeSkeleton plugin (thanks to Ignacio)
-   we now bundle the Gray Morphology and several Colocalisation plugins, too
-   Bio-Formats was updated to include the new AmiraMesh reader (thanks to Gregory Jefferis)
-   changes in the bUnwarpJ plugin (amongst other enhancements) allow tighter integration with the Register Virtual Stack plugin (thanks Ignacio)
-   the Level Sets plugin is now available inside TrakEM2, too
-   if you have problems with a particular file format, you can send it to us using {% include bc path='Help | Upload Sample Image'%}
-   on MacOSX/PowerPC64, Fiji does not try to launch a 64-bit Java (because Apple does not provide any 64-bit Java for PowerPC)
-   scripts are sorted alphabetically before they are added to menus (for consistency between different computers)
-   loading compressed NRRD images via network is much faster now (thanks Greg!)
-   thanks to Bob Dougherty, we bundle the Local Thickness plugin, too
-   compressed BioRad images are read correctly now (thanks Greg)
-   fixed rendering in the SIFT Align plugin (thanks to Stephan Saalfeld and Gabriel Landini)
-   upgraded the 3D Object Counter plugin (thanks to Fabrice Cordelières and Daniel James White)
-   when launched on MacOSX by double-clicking the icon (or by running from the Dock), the current directory is set to Fiji's application directory (thanks Greg and Christophe)
-   upgraded the LSM Toolbox/Reader to version 0.4f (thanks Mark Longair, Greg, Patrick Pirotte and Jerôme Mutterer)
-   on MacOSX, the Lookup Tables from the luts/ directory are finally visible.
-   {% include bc path='Help | Update Menus'%} handles not only .jar and .class plugins, but plugin scripts, too
-   we have {% include bc path='File | New | Fiji Tutorial'%}, which makes it a breeze to write tutorials with screen shots for the Fiji Wiki
-   on MacOSX, the default Java extensions were not available by mistake
-   sample images can be stored in the samples/ subdirectory, allowing for completely off-line {% include bc path='File | Open Sample | ?'%}
-   the Stitching plugin now has a file and directory chooser (thanks to Stephan Preibisch)
-   .lei and .dv files are opened with Bio-Formats now
-   .rec and .st files are opened using our MRC reader, too
-   updated the Java runtime environments on Windows and Linux to Java6u16
-   we ship the Manual Tracking plugin now, too (thanks to Fabrice Cordelières)
-   we ship the Calculator Plus plugin now, too (thanks to Daniel Hornung and Gabriel)
-   the command line option "--run" can be used to tell Fiji to run a given plugin right away
-   Fiji is built with Java5 every night to ensure that it continues to work on 32-bit MacOSX prior to 10.6.
-   the Macro Interpreter outputs the help correctly again (thanks Gabriel)
-   added two lookup tables, "thermal" and "glasbey" (thanks to Michael Doube and Gabriel)
-   the {% include bc path='Help | Report a Bug'%} plugin is much more user-friendly now (thanks Mark)
-   updated View5D, fixed interaction with Bio-Formats (thanks to Simon Kidd)
-   when JAVA\_HOME is set to a bogus location Fiji ignores it now (thanks Albert)
-   avoid mistaking .jar files inside .rsrc/ directories for real .jar files, working around a MacOSX quirk (thanks to Maté Biro)
-   ImageJ was updated to version 1.43h
-   the Command Launcher ({% include key keys='Ctrl|L' %} in the Fiji window) was converted to Swing, and sports a fuzzy matching, and an option to show more information such as which .jar file that plugin is contained in, for a much-improved user-experience (thanks Mark)
-   ImageJ now opens websites in the preferred browser on Linux
-   cropping an image marks it as modified now
-   {% include bc path='Plugins | Install Plugin...'%} can install .jar files now (provided you have permission to write to Fiji's plugins/ directory)

The list is so long because we did not get the opportunity to meet and get a new release done in July, as we all hoped.

A big thanks goes to all the contributors and users, especially those who helped us find and fix bugs!


