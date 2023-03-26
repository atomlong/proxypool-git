# Maintainer: Atom Long <atom.long@hotmail.com>

_pkgname=proxypool
pkgname=${_pkgname}-git
pkgver=v0.7.12_27_gf4678f5
pkgrel=1
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
        "0003-Set-the-country-info-based-on-Out-IP.patch"
        "0004-set-country-info-when-parse-proxy-from-clash.patch")
sha256sums=("SKIP"
            "SKIP"
            "647aebfb3e20dba9801fa28f8c22c5662fcb677949ffd679268a0e09e44f9c7c"
            "688a3b0e5d14338423457590d98b854cfc72e2364218327cc30b9e1cf6fc145e"
            "574d30c50e8062735251c25794fa973516ccc55330c4c1a087d053e7359a7179"
            "11fbdc921e125706afb288d475e836b4a858dd81af89e191c268e2fba0219934"
            "c1daa40508da9d3aaf30691fbec2905c867707988ed3b1b50f41b2f54be0d732")

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
  git describe --tags --long | sed s/-/_/g
}

prepare() {
  cd ${srcdir}/${pkgname/-git}
  git apply ../0001-update-clash-config-template.patch
  git apply ../0002-support-mysql.patch
  git apply ../0003-Set-the-country-info-based-on-Out-IP.patch
  git apply ../0004-set-country-info-when-parse-proxy-from-clash.patch
  mv -vf ../GeoLite2-City.mmdb assets/
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
  
  install -Dm755 bin/proxypool-${GOOS}-${GOARCH} ${pkgdir}/usr/bin/proxypool
  
  install -Dm644 config/config.yaml -t ${pkgdir}/etc/proxypool/
  install -Dm644 config/source.yaml -t ${pkgdir}/etc/proxypool/
  install -d "${pkgdir}"/usr/share/webapps/proxypool
  ln -vsf /etc/proxypool/config.yaml "${pkgdir}"/usr/share/webapps/proxypool
  ln -vsf /etc/proxypool/source.yaml "${pkgdir}"/usr/share/webapps/proxypool
  
  install -Dm644 ${srcdir}/${pkgname/-git}.service -t "${pkgdir}"/usr/lib/systemd/system/
}
