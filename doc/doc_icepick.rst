IcePick2
========

IcePick2 (added in v0.4) is a single tool for browsing, processing, and picking
radar data. Prior to this, the seperate tools *irview* and *icepick* performed
these tasks.

*IcePick2* is started from the command line using the syntax

::

    icepick2.py -f HDFNAME [-L LINENO]

where ``HDFNAME`` is the name of the radar survey file and ``LINENO`` is an
optional line number (default ``0``).

Upon launch, the terminal window is converted into an *IcePick2* console, and a
Radargram window (B-scan) is opened automatically for the current file and line.
Clicking on the Radargram shows information about the trace and depth clicked.

Commands can be typed in the console. Typing ``help`` gives an idea of the
options. Examples include:

- ``info`` provides more detailed information about the currently opened line. 

- ``open LINENO`` allows different lines to be viewed without restarting
  *IcePick2*

- ``ylim T0 TF`` adjusts the vertical (time) range shown, where T0 and TF are
  specified in nanoseconds

- ``exit`` and ``q`` both close *IcePick2*.

Filters
-------

In addition to viewing raw data, *IcePick2* provides access to pre-defined
filters on the fly using the ``f`` command (mnemonic "filter"). For example,
typing::

    f gc

applies a linear gain control operator to each trace, increasing the amplitude
of later-arriving events. This modifies the data in memory, and **it does not
alter the data in the HDF file**. Help for individual filters is obtained by
typing ``help [filtername]``. In order to determine what filters have been
applied to a dataset, typing ``f`` alone lists them, along with any parameters
required to reproduce the presently displayed data. The data can be reset to
the version originally loaded by typing ``nf`` (mnemonic "no filter").

The presently defined filters include common operations such as time-domain and
frequency-domain filtering, dewow, linear and automatic gain control, F-K
migration, and instrument ringing removal. Defining custom filters can be done
by constructing a subclass of irlib.app.filters.Command. The operation performed
by the filter is contained in the ``apply()`` method, and can include *irlib*
calls, or any other Python manipulation of the ``Gather`` data.

**Non-comprehensive list of filters**

=============== ==============================================================
Command         Description
=============== ==============================================================
*Gain Control*
``gc``          Applies a linear gain enhancement
``agc``         Applies an automatic (nonlinear) gain enhancement
*Convolutions*
``dewow``       "Dewowing" filter to remove instrument drift
``lowpass``     Performs a frequency-domain lowpass filter with a cutoff
                frequency of 25 MHz
``highpass``    Performs a frequency-domain lowpass filter with a cutoff
                frequency of 25 MHz
``lowpass_td``  Performs a time-domain lowpass filter (moving average)
``highpass_td`` Performs a time-domain highpass filter (inverted moving
                average)
*Recursive*
``iir30low``    Chebyschev lowpass filter with cutoff at 30 MHz
``iir25high``   Chebyschev highpass filter with cutoff at 25 MHz
*Migration*
``migfk``       Stolt (F-K) migration
*Misc*
``abs``         Displays the absolute value of the data
``wiener``      Wiener statistical noise filter
``ringing``     Horizontal ringing filter based on singular value decomposition
``project``     Project radar line to straight segments with equal trace
                spacing
=============== ==============================================================

A comprehensive list is provided by typing ``help`` with no arguments.


Windows
-------

The console is the root of the IcePick, however data are displayed and
interacted with in various windows. Windows are generally launched (or closed)
by typing the name of the window followed by ``on`` or ``off``. For example,

::

    pick on

opens a window displaying a set of individual radar traces, which can be clicked
with the mouse for picking, and

::

    map on

opens a simple map of the current line.

Radargram
~~~~~~~~~

The Radargram, opened by default, is the primary graphical window, and shows the
``Gather`` data and annotations. The Radargram window can also be used for
digitizing features, such as englacial scattering (EXPAND HERE...)

PickWindow
~~~~~~~~~~

The PickWindow reproduces the functionality of *icepick.py*, prior to v0.4. A
series of radar traces are shown, and can be changed by pressing the **h** and
**l** (ell) keys to move up or down the radar line. A set of yellow lines in the
Radargram shows which traces are shown by the PickWindow.

The process of picking is fairly simple. Click the mouse on the part of the
trace representing a reflection to be timed. Right-clicking removes the pick if
you've made a mistake. Fine adjustments can be made by pressing the **j** (down)
and **k** (up) keys.

The "picking mode" can be switched between bed-picking and airwave-picking by
pressing the middle mouse button.

Automatic picking can be performed by typing ``pick dc`` and ``pick bed``, for
the direct-coupling and bed reflection, respectively. A pair of optional
arguments can be appended to these commands, i.e.

::

    pick bed [min, max]

which uses the integers ``min`` and ``max`` as constraints in identifying the
reflection. This works well where the reflection is clear and the radargram is
clean, but will typically require manual clean-up.

Once the picks are satisfactory, type ``pick save`` to save the timing data to
the folder ``picking/``.


MapWindow
~~~~~~~~~

The MapWindow shows the current line as a series of dots shown on a Mercator
projection.


Caching
-------

The performance of *icepick2* can be enhanced substantially by pre-caching of
the radar lines. This can be done using the API (``Gather.Dump()``), or by
running the commandline utility ``h5_generate_caches`` (discussed previously).
Any filter can be applied at the time of cache generation. Caches are Python
"pickles" (serialized data), and contain a snapshot of the radar data, as well
as a reference to ``irlib``. Substantial changes to ``irlib`` may require cache
regeneration.


.. Digitizing
.. ----------
.. 
.. A tangential application of *IcePick2* is hand-digitizing of radar features. This
.. is less precise that trace-by-trace picking (see *icepick*), but more
.. appropriate for selecting volumetric features, or features for which the
.. individual traces are messy (geological applications?).
.. 
.. To begin digitizing a feature, type::
.. 
..     dnew [FEATURENAME]
..     
.. where ``FEATURENAME`` is an optional descriptive word or comment. The radargram
.. can then be clicked with the left mouse button to delineate shapes by vertex.
.. Pressing the middle mouse button with "undo" the last vertex created. Pressing
.. the right mouse button will create a final vertex and then end the feature.
.. 
.. *Alternatively, pressing "N" (Shift+n) while the figure window is focused can
.. be used to start a new feature (with no comment) and "E" (Shift+e) will end the
.. feature.*
.. 
.. Once all desired features have been digitized, typing::
.. 
..     dsave
.. 
.. saves the vertices to a text file. The saved file is Tab-delimited with blank
.. lines between features.
.. 
.. =========== ===================================
.. Column      Description
.. =========== ===================================
.. 1           Trace at vertex location
.. 2           Longitude
.. 3           Latitude
.. 4           Time (ns) from the top of the trace
.. =========== ===================================
.. 
.. Presently, comments are not saved in the file, and there is no way to load
.. previously-created features across sessions.
.. 
.. Additional commands:
.. 
.. - ``dls`` lists previously-created features
.. 
.. - ``drm NUMBER`` deletes the feature identified by ``NUMBER``






icerate
=======

*icerate* is a tool for rating the quality of picks before surface
interpolation. The interface is similar to *IcePick2*, although missing a number
of features. In the future, *icerate* will be replaced by a RatingWindow in
*IcePick2*.

Once picks have been made, they can be rated for quality. *icerate* shows the
picks made in *IcePick2* (either in order or randomly), and allows a numeric
quality rating to be applied (individually or in small groups) by typing
**1-5**. When ``save`` is typed, the results will go into ``rating/``.

