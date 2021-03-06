.. module:: ase.calculators.dftb

=====
DFTB+
=====

Introduction
============

`DFTB+`_ is a density-functional based tight-binding code using
atom-centered orbitals. This interface makes it possible to use `DFTB+`_
as a calculator in ASE. You need Slater-Koster files for the combination
of atom types of your system. These can be obtained at dftb.org_.

.. _DFTB+: https://www.dftbplus.org/
.. _dftb.org: http://www.dftb.org/


Environment variables
=====================

.. highlight:: bash

The default command that ASE will use to start DFTB+ is
``dftb+ > PREFIX.out``. You can change this command by setting the
:envvar:`ASE_DFTB_COMMAND` environment variable, e.g.::

  $ export ASE_DFTB_COMMAND="/path/to/dftb+ > PREFIX.out"

For compatibility, also the old :envvar:`DFTB_COMMAND` variable can
be used, and the resulting command will be ``$DFTB_COMMAND > PREFIX.out``.
Before each DFTB+ calculation, also make sure that the
:envvar:`DFTB_PREFIX` variable points to the directory where
the Slater-Koster files are kept, e.g.::

  $ export DFTB_PREFIX=/path/to/mio-0-1/


Parameters
==========

As a FileIOCalculator, ``ase.calculators.dftb.Dftb`` writes input files,
runs DFTB+, and extracts the required information from the resulting output.
The input files are :file:`dftb_in.hsd` (the calculation settings),
:file:`geo_end.gen` (the initial geometry) and
:file:`dftb_external_charges.dat` (the external point charges
in case of electrostatic QM/MM embedding).

.. highlight:: none

All keywords for the :file:`dftb_in.hsd` input file (see the DFTB+ manual)
can be set by ASE. Consider the following input file block::

  Hamiltonian = DFTB {
    SCC = Yes
    SCCTolerance = 1e-8
    MaxAngularMomentum = {
      H = s
      O = p
    }
  }

.. highlight:: python

This can be generated by the DFTB+ calculator by using the
following settings::

  calc = Dftb(Hamiltonian_='DFTB',  # this line is included by default
              Hamiltonian_SCC='Yes',
              Hamiltonian_SCCTolerance=1e-8,
              Hamiltonian_MaxAngularMomentum_='',
              Hamiltonian_MaxAngularMomentum_H='s',
              Hamiltonian_MaxAngularMomentum_O='p')

In addition to keywords specific to DFTB+, also the following keywords
arguments can be used:

    restart: str
        Prefix for restart file.  May contain a directory.
        Default is None: don't restart.
    ignore_bad_restart_file: bool
        Ignore broken or missing restart file. By default, it is an
        error if the restart file is missing or broken.
    label: str (default 'dftb')
        Prefix used for the main output file (<label>.out).
    atoms: Atoms object (default None)
        Optional Atoms object to which the calculator will be
        attached. When restarting, atoms will get its positions and
        unit-cell updated from file.
    kpts: (default None)
        Brillouin zone sampling:

        * ``(1,1,1)`` or ``None``: Gamma-point only
        * ``(n1,n2,n3)``: Monkhorst-Pack grid
        * ``dict``: Interpreted as a path in the Brillouin zone if it
          contains the 'path_' keyword. Otherwise it is converted into a
          Monkhorst-Pack grid using ``ase.calculators.calculator.kpts2sizeandoffsets``
        * ``[(k11,k12,k13),(k21,k22,k23),...]``: Explicit (Nkpts x 3) array of k-points
          in units of the reciprocal lattice vectors (each with equal weight)

.. _path: https://wiki.fysik.dtu.dk/ase/ase/dft/kpoints.html#ase.dft.kpoints.bandpath


Examples
========

Below are three examples of how to use the DFTB+ calculator. The required
SKF files (i.e. :file:`H-H.skf`, :file:`H-O.skf`, :file:`O-H.skf`
and :file:`O-O.skf`) can for example be chosen from the mio-1-1_
parameter set.

.. _mio-1-1: http://www.dftb.org/parameters/download/mio/mio-1-1-cc/


Geometry optimization by ASE
----------------------------

.. literalinclude:: dftb_ex1_relax.py


Geometry optimization by DFTB+
------------------------------

.. literalinclude:: dftb_ex2_relaxbyDFTB.py


NVE-MD followed by NVT-MD (both by DFTB+)
-----------------------------------------

Note that this example is unphysical for at least two reasons:

    - no spin polarization for the oxygen molecule
    - the Berendsen coupling is too strong (0.01 here should be 0.0001)


.. literalinclude:: dftb_ex3_make_2h2o.py


DFTB+ calculator class
======================

.. autoclass:: ase.calculators.dftb.Dftb
