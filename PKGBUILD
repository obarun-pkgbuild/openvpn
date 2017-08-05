# Maintainer: Eric Vidal <eric@obarun.org>
# Contributor: Danial Spruce <bit@teknik.io>
# based on the original https://git.archlinux.org/svntogit/packages.git/tree/trunk?h=packages/openvpn
# 						Maintainer: Thomas Bächler <thomas@archlinux.org>
#						Contributor: Christian Hesse <mail@eworm.de>

pkgname=openvpn
pkgver=2.4.3
pkgrel=4
pkgdesc='An easy-to-use, robust and highly configurable VPN (Virtual Private Network)'
arch=(x86_64)
url='http://openvpn.net/index.php/open-source.html'
depends=('openssl' 'lzo' 'iproute2' 'pkcs11-helper')
optdepends=('easy-rsa: easy CA and certificate handling'
			'pam: authenticate via PAM')
makedepends=('cmake')
install=openvpn.install
license=('custom')
source=("http://www.eurephia.net/openvpn/openvpn-${pkgver}.tar.xz"
        "openvpn-${pkgver}.tar.xz"
        '0001-set-tls-cipher-restriction-before-loading-certificates.patch'
        '0002-management-preserve-wait_for_push-field-when-asking-for-user_pass.patch'
		openvpn.conf)
sha256sums=('df5c4f384b7df6b08a2f6fa8a84b9fd382baf59c2cef1836f82e2a7f62f1bff9'
            'd801b1118d64c0667eae87ab1da920179f339614da22c5c8bed75d17650fad03'
            'd23b4b7642450eab01873c9dbc95c851324f9726f5706541fffde7551818ebb9'
            'e4b95b766b5e0c4db3e7f67dcc5bba1e63e259861ef4b28244c180a88e1d643a'
            '407217c63b3ba5abaecaaa01ac655a8c9a0dbe754f581389b7ac99d922c3bc6c')

validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal


prepare() {
  cd "${srcdir}"/${pkgname}-${pkgver}
  
  # Set tls-cipher restriction before loading certificates
  patch -Np1 < "${srcdir}"/0001-set-tls-cipher-restriction-before-loading-certificates.patch

  # management: preserve wait_for_push field when asking for user/pass
  patch -Np1 < "${srcdir}"/0002-management-preserve-wait_for_push-field-when-asking-for-user_pass.patch

  # regenerate configure script
  autoreconf -fi
}

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
