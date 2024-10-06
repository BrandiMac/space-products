#########################
VisibilityCalculators
#########################

TODO <include blurb about what "visibility" means or "occulting bodies"?

The :ref:`VisibilitySegment` object allows users to determine whether an observer can see a target, based on the
user-specified configuration for occulting bodies, occulting terrain, celestial object shape models, and refraction.
The :ref:`VisibilityCalculator` object allows users to work with multiple ``VisibilitySegments`` at once in order to
determine
whether ``any`` or ``all`` of the segments are simultaneously true.


.. contents:: Contents
    :depth: 3

***********************
Sample Mission Plans
***********************
For missions demonstrating the use of the
:ref:`VisibilityCalculator` and :ref:`VisibilitySegment` objects, you may find the following `Sample Mission Plans
<https://ai-solutions
.com/_help_Files/sample_mission_plans.htm>`_ (included with
your FreeFlyer installation) helpful:

Coverage and Contact Samples:
==============================

* `Chain Visibility <https://ai-solutions.com/_help_Files/coverage_and_contact_smp.htm#achr_chains>`_
* `Mutual Visibility <https://ai-solutions.com/_help_Files/coverage_and_contact_smp.htm#achr_mutualvis>`_
* `Refraction Model Types <https://ai-solutions.com/_help_Files/coverage_and_contact_smp.htm#achr_refraction>`_
* `VisibilityCalculator <https://ai-solutions.com/_help_Files/coverage_and_contact_smp .htm#achr_visibilitycalc>`_

Interplanetary Samples:
========================

* `Asteroid Contact <https://ai-solutions.com/_help_Files/interplanetary_smp.htm#achr_astrcontact>`_


***********************
VisibilitySegment
***********************

The `VisibilitySegment object <https://ai-solutions.com/_help_Files/>`_ determines whether an observer can see a
target based on your specified configuration for occulting bodies, celestial object shape models, and refraction.

Setting the Observer and Target
==================================
Observer and target are required inputs for the :ref:`VisibilitySegment` object. The :ref:`VisibilitySegment` object
allows the following observer objects:

* `Spacecraft <https://ai-solutions.com/_help_Files/the_spacecraft_object.htm>`_
* `Sensor <https://ai-solutions.com/_help_Files/sensors.htm>`_
* `GroundStation <https://ai-solutions.com/_help_Files/groundstations.htm>`_
* `GroundVehicle <https://ai-solutions.com/_help_Files/groundvehicles.htm>`_


The :ref:`VisibilitySegment` object allows the following target objects:

* `Spacecraft <https://ai-solutions.com/_help_Files/the_spacecraft_object.htm>`_
* `Sensor <https://ai-solutions.com/_help_Files/sensors.htm>`_
* `GroundStation <https://ai-solutions.com/_help_Files/groundstations.htm>`_
* `GroundVehicle <https://ai-solutions.com/_help_Files/groundvehicles.htm>`_
* `CelestialObject <https://ai-solutions.com/_help_Files/celestial_objects.htm>`_
* `Star <https://ai-solutions.com/_help_Files/stars.htm>`_
* `PointGroupPoint <https://ai-solutions.com/_help_Files/pointgroups.htm>`_


To create a :ref:`VisibilitySegment` and set its observer and target via scripting, please follow the example below.

.. code-block:: c++

    VisibilitySegment Segment;
    Segment.SetObserver(Spacecraft1);
    Segment.SetTarget(GroundStation1);

    // Set the target of a VisibilitySegment to the first point in a PointGroup
    Segment.SetTarget(PointGroup1[0]);


If the target is a ``CelestialObject``, you may specify whether to model the target as a point, sphere, or
ellipsoid, as shown below.

.. code-block:: c++

    Segment.CelestialObjectTargetModel = 1;  // Sphere


Setting up Occulting Bodies
===============================
You may remove occulting bodies in order to define the planets or moons that will be considered
when determining if the observer can see the target. You may specify whether to model occulting bodies as spheres
or ellipsoids using the `VisibilitySegment.CelestialObjectOccultationModel <https://ai-solutions
.com/_help_Files/visibilitysegment_celestialobjectoccultationmodel_nanosecond.htm>`_ property, as shown in the
script below.


.. code-block:: c++

    Segment.AddOccultingBody(Earth, Moon);
    Segment.RemoveOccultingBody(Jupiter);
    Segment.CelestialObjectOccultationModel = 1;  // Ellipsoid


.. note::

    TODO rewrite
    The ``OccultingBodies`` property is by default ``empty``. The central bodies of the source and target are not
    assumed to be occulting bodies. Additionally, if a ``GroundStation`` object is the observer, its central body will be
    ignored as an occulting body (even if explicitly added) as the ``GroundStation`` mask is assumed to be a more accurate
    representation of occultation due to the local terrain. If no mask is selected for the ``GroundStation`` observer,
    the :ref:`VisibilitySegment` will treat it as a cone mask with a zero elevation angle for purposes of visibility
    calculations. If a ``GroundStation`` or ``PointGroundPoint`` object is the target, users should not model a
    ``GroundStation`` inside a spherical occulting body. An ellipsoid occultation model should be used instead.

Setting up Occulting Terrain
===============================
You may add or remove occulting `Terrain <https://ai-solutions.com/_help_Files/working_with_terrain.htm>`_ in order
to define the surface features considered
in determining target visibility. When including occulting terrain, you must model occulting
bodies as ``ellipsoids`` using the `VisibilitySegment.CelestialObjectOccultationModel <https://ai-solutions
.com/_help_Files/visibilitysegment_celestialobjectoccultationmodel_nanosecond.htm>`_ property, as shown in the
script below.


.. code-block:: c++

    Terrain Terrain1;
    Terrain Terrain2;
    Segment.AddOccultingTerrain(Terrain1, Terrain2);
    Segment.RemoveOccultingTerrain(Terrain2);
    Segment.CelestialObjectOccultationModel = 1;  // Ellipsoid


.. note::

    TODO rewrite
    If the target of a :ref:`VisibilitySegment` both includes occulting terrain and is a Celestial Object, you must set
    the
    `VisibilitySegment.CelestialObjectTargetModel <https://ai-solutions
    .com/_help_Files/visibilitysegment_celestialobjecttargetmodel_nanosecond.htm>`_ property to model a point.
    Additionally, the ``VisibilitySegments``
    does not currently support using both terrain occultion and refraction at the same time.


Setting up Refraction
============================
By default, refraction effects are not modeled when determining target visibility. You may enable refraction modeling
by setting the `VisibilitySegment.RefractionModelType <https://ai-solutions
.com/_help_Files/visibilitysegment_refractionmodeltype_nanosecond.htm>`_ property. The signal frequency used
when computing refraction is set using the `VisibilitySegment.RefractionFrequency <https://ai-solutions
.com/_help_Files/visibilitysegment_refractionfrequency_nanosecond.htm>`_ property. There are two valid
ranges of values when setting the refraction frequency: the radio spectrum (100 MHz - 100 GHz) and visible spectrum
(176.3485 THz - 999.3081 THz).


.. code-block:: c++

    Segment.RefractionModelType = 2;  // Numeric - Multiple Chapman Profiles
    Segment.RefractionFrequency = 2e9;


Please use the table below to learn more about our available refraction models:

.. table:: Refraction Models
    :widths: 20 20 20 20 20

    +-------------------------------------+----------------+-------------------------------------------------------------------+-----------+-----------+
    | Refraction Model                    | Frequency Band | Description                                                       | Accuracy  | Runtime   |
    +=====================================+================+===================================================================+===========+===========+
    | None                                | N/A            | No refraction.                                                    | N/A       | N/A       |
    +-------------------------------------+----------------+-------------------------------------------------------------------+-----------+-----------+
    | Numeric - Single Chapman Profile    | Visible        | * Tropospheric refraction effects are modeled using               | Very high | Fast      |
    |                                     |                |   the Edlén model. :sup:`1234`                                    |           |           |
    |                                     |                | * Ionospheric refraction effects are not modeled.                 |           |           |
    |                                     |                | * In the visible spectrum, this model matches the                 |           |           |
    |                                     |                |   "Numeric - Multiple Chapman Profiles" model,                    |           |           |
    |                                     |                |   and their results will be identical.                            |           |           |
    |                                     +----------------+-------------------------------------------------------------------+-----------+-----------+
    |                                     | Radio          | * Tropospheric refraction effects are modeled using the           | High      | Fast      |
    |                                     |                |   International Telecommunications Union (ITU) model.:sup:`5`     |           |           |
    |                                     |                | * Ionospheric refraction effects are modeled using the            |           |           |
    |                                     |                |   International Reference Ionosphere (IRI) and Chapman            |           |           |
    |                                     |                |   electron density profile at the latitude and longitude          |           |           |
    |                                     |                |   location of the minimum-height point along the ray path,        |           |           |
    |                                     |                |   and applies the Chapman profile from that location along        |           |           |
    |                                     |                |   the whole ray path.                                             |           |           |
    +-------------------------------------+----------------+-------------------------------------------------------------------+-----------+-----------+
    | Numeric - Multiple Chapman Profiles | Visible        | * Tropospheric refraction effects are modeled using               | Very high | Fast      |
    |                                     |                |   the Edlén model.:sup:`1234`                                     |           |           |
    |                                     |                | * Ionospheric refraction effects are not modeled.                 |           |           |
    |                                     |                | * In the visible spectrum, this model matches the                 |           |           |
    |                                     |                |   "Numeric - Single Chapman Profile" model, and                   |           |           |
    |                                     |                |   their results will be identical.                                |           |           |
    |                                     +----------------+-------------------------------------------------------------------+-----------+-----------+
    |                                     | Radio          | * Tropospheric refraction effects are modeled using               | Very high | Slower    |
    |                                     |                |   the International Telecommunications Union (ITU) model.:sup:`5` |           |           |
    |                                     |                | * Ionospheric refraction effects are modeled using the            |           |           |
    |                                     |                |   International Reference Ionosphere (IRI) and Chapman            |           |           |
    |                                     |                |   electron density profile at multiple points along the           |           |           |
    |                                     |                |   ray path, in order to determine the electron density            |           |           |
    |                                     |                |   at the height of the ray path.6                                 |           |           |
    +-------------------------------------+----------------+-------------------------------------------------------------------+-----------+-----------+
    | Analytic - Chapman                  | Any            | This Earth refraction model is based on analytic                  | Medium    | Very fast |
    |                                     |                | Chapman theory.:sup:`7` This refraction model is only             |           |           |
    |                                     |                | allowed when the observing object is a GroundStation.             |           |           |
    +-------------------------------------+----------------+-------------------------------------------------------------------+-----------+-----------+
    | Analytic - TRORD                    | N/A            | * Tropospheric refraction effects are modeled                     | Medium    | Very fast |
    |                                     |                |   based on the TRORD refraction model.:sup:`8`                    |           |           |
    |                                     |                |   This model is only allowed when the observing object            |           |           |
    |                                     |                |   is a GroundStation. This model uses the local-mean              |           |           |
    |                                     |                |   refractivity coefficients for each month specified by           |           |           |
    |                                     |                |   the GroundStation.RefractivityNs property of the observing      |           |           |
    |                                     |                |   GroundStation. The VisibilitySegment.RefractionFrequency        |           |           |
    |                                     |                |   setting is not used with this model.                            |           |           |
    |                                     |                | * Ionospheric refraction effects are not modeled                  |           |           |
    +-------------------------------------+----------------+-------------------------------------------------------------------+-----------+-----------+
    | Numeric - Exponential               | N/A            | This simple exponential model calculates a long-term              | Medium    | Very fast |
    |                                     |                | global mean refractive index as a function of height.:sup:`9`     |           |           |
    |                                     |                | The VisibilitySegment.RefractionFrequency setting is not          |           |           |
    |                                     |                | used with this model.                                             |           |           |
    +-------------------------------------+----------------+-------------------------------------------------------------------+-----------+-----------+


.. note::

    These refraction models are only used for calculating refraction through the Earth's atmosphere. Refraction is not
    modeled for any other celestial bodies.


Output
==============
Once the ``Segment`` has been configured, use the following instantaneous methods for generating output.
These methods report the instantaneous value of the azimuth and elevation angles from the observer to the target.
The ``Visibility()`` method returns an instantaneous evaluation of whether the observer can see the target.

 TODO add definitions here

* `VisibilitySegment.Azimuth() <https://ai-solutions.com/_help_Files/visibilitysegment_azimuth_nanosecond.htm>`_
* `VisibilitySegment.Elevation() <https://ai-solutions.com/_help_Files/visibilitysegment_elevation_nanosecond.htm>`_
* `VisibilitySegment.Visibility() <https://ai-solutions.com/_help_Files/visibilitysegment_visibility_nanosecond.htm>`_


The following `interval methods <https://ai-solutions.com/_help_Files/interval_methods.htm>`_ are also available. These
methods return the exact times of the visibility events.

* `VisibilitySegment.ElevationTimes() <https://ai-solutions
  .com/_help_Files/visibilitysegment_elevationtimes_nanosecond.htm>`_ - Calculates the exact times for start of
  visibility, end of visibility, and
  maximum elevation
* `VisibilitySegment.VisibilityTimes() <https://ai-solutions
  .com/_help_Files/visibilitysegment_visibilitytimes_nanosecond.htm>`_ - Calculates the exact times for start of
  visibility and end of visibility

TODO say what script does

.. code-block:: c++

    While (Spacecraft1.ElapsedTime < TIMESPAN(1 days));
        Report Spacecraft1.EpochText, Segment.Visibility(Spacecraft1.Epoch);
        Step Spacecraft1;
    End;


.. warning::

    As of FreeFlyer 7.3, the default timing precision mode is ``nanosecond`` precision mode. For older Mission
    Plans that have not yet been converted from ``millisecond`` precision mode, the syntax for working with times is
    different. See the `timing precision mode <https://ai-solutions.com/_help_Files/timing_precision_modes.htm>`_ page
    for more information.

***********************
VisibilityCalculator
***********************
TODO rewrite
A :ref:`VisibilityCalculator` object can be used to create and manage multiple ``VisibilitySegments`` when you want to
evaluate
whether all or any of a set of Segments are simultaneously true. For example, you may wish to know whether any Sensor
on a Spacecraft can see a particular GroundStation. In that case, you can create a separate Segment for each Sensor,
where the Sensor is the observer and the GroundStation is the target. Another example would be calculating a "chain" of
contact. You may wish to know the times when a GroundStation can see a Spacecraft in a low-Earth orbit and that
Spacecraft can also see another Spacecraft in a geosynchronous orbit. In that case, you can create a ``Segment`` for
each
leg of communication and instruct the :ref:`VisibilityCalculator` to compute the times when all the Segments are complete.


Setting up the Calculator
================================
TODO where is it stated that the calculator requires this?

The ``any`` or ``all`` requirement can be set using the `VisibilityCalculator.VisibilityRequirement
<https://ai-solutions
.com/_help_Files/visibilitycalculator_visibilityrequirement_nanosecond.htm>`_ property.

TODO what is this code doing

.. code-block:: c++

    VisibilityCalculator Calculator;
    Calculator.VisibilityRequirement = 0;  // All


Adding, Accessing, and Modifying Segments
==========================================
Segments can be created and added to a :ref:`VisibilityCalculator` using the ``AddSegment()`` method as shown below. You
can optionally specify a label for the ``Segment``.

.. code-block:: c++

    Calculator.AddSegment("sc-to-gs");


Once a :ref:`VisibilitySegment` has been added to a :ref:`VisibilityCalculator`, you can access the :ref:`VisibilitySegment` and configure it
as shown below. Additional configuration options for the :ref:`VisibilitySegment` are described above.


.. code-block:: c++

    Report Calculator.Segments.Count;
    Report Calculator.Segments[0].Label;
    Calculator.Segments[0].SetObserver(Spacecraft1);
    Calculator.Segments[0].SetTarget(GroundStation1);


Individual ``VisibilitySegments`` can also be included or excluded from the :ref:`VisibilityCalculator` by setting the
`VisibilitySegment.Active <https://ai-solutions.com/_help_Files/visibilitysegment_active_nanosecond.htm>`_ property.

.. code-block:: c++

    Calculator.Segments[0].Active = 1;  // Active (default)


``VisibilitySegments`` can be removed from a :ref:`VisibilityCalculator` using the ``RemoveSegment()`` method. You can
also
``RemoveAllSegments()`` or ``ResetConfiguration()`` for a :ref:`VisibilityCalculator`.


Output
============
Once the :ref:`VisibilityCalculator` and all ``VisibilitySegments`` have been configured, the following methods are
available for generating output. See above for a description of the output methods available for each individual
:ref:`VisibilitySegment`.

* `VisibilityCalculator.Visibility() <https://ai-solutions.com/_help_Files/visibilitycalculator_visibility_nanosecond
  .htm>`_ - Instantaneous evaluation of visibility across all active ``Segments``
* `VisibilityCalculator.VisibilityTimes() <https://ai-solutions
  .com/_help_Files/visibilitycalculator_visibilitytimes_nanosecond.htm>`_ - `Interval method <https://ai-solutions
  .com/_help_Files/interval_methods.htm>`_ - Evaluation of visibility
  across all active ``Segments``

.. code-block:: c++

    While (Spacecraft1.ElapsedTime < TIMESPAN(1 days));
          // Report whether all segments are complete
          Report Spacecraft1.EpochText, Calculator.Visibility(Spacecraft1.Epoch);
          // Report whether each segment is complete
          For i = 0 to Calculator.Segments.Count-1;
                Report Spacecraft1.EpochText, Calculator.Segments[i].Label, Calculator.Segments[i].Visibility(Spacecraft1.Epoch);
          End;
          Step Spacecraft1;
    End;

.. note::

    A `VisibilityTimes <https://ai-solutions.com/_help_Files/interval_methods.htm#achr_visibilitytimes>`_ usage example can
    be found on the `interval methods <https://ai-solutions.com/_help_Files/interval_methods.htm>`_ page.


**************
See Also
**************

Related Content
=====================
You may find the following adjacent pages useful when creating a ``VisibilitySegment`` or ``VisibilityCalculator``:

* `VisibilityCalculator Properties and Methods <https://ai-solutions.com/_help_Files/visibilitycalculator_nanosecond
  .htm>`_
* `VisibilitySegment Properties and Methods <https://ai-solutions.com/_help_Files/visibilitysegment_nanosecond.htm>`_
* `Interval Methods <https://ai-solutions.com/_help_Files/interval_methods.htm>`_
* `Contact Method Summary <https://ai-solutions.com/_help_Files/contact_method_summary.htm>`_
* `The Spacecraft Object <https://ai-solutions.com/_help_Files/the_spacecraft_object.htm>`_
* `Sensors <https://ai-solutions.com/_help_Files/sensors.htm>`_
* `GroundStations <https://ai-solutions.com/_help_Files/groundstations.htm>`_
* `GroundVehicle <https://ai-solutions.com/_help_Files/groundvehicles.htm>`_
* `CelestialObjects <https://ai-solutions.com/_help_Files/celestial_objects.htm>`_
* `Stars <https://ai-solutions.com/_help_Files/stars.htm>`_

Scripts
===================
TODO add section with full script

----

References
**************

#. "The refractive index of air," B. Edlén, Metrologia 2, 71-80 (1966)
#. "An updated Edlén equation for the refractive index of air," K.P. Birch and M.J. Downs, Metrologia 30, 155-162 (1993)
#. "Correction to the updated Edlén equation for the refractive index of air," K.P. Birch and M.J. Downs, Metrologia
   31, 315-316 (1994)
#. NIST Engineering Metrology Toolbox at http://emtoolbox.nist.gov/Wavelength/Documentation.asp
#. "Recommendation ITU-R P.453-10 (02.12), The radio refractive index: its formula and refractivity data", Section 1,
   February 2012
#. "The Theory of Scintillation with Applications in Remote Sensing", Charles L. Rino, January 2011
#. "Goddard Trajectory Determination System (GTDS) Mathematical Theory", Section 7, July 1989
#. "Software Requirements Specification for Tracking and Orbit Determination (TRORD) CPCI 202 of the Command and
   Control Segment Math Appendix", September 1992
#. "Recommendation ITU-R P.453-10 (02.12), The radio refractive index: its formula and refractivity data", Section 2,
   Equation 8, February 2012

