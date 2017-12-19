.. _inputFile:

Main input files
================

Input files introduction


em1dfm
^^^^^^

This file is the main input file for the em1dfm code and **must** be given the name **em1dtm.in**. This file contains the parameters specifying the model and inversion algorithm types, the name of the file containing the observations, and information specifying the various starting and reference models. The structure of the file **em1dfm.in** is described below. 

.. tabularcolumns:: |C|C|L|

+--------+---------------+---------------------------------------------------------------+
| Line # | Parameter     | Description                                                   |
+========+===============+===============================================================+
|1       |rootname       |root for names of output files                                 |
+--------+---------------+---------------------------------------------------------------+
|2       |obsfname       |name of file containing the observations                       |
+--------+---------------+---------------------------------------------------------------+
|3       |mtype          |type of model in the inversion                                 |
+--------+---------------+---------------------------------------------------------------+
|3a      |stconfname     |starting conductivity model (omit line if mtype=2)             |
+--------+---------------+---------------------------------------------------------------+ 
|3b      |stsusfname     |starting susceptibility model                                  |
+--------+---------------+---------------------------------------------------------------+
|3c      |rsconfname     |reference (smallest), or background, conductivity model        |
+--------+---------------+---------------------------------------------------------------+
|3d      |rssusfname     |reference (smallest), or background, susceptibility model      |
+--------+---------------+---------------------------------------------------------------+
|3e      |rzconfname     |reference (flattest) conductivity model                        |
+--------+---------------+---------------------------------------------------------------+
|3f      |rzsusfname     |reference (flattest) susceptibility model                      |
+--------+---------------+---------------------------------------------------------------+
|4       |weightname     |additional model weights file                                  |
+--------+---------------+---------------------------------------------------------------+
|5       |alphas         |coefficients of model norm components                          |
+--------+---------------+---------------------------------------------------------------+
|6       |iatype         |type of inversion algorithm                                    |
+--------+---------------+---------------------------------------------------------------+
|7       |iapara(s)      |additional inversion algorithm parameter(s)                    |
+--------+---------------+---------------------------------------------------------------+
|8       |maxniters      |maximum number of iterations in an inversion                   |
+--------+---------------+---------------------------------------------------------------+
|9       |logstretch     |stretch factor for logarithmic barrier term (omit if mtype=1,4)|
+--------+---------------+---------------------------------------------------------------+
|10      |numconv        |small number for convergence tests                             |
+--------+---------------+---------------------------------------------------------------+
|11      |hankeleval     |number of explicit evaluations of Hankel transform kernels     |
+--------+---------------+---------------------------------------------------------------+
|12      |outflg         |flag indicating amount of output                               |
+--------+---------------+---------------------------------------------------------------+

.. _invL1:

**Line 1: rootname**

*rootname* is the root for the names of all output files.


.. _invL2:

**Line 2: obsfname**

obsfname (link) is the name of the file containing the observations (see section 3.1.2).


.. _invL3:

**Line 3: mtype**

mtype indicates the type of model in the inversion. Your choice here affects exactly what is required for several other parameters, especially the starting and
reference models. Please check all parameter lines carefully.

    - mtype = 1 implies just conductivity is active in the inversion;
    - mtype = 2 implies just susceptibility (with positivity constrained by means of a logarithmic barrier term) is active in the inversion;
    - mtype = 3 implies both conductivity and susceptibility are active with susceptibility constrained to be positive;
    - mtype = 4 implies both conductivity and susceptibility are active but with no constraints on the susceptibility;

.. _invL3a:

**Line 3a: stconfname**

stconfname is the name of the file containing the starting conductivity model (see section 3.1.3 for the format). Note: this can be a layers only file (layer
thicknesses but no conductivity column) if the best-fitting halfspace is to be used as the starting model. See also the GUI instructions., section on "starting
conductivity model".

    - Required if mtype = 1, 3 or 4;
    - omit if mtype = 2.

.. _invL3b:

**Line 3b: stsusfname**

stsusfname is the name of the file containing the starting susceptibility model (see section 3.1.3 for the format).

    - Omit if mtype = 1.
    - For mtype=2: starting susceptibility model, OR layers-only file (layer thicknesses but no susceptibility column) if the best-fitting halfspace is to be used as the starting model.
    - For mtype=3, 4: starting susceptibility model, OR value of halfspace susceptibility, OR "default" if the best-fitting halfspace is to be used as the starting model. See also the GUI instructions., section on "starting susceptibility model".

.. _invL3c:

**Line 3c: rsconfname**

rsconfname is a file name (format as for all models), OR a value for a halfspace OR "default" if the best-fitting halfspace is to be used. This file or
parameters is used as:

    - the reference conductivity model for the smallest component of the model norm (see section 3.1.3).
    - the background conductivity model if only susceptibility is active in the inversion.
    - Required if mtype = 1, 3 or 4, and acs > 0, or if mtype = 2
    - Enter "none" if not required.

.. _invL3d:

**Line 3d: rssusfname**

rssusfname is a file name (format as for all models), OR a value for a halfspace OR "default" if the best-fitting halfspace is to be used. This file or
parameters is used as:

    - the reference susceptibility model for the smallest component of the model norm (see section 3.1.3)
    - the background susceptibility model if only conductivity is active in the inversion.
    - Required if mtype = 2, 3 or 4, and ass > 0, or if mtype = 1
    - Enter "none" if not required.

.. _invL3e:

**Line 3e: rzconfname**

rzconfname is a file name (format as for all models), OR a value for a halfspace OR "default" if the best-fitting halfspace is to be used. This file or
parameters is used as:

    - the reference conductivity model for the flattest component of the model norm (see section 3.1.3).
    - optional for mtype = 1, 3, or 4. If such a model is supplied in a file whose name is given here then it well be used in the inversion,
    - if "none" is given there will be no reference conductivity model in the flattest component of the model norm

.. _invL3f:

**Line 3f: rzsusfname**

rzsusfname is a file name (format as for all models), OR a value for a halfspace OR "default" if the best-fitting halfspace is to be used. This file or
parameters is used as:

    - the reference susceptibility model for the flattest component of the model norm (see section 3.1.3)
    - optional for mtype = 2, 3, or 4. If such a model is supplied in a file whose name is given here then it well be used in the inversion,
    - If "none" is given there will be no reference susceptibility model in the flattest component of the model norm


.. _invL4:

**Line 4: weightname**

    - NONE" to indicate that no additional user-supplied weights are to be provided for use in the model norm,
    - or the name of the file containing the additional weights for the model norm (see section 3.1.4 for the format of this file).

.. _invL5:

**Line 5: alphas**

Coefficients of model norm components:

    - if mtype = 1, the two parameters acs and acz are required, or,
    - if mtype = 2, the two parameters ass and asz are required, or,
    - if mtype = 3 or 4 enter either:
        - the string "DEFAULT" and all four parameters acs , acz , ass and asz are required, or
        - the six parameters Ac , As, acs , acz , ass and asz, where the value of Ac is :math:`A^c` in the expression for the model norm below, the value of As is :math:`A^s`, the value of acs is :math:`\alpha_s^c`, the value of acz is :math:`\alpha_z^`, the value of ass is :math:`\alpha_s^s`, and the value of asz is :math:`\alpha_z^s`.


.. _invL6:

**Line 6: iatype**

iatype indicates the type of inversion algorithm to be used,

    - iatype = 1 implies a fixed, user-supplied value for the trade-off parameter,
    - iatype = 2 implies that the trade-off parameter will be chosen by means of a line search so that a target misfit is achieved (or, if this is not possible, then the
    smallest misfit)
    - iatype = 3 implies the trade-off parameter will be chosen using the GCV criterion, and
    - iatype = 4 implies that the trade-off parameter will be chosen using the L-curve criterion;


.. _invL7:

**Line 7: iapara**

Parameters required by the specified inversion algorithm:
    - if iatype = 1, the value of the trade-off parameter is expected, or
    - if iatype = 2, both the target misfit (in terms of the factor chifac where the target misfit is chifac times the total number of observations for the sounding) and
    the greatest allowed decrease in the misfit at any one iteration are expected (in terms of mfac where ; see eq (58) section
    2.5.3 of the theory section), or
    - if iatype = 3 or 4, the greatest allowed decrease in the trade-off parameter at any one iteration (in terms of bfac where n+1 = max( *, bfac x n); see eq
    (62) of the theory section)


.. _invL8:

**Line 8: maxniters**

maxniters is the maximum number of iterations to be carried out in an inversion


.. _invL9:

**Line 9: logstretch**

Logarithmic barrier term stretch factor:

    - either "DEFAULT" can be entered to indicate that the default value of 1 is to be used as the coefficient in the logarithmic barrier term, or
    - some other value (a strictly positive real number) can be entered (only required if mtype = 2 or 3);

.. _invL10:

**Line 10: numconv**

"Small" number for convergence tests:
    
    - either "DEFAULT" can be entered to indicate that the default value of 0.01 is to be used in the tests of convergence for an inversion, or,
    - if another value is desired, it can be entered on this line;


.. _invL11:

**Line 11: hankeleval**

Number of explicit evaluations of Hankel transform kernels:

    - either "DEFAULT" can be entered to indicate the kernel of the Hankel transforms is to be explicitly evaluated the default number of times ( = 50), or,
    - if there are concerns about the accuracy of the Hankel transform computations, a number greater than 50 can be entered on this line;


.. _invL12:

**Line 12: outflg**

outflg is the flag indicating the amount of output from the program. (WARNING: it is highly recommended that outflg = 3 or 4 is NOT specified if
there are more than a few soundings to be inverted in a single run.)

    - outflg = 1 implies the output of a brief convergence / termination report for each sounding plus the final two-dimensional composite model (cond &/or susc)
    for all the soundings, and the corresponding forward-modelled data. If only one sounding is being considered the model(s) are output in one-dimensional
    format.
    - outflg = 2 implies output as for outflg = 1 plus an iteration by iteration summary of the various components of the objective function.
    - outflg = 3 implies output as for outflg = 2 plus the one-dimensional models and corresponding predicted data for each iteration for each sounding. The
    diagnostics file is also produced.
    - outflg = 4 implies output as for outflg = 3 plus any line-search information from misfit, GCV function or L-curve curvature versus trade-off paramenter.
    Also produced is a diagnostics file for the LSQR solution routine if it is used.



em1dfmfwd
^^^^^^^^^


