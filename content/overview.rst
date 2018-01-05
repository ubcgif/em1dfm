.. _overview:

EM1DFM package overview
=======================

Description
-----------

EM1DFM is a Fortran-based, multi-platform program library for carrying out the one-dimensional inversion of frequency-domain, small loop, electromagnetic data acquired to determine the spatial variation of the electrical conductivity and/or magnetic susceptibility of the subsurface. The name "EM1DFM" derives from: electromagnetics ("EM"), one-dimensional models ("1D"), frequency-domain observations ("F"), and magnetic (dipole) sources and
receivers ("M"). 

The acquired data are measurements of the magnetic field associated with electric currents and magnetization induced in the subsurface by a time-varying current in a transmitter loop. Information about how the conductivity and susceptibility vary with depth is obtained by making measurements for different frequencies of the transmitter current, and for different separations, heights and orientations of the transmitters and receivers. Making such measurements at different locations gives information about the lateral variation in the subsurface.

The mathematical representation that EM1DFM uses to model the Earth varies only with depth. In particular, the representation comprises many uniform, horizontal layers, and it is the conductivity and/or susceptibility within each layer that is computed. Four options are available: just the conductivity in each layer can be constructed, or just the susceptibility (constrained to be positive) in each layer, or both the conductivity and susceptibility with the positivity constraint on the susceptibility, or both the conductivity and susceptibility without the positivity constraint.

The initial research underlying this program library was funded principally by the “IMAGE” consortium, of which the following companies were participants: AGIP, Anglo American, Billiton, Cominco, Falconbridge, INCO, MIM, Muskox Minerals, Newmont, Placer Dome and Rio Tinto, and from the Natural Sciences and Engineering Research Council of Canada (NSERC).

**ANY ADDITIONAL STUFF REQUIRED**

Program library content
-----------------------

Executable programs
^^^^^^^^^^^^^^^^^^^

This package consists of two programs:

   - EM1DFM: carries out the inversion of small loop, frequency-domain EM data assuming a layered Earth model
   - EM1DFMFWD: a stand-alone program for forward modeling the frequency-domain response assuming a layered Earth model

Graphical user interfaces
^^^^^^^^^^^^^^^^^^^^^^^^^
GUI-based utilities for these codes include respective viewers for the data and models. They are only available on Windows platforms and can be freely downloaded through the UBC-GIF website:

   - `GM_DATA_VIEWER <http://www.eos.ubc.ca/~rshekhtm/utilities/gm-data-viewer.zip>`__: a utility for viewing raw surface or airborne data (not borehole data), error distributions, and for comparing observed to predicted data directly or as difference maps.
   - `MeshTools3D <http://www.eos.ubc.ca/~rshekhtm/utilities/MeshTools3d.zip>`__: a utility for displaying resulting 3D models as volume renderings. Susceptibility volumes can be sliced in any direction, or isosurface renderings can be generated.
   - `GUI <http://gif.eos.ubc.ca/sites/default/files/grav3d-gui.zip>`__: a GUI to run GRAV3D v5.0 on either Linux or Windows. **NOTE: The download does not contain the inversion/modelling codes.**

Licensing
---------

A **constrained educational version** of the program is available with
the `IAG <http://www.flintbox.com/public/project/1605/>`__ package
(please visit `UBC-GIF website <http://gif.eos.ubc.ca>`__ for details).
The educational version is fully functional so that users can learn how
to carry out effective and efficient 3D inversions of magnetic data.
**However, RESEARCH OR COMMERCIAL USE IS NOT POSSIBLE because the
educational version only allows a limited number of data and model
cells**.

Licensing for an unconstrained academic version is available - see the
`Licensing policy document <http://gif.eos.ubc.ca/software/licenses>`__.

**NOTE:** All academic licenses will be **time-limited to one year**.
You can re-apply after that time. This ensures that everyone is using
the most recent versions of codes.

Licensing for commercial use is managed by third party distributors.
Details are in the `Licensing policy document <http://gif.eos.ubc.ca/software/licenses>`__.

Installing
----------

There is no automatic installer currently available for the . Please
follow the following steps in order to use the software:

#. Extract all files provided from the given zip-based archive and place
   them all together in a new folder such as

#. Add this directory as new path to your environment variables.

Two additional notes about installation:

-  Do not store anything in the "bin" directory other than executable
   applications and Graphical User Interface applications (GUIs).

-  A Message Pass Interface (MPI) version is available for Linux upon
   and the installation instructions will accompany the code.

Highlights of changes from version 3.2
--------------------------------------

The principal upgrades, described below, allow the new code to take advantage of current multi-core computers and also provide greater flexibility to incorporate the geological information.

Improvements since the previous version:

#. A new projected gradient algorithm is used to implement hard
   constraints.

#. Fully parallelized computational capability (for both sensitivity matrix calculations and inversion calculations).

#. A facility to have active and inactive (i.e. fixed) cells.

#. Bounds are be specified through two separate files, rather than one two-column file.

#. Additional flexibility for incorporating the reference model in the model objective function facilitates the generation of smooth models when borehole constraints are incorporated.

#. The ``gzinv3d.log`` file has been simplified and detailed information on the inversion can be found in the ``gzinv3d.out`` file.

#. Backward compatibility: The new version has changed the input file format and the bounds file. Data, mesh, model, and topographic file formats have not changed.

Notes on computation speed
^^^^^^^^^^^^^^^^^^^^^^^^^^

-  For large problems, GZSEN3D is significantly faster than the previous single
   processor inversion because of the parallelization for computing the
   sensitivity matrix computation and inversion calculations. Using
   multiple threads for running the parallelized version resulted in
   sensitivity matrix calculation speedup proportional to the number of
   threads. The increase in speed for the inversion was less pronounced,
   but still substantial.

-  It is strongly recommended to use multi-core processors for running
   the and . The calculation of the sensitivity matrix (:math:`\mathbf{G}`) is
   directly proportional to the number of data. The parallelized
   calculation of the :math:`n` rows of :math:`\mathbf{G}` is split
   between :math:`p` processors. By default, all available processors
   are used. There is a feature to limit :math:`p` to a user-defined
   number of processors.

-  In the parallelized inversion calculation,
   :math:`\mathbf{G}^T \mathbf{G}` is multiplied by a vector, therefore
   each parallel process uses only a sub-matrix of :math:`\mathbf{G}`
   and then the calculations are summed. Since there is significant
   communication between the CPUs, the speedup is less than a direct
   proportionality to the number of processors. However when running the
   same inversion under MPI environment on multiple computers the
   advantage is that a single computer does not have to store the entire
   sensitivity matrix.

-  For incorporating bound information, the implementation of the projected gradient algorithm in version 5.0 is primarily that the projected gradient results in a significantly faster solution than the logarithmic barrier technique used in earlier versions.


