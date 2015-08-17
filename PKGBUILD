# Maintainer: Andre Bartke <dev@bartke.cc>
# Contributor: Vojtech Horky

_pkgname=binutils
_target="sparc64-unknown-elf"
_sysroot="/usr/lib/cross-${_target}"
 
pkgname=${_target}-${_pkgname}
pkgver=2.23.2
pkgrel=1
pkgdesc="Bare-metal binutils for sparc targets"
arch=('i686' 'x86_64')
url="http://www.gnu.org/software/binutils/"
license=('GPL')
depends=('zlib')
options=('!libtool' '!distcc' '!ccache' '!buildflags')
source=("ftp://ftp.gnu.org/gnu/binutils/${_pkgname}-${pkgver}.tar.bz2"
        "binutils-2.23.2-texinfo-5.0.patch")
 
prepare() {
	cd ${srcdir}/${_pkgname}-${pkgver}

	# apply texinfo patch
	patch -p1 -i ${srcdir}/binutils-2.23.2-texinfo-5.0.patch

	# libiberty configure tests for header files using "$CPP $CPPFLAGS"
	sed -i "/ac_cpp=/s/\$CPPFLAGS/\$CPPFLAGS -O2/" libiberty/configure

	rm -rf build
	mkdir build
}

build() {
	cd ${srcdir}/${_pkgname}-${pkgver}/build

	../configure --prefix=${_sysroot} \
		--with-sysroot=${_sysroot} \
		--target=${_target} \
		--enable-64-bit-bfd \
		--disable-shared \
		--disable-nls \
		--with-gnu-as \
		--with-gnu-ld \
		--enable-plugins \
		--enable-gold \
		--enable-multilib \
		--without-included-gettext

	make configure-host || return 1
	make || return 1
}
 
package() {
	cd ${srcdir}/${_pkgname}-${pkgver}/build
	make DESTDIR=${pkgdir} install || return 1

	install -dm755 ${pkgdir}/${_sysroot}/include
	install -vm644 ${srcdir}/${_pkgname}-${pkgver}/include/libiberty.h ${pkgdir}${_sysroot}/include/
	install -vm644 ${srcdir}/${_pkgname}-${pkgver}/include/demangle.h ${pkgdir}${_sysroot}/include/

	msg "Cleaning-up cross compiler tree..."
	rm -rf ${pkgdir}/${_sysroot}/share/{info,man}
	rm ${pkgdir}/${_sysroot}/${_target}/bin/*

	msg "Creating out-of-path executables..."
	mkdir -p ${pkgdir}/${_sysroot}/${_target}/bin/
	cd ${pkgdir}${_sysroot}/${_target}/bin/
	for bin in ${pkgdir}${_sysroot}/bin/${_target}-*; do
		bbin=`basename "$bin"`;
		ln -s ${_sysroot}/bin/$bbin `echo "$bbin" | sed "s#^${_target}-##"`;
	done

	msg "Creating /usr/bin symlinks..."
	mkdir -p $pkgdir/usr/bin
	for bin in ${pkgdir}${_sysroot}/bin/${_target}-*; do
		bbin=`basename "$bin"`;
		ln -s "${_sysroot}/bin/${bbin}" "${pkgdir}/usr/bin/${bbin}";
	done
}

md5sums=('4f8fa651e35ef262edc01d60fb45702e'
         '34e439ce23213a91e2af872dfbb5094c')

# vim: set noet ci pi sts=0 sw=4 ts=4:
