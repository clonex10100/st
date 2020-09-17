# Maintainer: Jose Riha <jose1711 gmail com>
# Maintainer: Sebastian J. Bronner <waschtl@sbronner.com>
# Contributor: Patrick Jackson <PatrickSJackson gmail com>
# Contributor: Christoph Vigano <mail@cvigano.de>

pkgname=st
pkgver=0.8.4
pkgrel=2
pkgdesc='A simple virtual terminal emulator for X. Customizations By Clone'

arch=('i686' 'x86_64' 'armv7h' 'aarch64')
license=('MIT')
depends=(libxft)
url=https://st.suckless.org
_patches=./*.diff
source=(https://dl.suckless.org/$pkgname/$pkgname-$pkgver.tar.gz
        st.desktop
        $_patches
        )
sha256sums=('d42d3ceceb4d6a65e32e90a5336e3d446db612c3fbd9ebc1780bc6c9a03346a6'
            '86f65aad90229cf7ef050613263781174f332da2edeae0e47a9d6404bed1983c'
            '9c5b4b4f23de80de78ca5ec3739dc6ce5e7f72666186cf4a9c6b614ac90fb285'
            '1e41fe17a5ef5a8194eea07422b49d815e2c2bb4d58d84771f793be423005310'
            'a7b3681d849fd8cca75cd38022a14fef2e26dc085bed1ebd8dcf0a65baa14898'
            '4a3fa7fcdadac2896e9cfdcc321b51452f67ba0b4a799822abeb127f4dd81166')
_sourcedir=$pkgname-$pkgver
_makeopts="--directory=$_sourcedir"

prepare() {
  for patch in $_patches; do
      echo $patch
      patch --directory="$_sourcedir" -p1 -i "$BUILDDIR/$patch"
  done

  # This package provides a mechanism to provide a custom config.h. Multiple
  # configuration states are determined by the presence of two files in
  # $BUILDDIR:
  #
  # config.h  config.def.h  state
  # ========  ============  =====
  # absent    absent        Initial state. The user receives a message on how
  #                         to configure this package.
  # absent    present       The user was previously made aware of the
  #                         configuration options and has not made any
  #                         configuration changes. The package is built using
  #                         default values.
  # present                 The user has supplied his or her configuration. The
  #                         file will be copied to $srcdir and used during
  #                         build.
  #
  # After this test, config.def.h is copied from $srcdir to $BUILDDIR to
  # provide an up to date template for the user.
  if [ -e "$BUILDDIR/config.h" ]
  then
    cp "$BUILDDIR/config.h" "$_sourcedir"
    warning "fuck"
    if ! diff -q "$BUILDDIR/config.h" "$_sourcedir/config.def.h" &> /dev/null; 
    then
        #mkdir "$BUILDDIR/a"
        #mkdir "$BUILDDIR/b"
        cp "$_sourcedir/config.def.h" "$BUILDDIR/a/config.def.h"
        cp "$BUILDDIR/config.h" "$BUILDDIR/b/config.def.h"
        #diff -up "$BUILDDIR/a/config.def.h" "$BUILDDIR/b/config.def.h" > "$BUILDDIR/your-patch.dif"
    fi
  elif [ ! -e "$BUILDDIR/config.def.h" ]
  then
    msg='This package can be configured in config.h. Copy the config.def.h '
    msg+='that was just placed into the package directory to config.h and '
    msg+='modify it to change the configuration. Or just leave it alone to '
    msg+='continue to use default values.'
    warning "$msg"
  fi
  cp "$_sourcedir/config.def.h" "$BUILDDIR"
}

build() {
  make $_makeopts X11INC=/usr/include/X11 X11LIB=/usr/lib/X11
}

package() {
  local installopts='--mode 0644 -D --target-directory'
  local shrdir="$pkgdir/usr/share"
  local licdir="$shrdir/licenses/$pkgname"
  local docdir="$shrdir/doc/$pkgname"
  local dskdir="$shrdir/applications"
  make $_makeopts PREFIX=/usr DESTDIR="$pkgdir" install
  install $installopts "$licdir" "$_sourcedir/LICENSE"
  install $installopts "$docdir" "$_sourcedir/README"
  install $installopts "$shrdir/$pkgname" "$_sourcedir/st.info"
  install $installopts "$dskdir" st.desktop
}
