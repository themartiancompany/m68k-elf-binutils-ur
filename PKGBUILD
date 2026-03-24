# SPDX-License-Identifier: AGPL-3.0

#    ----------------------------------------------------------------------
#    Copyright © 2024, 2025, 2026  Pellegrino Prevete
#
#    All rights reserved
#    ----------------------------------------------------------------------
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU Affero General Public License as
#    published by the Free Software Foundation, either version 3 of
#    the License, or (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU Affero General Public License for more details.
#
#    You should have received a copy of the GNU Affero General Public License
#    along with this program.
#    If not, see <https://www.gnu.org/licenses/>.

# Maintainers:
#   Truocolo
#     <truocolo@aol.com>
#     <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
#   Pellegrino Prevete (dvorak)
#     <pellegrinoprevete@gmail.com>
#     <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
# Maintainer: Jesus Alonso <doragasu at hotmail dot com>
# NOTE: As I want these packages for Genesis/Megadrive development, they do
# only support the m68000 CPU. If you want to support other m68k variants,
# either modify _target_cpu to suit your needs, or go wild, remove the
# --with-cpu switches and change --disable-multilib with --enable-multilib.
# Be warned that multilib packages will take a lot more time to build, and
# will also require more disk space.

_os="$(
  uname \
    -o)"
if [[ "${_os}" == "Android" ]]; then
  _libc="ndk-sysroot"
  _compiler="clang"
  _libcompiler="libllvm"
  _sh="dash"
elif [[ "${_os}" == "GNU/Linux" ]]; then
  _libc="glibc"
  _compiler="gcc"
  _libcompiler="libgcc"
  _sh="sh"
elif [[ "${_os}" == "Msys" ]]; then
  _libc="msys2-w32api-runtime"
  _libc_headers="msys2-w32api-headers"
  _compiler="gcc"
  _libcompiler="gcc-libs"
  _sh="sh"
else
  _msg=(
    "Unknown os '${_os}'."
  )
  msg \
    "${_msg[*]}"
  _libc="msys2-w32api-runtime"
  _libc_headers="msys2-w32api-headers"
  _compiler="gcc"
  _libcompiler="gcc-libs"
  _sh="sh"
fi
_target=m68k-elf
_target_cpu=m68000
pkgbase="${_target}-binutils"
pkgname=(
  "${pkgbase}"
)
pkgver=2.45
pkgrel=4
_pkgdesc=(
  "A set of programs to assemble and"
  "manipulate binary and object files"
  "(${_target})."
)
pkgdesc="${_pkgdesc[*]}"
arch=(
  'i686'
  'x86_64'
)
url="http://www.gnu.org/software/binutils"
license=(
  'GPL'
)
depends=(
  'zlib'
)
if [[ "" != "Android" ]]; then
  depends+=(
    "${_libc}>=2.23"
  )
else
  depends+=(
    "${_libc}"
  )
fi
makedepends=(
  "${_libc}"
  "${_compiler}"
)
if [[ "${_os}" == "Msys" ]]; then
  makedepends+=(
    "${_libc_headers}"
    "windows-default-manifest"
  )
fi
options=(
  'staticlibs'
  '!distcc'
  '!ccache'
)
_sig_sum="7c0f34aa65aa1e73eb5333bef182a43b16ece95fab5e74930c0ac0bec456c023"
PKGEXT=".pkg.tar.zst"
source=(
  "https://ftp.gnu.org/gnu/binutils/binutils-${pkgver}.tar.xz"
  "https://ftp.gnu.org/gnu/binutils/binutils-${pkgver}.tar.xz.sig"
)
sha256sums=(
  "c50c0e7f9cb188980e2cc97e4537626b1672441815587f1eab69d2a1bfbef5d2"
  "SKIP"
)
validpgpkeys=(
  "3A24BC1E8FB409FA9F14371813FCEF89DD9E3C4F"
)
prepare() {
  cd binutils-${pkgver}

  # hack! - libiberty configure tests for header files using "$CPP $CPPFLAGS"
  sed -i "/ac_cpp=/s/\$CPPFLAGS/\$CPPFLAGS -O2/" libiberty/configure

  mkdir ${srcdir}/binutils-build
}

build() {
  cd binutils-build

  ../binutils-${pkgver}/configure --prefix=/usr \
      --target=${_target} \
      --disable-multilib \
      --with-cpu=${_target_cpu} \
      --disable-nls

  make
}

package() {
  cd binutils-build
  make prefix=${pkgdir}/usr install

  # Remove unwanted and conflicting files
  rm -rf ${pkgdir}/usr/share
  rm ${pkgdir}/usr/lib/bfd-plugins/libdep.so
}

