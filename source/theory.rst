.. _theory:

Theory
======

This page introduces the governing equations and the numerical discretization
at a high level. nekRS includes models for incompressible flow, a partially
compressible low-Mach formulation, the Stokes equations, and the :math:`k`-:math:`\tau`
:term:`RANS` equations.

.. _ins_model:

Incompressible Flow Model
-------------------------

The governing equations for conservation of mass, momentum, and energy for
an incompressible fluid are

.. math::

  \nabla\cdot\mathbf u=0

.. math::

  \rho\left(\frac{\partial\mathbf u}{\partial t}+\mathbf u\cdot\nabla\mathbf u\right)=-\nabla P=\nabla\cdot\tau+\rho\ \mathbf f

.. math::

  \rho C_p\left(\frac{\partial T}{\partial t}+\mathbf u\cdot\nabla T\right)=\nabla\cdot\left(k\nabla T\right)+\dot{q}

In these equations, :math:`t` is time,
:math:`\mathbf u` is the velocity vector, :math:`\rho` is the density, :math:`P` is the pressure,
:math:`\tau` is the viscous stress tensor, :math:`\mathbf f` is a force vector, :math:`C_p` is the
isobaric specific heat capacity, :math:`T` is the temperature, :math:`k` is the thermal conductivity,
and :math:`\dot{q}` is a volumetric heat source. If the viscosity is constant, the viscous stress tensor
can be contracted to give

.. math::

  \nabla\cdot\tau=\nabla^2\mathbf u

This is referred to as the "no-stress" formulation. In the general case for non-constant viscosity,
the viscous stress tensor is given by the Navier-Stokes closure as

.. math::

  \nabla\cdot\tau=\nabla\cdot\left\lbrack\mu\left(\nabla\mathbf u+\nabla\mathbf u^T\right)\right\rbrack

.. _nondimensional_eqs:

Non-Dimensional Formulation
"""""""""""""""""""""""""""

It is often advantageous to solve these equations in non-dimensional form. Here, we introduce
the non-dimensional form in as general a manner as possible, assuming the use of variable
material properties for density, viscosity, specific heat capacity, and thermal conductivity
that are functions of temperature, :math:`\rho=\rho(T)`, :math:`\mu=\mu(T)`,
:math:`C_p=C_p(T)`, and :math:`k=k(T)`. For simplicity, the functional notation is
omitted throughout.

Introduce
the non-dimensional variables :math:`\mathbf x^\dagger=\frac{\mathbf x}{L}`,
:math:`\mathbf u^\dagger=\frac{\mathbf u}{U}`, :math:`t^\dagger=\frac{tU}{L}`,
and :math:`\mathbf f^\dagger=\frac{\mathbf f L}{U^2}`. For the material properties,
we non-dimensionalize based on some reference temperature :math:`T_0`, such that
:math:`\rho^\dagger=\frac{\rho}{\rho_0}`, :math:`\mu^\dagger=\frac{\mu}{\mu_0}`,
:math:`k^\dagger=\frac{k}{k_0}`, and :math:`C_p^\dagger=\frac{C_p}{C_{p,0}}`. Here,
a subscript of :math:`0` is shorthand notation that indicates that the property
is evaluated at :math:`T_0`, such that :math:`k_0\equiv k(T_0)`.
Finally, for convection-dominated flows,
the pressure is non-dimensionalized in terms of the dynamic pressure as
:math:`P^\dagger=\frac{P}{\rho_0 U^2}`.

Inserting these non-dimensional variables
into the conservation of mass, momentum, and energy equations gives

.. math::

  \frac{\partial u_i^\dagger}{\partial x_i^\dagger}=0

.. math::

  \rho^\dagger\left(\frac{\partial u_i^\dagger}{\partial t^\dagger}+u_j^\dagger\frac{\partial u_i^\dagger}{\partial x_j^\dagger}\right)=-\frac{\partial P^\dagger}{\partial x_i^\dagger}+\frac{1}{Re}\frac{\partial \tau_{ij}^\dagger}{\partial x_j^\dagger}+\rho^\dagger f_i^\dagger

In these equations, the :math:`\nabla` are expanded to explicitly show that all derivatives
are taken with respect to the nondimensional space variable :math:`\mathbf x^\dagger`. :math:`Re`
is the Reynolds number

.. math::

  Re\equiv\frac{\rho_0 UL}{\mu_0}

To non-dimensionalize the energy conservation equation, use the previous non-dimensional
variables in addition to a non-dimensional temperature, :math:`T^\dagger=\frac{T-T_0}{\Delta T}`,
where :math:`\Delta T` is a reference temperature rise relative to a baseline temperature
:math:`T_0`. The heat source is non-dimensionalized as :math:`\dot{q}^\dagger=\frac{\dot{q}}{\rho_0 C_{p,0} U\Delta T/L}`,
which arises naturally from the simple formulation of bulk energy conservation of
:math:`Q=\dot{m}C_p\Delta T`, where :math:`Q` is a heat source (units of Watts) and
:math:`\dot{m}` is a mass flowrate.

Inserting these non-dimensional variables into the energy conservation equation gives

.. math::

  \rho^\dagger C_p^\dagger\left(\frac{\partial T^\dagger}{\partial t^\dagger}+u_i^\dagger\frac{\partial T^\dagger}{\partial x_i^\dagger}\right)=\frac{1}{Pe}\frac{\partial}{\partial x_i^\dagger}\left(k^\dagger\frac{\partial T^\dagger}{\partial x_i^\dagger}\right)+\dot{q}^\dagger

where :math:`Pe` is the Peclet number,

.. math::

  Pe\equiv\frac{LU}{\alpha}

and :math:`\alpha` is the thermal diffusivity,

.. math::

  \alpha\equiv\frac{k_0}{\rho_0 C_{p,0}}

Low-Mach Partially-Compressible Model
-------------------------------------

Stokes Equations
----------------

RANS Models
-----------

.. note::

  Even if the molecular viscosity is constant, you must set ``stressFormulation = true`` in
  the input file because the total viscosity (molecular plus turbulent) will not be constant.


