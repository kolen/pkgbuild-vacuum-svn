# Contributor: Konstantin Mochalov <incredible.angst@gmail.com>

pkgname=vacuum-svn
pkgver=928
pkgrel=3
pkgdesc="Jabber IM client using Qt"
arch=('i686' 'x86_64')
url="http://code.google.com/p/vacuum-im/"
license=('GPL3')
conflicts=()
depends=('qt>=4.5.0' 'libidn')
makedepends=('subversion')
source=(vacuum.desktop vacuum_ld_config_install_prefix.patch)
md5sums=('c78d4d7a22510017aa0c89bf848e3146'
         '4933c96d92f2a38c2f6d61695dcb9a4b')

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

  cp -rf $_svnmod $_svnmod-build || return 1
  cd $_svnmod-build || return 1

  patch -p0 -N <${startdir}/vacuum_ld_config_install_prefix.patch || return 1

  qmake INSTALL_PREFIX=/usr -recursive vacuum.pro || return 1

  msg "Starting make..."
  make || return 1

  msg "Installing to package directory..."
  make INSTALL_ROOT=${pkgdir} install || return 1

  install -Dm644 ${startdir}/vacuum.desktop \
    ${pkgdir}/usr/share/applications/vacuum.desktop || return 1

  # make install leaves .svn directories, remove them
  find ${pkgdir} -type d -name '.svn' -prune -exec rm -rf '{}' \;

  msg "Removing /etc/ files from build directory"
  rm -rf ${pkgdir}/etc || return 1
  msg "Moving libs"
  mv ${pkgdir}/usr/lib/vacuum/libvacuumutils.so* ${pkgdir}/usr/lib/ || return 1

  rm -rf ${startdir}/src/$_svnmod-build
}
