.. _theory:

Background theory
=================

Introduction
------------




Setup
-----

Detail about source
^^^^^^^^^^^^^^^^^^^

Program EM1DFM is designed to interpret frequency-domain, small loop, electromagnetic data. These data
are measurements of the magnetic field due to currents and magnetization induced in the Earth by a sinusoidal
time-varying current in a small transmitter loop. The receiver loop is assumed to be sufficiently small that
the measurements can be considered as point measurements of the magnetic field, and the transmitter loop
is assumed to be sufficiently small that it can be represented by a magnetic dipole. Program EM1DFM
can handle any combination of measurements made at different frequencies of the transmitter current, and
for any separations and heights above the Earth’s surface of the transmitter and receiver loops. The loops
can be oriented in the x-, y- or z-directions. Any combination of in phase and/or quadrature parts of the
fields can be dealt with. Program EM1DFM accepts observations in four different forms: values of the
secondary magnetic field (total minus free-space) normalized by the free-space field and given in parts-per-
million, values of the secondary field normalized by the free-space field and given in percent, values of the
secondary H-field in A/m, and values of the total H-field in A/m. If the transmitter and receiver have
the same orientation, the x-, y- and z-components of the secondary field are normalized by the
x-, y- and z-components of the free-space field respectively. If the transmitter and receiver have different orientations,
the secondary field is normalized by the magnitude of the free-space field.


Details about domain
^^^^^^^^^^^^^^^^^^^^

Program EM1DFM models the Earth beneath a measurement location as a stack of uniform, horizontal
layers. It is the physical properties of the layers that are obtained during the inversion of all measurements
gathered at a single location, with the depths to the layer interfaces remaining fixed. If measurements made
at multiple locations are being interpreted, the corresponding one-dimensional models are juxtaposed to
create a two-dimensional image of the subsurface.

Because horizontal position is meaningless in a model that varies only vertically, all measurements that
are to be inverted for a single one-dimensional model must be grouped together as a “sounding”. Each
different sounding is inverted for a separate one-dimensional model. The horizontal location at which a
sounding is centered is not used within the program, but is written out to distinguish results for different
soundings.

The electrical conductivity of Earth materials varies over several orders of magnitude, making it more
natural to invert for the logarithms of the layer conductivities rather than the conductivities themselves.
This also ensures that conductivities in the constructed model are positive. It is not appropriate, however, to
invert for the logarithms of the layer susceptibilities. Near-zero values of susceptibility would become overly
important and large values would be overestimated (Zhang & Oldenburg, 1997). Therefore susceptibilities
are found directly by the inversion, with the option of imposing a positivity constraint.

The coordinate system used for describing the Earth models has z as positive downwards, with the
surface of the Earth at z=0. The z-coordinates of the source and receiver locations (which must be above
the surface) are therefore negative.


Forward Modeling
----------------

The method used to compute the magnetic field values for a particular source-receiver arrangement over a
layered Earth model is the matrix propagation approach described in Farquharson et al. (2000). The method
uses the z-component of the Schelkunoff F-potential (Ward & Hohmann, 1987):

.. math::
    \begin{align}
    \mathbf{E} &= -\nabla \times \mathbf{F} \\
    \mathbf{H} &= -\sigma \mathbf{F} + \dfrac{1}{i \omega \mu} \nabla \big ( \nabla \cdot \mathbf{F} \big )
    \end{align}
    :name: Schelkunoff

where :math:`\mathbf{E}` and :math:`\mathbf{H}` are the electric and magnetic fields, :math:`\sigma` and :math:`\mu` 
are the conductivity and permeability of the uniform region to which the above equations refer, and a time-dependence of
:math:`e^{i\omega t}` has been assumed.

In the :math:`j^{th}` layer (where :math:`j>0`) with conductivity :math:`\sigma` and permeability :math:`\mu`,the
z-component of the Schelkunoff potential satisfies the following equation (assuming the quasi-static approximation):

.. math::
    \nabla^2 \mathbf{F_j} - i\omega \mu_j \sigma_j \mathbf{F} = 0
    :name: Helmholtz

The permeability is related to the susceptibility :math:`\kappa_j` via the following equation:

.. math::
    \mu_j = \mu_0 \big ( 1 + \kappa_j \big )
    :name: susc_def

where :math:`\mu_0` is the permeability of free space. Applying the two-dimensional Fourier transform to eq. :eq:`Helmholtz` gives:

.. math::
    \frac{d^2 \tilde{F}_j}{dz^2} - u_j^2 \tilde{F}_j = 0
    :name: Helmholtz1D

where :math:`u_j^2 = k_x^2 + k_y^2 + i \omega \mu_j \sigma_j`, and :math:`k_x` and :math:`k_y` are the horizontal wavenumbers. The
solution to this equation is:

.. math::
    \tilde{F}_j (k_x,k_y,z,\omega ) = D_j (k_x, k_y, \omega) \, e^{-u_j (z-z_j)} + U_j (k_x, k_y, \omega) \, e^{u_j (z-z_j)}
    :name: Helmholtz_gen_sol

where :math:`D_j` and :math:`U_j` are the coefficients of the downward and upgrade decaying parts of the solution, respectively. At
the interface between layer :math:`j-1` and layer :math:`j`, which is at depth :math:`z_j`, the conditions on :math:`\tilde{F}` are:

.. math::
    \begin{align}
    \tilde{F}_{j-1} \Big |_{z-z_j} &= \tilde{F}_j \Big |_{z-z_j}, \\
    \dfrac{1}{\mu_{j-1}} \dfrac{d \tilde{F}_{j-1}}{dz} \Bigg |_{z-z_j} &= \dfrac{1}{\mu_{j}} \dfrac{d \tilde{F}_{j}}{dz} \Bigg |_{z-z_j}
    \end{align}
    :name: bound_cond

Applying these conditions to the solutions in layer :math:`j` and layer :math:`j-1` (:math:`j \geq 2` ) gives:

.. math::
    \begin{bmatrix} e^{-u_{j-1} t_{j-1}} & e^{u_{j-1} t_{j-1}} \\ - \frac{u_{j-1}}{\mu_{j-1}} e^{-u_{j-1} t_{j-1}} & \frac{u_{j-1}}{\mu_{j-1}} e^{u_{j-1} t_{j-1}} \end{bmatrix}
    \begin{bmatrix} D_{j-1} \\ U{j-1} \end{bmatrix} =
    \begin{bmatrix} 1 & 1 \\ -\frac{u_j}{\mu_j} & \frac{u_j}{\mu_j} \end{bmatrix}
    \begin{bmatrix} D_{j} \\ U{j} \end{bmatrix},
    :name: Layer_soln

where :math:`t_{j-1} = z_j - z_{j-1}` is the thickness of layer :math:`j-1`. Rearranging, explicitly factoring out from the matrix
the exponential term whose argument has a positive real part, gives:

.. math::
    \begin{bmatrix} D_{j-1} \\ U_{j-1} \end{bmatrix} =
    e^{u_{j-1}t{j-1}} \mathbf{M_j} \begin{bmatrix} D_{j} \\ U{j} \end{bmatrix},
    :name:

where

.. math::
    \mathbf{M_j} = \begin{bmatrix} \frac{1}{2} \Big ( 1 + \frac{\mu_{j-1} u_j}{\mu_j u_{j-1}} \Big ) & \frac{1}{2} \Big ( 1 - \frac{\mu_{j-1} u_j}{\mu_j u_{j-1}} \Big ) \\
    \frac{1}{2} \Big ( 1 - \frac{\mu_{j-1} u_j}{\mu_j u_{j-1}} \Big ) e^{-2u_{j-1} t_{j-1}} & \frac{1}{2} \Big ( 1 + \frac{\mu_{j-1} u_j}{\mu_j u_{j-1}} \Big ) e^{-2u_{j-1} t_{j-1}} \end{bmatrix}
    :name:

for :math:`j \geq 2`. In layer 0 (the air interface), :math:`\tilde{F}` is given by:

.. math::
    \tilde{F}_0 = D_0 e^{-u_0 z} + U_0 e^{u_0 z},
    :name:

which leads to

.. math::
    \begin{bmatrix} D_0 \\ U_0 \end{bmatrix} = \mathbf{M_1} \begin{bmatrix} D_1 \\ U_1 \end{bmatrix}
    :name: Layer_soln_0

and

.. math::
    \mathbf{M_1} = \begin{bmatrix} \frac{1}{2} \Big ( 1 + \frac{\mu_0 u_1}{\mu_1 u_0} \Big ) & \frac{1}{2} \Big ( 1 - \frac{\mu_0 u_1}{\mu_1 u_0} \Big ) \\
    \frac{1}{2} \Big ( 1 - \frac{\mu_0 u_1}{\mu_1 u_0} \Big ) & \frac{1}{2} \Big ( 1 + \frac{\mu_0 u_1}{\mu_1 u_0} \Big ) \end{bmatrix}
    :name:

Application of eqs. :eq:`Layer_soln` and :eq:`Layer_soln_0` relates the coefficients, :math:`U_0` and :math:`D_0`, of the solution in the air to those, :math:`U_M` and :math:`D_M`, of
the solution in the basement halfspace:

.. math::
    \begin{bmatrix} D_0 \\ U_0 \end{bmatrix} = \mathbf{M_1} exp \Bigg ( \sum_{j=1}^M u_{j-1} t_{j-1} \Bigg ) \prod_{j=1}^M \mathbf{M_j} \begin{bmatrix} D_M \\ U_M \end{bmatrix}
    :name: Matrix_soln

There is no upward-decaying part of the solution in the basement halfspace (thus :math:`U_M = 0`). In the air, the
downward-decaying part is due to the source (thus :math:`D_0 = D_0^s`). Therefore eq. :eq:`Matrix_soln` can be rewritten as:

.. math::
    \begin{bmatrix} D_0^2 \\ U_0 \end{bmatrix} = E \, \mathbf{ P} \begin{bmatrix} D_M \\ 0 \end{bmatrix}
    :name: Matrix_soln2

where the matrix :math:`\mathbf{P}` is given by

.. math::
    \mathbf{P} = \mathbf{M_1} \prod_{j=1}^M \mathbf{M_j}
    :name: M_prod

and the factor :math:`E` is given by:

.. math::
    E = exp \Bigg ( \sum_{j=1}^M u_{j-1} t_{j-1} \Bigg )
    :name:

From eq. :eq:`Matrix_soln2`, we see that:

.. math::
    D_M = \frac{1}{E} \frac{1}{P_{11}} D_0^s
    :name:

and

.. math::
    U_0 = E \, P_{21} \, D_M
    :name:

Substituting the previous two equations gives:

.. math::
    U_0 = \frac{P_{21}}{P_{11}} D_0^s
    :name:

which does not involve any exponential terms whose arguments have positive real parts, making this formulation inherently stable.
The solution for :math:`\tilde{F}` in the air halfspace is therefore given by:

.. math::
    \tilde{F}_0 = D_0^s \Big ( e^{-u_0 z} + \frac{P_{21}}{P_{11}} e^{u_0 z} \Big )
    :name: Final_soln

For a unit vertical magnetic dipole source at a height :math:`h` (i.e. :math:`z = -h` for :math:`h>0`) above the surface of the Earth:

.. math::
    D_0^s = \frac{i\omega \mu_0}{2 u_0}e^{-u_0 h}
    :name: Source_vert

(Ward & Hohmann, 1987, eq. 4.40), and for a unit x-directed magnetic dipole source at :math:`z=-h`:

.. math::
    D_0^s = - \frac{i\omega \mu_0}{2} \frac{ik_x}{k_x^2 + k_y^2} e^{-u_0 h}
    :name: Source_horiz

(Ward & Hohmann, 1987, eq. 4.106). Once whichever of these terms is appropriate is substituted into
eq. :eq:`Final_soln`, the solution is completed by converting the required inverse two-dimensional Fourier transform to
a Hankel transform, and using eq. :eq:`Schelkunoff` to obtain the three components of the H-field above the Earth model (:math:`z<0`):

.. math::
    \begin{align}
    H_x(x,y,z,\omega) &= \frac{1}{4\pi} \frac{x}{r} \int_0^\infty \Big ( e^{-\lambda |z+h|} - \frac{P_{21}}{P_{11}} e^{\lambda (z-h)} \Big ) \lambda^2 J_1(\lambda r) d\lambda \\
    H_y(x,y,z,\omega) &= \frac{1}{4\pi} \frac{y}{r} \int_0^\infty \Big ( e^{-\lambda |z+h|} - \frac{P_{21}}{P_{11}} e^{\lambda (z-h)} \Big ) \lambda^2 J_1(\lambda r) d\lambda \\
    H_z(x,y,z,\omega) &= \frac{1}{4\pi}             \int_0^\infty \Big ( e^{-\lambda |z+h|} + \frac{P_{21}}{P_{11}} e^{\lambda (z-h)} \Big ) \lambda^2 J_0(\lambda r) d\lambda
    \end{align}
    :name: Soln_zdip

for a z-directed magnetic dipole source at (:math:`0,0,-h`), :math:`h>0`, and 

.. math::
    \begin{align}
    H_x(x,y,z,\omega) &= -\frac{1}{4\pi} \Big ( \frac{1}{r} - \frac{2x^2}{r^3} \Big ) \int_0^\infty \Big ( e^{-\lambda |z+h|} - \frac{P_{21}}{P_{11}} e^{\lambda (z-h)} \Big ) \lambda J_1(\lambda r) d\lambda \\
    &-\frac{1}{4\pi} \frac{x^2}{r^2} \int_0^\infty \Big ( e^{-\lambda |z+h|} - \frac{P_{21}}{P_{11}} e^{\lambda (z-h)} \Big ) \lambda^2 J_0(\lambda r) d\lambda \\
    H_y(x,y,z,\omega) &= \frac{1}{2\pi} \frac{xy}{r^3} \int_0^\infty \Big ( e^{-\lambda |z+h|} - \frac{P_{21}}{P_{11}} e^{\lambda (z-h)} \Big ) \lambda J_1(\lambda r) d\lambda \\
    &-\frac{1}{4\pi} \frac{xy}{r^2} \int_0^\infty \Big ( e^{-\lambda |z+h|} - \frac{P_{21}}{P_{11}} e^{\lambda (z-h)} \Big ) \lambda^2 J_0(\lambda r) d\lambda \\
    H_z(x,y,z,\omega) &= \frac{1}{4\pi} \frac{x}{r} \int_0^\infty \Big ( e^{-\lambda |z+h|} + \frac{P_{21}}{P_{11}} e^{\lambda (z-h)} \Big ) \lambda^2 J_1(\lambda r) d\lambda
    \end{align}
    :name: Soln_xdip

for a x-directed magnetic dipole source at (:math:`0,0,-h`), :math:`h>0`.

The Hankel transforms in eqs. :eq:`Soln_zdip` and :eq:`Soln_xdip` are computed using the digital filtering routine of Anderson
(1982). The kernels of these equations are pre-computed at a certain number of logarithmically-spaced values of :math:`\lambda`.
Anderson’s routine then extracts the values of the kernels at the values of :math:`\lambda` it requires by cubic
spline interpolation. The number of values of :math:`\lambda` at which the kernels are pre-computed (50 minimum) can
be specified in the input file “em1dfm.in”; see “line 11” in the input file description (Section 3.1.1 of the
Manual).

There are three places where previously-computed components of eqs. eqs. :eq:`Soln_zdip` and :eq:`Soln_xdip` can be re-used. The
propagation of the matrices through the layers depends on frequency, and must be re-done for each different
value. However, the propagated matrix :math:`\mathbf{P}`, and hence the ratio :math:`P_{21}/P_{11}`, does not depend on the relative
location and orientation of the transmitter and receiver, and so can be re-used for all transmitters and
receivers for the same frequency. Furthermore, if there are multiple transmitter-receiver pairs with the same
height (and the same frequency), there is no difference in the kernels of their Hankel transforms, and so the
values of the kernels computed for one pair can be re-used for all the others. It is to ensure this grouping of
the survey parameters that the observations file is structured the way it is (see Section 3.1.2 of the Manual).

The individual propagation matrices :math:`\mathbf{M_j}`, and each matrix computed in the construction of the propa-
gation matrix :math:`\mathbf{P}`, are saved in the forward-modelling routine. These are then re-used in the computation of the sensitivities.


Computing Sensitivities
-----------------------

The inverse problem of determining the conductivity and/or susceptibility of the Earth from electromagnetic
measurements is nonlinear. Program EM1DFM uses an iterative procedure to solve this problem. At each
iteration the linearized approximation of the full nonlinear problem is solved. This requires the Jacobian
matrix of sensitivities, :math:`\mathbf{J} = (\mathbf{J^\sigma}, \mathbf{J^\kappa})` where:

.. math::
    \begin{align}
    J_{ij}^\sigma &= \frac{\partial d_i}{\partial log \, \sigma_j} \\
    J_{ij}^\kappa &= \frac{\partial d_i}{\partial k_j}
    \end{align}
    :name: Sensitivity

in which :math:`d_i` is the :math:`i^{th}` observation, and :math:`\sigma_j` and :math:`\kappa_j` are the conductivity and susceptibility of the :math:`j^th` layer.

The algorithm for computing the sensitivities is obtained by differentiating the expressions for the H-
fields (see Section 2.3) with respect to the model parameters (Farquharson et al., 2000). For example, the
sensitivity with respect to :math:`m_j` (either the conductivity or susceptibility of the :math:`j^th` layer) of the
z-component of the H-field for a z-directed magnetic dipole source is given by differentiating the third expression in :eq:`Soln_zdip`:

.. math::
    \frac{\partial H_z}{\partial m_j} (x,y,z,\omega) = \frac{1}{4\pi} \int_0^\infty \Big ( e^{-\lambda |z+h|} + \frac{\partial}{\partial m_j} \Bigg [ \frac{P_{21}}{P_{11}} \Bigg ] e^{\lambda (z-h)} \Big ) \lambda^2 J_0(\lambda r) d\lambda
    :name:

The derivative of the coefficient is simply:

.. math::
    \frac{\partial}{\partial m_j} \Bigg [ \frac{P_{21}}{P_{11}} \Bigg ] = \frac{\partial P_{21}}{\partial m_j} \frac{1}{P_{11}} - \frac{\partial P_{11}}{\partial m_j} \frac{P{21}}{P_{11}^2}
    :name:

where :math:`P_{11}` and :math:`P_{21}` are elements of the propagation matrix :math:`\mathbf{P}` given by eq. :eq:`M_prod`. The derivative of :math:`\mathbf{P}` with respect to :math:`m_j` (:math:`1 \leq j \leq M-1`) is

.. math::
    \frac{\partial \mathbf{P}}{\partial m_j} = \mathbf{M_1 M_2 ... M_{j-1}} \Bigg ( \frac{\partial \mathbf{M_j}}{\partial m_j} \mathbf{M_{j+1}} + \mathbf{M_j} \frac{\partial \mathbf{M_{j+1}}}{\partial m_j} \Bigg ) \mathbf{M_{j+2} ... M_M}
    :name:

The sensitivities with respect to the conductivity and susceptibility of the basement halfspace are given by"

.. math::
    \frac{\partial \mathbf{P}}{\partial m_M} = \mathbf{M_1 M_2 ... M_{M-1}} \frac{\partial \mathbf{M_M}}{\partial m_M} 
    :name:

The derivatives of the individual layer matrices with respect to the conductivities and susceptibilities are
straightforward to derive, and are not given here.

Just as for the forward modelling, the Hankel transform in eq. (32), and those in the corresponding
expressions for the sensitivities of the other observations, are computed using the digital filtering routine of Anderson (1982).

The partial propagation matrices

.. math::
    \mathbf{P_k} = \mathbf{M_1} \prod_{j=2}^k \mathbf{M_j}, \;\;\; k=2,...,M
    :name:

are computed during the forward modelling, and saved for re-use during the sensitivity computations. This
sensitivity-equation approach therefore has the efficiency of an adjoint-equation approach.


Inversion Methodologies
-----------------------

In program EM1DFM, there are four different inversion algorithms. They all have the same general formulation (described in Section 2.5.1), but differ in their treatment of the trade-off parameter (see Sections 2.5.2
to 2.5.5). In addition, there are four possibilities for the Earth model constructed by the inversion: (a) just conductivity, (b) just susceptibility (with positivity enforced), (c) both conductivity and susceptibility (with
positivity of the susceptibilities enforced), and (d) both conductivity and susceptibility (without the positivity constraint).

General formulation
^^^^^^^^^^^^^^^^^^^

The aim of each inversion algorithm is to construct the simplest model that adequately reproduces the
observations. This is achieved by posing the inverse problem as an optimization problem in which the model
is sought that minimizes the objective function:

.. math::
    \Phi = \phi_d + \beta \phi_m - \gamma \phi_{LB}
    :name:

The three components of this objective function are as follows. :math:`\phi_d` is the data misfit:

.. math::
    \phi_d = \| \mathbf{W_d} (\mathbf{d - d^{obs}} ) \|^2
    :name:

where :math:`\| \, \cdot \. \|` represents the :math:`l_2`-norm, :math:`d^{obs}` is the vector containing the
:math:`N` observations, and :math:`d` is the forward-modelled data. It is assumed that the noise in the observations is Gaussian and uncorrelated, and that the
estimated standard deviation of the noise in the :math:`i^{th}` observation is of the form :math:`s_0 \hat{s}_i`, where :math:`\hat{s}_i` indicates the
amount of noise in the :math:`i^{th}` observation relative to that in the others, and is a scale factor that specifies
the total amount of noise in the set of observations. The matrix :math:`\mathbf{W_d}` is therefore given by:








