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

## Library Updates:
Get gcc-libstdc++-4.9.1-1.ram0.99.x86_64.rpm
https://sourceforge.net/projects/ramonelinux/files/Rel_0.99/releases/x86_64/packages/

### Extract
$ rpm2cpio gcc-libstdc++-4.9.1-1.ram0.99.x86_64.rpm | cpio -idmv

### Copy to location
$sudo cp libstdc++.so.6.0.20 to /usr/lib64/

### Remove and re-create symlinks to new library
$cd /usr/lib64/
#rm libstdc++.so.6
#ln -s libstdc++.so.6.0.20 libstdc++.so.6

### Additional Requirements
#### libstdc++.so.6.0.21 for patchelf
####  https://github.com/NixOS/patchelf

After successful compile, run commands:
patchelf --set-rpath $HOME/local/stata-png-fix/lib xstata-mp
patchelf --add-needed libpng16.so.16 xstata-mp


#### https://github.com/kylebarron/stata-png-fix
#### require libpng16.so.16 to address the libpng error
