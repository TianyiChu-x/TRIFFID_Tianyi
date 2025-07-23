## LOTKA

### Overview
The LOTKA module is a core component of the TRIFFID (Top-down Representation of Interactive Foliage and Flora Including Dynamics) vegetation model in HadCM3. It implements the Lotka-Volterra equations for interspecies competition to update the fractional coverage of each Plant Functional Type (PFT).
### Purpose
The LOTKA subroutine simulates vegetation competition by:
1. Calculating competition coefficients between different vegetation types
2. Establishing dominance hierarchies based on canopy height
3. Computing available space for vegetation expansion (**mod1712 ??**)
4. Updating fractional coverage of each PFT using competition dynamics
### Key Files
1. Source Code: **/PUM64/um/vn4.5/src/a19_2a/A1G1G2/lotka2a.f**
2. Parameter Definitions: **/PUM64/um/vn4.5/source/umpl.dir/LOTKA2A.dk**
3. Competition Algorithm: **/PUM64/um/vn4.5/src/a19_2a/A1G1G2/compt2a.f**

### Plant Functional Types (PFTs)
The model operates on 5 vegetation types:
1. Broadleaf Tree (BT) - Type 1
2. Needleleaf Tree (NT) - Type 2
3. C3 Grass (C3G) - Type 3
4. C4 Grass (C4G) - Type 4
5. Shrub (S) - Type 5

Plus 4 non-vegetation surface types (Urban, Water, Soil, Ice).

### Competition Algorithm
1. **Competition Coefficient Calculation**
The model calculates competition coefficients $COM(L,N,M)$ representing the influence of PFT $M$ on PFT $N$ at land point $L$.
- **Height-Based Competition**
  - Tree Competition: Broadleaf vs Needleleaf trees compete based on canopy height
  - Grass Competition: C3 vs C4 grasses compete based on canopy height
  - Hierarchical Dominance: Trees dominate grasses; shrubs have intermediate position
- **Canopy Height Formula**
	- $HC = A_{WL}/(A_{WS}*ETA_{SL}) * (LAI^{(B_{WL}-1)})$
		- $A_{WL}$: Allometric coefficient (woody biomass to LAI)
		- $A_{WS}$: Woody biomass multiplier
		- $ETA_{SL}$: Live stemwood coefficient
		- $LAI$: Leaf Area Index
		- $B_{WL}$: Allometric exponent
- **Sigmoidal Competition Function**
	- $COM(L,i,j) = 1.0/(1+EXP(POW*DIFF_{SUM}))$
		- $POW = 20.0$: Sharpness parameter for competition
		- $DIFF_{SUM} = (HC_i - HC_j)/(HC_i + HC_j)$: Normalized height difference

2. **Dominance Hierarchy**
The model establishes dominance order based on canopy heights:
- **Tree Layer:**
	- Taller tree type gets DOM rank 1, shorter gets rank 2
- **Shrub Layer:**
	- Shrubs get DOM rank 3
- **Grass Layer:**
	- Taller grass type gets DOM rank 4, shorter gets rank 5

3. **Competition Rules**
Specific Competition Pairs:
- **Broadleaf vs Needleleaf**: Height-based sigmoidal competition
- **C3 vs C4 Grass**: Height-based sigmoidal competition
- **Trees vs Grasses**: Trees completely dominate (COM = 0.0)
- **Shrubs vs Grasses**: Shrubs completely dominate (COM = 0.0)

4. **Space Calculation**
Available space for each PFT expansion:
- $SPACE(L,N) = 1.0 - NOSOIL(L) - FRAC_{MIN}*(NPFT-K) - SUM(COM(L,N,M)*FRAC(L,M))$
	- $NOSOIL(L) = 1 - FRAC_{VS}(L)$: Fraction unavailable to vegetation
	- $FRAC_{MIN} = 0.01$: Minimum "seed" fraction for each PFT
	- $FRAC_{VS}(L)$: Total vegetation + soil fraction

5. **Rate Calculation**
The change rate for each PFT:
- $B(L,N) = PC_S(L,N)*SPACE(L,N)/C_{VEG}(L,N) - G_{AREA}(N) - G_{ANTH}(L)$
	- $PC_S(L,N)$: Net carbon flux available for spreading
	- $C_{VEG}(L,N)$: Vegetation carbon content
	- $G_{AREA}(N)$: Natural disturbance rate for PFT N
	- $G_{ANTH}(L)$: Anthropogenic disturbance rate


### Input Parameters
| Parameter  | Description                      | Units           |
| ---------- | -------------------------------- | --------------- |
| LAND_FIELD | Total number of land points      | -               |
| TRIF_PTS   | Number of TRIFFID-active points  | -               |
| TRIF_INDEX | Indices of TRIFFID-active points | -               |
| C_VEG      | Vegetation carbon content        | kg C/m²         |
| FORW       | Forward timestep weighting       | -               |
| FRAC_VS    | Total vegetation + soil fraction | -               |
| GAMMA      | Inverse timestep                 | /360days        |
| G_ANTH     | Anthropogenic disturbance rate   | /360days        |
| LAI        | Leaf Area Index                  | -               |
| PC_S       | Net carbon flux for spreading    | kg C/m²/360days |

### Output Parameters
| Parameter | Description                      | Units     |
|-----------|----------------------------------|-----------|
| FRAC      | Updated fractional cover by type | -         |
| DFRAC     | Increment to areal fraction      | /timestep |


### Algorithm Flow
1. Initialize Competition Matrix: Set all coefficients to 1.0
2. Calculate Canopy Heights: Compute competitive heights for each PFT
3. Set Competition Coefficients: Apply height-based competition rules
4. Establish Dominance Hierarchy: Order PFTs by competitive ability
5. Calculate Available Space: Determine expansion space for each PFT
6. Compute Change Rates: Calculate vegetation fraction change rates
7. Update Fractions: Call COMPETE subroutine to solve competition

in /PUM64/um/vn4.5/source/umpl.dir/LOTKA2A.dk
![[./img/LOTKA2A.dk.png]](./img/LOTKA2A.dk.png)