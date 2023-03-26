# Maintainer: Atom Long <atom.long@hotmail.com>

_pkgname=proxypool
pkgname=${_pkgname}-git
pkgver=0.7.12.r28.gae4e6bd
pkgrel=7
pkgdesc="Automatically grab ss, ssr, vmess, trojan node information from the Internet"
arch=('i686' 'x86_64' 'arm' 'armv6h' 'armv7h' 'aarch64')
url="https://github.com/ssrlive/proxypool"
license=('GPL3')
makedepends=('go' 'git')
options=(!lto)
conflicts=("${pkgname/-git}")
provides=("${pkgname/-git}")
source=("${pkgname/-git}::git+${url}"
        "https://github.com/atomlong/GeoLite.mmdb/releases/latest/download/GeoLite2-City.mmdb"
        "${pkgname/-git}.service"
        "0001-update-clash-config-template.patch"
        "0002-support-mysql.patch"
        "0003-fix-country-filter.patch"
        "0004-set-country-info-when-parse-proxy-from-clash.patch")
sha256sums=("SKIP"
            "SKIP"
            "647aebfb3e20dba9801fa28f8c22c5662fcb677949ffd679268a0e09e44f9c7c"
            "9c9ff209756b69657ffaa96315772f47e41ddce35247d9315d61c1fed03e0112"
            "fda238613d9cdd1d23a6bea4dd8a4ef9f44bcd996ad7db71081462d34628f47a"
            "8944bbc6101a670d47d008a5ddc395efb2940934b85101ef8cd844fcb216bbe4"
            "e24c042130703fa8bb6ed23db32d521487c7e06ced084ce35330f423ccd27eb7")

export GOOS=linux
case "$CARCH" in
  x86_64) export GOARCH=amd64 ;;
  i686) export GOARCH=386 ;;
  arm) export GOARCH=armv5 ;;
  armv6h) export GOARCH=armv6 ;;
  armv7h) export GOARCH=armv7 ;;
  aarch64) export GOARCH=armv8 ;;
esac

pkgver() {
  cd "${srcdir}/${pkgname/-git}"
  git describe --long --tags | sed 's/^v//;s/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
  cd ${srcdir}/${pkgname/-git}
  git apply ../0001-update-clash-config-template.patch
  git apply ../0002-support-mysql.patch
  git apply ../0003-fix-country-filter.patch
  git apply ../0004-set-country-info-when-parse-proxy-from-clash.patch
  cp -vf ../GeoLite2-City.mmdb assets/
  # Upgrade dependencies
  go get -u ./...
  # Remove unused dependencies
  go mod tidy
}

build() {
  cd "${srcdir}/${pkgname/-git}"
  export GOPATH="${srcdir}"/gopath
  make ${GOOS}-${GOARCH}
}

package() {
  cd "${srcdir}/${pkgname/-git}"
  
  install -Dm644 config/config.yaml -t "${pkgdir}"/etc/proxypool/
  install -Dm644 config/source.yaml -t "${pkgdir}"/etc/proxypool/
  
  install -Dm755 bin/proxypool-${GOOS}-${GOARCH} "${pkgdir}"/usr/share/webapps/proxypool/proxypool
  cp -rf assets/ "${pkgdir}"/usr/share/webapps/proxypool/
  ln -vsf /etc/proxypool/config.yaml "${pkgdir}"/usr/share/webapps/proxypool/
  ln -vsf /etc/proxypool/source.yaml "${pkgdir}"/usr/share/webapps/proxypool/
  
  install -d "${pkgdir}"/usr/bin
  ln -vsf /usr/share/webapps/proxypool/proxypool "${pkgdir}"/usr/bin/proxypool
  
  install -Dm644 ${srcdir}/${pkgname/-git}.service -t "${pkgdir}"/usr/lib/systemd/system/
}
