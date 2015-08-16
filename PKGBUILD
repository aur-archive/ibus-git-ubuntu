# Maintainer: Que Quotion <quequotion@mailinator.com>
# Contributor: Limao Luo <luolimao+AUR@gmail.com>
# Contributor: Jekyll Wu <adaptee@gmail.com>
# Contributor: riverscn <riverscn@gmail.com>
# Contributor: Lee.MaRS <leemars@gmail.com>

pkgname=ibus-git-ubuntu
pkgver=1.5.6.3.g6ca5ddb.2510
pkgrel=1
pkgdesc="Next Generation Input Bus for Linux (git version with Ubuntu patches from bzr)"
arch=(i686 x86_64)
license=(LGPL2.1)
url=http://ibus.googlecode.com
depends=(gtk2 gtk3 libnotify)
optdepends=(notification-daemon)
makedepends=(dconf gconf gnome-common gobject-introspection gtk-doc intltool iso-codes vala bzr)
provides=(ibus=$pkgver ibus-ubuntu)
options=(!emptydirs !libtool)
conflicts=('ibus' '${pkgname%-*}')
install=ibus.install
source=('ibus-git::git://github.com/ibus/ibus.git'
	'repatch-ibus-xx-f19-password-gtk3.patch')
sha512sums=('SKIP'
            '814c4bb388c7a6c0639b5bbc3e994b409a5bc38a5a4a3b5e85d61f9f1b99c315fb3b8d31360049824e364ff0625607775e6fa4e8be5626518d72b538ce112b6a')

pkgver() {
	cd ${pkgname%-*}
	echo $(git describe --tags | sed 's/-/./g').$(git shortlog | grep -c '\s\+')
}

build() {
	cd $srcdir

	#Pull latest Ubuntu patchset (minus git cherry-picks)
	bzr cat lp:ubuntu/ibus/debian/patches/series > series
	while read i; do
    		if [[ ${i} =~ ^# || -z ${i} || ${i} == *git* ]]; then
			continue
		else
			msg "Downloading ${i} ..."
			bzr cat -q lp:ubuntu/ibus/debian/patches/${i} > ${i}
			#Apply available repatches
			if [ -f "repatch-${i}" ]; then
				msg "Updating ${i} ..." 
				patch -si "repatch-${i}"
			fi
		fi
	done < series

	cd ${pkgname%-*}

	#Apply Ubuntu patches
	while read i; do
    		if [[ ${i} =~ ^# || -z ${i} || ${i} == *git* ]]; then
			continue
		else
			msg "Applying ${i} ..."
			patch -Nsp1 "$srcdir/${i}"
		fi
	done < $srcdir/series

	export PYTHON=python2
	./autogen.sh \
		--prefix=/usr \
		--libexecdir=/usr/lib/${pkgname%-*} \
		--sysconfdir=/etc \
		--enable-dconf \
		--disable-gconf \
		--disable-memconf
	make
}

package() {
	make -C ${pkgname%-*} DESTDIR="$pkgdir" install
}
