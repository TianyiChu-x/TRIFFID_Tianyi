## LOTKA

#### Overview
The LOTKA module is a core component of the TRIFFID (Top-down Representation of Interactive Foliage and Flora Including Dynamics) vegetation model in HadCM3. It implements the Lotka-Volterra equations for interspecies competition to update the fractional coverage of each Plant Functional Type (PFT).
#### Purpose
The LOTKA subroutine simulates vegetation competition by:
1. Calculating competition coefficients between different vegetation types
2. Establishing dominance hierarchies based on canopy height
3. Computing available space for vegetation expansion (==mod1712 ??==)
4. Updating fractional coverage of each PFT using competition dynamics
#### Key Files
Source Code: ==/PUM64/um/vn4.5/src/a19_2a/A1G1G2/lotka2a.f==
Parameter Definitions: ==/PUM64/um/vn4.5/source/umpl.dir/LOTKA2A.dk==
Competition Algorithm: ==/PUM64/um/vn4.5/src/a19_2a/A1G1G2/compt2a.f==

#### Plant Functional Types (PFTs)
The model operates on 5 vegetation types:
1. Broadleaf Tree (BT) - Type 1
2. Needleleaf Tree (NT) - Type 2
3. C3 Grass (C3G) - Type 3
4. C4 Grass (C4G) - Type 4
5. Shrub (S) - Type 5

Plus 4 non-vegetation surface types (Urban, Water, Soil, Ice).


#### Competition Algorithm



in /PUM64/um/vn4.5/source/umpl.dir/LOTKA2A.dk
![[LOTKA2A.dk.png]]