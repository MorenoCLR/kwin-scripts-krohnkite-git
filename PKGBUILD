# Maintainer:
# Contributor: Moreno <contact@morenoclr.com>

_gitname="krohnkite"
_pkgname="kwin-scripts-$_gitname"
pkgname="$_pkgname-git"
pkgver=0.9.9.2.r74.g1803454
pkgrel=1
pkgdesc="A dynamic tiling extension for KWin"
url="https://codeberg.org/anametologin/Krohnkite"
license=('MIT')
arch=('any')

makedepends=(
  'git'
  'npm'
  'typescript'
  'p7zip'
)

provides=("$_pkgname")
conflicts=("$_pkgname")

_pkgsrc="codeberg.krohnkite"
source=("$_pkgsrc"::"git+$url.git")
sha256sums=('SKIP')

pkgver() {
  cd "$_pkgsrc"
  git describe --long --tags --abbrev=7 | sed -E 's/^v//;s/([^-]*-g)/r\1/;s/-/./g'
}

build() {
  cd "$_pkgsrc"

  if [ -f "tsconfig.json" ]; then
    sed -i '/"outFile"/d' tsconfig.json
    if ! grep -q '"rootDir"' tsconfig.json; then
      sed -i '/"compilerOptions": {/a \    "rootDir": "./src",' tsconfig.json
    fi
  fi

  npm install --save-dev
  npm run tsc --

  mkdir -p pkg/contents/{code,config,ui}

  # Copy compiled files
  [ -f "krohnkite.js" ] && install -Dm644 krohnkite.js pkg/contents/code/script.js
  [ -f "res/main.js" ] && install -Dm644 res/main.js pkg/contents/code/
  [ -f "res/main.qml" ] && install -Dm644 res/main.qml pkg/contents/ui/
  [ -f "res/config.ui" ] && install -Dm644 res/config.ui pkg/contents/ui/
  [ -f "res/config.xml" ] && install -Dm644 res/config.xml pkg/contents/config/main.xml

  # Create metadata.json with version substitution
  sed -e "s/\$VER/${pkgver}/g" \
      -e "s/\$REV/${pkgver}/g" \
      res/metadata.json > pkg/metadata.json
}

package() {
  depends+=(
    'kwin'
  )

  mkdir -pm755 "$pkgdir/usr/share/kwin/scripts/$_gitname"
  cp -r "$_pkgsrc"/pkg/. "$pkgdir/usr/share/kwin/scripts/$_gitname/"

  install -Dm644 "$_pkgsrc/LICENSE" -t "$pkgdir/usr/share/licenses/$pkgname/"
}
