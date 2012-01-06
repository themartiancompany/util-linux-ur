# Maintainer: Tom Gundersen <teg@jklm.no>
# Contributor: judd <jvinet@zeroflux.org>

pkgname=util-linux
pkgver=2.20.1
pkgrel=2
pkgdesc="Miscellaneous system utilities for Linux"
url="http://userweb.kernel.org/~kzak/util-linux-ng/"
arch=('i686' 'x86_64')
groups=('base')
depends=('filesystem')
replaces=('linux32' 'util-linux-ng')
conflicts=('linux32' 'util-linux-ng' 'e2fsprogs<1.41.8-2')
provides=('linux32' "util-linux-ng=${pkgver}")
license=('GPL2')
options=('!libtool')
#source=(ftp://ftp.kernel.org/pub/linux/utils/${pkgname}/v${pkgver}/${pkgname}-${pkgver}.tar.bz2)
source=(ftp://ftp.infradead.org/pub/${pkgname}/v2.20/${pkgname}-${pkgver}.tar.bz2
	0001-findmnt-support-alternative-location-of-fstab.patch)
optdepends=('perl: for chkdupexe support')

build() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  # hardware clock
  sed -e 's%etc/adjtime%var/lib/hwclock/adjtime%' -i include/pathnames.h

  # backport patch needed for usr support in initramfs
  patch -p1 -i ../0001-findmnt-support-alternative-location-of-fstab.patch

  ./configure --enable-arch\
              --enable-write\
              --enable-raw\
              --disable-wall\
              --enable-partx\
              --enable-libmount-mount

  make
}

package() {
  cd "${srcdir}/${pkgname}-${pkgver}"

  install -dm755 "${pkgdir}/var/lib/hwclock"

  make DESTDIR="${pkgdir}" install
}
md5sums=('079b37517fd4e002a2e6e992e8b4e361'
         '823e2d87885b81f245b8c368e28f8cab')
