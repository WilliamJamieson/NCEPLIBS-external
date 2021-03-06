Setup instructions for NOAA RDHPC Hera using Intel-18.0.5.274

module purge
module load intel/18.0.5.274
module load impi/2018.0.4
module load netcdf/4.7.0
module use -a /scratch1/BMC/gmtb/software/modulefiles/generic
module load cmake/3.16.3
module li

> Currently Loaded Modules:
>  1) intel/18.0.5.274   2) impi/2018.0.4   3) netcdf/4.7.0   4) cmake/3.16.3

# Note: HDF5 is in: /apps/hdf5/1.10.5/intel/18.0.5.274
# Note: ZLIB and PNG are in: /usr/include, /usr/lib64/

export CC=icc
export CXX=icpc
export FC=ifort

export HDF5_ROOT=/apps/hdf5/1.10.5/intel/18.0.5.274
export PNG_ROOT=/usr

mkdir -p /scratch1/BMC/gmtb/software/NCEPLIBS-ufs-v1.0.0/intel-18.0.5.274/impi-2018.0.4/src
cd /scratch1/BMC/gmtb/software/NCEPLIBS-ufs-v1.0.0/intel-18.0.5.274/impi-2018.0.4/src

git clone -b ufs-v1.0.0 --recursive https://github.com/NOAA-EMC/NCEPLIBS-external
cd NCEPLIBS-external
mkdir build && cd build
# If netCDF is not built, also don't build PNG, because netCDF uses the default (OS) zlib in the search path
cmake -DBUILD_PNG=OFF -DBUILD_MPI=OFF -DBUILD_NETCDF=OFF -DCMAKE_INSTALL_PREFIX=/scratch1/BMC/gmtb/software/NCEPLIBS-ufs-v1.0.0/intel-18.0.5.274/impi-2018.0.4 .. 2>&1 | tee log.cmake
make VERBOSE=1 -j8 2>&1 | tee log.make

cd /scratch1/BMC/gmtb/software/NCEPLIBS-ufs-v1.0.0/intel-18.0.5.274/impi-2018.0.4/src
git clone -b ufs-v1.0.0 --recursive https://github.com/NOAA-EMC/NCEPLIBS
cd NCEPLIBS
mkdir build && cd build
cmake -DEXTERNAL_LIBS_DIR=/scratch1/BMC/gmtb/software/NCEPLIBS-ufs-v1.0.0/intel-18.0.5.274/impi-2018.0.4 -DCMAKE_INSTALL_PREFIX=/scratch1/BMC/gmtb/software/NCEPLIBS-ufs-v1.0.0/intel-18.0.5.274/impi-2018.0.4 .. 2>&1 | tee log.cmake
make VERBOSE=1 -j8 2>&1 | tee log.make
make install


- END OF THE SETUP INSTRUCTIONS -


The following instructions are for building the ufs-weather-model (standalone;
not the ufs-mrweather app - for the latter, the model is built by the workflow)
with those libraries installed.

This is separate from NCEPLIBS-external and NCEPLIBS, and details on how to get
the code are provided here: https://github.com/ufs-community/ufs-weather-model/wiki

After checking out the code and changing to the top-level directory of ufs-weather-model,
the following commands should suffice to build the model.


module purge
module load intel/18.0.5.274
module load impi/2018.0.4
module load netcdf/4.7.0
module use -a /scratch1/BMC/gmtb/software/modulefiles/generic
module load cmake/3.16.3

export CC=icc
export CXX=icpc
export FC=ifort

module use -a /scratch1/BMC/gmtb/software/modulefiles/intel-18.0.5.274/impi-2018.0.4
module load  NCEPlibs/1.0.0

export CMAKE_Platform=hera.intel
./build.sh 2>&1 | tee build.log
