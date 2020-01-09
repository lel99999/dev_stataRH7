# dev_stataRH7
Stata 16 deployment and development on RH7

## Additional configuration steps required:
### Update gcc
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxvf gcc-5.4.0.tar.bz2

### Install Dependencies
yum install gmp-devel mpfr-devel libmpc-devel

### Configuration and Install
mkdir gcc-5.4.0-build
cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-multilib
make -j$(nproc) && make install

### Post Installation
export PATH=/usr/local/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH
