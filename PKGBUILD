# Maintainer: thex <reinhart.lukas@gmail.com>
pkgname=dxgkrnl-dkms-git
pkgver=0.0.1
pkgrel=1
pkgdesc="Package that compiles the microsoft dxgkrnl driver from WSL Kernel for using partitioned GPUs from hyperV"
arch=('x86_64')
url="https://github.com/thexperiments/dxgkrnl-dkms-git"
license=('GPL2')
depends=('dkms')
makedepends=('git' 'linux-headers')
source=('sparse-checkout' 'dxgkrnl.h.patch' 'dxgmodule.c.patch')
sha256sums=('45f223292bb19819ff2074d96f8111817afbe6423ddd47976d7085c1cabb0572' 'b77aea6da2f8223b4dd847fe569c34275fce04a87140f95b3d7317596f812699' 'ed4b138464c991b55437f5168e986a46aef4bef42ee87530815a97488149e4d7')
install=dxgkrnl.install

prepare(){
    # Most of the info adapted from here: https://gist.github.com/krzys-h/e2def49966aa42bbd3316dfb794f4d6a

    # clean up
    rm -rf dxgkrnl-*
    rm -rf WSL2-Linux-Kernel-sparse
    # do a sparse checkout of the linux kernel, only stuff we need
    mkdir WSL2-Linux-Kernel-sparse
    cd WSL2-Linux-Kernel-sparse
    git init
    git config core.sparsecheckout true
    git remote add --no-fetch --no-tags origin https://github.com/microsoft/WSL2-Linux-Kernel.git
    cp "$srcdir/sparse-checkout" .git/info/
    git fetch --depth=1 --filter=blob:none origin linux-msft-wsl-6.1.y
    git checkout linux-msft-wsl-6.1.y

    _wsl_kernel_commit=$(git rev-parse --short HEAD)

    # Extract the files from the kernel sourcetree
    mkdir -p $srcdir/dxgkrnl-$_wsl_kernel_commit
    mkdir -p $srcdir/dxgkrnl-$_wsl_kernel_commit/inc/{uapi/misc,linux}
    cp -r drivers/hv/dxgkrnl/. "$srcdir/dxgkrnl-$_wsl_kernel_commit/"
    cp include/uapi/misc/d3dkmthk.h "$srcdir/dxgkrnl-$_wsl_kernel_commit/inc/uapi/misc/d3dkmthk.h"
    cp include/linux/hyperv.h "$srcdir/dxgkrnl-$_wsl_kernel_commit/inc/linux/hyperv_dxgkrnl.h"
    
    # Patch files for out of tree build
    sed -i 's/\$(CONFIG_DXGKRNL)/m/' "$srcdir/dxgkrnl-$_wsl_kernel_commit/Makefile"
    sed -i 's#linux/hyperv.h#linux/hyperv_dxgkrnl.h#' "$srcdir/dxgkrnl-$_wsl_kernel_commit/dxgmodule.c"
    echo "EXTRA_CFLAGS=-I\$(PWD)/inc" >> "$srcdir/dxgkrnl-$_wsl_kernel_commit/Makefile"

    # Generate dkms config
    cat > $srcdir/dxgkrnl-$_wsl_kernel_commit/dkms.conf <<EOF
PACKAGE_NAME="dxgkrnl"
PACKAGE_VERSION="$_wsl_kernel_commit"
BUILT_MODULE_NAME="dxgkrnl"
DEST_MODULE_LOCATION="/kernel/drivers/hv/dxgkrnl/"
AUTOINSTALL="yes"
EOF

    # Apply patches for current arch kernel
    patch -u $srcdir/dxgkrnl-$_wsl_kernel_commit/dxgkrnl.h $srcdir/dxgkrnl.h.patch
    patch -u $srcdir/dxgkrnl-$_wsl_kernel_commit/dxgmodule.c $srcdir/dxgmodule.c.patch

}

package() {
    install -dm 755 "${pkgdir}"/usr/src
    cp -dr --no-preserve='ownership' $srcdir/dxgkrnl-* "$pkgdir/usr/src/"
}
