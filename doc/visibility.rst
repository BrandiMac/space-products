#########################
VisibilityCalculators
#########################
The :ref:`VisibilitySegment` object allows you to determine a target's "visibility". In the context of FreeFlyer,
"visibility" is defined as an observer's ability to view a target based on occulting bodies, occulting terrain,
celestial object shape models, and refraction. The :ref:`VisibilityCalculator` object allows you
to work with multiple ``VisibilitySegments`` at once. You may use this calculator to determine whether ``any``
or ``all`` of the segments are simultaneously visible.


.. contents:: Contents
    :depth: 3

***********************
Sample Mission Plans
***********************
For sample missions demonstrating the use of the
:ref:`VisibilityCalculator` and :ref:`VisibilitySegment` objects, please view the following `Sample Mission Plans
<https://ai-solutions
.com/_help_Files/sample_mission_plans.htm>`_ (included with
your FreeFlyer installation):

Coverage and Contact Samples:
==============================
These sample Mission Plans show examples of working with the FreeFlyer objects relating to coverage and contact
analysis, such as Spacecraft and GroundStations.

* `Chain Visibility <https://ai-solutions.com/_help_Files/coverage_and_contact_smp.htm#achr_chains>`_
* `Mutual Visibility <https://ai-solutions.com/_help_Files/coverage_and_contact_smp.htm#achr_mutualvis>`_
* `Refraction Model Types <https://ai-solutions.com/_help_Files/coverage_and_contact_smp.htm#achr_refraction>`_
* `VisibilityCalculator <https://ai-solutions.com/_help_Files/coverage_and_contact_smp .htm#achr_visibilitycalc>`_

Interplanetary Samples:
========================
These sample Mission Plans cover topics relating to propagating a spacecraft when the central body is not Earth.

* `Asteroid Contact <https://ai-solutions.com/_help_Files/interplanetary_smp.htm#achr_astrcontact>`_


***********************
VisibilitySegment
***********************
The `VisibilitySegment object <https://ai-solutions.com/_help_Files/visibilitysegment_millisecond.htm?zoom_highlightsub=visibility>`_ determines whether an observer can see a
target based on the specified configuration for occulting bodies, celestial object shape models, and refraction.

Setting the Observer and Target
==================================
Observer and target are required inputs for the :ref:`VisibilitySegment` object. The :ref:`VisibilitySegment` object
accepts the following observer object types:

* `Spacecraft <https://ai-solutions.com/_help_Files/the_spacecraft_object.htm>`_
* `Sensor <https://ai-solutions.com/_help_Files/sensors.htm>`_
* `GroundStation <https://ai-solutions.com/_help_Files/groundstations.htm>`_
* `GroundVehicle <https://ai-solutions.com/_help_Files/groundvehicles.htm>`_


The :ref:`VisibilitySegment` object accepts the following target object types:

* `Spacecraft <https://ai-solutions.com/_help_Files/the_spacecraft_object.htm>`_
* `Sensor <https://ai-solutions.com/_help_Files/sensors.htm>`_
* `GroundStation <https://ai-solutions.com/_help_Files/groundstations.htm>`_
* `GroundVehicle <https://ai-solutions.com/_help_Files/groundvehicles.htm>`_
* `CelestialObject <https://ai-solutions.com/_help_Files/celestial_objects.htm>`_
* `Star <https://ai-solutions.com/_help_Files/stars.htm>`_
* `PointGroupPoint <https://ai-solutions.com/_help_Files/pointgroups.htm>`_


To create a :ref:`VisibilitySegment` and set its observer and target via scripting, please use the example script below.

.. code-block:: c++

    VisibilitySegment Segment;
    Segment.SetObserver(Spacecraft1);
    Segment.SetTarget(GroundStation1);

    // Set the target of a VisibilitySegment to the first point in a PointGroup
    Segment.SetTarget(PointGroup1[0]);


If the target is a ``CelestialObject``, you may specify whether to model the target as a point, sphere, or
ellipsoid by setting the value equal to 0, 1, or 2, respectively. The follow script demonstrates setting a spherical
`CelestialObjectTargetModel <https://ai-solutions
.com/_help_Files/visibilitysegment_celestialobjecttargetmodel_millisecond.htm>`_.

.. code-block:: c++

    Segment.CelestialObjectTargetModel = 1;  // Sphere


Setting up Occulting Bodies
===============================
In order to define the planets or moons that should be considered
when determining target visibility, you may add or remove occulting bodies from the ``Segment``. You may model
occulting bodies as spheres or ellipsoids by setting the `VisibilitySegment
.CelestialObjectOccultationModel <https://ai-solutions
.com/_help_Files/visibilitysegment_celestialobjectoccultationmodel_nanosecond.htm>`_ property to 0 or 1, respectively
. In the following script example, we are adding the Earth and Moon, removing Jupiter, and setting the occultation
model to 1, indicating an ellipsoid.


.. code-block:: c++

    Segment.AddOccultingBody(Earth, Moon);
    Segment.RemoveOccultingBody(Jupiter);
    Segment.CelestialObjectOccultationModel = 1;  // Ellipsoid


.. note::

    * The ``OccultingBodies`` property default is ``empty``. The central bodies of the observer and target are not
      assumed to be occulting bodies.
    * The ``GroundStation`` mask is assumed to
      be a more accurate representation of occultation due to the local terrain, and therefore its central body will be
      ignored as an occulting body even if explicitly added.
    * If no mask is selected for a ``GroundStation`` observer,
      the :ref:`VisibilitySegment` will treat it as a cone mask with a zero elevation angle for visibility
      calculations.
    * If the target is a ``GroundStation`` or ``PointGroundPoint``, users should **not** model a
      ``GroundStation`` inside a spherical occulting body, but instead us an ellipsoid occultation model.

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

    If the target of a :ref:`VisibilitySegment` and is a Celestial Object and includes occulting terrain, you must
    set the
    `VisibilitySegment.CelestialObjectTargetModel <https://ai-solutions
    .com/_help_Files/visibilitysegment_celestialobjecttargetmodel_nanosecond.htm>`_ property to model a point.
    Additionally, the ``VisibilitySegments`` objects
    do not currently support using both terrain occultion and refraction at the same time.


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
The ``Visibility()`` method returns an instantaneous evaluation of the target's visibility from the observer given an
evaluation epoch.

* `VisibilitySegment.Azimuth() <https://ai-solutions.com/_help_Files/visibilitysegment_azimuth_nanosecond.htm>`_ -
  Referenced to the X-Y plane of the observer's body frame and ranges from 0 to 360 degrees.
* `VisibilitySegment.Elevation() <https://ai-solutions.com/_help_Files/visibilitysegment_elevation_nanosecond.htm>`_ -
  Referenced from the X-Y plane of the observer's body frame and ranges from -90 to 90 degrees.
* `VisibilitySegment.Visibility() <https://ai-solutions.com/_help_Files/visibilitysegment_visibility_nanosecond.htm>`_ -
  Returns ``True`` (1) if target is visible by the observer and ``False`` (0) if target is not visible by the observer.


The following `interval methods <https://ai-solutions.com/_help_Files/interval_methods.htm>`_
return the exact times of the visibility events.

* `VisibilitySegment.ElevationTimes() <https://ai-solutions
  .com/_help_Files/visibilitysegment_elevationtimes_nanosecond.htm>`_ - Calculates the exact times for start of
  visibility, end of visibility, and
  maximum elevation.
* `VisibilitySegment.VisibilityTimes() <https://ai-solutions
  .com/_help_Files/visibilitysegment_visibilitytimes_nanosecond.htm>`_ - Calculates the exact times for start of
  visibility and end of visibility.

The following script reports the visibility of the target from observer (Spacecraft1) at different times throughout the
simulation.

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
When working with multiple ``Segments``, you may need to evaluate when all targets are visible by the observer. A
:ref:`VisibilityCalculator` object manages multiple ``VisibilitySegments`` and evaluates visibility based on an
``any`` or ``all`` condition. For example, you may wish to know whether any Sensor
on a Spacecraft can see a particular GroundStation. You can create a separate Segment for each Sensor,
with the Sensor as the observer and the GroundStation as the target. Another use for the :ref:`VisibilityCalculator`
is calculating a "chain" of contact. For example, you may wish to know the overlapping times when a
GroundStation can see a specific Spacecraft in a low-Earth orbit while the same Spacecraft can see another
Spacecraft in a geosynchronous orbit. In that case, you can create a ``Segment`` for
each leg of communication and instruct the :ref:`VisibilityCalculator` to compute the times when all the Segments are
complete.


Setting up the Calculator
================================
To set up the calculator, you must first set the ``VisibilityRequirement`` using the `VisibilityCalculator.VisibilityRequirement
<https://ai-solutions
.com/_help_Files/visibilitycalculator_visibilityrequirement_nanosecond.htm>`_ property, where 0 indicates **all**
targets must be viewable, and 1 indicates **any** target can be viewable.

The following script creates a ``VisibilityCalculator`` using the ``all`` requirement.

.. code-block:: c++

    VisibilityCalculator Calculator;
    Calculator.VisibilityRequirement = 0;  // All


Adding, Accessing, and Modifying Segments
==========================================
``Segments`` can be created and added to a :ref:`VisibilityCalculator` using the ``AddSegment()`` method.
You can optionally specify a label for the ``Segment`` within the ``AddSegment()`` call.

.. code-block:: c++

    Calculator.AddSegment("sc-to-gs");


Once a :ref:`VisibilitySegment` has been added to a :ref:`VisibilityCalculator`, you can access and configure the
:ref:`VisibilitySegment` as shown below. You may view additional configuration options for the
:ref:`VisibilitySegment` in the previous section.


.. code-block:: c++

    Report Calculator.Segments.Count;
    Report Calculator.Segments[0].Label;
    Calculator.Segments[0].SetObserver(Spacecraft1);
    Calculator.Segments[0].SetTarget(GroundStation1);


Individual ``VisibilitySegments`` can also be included or excluded from the :ref:`VisibilityCalculator` by setting the
`VisibilitySegment.Active <https://ai-solutions.com/_help_Files/visibilitysegment_active_nanosecond.htm>`_ property
value to 1 or 0, indicating activate or inactive, respectively.

.. code-block:: c++

    Calculator.Segments[0].Active = 1;  // Active (default)


``VisibilitySegments`` can be removed from a :ref:`VisibilityCalculator` using the ``RemoveSegment()`` method. You can
also
``RemoveAllSegments()`` or ``ResetConfiguration()`` for a :ref:`VisibilityCalculator`.


Output
============
Once the :ref:`VisibilityCalculator` and all ``VisibilitySegments`` have been configured, the following methods are
available for generating output. See the previous section for output methods available for each individual
:ref:`VisibilitySegment`.

* `VisibilityCalculator.Visibility() <https://ai-solutions.com/_help_Files/visibilitycalculator_visibility_nanosecond
  .htm>`_ - Instantaneous evaluation of visibility across all active ``Segments``
* `VisibilityCalculator.VisibilityTimes() <https://ai-solutions
  .com/_help_Files/visibilitycalculator_visibilitytimes_nanosecond.htm>`_ - `Interval method <https://ai-solutions
  .com/_help_Files/interval_methods.htm>`_ - Evaluation of visibility
  across all active ``Segments``

The following script first reports whether targets are visible by Spacecraft1 at the specified Epoch. Then,
visibility is reported for each active Segment in the calculator.

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

Relevant Pages
=================
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
Full scripting example using ``VisibilitySegment`` and ``VisibilityCalculator``

.. code-block:: c++

    // Create a VisibilitySegment
    VisibilitySegment Segment;
    Segment.SetObserver(Spacecraft1);
    Segment.SetTarget(GroundStation1);

    // Set the target of a VisibilitySegment to the first point in a PointGroup
    Segment.SetTarget(PointGroup1[0]);

    // Set as sphere model
    Segment.CelestialObjectTargetModel = 1;  // Sphere

    // Set occulting bodies
    Segment.AddOccultingBody(Earth, Moon);
    Segment.RemoveOccultingBody(Jupiter);
    Segment.CelestialObjectOccultationModel = 1;  // Ellipsoid

    // Set occulting terrain
    Terrain Terrain1;
    Terrain Terrain2;
    Segment.AddOccultingTerrain(Terrain1, Terrain2);
    Segment.RemoveOccultingTerrain(Terrain2);
    Segment.CelestialObjectOccultationModel = 1;  // Ellipsoid

    // Set the refraction model
    Segment.RefractionModelType = 2;  // Numeric - Multiple Chapman Profiles
    Segment.RefractionFrequency = 2e9;

    // Create the calculator with a visibility requirement of all
    VisibilityCalculator Calculator;
    Calculator.VisibilityRequirement = 0;  // All

    // Add a segment with label
    Calculator.AddSegment("sc-to-gs");

    // Set the first Segment in the calculator to use Spacecraft1 observer and GroundStation1 target
    Report Calculator.Segments.Count;
    Report Calculator.Segments[0].Label;
    Calculator.Segments[0].SetObserver(Spacecraft1);
    Calculator.Segments[0].SetTarget(GroundStation1);

    // Set the first segment as active
    Calculator.Segments[0].Active = 1;  // Active (default)

    // Return outputs
    While (Spacecraft1.ElapsedTime < TIMESPAN(1 days));
          // Report whether all segments are complete
          Report Spacecraft1.EpochText, Calculator.Visibility(Spacecraft1.Epoch);

          // Report whether each segment is complete
          For i = 0 to Calculator.Segments.Count-1;
                Report Spacecraft1.EpochText, Calculator.Segments[i].Label, Calculator.Segments[i].Visibility(Spacecraft1.Epoch);
          End;
          Step Spacecraft1;
    End;


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

