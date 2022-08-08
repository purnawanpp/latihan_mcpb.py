# Latihan menggunakan Molecular Dynamics Metal Ion menggunakan mcpb.py dari Amber #
Pemisahan Ligand dan Logam:
1. cat 1OKL.pdb | awk '$1=="HETATM"' | awk '$4=="MNS"' > MNS.pdb
2. cat 1OKL.pdb | awk '$1=="HETATM"' | awk '$4=="ZN"' > ZN.pdb

Latihan mcpb dimana diperoleh data berikut: 
1. File yang dibutuhkan tahap 2 MCPB.py -i 1OKL.in -s 2 yaitu file 1OKL_small_opt.fchk file tersebut adalah file gaussian
2. File yang dibutuhkan tahap 3 MCPB.py -i 1OKL.in -s 3 mcpb.py yaitu 1OKL_large_mk.log merupakan data log dari Perform the Merz-Kollman RESP charge calculation for the large model

# Mengecek apakah logam ZN sudah benar dengan perintah:
1. parmed -i mcpbpy_parmed.in -p 1OKL_solv.prmtop -c 1OKL_solv.inpcrd > check.txt

# Ubah format Amber ke Gromacs
1. Jalankan script python ini di jupyter notebook https://github.com/purnawanpp/latihan_mcpb.py/blob/main/Parmed_AmbertoGromacs.ipynb

# Running Gromacs (using terminal)
1. gmx grompp -f min.mdp -c gromacs.gro -p gromacs.top -o em.tpr
2. gmx mdrun -v -deffnm em
3. gmx grompp -f nvt.mdp -c em.gro -r em.gro -p gromacs.top -o nvt.tpr -maxwarn 1
4. gmx mdrun -v -deffnm nvt &
5. gmx grompp -f npt.mdp -c nvt.gro -t nvt.cpt -r nvt.gro -p gromacs.top -o npt.tpr -maxwarn 1
6. gmx mdrun -v -s npt.tpr -deffnm npt &
7. gmx grompp -f md.mdp -c npt.gro -t npt.cpt -p gromacs.top -o md.tpr
8. gmx mdrun -v -s md.tpr -deffnm md &

# Analisis
1. Baca file berikut: https://github.com/purnawanpp/latihan_mcpb.py/blob/main/mcpb.py.pdf

# Catatan:
1. Metode optimasi geometri dan Force konstan dapat diganti dengan metode dft dan basis set yang diinginkan, tetapi metode Merz-Kollman harus menggunakan DFT/SCF dan harus berhati-hati dengan perintah gaussian karena potensi eror yang tinggi

# Perhitungan MMGBSA
1. gmx make_ndx -f md.gro -o new_index.ndx
2. Pilih Protein dan Ligand (MS1): 1|13|14|15|17| akan dihasilkan Protein_HD1_HD2_HE1 :  4016 atoms
3. Keluar dengan mengetik: q
4. gmx trjconv -f md.xtc -o md_1_noPBC.xtc -s md.tpr -pbc mol -center -n new_index.ndx -ur compact
5. Pilih Protein dan System yaitu 1 dan 0
6. conda activate gmxMMPBSA
7. gmx_MMPBSA -O -i mmgbsa.in -cs md.tpr -cp gromacs.top -ci new_index.ndx -cg 21 16 -ct md_1_noPBC.xtc

# Referensi
1. http://ambermd.org/tutorials/advanced/tutorial20/mcpbpy.htm
