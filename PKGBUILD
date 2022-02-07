# Maintainer: Pierre Schmitz <pierre@archlinux.de>

pkgname=openssl
pkgver=3.0.1
pkgrel=1.1
pkgdesc='The Open Source toolkit for Secure Sockets Layer and Transport Layer Security'
arch=('x86_64')
url='https://www.openssl.org'
license=('APACHE')
depends=('glibc')
makedepends=('perl')
optdepends=('ca-certificates' 'perl')
replaces=('openssl-perl' 'openssl-doc')
backup=('etc/ssl/openssl.cnf')
source=("https://www.openssl.org/source/${pkgname}-${pkgver}.tar.gz"{,.asc}
	'ca-dir.patch')
sha256sums=('c311ad853353bce796edad01a862c50a8a587f62e7e2100ef465ab53ec9b06d1'
            'SKIP'
            'd38f2ec338685c7ec32579ea8d8172b633439c7609063a1c9c5f4cc065b38bbe')
validpgpkeys=('8657ABB260F056B1E5190839D9C4D26D0E604491'
	'7953AC1FBC3DC8B3B292393ED5E9E43F7DF9EE8C')

prepare() {
	cd "$srcdir/$pkgname-$pkgver"

	# set ca dir to /etc/ssl by default
	patch -p0 -i "$srcdir/ca-dir.patch"
}

build() {
	cd "$srcdir/$pkgname-$pkgver"

	./Configure --prefix=/usr --openssldir=/etc/ssl --libdir=lib \
		shared enable-ec_nistp_64_gcc_128 linux-x86_64

	make depend
	make
}

check() {
	cd "$srcdir/$pkgname-$pkgver"

	# the test fails due to missing write permissions in /etc/ssl
	# revert this patch for make test
	patch -p0 -R -i "$srcdir/ca-dir.patch"

	make test

	patch -p0 -i "$srcdir/ca-dir.patch"
	# re-run make to re-generate CA.pl from th patched .in file.
	make apps/CA.pl
}

package() {
	cd "$srcdir/$pkgname-$pkgver"

	make DESTDIR="$pkgdir" MANDIR=/usr/share/man MANSUFFIX=ssl install_sw install_ssldirs install_man_docs

	install -D -m644 LICENSE.txt "$pkgdir/usr/share/licenses/$pkgname/LICENSE.txt"
}
