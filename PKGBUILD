# Maintainer: Jason R. McNeil <jason@jasonrm.net>
# Contributor: Jesus Alvarez <jeezusjr at gmail dot com>
# Contributor: Kyle Fuller <inbox at kylefuller dot co dot uk>

_spl_utils_git_version=$(pacman -Ss '^spl-utils-git$' | grep spl-utils-git | awk '{print $2}')
_kernel_version=$(pacman -Q linux | awk '{print $2}')
_kernel_module_version=$(pacman -Ql linux | grep -oE '[0-9]+\.[0-9]+\.[0-9]+-[0-9]+' | head -n1)
_gitname="spl"

pkgname="spl-git"
pkgver=0.6.4.r14.g9eb361a_4.1.2_2
pkgrel=1
license=('GPL')
pkgdesc="Solaris Porting Layer kernel modules."
depends=("spl-utils-git=${_spl_utils_git_version}" "linux=${_kernel_version}")
makedepends=("git" "linux-headers=${_kernel_version}")
arch=("i686" "x86_64")
url="http://zfsonlinux.org/"
source=("${_gitname}::git+https://github.com/zfsonlinux/spl.git")
groups=("archzfs-git")
md5sums=('SKIP')
replaces=("spl")
provides=("spl")
conflicts=("spl" "spl-lts")
install=spl.install

pkgver() {
    cd ${srcdir}/${_gitname}
    REPO_VER=$(git describe --long | sed 's/^spl-//;s/\([^-]*-g\)/r\1/;s/-/./g')
    KERNEL_VER=$(pacman -Ql linux | grep -oE '[0-9]+\.[0-9]+\.[0-9]+-[0-9]+' | head -n1 | sed -r 's/-/_/g')
    echo "${REPO_VER}_${KERNEL_VER}"
}

build() {
    cd ${srcdir}/${_gitname}
    ./autogen.sh

    _at_enable=""
    [ "${CARCH}" == "i686" ] && _at_enable="--enable-atomic-spinlocks"

    ./configure --prefix=/usr \
                --libdir=/usr/lib \
                --sbindir=/usr/bin \
                --with-linux=/usr/lib/modules/${_kernel_module_version}-ARCH/build \
                --with-config=kernel \
                ${_at_enable}

    make
}

package() {
    cd ${srcdir}/${_gitname}
    make DESTDIR="${pkgdir}" install

    mv "${pkgdir}/lib" "${pkgdir}/usr/"
    sed -i "s+${srcdir}++" ${pkgdir}/usr/src/spl-*/${_kernel_version}-ARCH/Module.symvers
}
