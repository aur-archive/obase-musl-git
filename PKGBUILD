 
# Maintainer: Jens Staal <staal1978@gmail.com>

pkgname=obase-musl-git
pkgver=20120606
pkgrel=1
pkgdesc="OpenBSD userland ported to Linux, statically linked to musl libc"
arch=('i686' 'x86_64')
url="https://github.com/chneukirchen/obase/"
#url="http://www.openbsd.org/"
license=('BSD')
depends=('bmake' 'openssl' 'musl')
makedepends=('git' 'heirloom-cvs')
options=(!strip)
provides=('obase' 'libobase')
sources=('diff3make.patch' 'lordermake.patch')
md5sums=('2b83a0b6051deb5a2dce5eb4905e1f4f' '7d41f5824a857528205b6ae0659acbde')

_gitroot=('git://github.com/chneukirchen/obase.git musl')
_gitname=('obase')

build() {
 cd $srcdir

msg "getting obase sources"
   if [ -d $_gitname ] ; then
    cd $_gitname && git pull $_gitroot
    msg "The local files are updated."
  else
    git clone $_gitroot $_gitname
  fi
  

msg "setting up build directory"
  rm -rf $srcdir/build #starting fresh
  cp -ar $srcdir/$_gitname $srcdir/build
  cd $srcdir/build
  bmake CC=musl-gcc
}

package() {
  mkdir -p $pkgdir/opt/obase/{etc,bin,sbin,lib,usr/lib,usr/libexec,usr/bin,usr/sbin,usr/share/misc,usr/share/man,usr/share/dict/special/4bsd}
  export DESTDIR=$pkgdir/opt/obase
  cd $srcdir/build
  msg "patching some problematic makefiles and other hacks..."
# GNU coreutils install -s causes problems, use Heirloom install instead. Would be nice with an obase install
  rm -rf $srcdir/install
  ln -s /usr/heirloom/bin/ucb/install $srcdir/install
  export PATH=$srcdir:$PATH
  # an argument to install "c" changed to "-c".
  patch -p0 $srcdir/build/src/usr.bin/diff3/Makefile $startdir/diff3make.patch 
# install -S does not work with heirloom install, changed to -s  
  patch -p0 $srcdir/build/src/usr.bin/lorder/Makefile $startdir/lordermake.patch
# 
  msg "hacks end here, time for the real install..."
  bmake install
  msg2 "building done"
  msg2 "copying libobase include directory to /opt/obase/usr/include"
  /bin/cp -ar $srcdir/obase/libobase/include $pkgdir/opt/obase/usr/
  msg2 "copying some good-to-have binaries from Heirloom that can not (yet) be built from obase"
  cp /usr/heirloom/bin/ucb/install $pkgdir/opt/obase/usr/bin/
  cp /usr/heirloom/bin/grep $pkgdir/opt/obase/usr/bin/
  cp /usr/heirloom/bin/tail $pkgdir/opt/obase/usr/bin/
  msg2 "copying relevant Heirloom man pages to the corresponding directory in obase"
  cp /usr/heirloom/share/man/man1b/install.1b $pkgdir/opt/obase/usr/share/man/cat/cat1/install.1
  cp /usr/heirloom/share/man/man1/grep.1 $pkgdir/opt/obase/usr/share/man/cat/cat1/
  cp /usr/heirloom/share/man/man1/tail.1 $pkgdir/opt/obase/usr/share/man/cat/cat1/
}
