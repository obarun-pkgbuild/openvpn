# Maintainer: Eric Vidal <eric@obarun.org>
# Contributor: Danial Spruce <bit@teknik.io>
# based on the original https://git.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/openvpn
# 						Maintainer: Thomas Bächler <thomas@archlinux.org>
#						Contributor: Christian Hesse <mail@eworm.de>

pkgname=openvpn
pkgver=2.4.5
pkgrel=2
pkgdesc='An easy-to-use, robust and highly configurable VPN (Virtual Private Network)'
arch=(x86_64)
url='http://openvpn.net/index.php/open-source.html'
depends=('openssl' 'lzo' 'iproute2' 'pkcs11-helper')
optdepends=('easy-rsa: easy CA and certificate handling'
			'pam: authenticate via PAM')
makedepends=('cmake')
install=openvpn.install
license=('custom')
source=("https://swupdate.openvpn.net/community/releases/openvpn-${pkgver}.tar.xz"
		openvpn.conf)
sha256sums=('43c0a363a332350f620d1cd93bb431e082bedbc93d4fb872f758650d53c1d29e'
            '407217c63b3ba5abaecaaa01ac655a8c9a0dbe754f581389b7ac99d922c3bc6c')

validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal


build() {
  cd "${srcdir}"/$pkgname-$pkgver
  ./configure \
    --prefix=/usr \
    --sbindir=/usr/bin \
    --enable-iproute2 \
    --enable-pkcs11 \
    --enable-x509-alt-username \
    --enable-plugins \
    --disable-systemd
  make
}

check() {
  cd "${srcdir}"/${pkgname}-${pkgver}

  make check
}

package() {
  cd "${srcdir}"/$pkgname-$pkgver

  # Install openvpn
  make DESTDIR="${pkgdir}" install
  
  # Create empty configuration directories
  install -d -m 0750 -g 90 "${pkgdir}"/etc/openvpn/{client,server}

  # Install examples
  install -d -m 0755 "${pkgdir}"/usr/share/openvpn
  cp -r sample/sample-config-files "${pkgdir}"/usr/share/openvpn/examples

  # Install license
  install -d -m755 "${pkgdir}"/usr/share/licenses/${pkgname}/
  ln -sf /usr/share/doc/${pkgname}/{COPYING,COPYRIGHT.GPL} "${pkgdir}"/usr/share/licenses/${pkgname}/

  # Install contrib
  for FILE in $(find contrib -type f); do
    case "$(file --brief --mime-type "${FILE}")" in
      "text/x-shellscript") install -D -m0755 "${FILE}" "${pkgdir}/usr/share/openvpn/${FILE}" ;;
      *) install -D -m0644 "${FILE}" "${pkgdir}/usr/share/openvpn/${FILE}" ;;
    esac
  done
  
  # Install tmpfiles.d file
  install -D -m644 "${srcdir}"/openvpn.conf "${pkgdir}"/usr/lib/tmpfiles.d/openvpn.conf
}
