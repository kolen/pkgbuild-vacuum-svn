# Contributor: Konstantin Mochalov <incredible.angst@gmail.com>

pkgname=vacuum-svn
pkgver=902
pkgrel=1
pkgdesc="Jabber IM client using Qt"
arch=('i686' 'x86_64')
url="http://code.google.com/p/vacuum-im/"
license=('GPL3')
conflicts=()
depends=('qt>=4.5.0' 'libidn')
makedepends=('subversion')
source=(vacuum vacuum.desktop)
md5sums=('5e02bf737bb334487bba6dbd2def7e76'
         '965dce02d21ffacae1aa050df6c87d79')

_svntrunk=http://vacuum-im.googlecode.com/svn/trunk/
_svnmod=vacuum-im-svn

build() {
  cd "$srcdir"
  if [ -d $_svnmod/.svn ]; then
    (cd $_svnmod && svn up -r $pkgver)
  else
    svn co $_svntrunk --config-dir ./ -r $pkgver $_svnmod
  fi
  msg "SVN checkout done or server timeout"

  cp -r $_svnmod $_svnmod-build || return 1
  cd $_svnmod-build || return 1

  msg "Starting qmake..."
  qmake -recursive vacuum.pro || return 1

  msg "Starting make..."

  # For some reasons, first make fails, second make finishes successfully
  make  
  make || return 1

  msg "Installing to package directory..."
  install -D vacuum $pkgdir/usr/lib/vacuum/vacuum || return 1
  cp -d src/libs/libutils.so \
        src/libs/libutils.so.1 \
        src/libs/libutils.so.1.0 \
        src/libs/libutils.so.1.0.0 $pkgdir/usr/lib/vacuum/ || return 1
  install -D $startdir/vacuum $pkgdir/usr/bin/vacuum || return 1
  mkdir -p $pkgdir/usr/lib/vacuum/plugins/
  install plugins/*.so $pkgdir/usr/lib/vacuum/plugins/ || return 1
  mkdir -p $pkgdir/usr/share/
  svn export --force resources $pkgdir/usr/share/vacuum  || return 1
  ln -s ../../share/vacuum/ $pkgdir/usr/lib/vacuum/resources || return 1
  install -Dm644 $startdir/vacuum.desktop $pkgdir/usr/share/applications/vacuum.desktop

  rm -rf $startdir/src/$_svnmod-build
}
