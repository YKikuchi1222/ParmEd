# ParmEd

Cross-program parameter and topology file editor and molecular mechanical
simulator engine.

## Badges

![(Build/Test Status)](https://github.com/ParmEd/ParmEd/workflows/Tests/badge.svg)
[![codecov](https://codecov.io/gh/ParmEd/ParmEd/branch/master/graph/badge.svg)](https://codecov.io/gh/ParmEd/ParmEd)

## Description

ParmEd is a package designed to facilitate creating and easily manipulating
molecular systems that are fully described by a common classical force field.
Supported force fields include Amber, CHARMM, AMOEBA, and several others that
share a similar functional form (e.g., GROMOS).

ParmEd is capable of reading and writing to a wide array of different file
formats, like the Amber topology and coordinate files, CHARMM PSF, parameter,
topology, and coordinate files, Tinker parameter, topology, and coordinate
files, and many others. The expressive central data structure (the ``Structure``
class) makes it easy to quickly and safely manipulate a chemical system, its
underlying topology, and force field parameters describing its potential energy
function.

There are two parts of ParmEd---a documented API that you can incorporate into
your own Python scripts and programs, and a GUI/CLI pair of programs that
provide a means to quickly perform various modifications to chemical systems for
rapid prototyping.

The API also provides bindings to the [OpenMM](https://openmm.org)
library, permitting you to carry out full molecular dynamics investigations
using ParmEd on high-performant hardware, like AMD and NVidia GPUs.

## Installing ParmEd

To install ParmEd, either clone this git repository or download [the latest
release](https://github.com/ParmEd/ParmEd/releases) and unpack the resulting
tarball. This should create a new ParmEd source code directory. Change to that
directory and build ParmEd with the command

```sh
python setup.py install
```

Note, if you are using the system Python, you may need to either run the above
command as root (e.g., by using ``sudo``) or add the ``--user`` flag to install
it to your home directory. I would suggest the latter choice.

AMBER user can overwrite installed version by

```sh
python setup.py install --prefix=$AMBERHOME
```

## Testing ParmEd

In order to automatically run the ParmEd tests, execute the following:

```sh
cd test
py.test .
```

## Examples

```python
import parmed as pmd

# convert GROMACS topology to AMBER format
gmx_top = pmd.load_file('pmaawaterFE20mer2.top', xyz='pmaawaterFE20mer2.gro')
gmx_top.save('pmaa.top', format='amber')
gmx_top.save('pmaa.crd', format='rst7')

# convert AMBER topology to GROMACS, CHARMM formats
amber = pmd.load_file('prmtop', 'inpcrd')
# Save a GROMACS topology and GRO files
amber.save('gromacs.top')
amber.save('gromacs.gro')

# Save a CHARMM PSF and crd files
amber.save('charmm.psf')
amber.save('charmm.crd')

# Save a DLPOLY FIELD and CONFIG files
amber.save('dlpoly.field')
amber.save('dlpoly.config')

# convert mol2 to pdb file
mol2_parm = pmd.load_file('my.mol2')
mol2_parm.save('my.pdb')

# and many more
```

## Documentation

Want to learn more?  Visit the ParmEd documentation page at
https://parmed.github.io/ParmEd for examples, descriptions, and API
documentation.

## Authors and Contributors

The following people have contributed directly to the coding and validation
efforts in ParmEd (in alphabetical order).  And a special thanks to all of you
who helped improve this project either by providing feedback, bug reports, or
other general comments!

* Jason Swails (principal developer) | jason.swails@gmail.com

* Carlos Hernandez
* David L. Mobley
* Hai Nguyen
* Lee-Ping Wang
* Pawel Janowski

## License

```
                    LESSER GPL LICENSE INFO

Copyright (C) 2010 - 2014 Jason Swails

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU Lesser General Public License as published by
the Free Software Foundation

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU Lesser General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 59 Temple Place - Suite 330,
Boston, MA 02111-1307, USA.

The `fortranformat` package is released under the MIT license, Copyright (C)
Brendan Arnold. See `fortranformat/__init__.py` for more information.
```

## For Developers

The standard residue template library is generated from the chemical component database by running the `ccd.js` script
supplied by @dsehnal. Run it using the comments at the top of the file. The chemical component dictionary can be
downloaded from https://www.wwpdb.org/data/ccd.

The version used in the current release was downloaded on Jan 23, 2023.

The final file is generated using `get_hybridization_from_rdkit.py` to add the hybridization information as determined
by RDKit.

## Fork-specific Changes (YKikuchi1222)

This section documents local modifications in this fork only.

For TI/FEP dual-topology workflows used in `Hirabayashi_DDXGATA/exp10`:

- File: `parmed/tools/actions.py`
- Action: `tiMerge`
- Change: keep common atoms when they are connected to softcore atoms through
  CMAP terms (in addition to bond/angle/dihedral partner checks), to avoid
  dropping atoms that should remain in the merged topology.

## Citation

If you make use of ParmEd for research and would like to cite your use of it in
a research paper, please use the following citation:

https://dx.doi.org/10.1007/s10822-016-9977-1

## Change point
- file: `parmed/tools/actions.py` 
- logic: `keep_mask` of `tiMerge.execute`
- Updated the filtering logic to include a conditional scan of `atm2.cmaps`. If `atm1` is found within the `atom1` to `atom5` range of the CMAP entries, the element is retained by `setting keep_mask[i] = 1`.

### Addition
```python
if (atm1 in atm2.bond_partners or atm1 in atm2.angle_partners or
    atm1 in atm2.dihedral_partners):
    keep_mask[i] = 1
else:
    for cmap in atm2.cmaps:
        if atm1 in (cmap.atom1, cmap.atom2, cmap.atom3, cmap.atom4, cmap.atom5):
            keep_mask[i] = 1
            break
```
