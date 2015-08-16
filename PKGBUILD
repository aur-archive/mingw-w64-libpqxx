# Maintainer: Scott Furry <scott.wl.furry@gmail.com>
# Contributor: Giovanni Scafora <giovanni@archlinux.org>

_pkgname=libpqxx
pkgname=mingw-w64-${_pkgname}
pkgver=4.0.1
pkgrel=2
pkgdesc="C++ client API for PostgreSQL (mingw-w64)"
arch=(any)
license=('custom')
url="http://pqxx.org/development/libpqxx/"
depends=( 'mingw-w64-postgresql-libs>=8.4.1' )
makedepends=('python2' 'mingw-w64-gcc' 'mingw-w64-postgresql-libs>=8.4.1' 'postgresql-libs>=8.4.1' )
options=(!strip !buildflags staticlibs)
source=("http://pqxx.org/download/software/${_pkgname}/${_pkgname}-${pkgver}.tar.gz"
        'fix_configure.patch')
md5sums=('6ea888b9ba85dd7cef1b182dc5f223a2'
         '3d6ec2537dce6cf32fb0ea0576d26772')

_architectures="x86_64-w64-mingw32 i686-w64-mingw32"

prepare() {
  cd "${srcdir}/${_pkgname}-${pkgver}"
  # confgure scripts use wrong include and lib dirs
  patch -Np1 -i "${srcdir}/fix_configure.patch"
  # should be defined by environment? - unless python2 is explicitly needed
  sed -i 's|python|python2|' tools/splitconfig
}

build() {
  cd "${srcdir}/${_pkgname}-${pkgver}"
  for _arch in ${_architectures}; do
    export ARCH=${_arch}
    LDFLAGS="-L/usr/${_arch}/lib"
    cp -r "${srcdir}/${_pkgname}-${pkgver}" "${srcdir}/build-${_arch}"
    cd "${srcdir}/build-${_arch}"
    # shared libraries are not created even with the option --enable-shared 
    ./configure \
        --prefix=/usr/${_arch} \
        --build=${CHOST} \
        --host=${_arch} \
        --target=${_arch} \
        --disable-documentation
    make
  done
}

package() {
  for _arch in ${_architectures}; do
    export ARCH=${_arch}
    LDFLAGS="-L/usr/${_arch}/lib"
    cd "${srcdir}/build-${_arch}"
    make prefix="${pkgdir}/usr/${_arch}" install
    rm -rf "${pkgdir}/usr/${_arch}/share/man"
    ${_arch}-strip -g "${pkgdir}/usr/${_arch}/lib/"*.a
    ./libtool --finish "${pkgdir}/usr/${_arch}/lib/"*.la
  done
  install -D -m644 COPYING "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
