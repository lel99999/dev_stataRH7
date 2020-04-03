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
`$sudo cp libstdc++.so.6.0.20 to /usr/lib64/`<br/>

### Remove and re-create symlinks to new library
`$cd /usr/lib64/ or /lib64`<br/>

#rm libstdc++.so.6
#ln -s libstdc++.so.6.0.20 libstdc++.so.6
#ln -s libstdc++.so.6.0.21 libstdc++.so.6

### Additional Requirements
#### libstdc++.so.6.0.21 for patchelf
####  https://github.com/NixOS/patchelf

After successful compile, run commands:<br/>
`$patchelf --set-rpath $HOME/local/stata-png-fix/lib xstata-mp`<br/>
`$patchelf --add-needed libpng16.so.16 xstata-mp`<br/>


#### https://github.com/kylebarron/stata-png-fix
#### require libpng16.so.16 to address the libpng error
`$cp -r <source>/stata-png-fix <stata16_installdir>`<br/>
`$cd <stata16_installdir>/stata-png-fix`<br/>
**NOTE: view README or edit stata-png-fixed.sh and set STATA_DIR=<stata16_installdir><br/>
  need to copy libpng16.so.16.30.0 to /lib64<br/>
  `$sudo ln -s /lib64/libpng16.so.16.30.0 libpng16.so.16`<br/>

`$cd ./bash compile-stata-png-fix.sh`<br/>

#### Stata16 Gui requires JAVA (1.8 or higher) to be installed

#### To update Stata16 to latest
in stata type **db update**<br/>
or<br/>
type **update all, force**. (this also addresses **ERROR: Java installation not found**)<br/>
**NOTE <stata16_installdir> needs to be writeable<br/>

### Vagrant Notes
### For local Vagrant provisioning
$vagrant up --no-provision

for specific provisioning parts:
$vagrant provision --provision-with main
$vagrant provision --provision-with update

