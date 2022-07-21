# Latihan menggunakan Molecular Dynamics menggunakan mcpb.py dari Amber #
latihan mcpb dimana diperoleh data berikut: 
1. File yang dibutuhkan tahap 2 MCPB.py -i 1OKL.in -s 2 yaitu file 1OKL_small_opt.fchk file tersebut adalah file gaussian
2. File yang dibutuhkan tahap 3 MCPB.py -i 1OKL.in -s 3 mcpb.py1OKL_large_mk.log merupakan data log dari Perform the Merz-Kollman RESP charge calculation for the large model

# Ubah amber format ke Gromacs (dalam satu baris perintah jupyter notebook)
1. import parmed as pmd
2. #amber = pmd.load_file('prmtop', 'inpcrd')
3. amber = pmd.load_file('1OKL_solv.prmtop', '1OKL_solv.inpcrd')
4. #save a GROMACS topology and GRO file
5. amber.save('gromacs.top')
6. amber.save('gromacs.gro')

# Running AMBER MCPB.PY 
1. gmx grompp -f min.mdp -c gromacs.gro -p gromacs.top -o em.tpr
2. gmx mdrun -v -deffnm em
3. gmx grompp -f nvt.mdp -c em.gro -r em.gro -p gromacs.top -o nvt.tpr -maxwarn 1
4. gmx mdrun -v -deffnm nvt &
5. gmx grompp -f npt.mdp -c nvt.gro -t nvt.cpt -r nvt.gro -p gromacs.top -o npt.tpr -maxwarn 1
6. gmx mdrun -v -s npt.tpr -deffnm npt &
7. gmx grompp -f md.mdp -c npt.gro -t npt.cpt -p gromacs.top -o md.tpr
8. gmx mdrun -v -s md.tpr -deffnm md &
