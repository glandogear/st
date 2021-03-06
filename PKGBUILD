
pkgname=st
pkgver=0.8.4
pkgrel=1
pkgdesc='A simple virtual terminal emulator for X.'
arch=('x86_64')
license=('MIT')
depends=(libxft)
url='https://github.com/glandogear/st'
source=(
    https://dl.suckless.org/$pkgname/$pkgname-$pkgver.tar.gz
    https://st.suckless.org/patches/scrollback/st-scrollback-0.8.4.diff
    https://st.suckless.org/patches/font2/st-font2-20190416-ba72400.diff
    https://st.suckless.org/patches/anysize/st-anysize-20201003-407a3d0.diff
)
sha256sums=(
    'SKIP'
    'SKIP'
    'SKIP'
    'SKIP'
)
_sourcedir=$pkgname-$pkgver
_makeopts="--directory=$_sourcedir"

prepare() {
    patch --directory="$_sourcedir" < st-scrollback-0.8.4.diff
    patch --directory="$_sourcedir" < st-font2-20190416-ba72400.diff
    patch --directory="$_sourcedir" < st-anysize-20201003-407a3d0.diff

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
  elif [ ! -e "$BUILDDIR/config.def.h" ]
  then
    msg='This package can be configured in config.h. Copy the config.def.h '
    msg+='that was just placed into the package directory to config.h and '
    msg+='modify it to change the configuration. Or just leave it alone to '
    msg+='continue to use default values.'
    warning "$msg"
    cp "$_sourcedir/config.def.h" "$BUILDDIR"
  fi

  sed -i '/tic /d' "$_sourcedir/Makefile"
}

build() {
  make $_makeopts X11INC=/usr/include/X11 X11LIB=/usr/lib/X11
}

package() {
  local installopts='--mode 0644 -D --target-directory'
  local shrdir="$pkgdir/usr/share"
  local licdir="$shrdir/licenses/$pkgname"
  local docdir="$shrdir/doc/$pkgname"
  make $_makeopts PREFIX=/usr DESTDIR="$pkgdir" install
  install $installopts "$licdir" "$_sourcedir/LICENSE"
  install $installopts "$docdir" "$_sourcedir/README"
  install $installopts "$shrdir/$pkgname" "$_sourcedir/st.info"
}
