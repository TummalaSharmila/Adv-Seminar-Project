GROMACS CODE:

gmx pdb2gmx -f complex.pdb -o complex.gro -water spc
gmx editconf -f complex.gro -o complex_box.gro -c -d 1.0 -bt cubic
 
Solvate the complex
gmx solvate -cp complex_box.gro -cs spc216.gro -o solvated.gro -p topol.top
 
Add Ions
gmx grompp -f ions.mdp -c solvated.gro -p topol.top -o ions.tpr
gmx genion -s ions.tpr -o solvated_ions.gro -p topol.top -pname NA -nname CL -neutral
 
Minimize Energy
gmx grompp -f em.mdp -c solvated_ions.gro -p topol.top -o em.tpr
gmx mdrun -v -deffnm em
 
Equilibrate the System
gmx grompp -f nvt.mdp -c em.gro -p topol.top -o nvt.tpr
gmx mdrun -v -deffnm nvt
gmx grompp -f npt.mdp -c nvt.gro -r nvt.gro -p topol.top -o npt.tpr
gmx mdrun -v -deffnm npt
 
Run Molecular Dynamics
gmx grompp -f md.mdp -c npt.gro -p topol.top -o md.tpr  -n index.ndx -maxwarn 1
gmx mdrun -v -deffnm md
 
Analyze Results
Trajectory Analysis:
gmx trjconv -s md.tpr -f md.trr -o md.xtc
RMSD/RMSF:
gmx rms -s md.tpr -f md.xtc -o rmsd.xvg
 

