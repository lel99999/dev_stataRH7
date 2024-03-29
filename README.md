# dev_stataRH7
Stata 16 deployment and development on RH7

#### Passing Values by Reference like Usernames/Passwords
- Create a file to save your username and password <br/>
```
local myuid MyUserID
local mypwd MyPassword
```

- Reference the file in Stata <br/>
```
quietly include ~/<filename_with_info>

uid(`myuid;) password(`mypwd')
```

## Additional configuration steps required:
### Update gcc
`$curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O`<br/>
`$tar jxvf gcc-5.4.0.tar.bz2`<br/>

### Install Dependencies
`$sudo yum install gmp-devel mpfr-devel libmpc-devel`<br/>

### Configuration and Install
`$mkdir gcc-5.4.0-build`<br/>
`$cd gcc-5.4.0-build`<br/>
`$../gcc-5.4.0/configure --enable-languages=c,c++ --disable-multilib`<br/>
`$make -j$(nproc) && sudo make install`<br/>

### Post Installation
`$export PATH=/usr/local/bin:$PATH`<br/>
`$export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH`<br/>

## Library Updates:
Get gcc-libstdc++-4.9.1-1.ram0.99.x86_64.rpm
https://sourceforge.net/projects/ramonelinux/files/Rel_0.99/releases/x86_64/packages/

### Extract
`$rpm2cpio gcc-libstdc++-4.9.1-1.ram0.99.x86_64.rpm | cpio -idmv`<br/>

### Copy to location
`$sudo cp libstdc++.so.6.0.20 to /usr/lib64/`<br/>

### Remove and re-create symlinks to new library
`$cd /usr/lib64/ or /lib64`<br/>

`#rm libstdc++.so.6`<br/>
`#ln -s libstdc++.so.6.0.20 libstdc++.so.6` *** Use the latest .21 *** <br/>
`#ln -s libstdc++.so.6.0.21 libstdc++.so.6`<br/>

### Additional Requirements
#### libstdc++.so.6.0.21 for patchelf
####  https://github.com/NixOS/patchelf

After successful compile, run commands:<br/>
`$patchelf --set-rpath $HOME/local/stata-png-fix/lib xstata-mp`<br/>
`$patchelf --add-needed libpng16.so.16 xstata-mp`<br/>

#### Updated *** NOTES *** Use included files instead of compile
```
$sudo cp /tmp/libstdc++.so.6.0.21 /usr/lib64
```

#### https://github.com/kylebarron/stata-png-fix
#### require libpng16.so.16 to address the libpng error
`$cp -r <source>/stata-png-fix <stata16_installdir>`<br/>
`$cd <stata16_installdir>/stata-png-fix`<br/>
**NOTE: view README or edit stata-png-fixed.sh and set STATA_DIR=<stata16_installdir><br/>
  need to copy libpng16.so.16.30.0 to /lib64<br/>
  `$sudo ln -s /lib64/libpng16.so.16.30.0 libpng16.so.16`<br/>

`$cd ./bash compile-stata-png-fix.sh`<br/>

#### Stata16 Gui requires JAVA (1.8 or higher) to be installed
`$java -version`<br/>
![openjdk version 1.8](https://github.com/lel99999/dev_stataRH7/blob/master/java-version-01.PNG)

#### Install JAVA (openJDK 1.8)
`$sudo yum install java-1.8.0-openjdk -y` <br/>

#### To update Stata16 to latest
*** NOTE: Stata <installdir> needs to be writeable *** <br/>
in stata type **db update**<br/>
or<br/>
type **update all, force**. (this also addresses **ERROR: Java installation not found**)<br/>
**NOTE <stata16_installdir> needs to be writeable<br/>

### Vagrant Notes
### For local Vagrant provisioning
`$vagrant up --no-provision`<br/>

for specific provisioning parts:<br/>
`$vagrant provision --provision-with main`<br/>
`$vagrant provision --provision-with update`<br/>

#### Running Ansible Playbook on localhost prompt for sudo 
```
$ansible-playbook playbook_localhost.yml -kK
SSH password:
BECOME password[defaults to SSH password]:
```

#### Fixing permission issues with STATATMP - change rwx permissions, set sticky bit
```
$sudo chmod -R 777 /STATATMP
$sudo chmod o+t /STATATMP
```

#### Notes for updating to STATA v17
[Stata v17 Update URL](https://www.stata.com/support/updates/stata17/stata17update_linux64.tar) <br/>

```
$cd /usr/local/stata17
$mv stata.lic stata.lic.bak
$./stinit

## Follow instructions to initialize new license
$chmod 644 stata.lic

## This should also address log4j vulnerabilities with Feb/22 Update
## launch Stata - check for updates
$update query

## if update package is available
$update all

```

#### Using with JDBC
- Make sure to add jdbc jar to adopath <br/>
  In STATA: <br/>
  ```
  local jar "postgresql42.3.3.jar"
  local driverc "org.postgresql.Driver"
  local url "jdbc:postgresql://<hostname>/<database>"
  jdbc connect jar("`jar'") driverclass("`driverc'") url("`url'") user("<username>") password("<password>")
  jdbc showtables/exec("<SQL>")

  jdbc describe <table>
  jdbc load table("<tablename>") 
  jdbc load, exec("`SQL'")
  ```

