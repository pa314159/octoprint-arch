# Maintainer: pappy <pa314159@users.noreply.github.com>

pkgname=octoprint
pkgver=1.7.2
pkgrel=1
pkgdesc="The snappy web interface for your 3D printer on Arch Linux"
arch=(any)
url="http://octoprint.org/"
license=('AGPL3')
depends=(
		python-regex
		python-flask-login
		python-pyserial
		python-netaddr
		python-netifaces
		python-wheel
		python-pkginfo
		python-requests
		python-psutil
		python-future
		python-websocket-client
		python-wrapt
		python-filetype
		python-unidecode
		python-blinker
		python-tornado
		python-pathvalidate
		python-colorlog

		# aur
		python-sarge
		python-cachelib
		python-pylru
		python-immutabledict
)
makedepends=('python-virtualenv' 'rust')
optdepends=('ffmpeg: timelapse support'
			'curaengine: fast and robust engine for processing 3D models'
			'mjpg-streamer: stream images from webcam'
			'motion: motion detector which grabs images from video4linux devices and/or from webcams'
			)
conflicts=('octoprint-venv')
install=octoprint.install
backup=(etc/conf.d/octoprint)
source=("${pkgname}-${pkgver}.tar.gz::https://github.com/OctoPrint/OctoPrint/archive/refs/tags/${pkgver}.tar.gz"
		octoprint.sysusers
		octoprint.service
		octoprint-serve
		octoprint.conf
		)
options=(!distcc !ccache)
sha256sums=('7b0447d33b7b593f096a9b9da02244f659a21d0d9bab14f7625e6d04be41a0c7'
            'bd9b7f989aefb02da1ac414f306861f21f084d886f0283eea11516482b407d65'
            '231685e84b0241a466766c766f8d3ba31efda3238f19e9adedea380e7b861737'
            '08e6ff10fb7f61c40e5770b67e8f7201d02d82d3bd46c5441a7f2b0435fbe9c2'
            '02be5d5a18febe215809882d96f068092c4474abb4e76d82e4450b860a4e9ef5')

prepare()
{
	cd $srcdir/OctoPrint-${pkgver}

	for s in ${source[@]}; do
		case $s in
			*.patch)	echo -n Applying patch $s...
						patch -s -p1 -i ${srcdir}/$s
						echo ' done'
						;;
		esac
	done
}

package() {
	# TODO remove venv
	python -m venv --system-site-packages --symlinks $pkgdir/usr/lib/$pkgname
	source $pkgdir/usr/lib/$pkgname/bin/activate

	pushd $srcdir/OctoPrint-${pkgver}
	pip --use-feature=in-tree-build --no-cache-dir install --install-option '--optimize=1' .
	popd

	find $pkgdir/usr/lib/$pkgname/bin -type f -exec grep -q $pkgdir {} \; -exec sed -i "s:$pkgdir::g" {} \;
	find $pkgdir/usr/lib/$pkgname/lib -type d \( -name testsuite -o -name tests \) -exec rm -rf {} \; 2>/dev/null || true

	install -Dm644 octoprint.sysusers $pkgdir/usr/lib/sysusers.d/octoprint.conf
	install -Dm644 octoprint.service $pkgdir/usr/lib/systemd/system/octoprint.service
	install  -m755 octoprint-serve $pkgdir/usr/lib/$pkgname/bin
	install -Dm644 octoprint.conf $pkgdir/etc/conf.d/octoprint
	install -dm750 $pkgdir/var/lib/$pkgname
}

