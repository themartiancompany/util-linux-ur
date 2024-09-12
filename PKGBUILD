# Maintainer: Tom Gundersen <teg@jklm.no>
# Maintainer: Dave Reisner <dreisner@archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>

_py="python"
_pkg=util-linux
pkgbase="${_pkg}"
pkgname=(
  "${_pkg}"
  "${_pkg}-libs"
)
_tag='2.40.2'
pkgver="${_tag/-/}"
pkgrel=1
pkgdesc='Miscellaneous system utilities for Linux'
url="https://github.com/${_pkg}/${_pkg}"
arch=(
  'x86_64'
  'arm'
  'armv7l'
  'aarch'
  'armv6l'
  'mips'
  'i686'
  'pentium4'
  'powerpc'
)
makedepends=(
  'asciidoctor'
  'bash-completion'
  'git'
  'libcap-ng'
  'libxcrypt'
  'meson'
  "${_py}"
  'sqlite'
  'systemd'
)
license=(
  'BSD-2-Clause'
  'BSD-3-Clause'
  'BSD-4-Clause-UC'
  'GPL-2.0-only'
  'GPL-2.0-or-later'
  'GPL-3.0-or-later'
  'ISC'
  'LGPL-2.1-or-later'
  'LicenseRef-PublicDomain'
)
options=(
  'strip'
)
validpgpkeys=(
  # Karel Zak
  'B0C64D14301CC6EFAEDF60E4E4B71D5EEC39C284'
)
_gh="https://raw.githubusercontent.com"
_xxhash_commit="f035303b8a86c1db9be70cbb638678ef6ef4cb2d"
source=(
  "git+${_url}#tag=v${_tag}?signed"
  "${_pkg}-BSD-2-Clause.txt::${_gh}/Cyan4973/xxHash/${_xxhash_commit}/LICENSE"
  pam-{login,common,remote,runuser,su}
  "${_pkg}.sysusers"
  '60-rfkill.rules'
  'rfkill-unblock_.service'
  'rfkill-block_.service'
)
sha256sums=(
  'ccae05afaddd61119bbf35173f7045d1c8e98cc42c1355f5e9072433ebc42ee1'
  '6ffedbc0f7878612d2b23589f1ff2ab15633e1df7963a5d9fc750ec5500c7e7a'
  'ee917d55042f78b8bb03f5467e5233e3e2ddc2fe01e302bc53b218003fe22275'
  '57e057758944f4557762c6def939410c04ca5803cbdd2bfa2153ce47ffe7a4af'
  '8bfbee453618ba44d60ba7fb00eced6c62edebfc592f2e75dede08e769ed8931'
  '48d6fba767631e3dd3620cf02a71a74c5d65a525d4c4ce4b5a0b7d9f41ebfea1'
  '3f54249ac2db44945d6d12ec728dcd0d69af0735787a8b078eacd2c67e38155b'
  '10b0505351263a099163c0d928132706e501dd0a008dac2835b052167b14abe3'
  '7423aaaa09fee7f47baa83df9ea6fef525ff9aec395c8cbd9fe848ceb2643f37'
  '8ccec10a22523f6b9d55e0d6cbf91905a39881446710aa083e935e8073323376'
  'a22e0a037e702170c7d88460cc9c9c2ab1d3e5c54a6985cd4a164ea7beff1b36'
)

_backports=(
)

_reverts=(
)

prepare() {
  local \
    _c \
    _l
  cd \
    "${pkgbase}"
  for _c in "${_backports[@]}"; do
    if [[ "${_c}" == *..* ]]; then
      _l='--reverse';
    else
      _l='--max-count=1';
    fi
    git \
      log \
        --oneline \
	"${_l}" \
	"${_c}"
    git \
      cherry-pick \
      --mainline \
        1 \
      --no-commit \
        "${_c}"
  done
  for _c in "${_reverts[@]}"; do
    if [[ "${_c}" == *..* ]]; then
      _l='--reverse';
    else
      _l='--max-count=1';
    fi
    git \
      log \
        --oneline \
	"${_l}" \
	"${_c}"
    git \
      revert \
      --mainline \
        1 \
      --no-commit \
        "${_c}"
  done
  # do not mark dirty
  sed \
    -i \
    '/dirty=/c dirty=' \
    tools/git-version-gen
}

build() {
  local \
    _meson_options=()
  _meson_options=(
    -Dfs-search-path="/usr/bin:/usr/local/bin"
    -Dlibuser=disabled
    -Dlibutempter=disabled
    -Dncurses=disabled
    -Dncursesw=enabled
    -Deconf=disabled
    -Dbuild-chfn-chsh=enabled
    -Dbuild-line=disabled
    -Dbuild-mesg=enabled
    -Dbuild-newgrp=enabled
    -Dbuild-vipw=enabled
    -Dbuild-write=enabled
  )
  arch-meson \
    "${_pkg}" \
    build \
      "${_meson_options[@]}"
  meson \
    compile \
    -C \
      build
}

package_util-linux() {
  local \
    _py_stdlib=()
  conflicts=(
    'rfkill'
    'hardlink'
  )
  provides=(
    'rfkill'
    'hardlink'
    'uuid-utils' # Debian nomenclature
  )
  replaces=(
    'rfkill'
    'hardlink'
  )
  depends=(
    "${_pkg}-libs=${pkgver}"
    'coreutils'
    'file'
    'libmagic.so'
    'glibc'
    'libcap-ng'
    'libxcrypt'
    'libcrypt.so'
    'ncurses'
    'libncursesw.so'
    'pam'
    'readline'
    'shadow'
    'systemd-libs'
    'libsystemd.so'
    'libudev.so'
    'zlib'
  )
  optdepends=(
    'words: default dictionary for look'
  )
  backup=(
    etc/pam.d/chfn
    etc/pam.d/chsh
    etc/pam.d/login
    etc/pam.d/remote
    etc/pam.d/runuser
    etc/pam.d/runuser-l
    etc/pam.d/su
    etc/pam.d/su-l
  )
  _py_stdlib=(
    'import sysconfig;'
    'print(sysconfig.get_paths()["stdlib"])'
  )
  _python_stdlib="$( \
    "${_py}" \
      -c \
        "${_py_stdlib[*]}")"
  DESTDIR="${pkgdir}" \
  meson \
    install \
      -C \
        build
  # remove static libraries
  rm \
    "${pkgdir}"/usr/lib/lib*.a*
  # setuid chfn and chsh
  chmod \
    4755 \
    "${pkgdir}/usr/bin/"{"newgrp","ch"{"sh","fn"}}
  # install PAM files for login-utils
  install \
    -Dm0644 \
    pam-common \
    "${pkgdir}/etc/pam.d/chfn"
  install \
    -m0644 \
    pam-common \
    "${pkgdir}/etc/pam.d/chsh"
  install \
    -m0644 \
    pam-login \
    "${pkgdir}/etc/pam.d/login"
  install \
    -m0644 \
    pam-remote \
    "${pkgdir}/etc/pam.d/remote"
  install \
    -m0644 \
    pam-runuser \
    "${pkgdir}/etc/pam.d/runuser"
  install \
    -m0644 \
    pam-runuser \
    "${pkgdir}/etc/pam.d/runuser-l"
  install \
    -m0644 \
    pam-su \
    "${pkgdir}/etc/pam.d/su"
  install \
    -m0644 \
    pam-su \
    "${pkgdir}/etc/pam.d/su-l"
  # TODO(dreisner): offer this upstream?
  sed \
    -i \
    '/ListenStream/ aRuntimeDirectory=uuidd' \
    "${pkgdir}/usr/lib/systemd/system/uuidd.socket"
  # runtime libs are shipped as part of util-linux-libs
  install \
    -dm0755 \
    "${_pkg}-libs/lib/"
  mv \
    "${pkgdir}/usr/lib/lib"*.so* \
    "${_pkg}-libs/lib/"
  mv \
    "${pkgdir}/usr/lib/pkgconfig" \
    "${_pkg}-libs/lib/pkgconfig"
  mv \
    "${pkgdir}/usr/include" \

    "${_pkg}-libs/include"
  mv \
    "${pkgdir}/${_python_stdlib}/site-packages" \
    "${_pkg}-libs/site-packages"
  rmdir \
    "${pkgdir}/${_python_stdlib}"
  mv \
    "${pkgdir}/usr/share/man/man3" \
    "${_pkg}-libs/man3"
  # install systemd-sysusers
  install \
    -Dm0644 \
    "${_pkg}.sysusers" \
    "${pkgdir}/usr/lib/sysusers.d/${_pkg}.conf"
  install \
    -Dm0644 \
    60-rfkill.rules \
    "${pkgdir}/usr/lib/udev/rules.d/60-rfkill.rules"
  install \
    -Dm0644 \
    rfkill-unblock_.service \
    "${pkgdir}/usr/lib/systemd/system/rfkill-unblock@.service"
  install \
    -Dm0644 \
    rfkill-block_.service \
    "${pkgdir}/usr/lib/systemd/system/rfkill-block@.service"
  install \
    -vDm644 \
    "${_pkg}/Documentation/licenses/COPYING."{BSD*,ISC} \
    -t \
    "${pkgdir}/usr/share/licenses/${pkgname}/"
  install \
    -vDm644 \
    "${_pkg}-BSD-2-Clause.txt" \
    -t \
    "${pkgdir}/usr/share/licenses/${pkgname}/"
}

package_util-linux-libs() {
  pkgdesc='util-linux runtime libraries'
  depends=(
    'glibc'
    'sqlite'
  )
  provides=(
    "lib${_pkg}"
    'libblkid.so'
    'libfdisk.so'
    'libmount.so'
    'libsmartcols.so'
    'libuuid.so'
  )
  conflicts=(
    "lib${_pkg}"
  )
  replaces=(
    "lib${_pkg}"
  )
  optdepends=(
    "${_py}: python bindings to libmount"
  )
  install \
    -dm0755 \
    "${pkgdir}"/{"${_python_stdlib}",usr/share/man/}
  mv \
    "${_pkg}-libs/lib/"* \
    "${pkgdir}/usr/lib/"
  mv \
    "${_pkg}-libs/include" \
    "${pkgdir}/usr/include"
  mv \
    "${_pkg}-libs/site-packages" \
    "$pkgdir/${_python_stdlib}/site-packages"
  mv
    "${_pkg}-libs/man3" \
    "$pkgdir"/usr/share/man/man3
  install \
    -vDm644 \
    "${_pkg}/Documentation/licenses/COPYING."{BSD*,ISC} \
    -t \
    "${pkgdir}/usr/share/licenses/${pkgname}/"
  install \
    -vDm644 \
    "${pkgbase}-BSD-2-Clause.txt" \
    -t \
    "${pkgdir}/usr/share/licenses/${pkgname}/"
}
