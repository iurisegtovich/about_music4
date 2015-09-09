# Use this Makefile with gmake

# Executable name 
CMD = post.exe

# -------- description of DFLAGS --------------- 
# DNAGCOMPILER - some nag ocmpiler specific parts especially for using
# system calls
# DSITE_HACK - some hacks in post code, which can be integrated to
#regular code with some work
# DBENT_GCMC - some special adjustments in rigidmoves for dealing with
# molecules whose COM might fall outside the tabulated area. This code
# is in test stage (2-12-03). Too dangerous to mix with regular code.
# DIDEAL_GAS - to prevent molecules  flying away from origin during
# ideal gas simulations
# DFAST_ASINT - fast atom sorbate interactions, for cb-gcmc 
# DSMAPNOZERO - any non zero site is turned to nearest nonzero site
  

# -------- Define environmental variable FORTRAN_COMPILER -----------
# Make sure it is defined
#          ifeq ($(strip$(FORTRAN_COMPILER)),)
# Otherwise you can define it here also by uncommenting next line
 FORTRAN_COMPILER= gfortran -fmax-errors=1
ifeq ($(FORTRAN_COMPILER),)
 FC = defaultCompiler
else
 FC= $(FORTRAN_COMPILER)
endif

# commented out for now !!
#define the general flags, used for all extension types
#GEN_FLAGS = -g -static 
#GEN_FLAGS =  -non_shared
#define the F77 compiler flags
F77FLAGS = 


### ----------- define flags for default compiler --------------------- 
###
###
ifeq ($(FC),defaultCompiler)
 F90FLAGS = 
 DEBUG_FLAGS = 
 DFLAGS = 
 OFLAGS =  
 LINKERFLAGS = 
endif


### ------------ define flags for ifort, intel-linux-compiler by INTEL ---------
###
###
ifeq ($(FC),ifort)
F90FLAGS = -g -debug all -check all # -O3 # -DSPME # untested options
F77FLAGS =  # -O3
DEBUG_FLAGS = 
OFLAGS = 
LINKERFLAGS = #-lfftw3 #-static-libcxa # for not using runtime libraries
endif


### ------------ define flags for nagf95, intel-linux compiler by NAG ---------
###
###
ifeq ($(FC),nagf95)
F90FLAGS = -w=unused #-useful for NAG compiler
#DEBUG_FLAGS = -g -C -gline #debug flags for NAG compiler
DFLAGS = -DNAGCOMPILER #-Define the cpp flags to be used
OFLAGS =  -O4 # optimization
#OFLAGS = -Wc,-mcpu=pentium -O4 # OR ,-mcpu=pentium4, athlon etc..
#LINKERFLAGS = -L/usr/local/lib/NAGWare/ -lf96 # maybe needed for NAG compiler
#LINKERFLAGS = -unsharedf95 # for not using runtime libraries
endif


### ----------- define flags for Old DEC/Compaq, alpha-linux ---------------
###
###
ifeq ($(FC),f90)
F90FLAGS = -u -std95  #-useful for DEC/Compaq compilers
DEBUG_FLAGS = -g -C -ladebug -warn unused -check underflow -check overflow -align records -check bounds #-debugging flags
#OFLAGS =  -fast # optimization
endif



### ---------- define flags for intel-linux lahey compiler -------------- 
### This compiler does not work for music code
###
ifeq ($(FC),lf95)
F90FLAGS = --f95 #-useful for the Lahey compiler
DEBUG_FLAGS = -g --ap --chkglobal --lst --psa --sav --trace --info --xref #-useful for the Lahey compiler
endif


### ---------- define flags for Absoft compiler ------------------------ 
###
###
ifeq ($(FC),AbSf95)
#DEBUG_FLAGS = -g -Rb -Rc -Rp -Rs #for Absoft compiler  (-Rb doesn't work)
DEBUG_FLAGS = -s -g -B111 -B80 -Rc -Rp -Rs #for Absoft compiler
LINKERFLAGS = -lU77  #needed for Absoft compiler, accesses UNIX commands
endif



# Targets and rules
#
#
#
#
# Implicit rules for handling src files
#  ( uses static pattern rules, see info for make )
%.o : %.F90 
	$(FC) $(GEN_FLAGS) $(DEBUG_FLAGS) $(F90FLAGS) $(OFLAGS) $(DFLAGS) -c $<
%.o : %.F
	$(FC) $(GEN_FLAGS) $(DEBUG_FLAGS) $(F77FLAGS) $(OFLAGS) -c $<

OBJECTS = eispack.o defaults.o gqbfgs.o utils.o file.o random.o \
matrixops.o stats.o sort.o vector.o wtime.o ffstate.o mapheader.o atom.o visxyz.o \
nrgcarrier.o morse.o harmonica.o cosexpansion.o cosexpansiondr.o mondello.o \
outofplane.o smooth.o spline.o commandline.o general.o matrix.o fundcell.o \
harmonic.o torjorg.o buck.o connects.o localcoords.o match.o storestats.o \
coul.o evansmorriss.o readgulp.o ljel.o coulomb.o wfcoul.o benzpot.o lj.o \
rattle.o readgauss2.o readturbo.o ljrep.o lj206.o ewr.o hs.o amap.o \
interpolate.o stopcriteria.o gcmodels.o rigidcoords.o nbrlist.o ciccotti.o \
ewald.o maps.o gmap.o simcell.o readoldgcmc.o readstruc.o gaussio.o \
turboio.o ssmap.o datafile.o sssum.o pmap.o movie.o molecules.o \
branchedcoords.o bbmodel.o ffcall.o molecule.o icmodel.o xternal.o bsmodel.o \
ipmodel.o conmodel.o ssdriver.o gulpio.o config.o tormodel.o music.o ff.o \
ssbasic.o pairmodel.o intramolecular.o 

$(CMD) : $(OBJECTS)
	$(FC) $(DEBUG_FLAGS) $(OFLAGS) $(LINKERFLAGS) $(OBJECTS)  -o ../$(CMD)

dec : $(OBJECTS)
	$(FC) $(DEBUG_FLAGS) $(OFLAGS) $(OBJECTS) -o ../$(CMD)

clean:
	/bin/rm -f *.o *.i *.il *.mod a.out make.log

cleano:
	rm -f *.o *.i 


depend : 
	makemake *.F90 *.F 

eispack.o                 :
defaults.o                :
gqbfgs.o                  : defaults.o
utils.o                   : defaults.o
file.o                    : defaults.o
random.o                  : defaults.o
matrixops.o               : defaults.o
stats.o                   : defaults.o
sort.o                    : defaults.o
vector.o                  : defaults.o utils.o
wtime.o                   : defaults.o utils.o
ffstate.o                 : simcell.o defaults.o
mapheader.o               : defaults.o utils.o
atom.o                    : defaults.o utils.o file.o
visxyz.o                  : defaults.o vector.o file.o
nrgcarrier.o              : defaults.o utils.o molecules.o
morse.o                   : defaults.o vector.o utils.o
harmonica.o               : utils.o defaults.o vector.o
cosexpansion.o            : defaults.o vector.o utils.o
cosexpansiondr.o          : defaults.o vector.o utils.o
mondello.o                : defaults.o vector.o utils.o
outofplane.o              : defaults.o vector.o utils.o
smooth.o                  : defaults.o utils.o file.o
spline.o                  : defaults.o utils.o matrixops.o
commandline.o             : defaults.o utils.o file.o general.o
general.o                 : defaults.o utils.o file.o random.o
matrix.o                  : defaults.o utils.o vector.o matrixops.o
fundcell.o                : defaults.o utils.o vector.o matrix.o
harmonic.o                : defaults.o utils.o vector.o ffstate.o
torjorg.o                 : defaults.o utils.o vector.o cosexpansion.o
buck.o                    : defaults.o utils.o vector.o ffstate.o
connects.o                : defaults.o utils.o file.o vector.o fundcell.o
localcoords.o             : defaults.o utils.o file.o vector.o matrix.o
match.o                   : defaults.o utils.o vector.o readstruc.o visxyz.o
storestats.o              : defaults.o utils.o stats.o molecules.o nrgcarrier.o
coul.o                    : defaults.o utils.o file.o vector.o atom.o
evansmorriss.o            : defaults.o general.o vector.o utils.o matrixops.o
readgulp.o                : defaults.o file.o vector.o utils.o visxyz.o
ljel.o                    : defaults.o utils.o file.o vector.o atom.o
coulomb.o                 : defaults.o utils.o file.o vector.o ffstate.o
wfcoul.o                  : defaults.o utils.o file.o vector.o ffstate.o
benzpot.o                 : defaults.o utils.o file.o vector.o ffstate.o
lj.o                      : defaults.o utils.o file.o vector.o atom.o ffstate.o
rattle.o                  : defaults.o general.o vector.o utils.o matrixops.o ffstate.o
readgauss2.o              : defaults.o file.o vector.o utils.o atom.o visxyz.o
readturbo.o               : defaults.o file.o vector.o utils.o atom.o visxyz.o
ljrep.o                   : defaults.o utils.o file.o vector.o atom.o ffstate.o
lj206.o                   : defaults.o utils.o file.o vector.o atom.o ffstate.o
ewr.o                     : defaults.o utils.o file.o vector.o ffstate.o simcell.o
hs.o                      : defaults.o utils.o file.o vector.o atom.o ffstate.o
amap.o                    : defaults.o utils.o vector.o simcell.o file.o ffstate.o
interpolate.o             : defaults.o utils.o vector.o simcell.o mapheader.o ffstate.o
stopcriteria.o            : config.o defaults.o file.o molecules.o storestats.o utils.o
gcmodels.o                : defaults.o utils.o vector.o molecules.o simcell.o rigidcoords.o branchedcoords.o
rigidcoords.o             : defaults.o utils.o vector.o matrix.o simcell.o molecules.o localcoords.o
nbrlist.o                 : config.o defaults.o file.o molecules.o simcell.o utils.o vector.o
ciccotti.o                : defaults.o general.o vector.o utils.o file.o visxyz.o matrixops.o
ewald.o                   : defaults.o utils.o vector.o fundcell.o simcell.o molecules.o ffstate.o
maps.o                    : defaults.o utils.o simcell.o vector.o pmap.o gmap.o ffstate.o
gmap.o                    : defaults.o utils.o file.o vector.o simcell.o mapheader.o interpolate.o
simcell.o                 : defaults.o utils.o molecules.o atom.o file.o fundcell.o vector.o random.o
readoldgcmc.o             : defaults.o utils.o file.o vector.o matrix.o molecules.o readstruc.o match.o
readstruc.o               : defaults.o utils.o file.o vector.o matrix.o molecules.o visxyz.o fundcell.o
gaussio.o                 : defaults.o file.o general.o utils.o vector.o readgauss2.o atom.o molecules.o
turboio.o                 : defaults.o file.o general.o utils.o vector.o readturbo.o atom.o molecules.o
ssmap.o                   : defaults.o utils.o vector.o simcell.o atom.o molecules.o config.o maps.o
datafile.o                : config.o defaults.o file.o general.o molecules.o storestats.o utils.o vector.o simcell.o
sssum.o                   : defaults.o vector.o ewald.o molecules.o utils.o config.o simcell.o atom.o ffstate.o
pmap.o                    : defaults.o utils.o file.o vector.o simcell.o amap.o mapheader.o interpolate.o ffstate.o
movie.o                   : defaults.o file.o utils.o vector.o config.o simcell.o atom.o molecules.o visxyz.o
molecules.o               : defaults.o utils.o file.o vector.o matrix.o fundcell.o connects.o localcoords.o molecule.o atom.o
branchedcoords.o          : defaults.o file.o random.o utils.o vector.o matrix.o atom.o molecule.o molecules.o connects.o
bbmodel.o                 : defaults.o utils.o file.o vector.o harmonica.o atom.o molecules.o connects.o simcell.o ffstate.o
ffcall.o                  : config.o defaults.o file.o molecules.o storestats.o utils.o ff.o simcell.o vector.o nrgcarrier.o
molecule.o                : defaults.o utils.o file.o vector.o matrix.o atom.o localcoords.o fundcell.o stats.o connects.o visxyz.o
icmodel.o                 : defaults.o utils.o file.o vector.o coul.o atom.o molecule.o molecules.o connects.o simcell.o ffstate.o
xternal.o                 : defaults.o utils.o file.o vector.o config.o molecules.o simcell.o gulpio.o gaussio.o turboio.o ffstate.o
bsmodel.o                 : defaults.o utils.o file.o vector.o morse.o harmonic.o atom.o molecule.o molecules.o connects.o simcell.o ffstate.o
ipmodel.o                 : defaults.o utils.o file.o vector.o lj.o buck.o atom.o molecule.o molecules.o connects.o simcell.o ffstate.o
conmodel.o                : defaults.o utils.o file.o vector.o evansmorriss.o rattle.o ciccotti.o atom.o molecule.o molecules.o connects.o simcell.o
ssdriver.o                : defaults.o vector.o utils.o file.o molecules.o config.o simcell.o ssbasic.o ssmap.o sssum.o xternal.o ffstate.o
gulpio.o                  : defaults.o file.o general.o vector.o utils.o readgulp.o simcell.o atom.o molecules.o bsmodel.o bbmodel.o tormodel.o ipmodel.o
config.o                  : defaults.o utils.o file.o general.o vector.o matrix.o atom.o molecules.o gcmodels.o simcell.o readoldgcmc.o readstruc.o match.o visxyz.o
tormodel.o                : defaults.o utils.o file.o vector.o cosexpansion.o cosexpansiondr.o torjorg.o mondello.o outofplane.o atom.o molecules.o connects.o simcell.o ffstate.o
music.o                   : defaults.o utils.o file.o vector.o commandline.o general.o wtime.o atom.o molecules.o datafile.o simcell.o config.o ff.o movie.o stopcriteria.o ffcall.o
ff.o                      : defaults.o utils.o vector.o file.o atom.o molecules.o nbrlist.o simcell.o config.o intramolecular.o sssum.o ssdriver.o ffstate.o nrgcarrier.o storestats.o spline.o sort.o
ssbasic.o                 : defaults.o file.o utils.o vector.o atom.o molecules.o config.o simcell.o nbrlist.o pairmodel.o smooth.o ffstate.o coulomb.o wfcoul.o ewr.o lj.o benzpot.o hs.o ljrep.o
pairmodel.o               : defaults.o file.o utils.o simcell.o vector.o atom.o molecules.o lj.o ljrep.o lj206.o ljel.o coulomb.o ewr.o hs.o wfcoul.o buck.o benzpot.o harmonic.o smooth.o ffstate.o
intramolecular.o          : defaults.o utils.o file.o atom.o molecule.o molecules.o connects.o vector.o matrix.o stats.o config.o simcell.o bsmodel.o bbmodel.o tormodel.o ipmodel.o icmodel.o conmodel.o xternal.o ciccotti.o ffstate.o
