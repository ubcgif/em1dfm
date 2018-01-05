.. _overview:

EM1DFM package overview
=======================

Description
-----------

EM1DFM is a Fortran-based, multi-platform program library for carrying out the one-dimensional inversion of frequency-domain, small loop, electromagnetic data acquired to determine the spatial variation of the electrical conductivity and/or magnetic susceptibility of the subsurface. The name "EM1DFM" derives from: electromagnetics ("EM"), one-dimensional models ("1D"), frequency-domain observations ("F"), and magnetic (dipole) sources and
receivers ("M"). 

The acquired data are measurements of the magnetic field associated with electric currents and magnetization induced in the subsurface by a time-varying current in a transmitter loop. Information about how the conductivity and susceptibility vary with depth is obtained by making measurements for different frequencies of the transmitter current, and for different separations, heights and orientations of the transmitters and receivers. Making such measurements at different sounding locations gives information about the lateral variation in the subsurface. Sounding locations refer to horizontal positions where the Earth can locally be considered a 1D layered Earth.

For any sounding location, the mathematical representation that EM1DFM uses to model the Earth varies only with depth. In particular, the representation comprises many uniform, horizontal layers, and it is the conductivity and/or susceptibility within each layer that is computed. Four options are available: just the conductivity in each layer can be constructed, or just the susceptibility (constrained to be positive) in each layer, or both the conductivity and susceptibility with the positivity constraint on the susceptibility, or both the conductivity and susceptibility without the positivity constraint. For a complete mathematical treatment of the forward and inversion problem, see :ref:`Background Theory<theory>`.

The initial research underlying this program library was funded principally by the “IMAGE” consortium, of which the following companies were participants: AGIP, Anglo American, Billiton, Cominco, Falconbridge, INCO, MIM, Muskox Minerals, Newmont, Placer Dome and Rio Tinto, and from the Natural Sciences and Engineering Research Council of Canada (NSERC).


Program library content
-----------------------

This package consists of two programs:

   - **EM1DFM:** carries out the inversion of small loop, frequency-domain EM data assuming a layered Earth model

   - **EM1DFMFWD:** a stand-alone program for forward modeling the frequency-domain response assuming a layered Earth model


Licensing
---------



Installing
----------





