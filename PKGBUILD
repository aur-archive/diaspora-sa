# Maintainer: Tom Parys <tom.parys @ gmail {d} com>
# Contributor: ZekeSulastin <zekesulastin+aur@gmail.com>
# Contributor: David H. Bronke <whitelynx@gmail.com>
# Contributor: Mr_Robotic_Evil <mr.robotic.evil@googlemail.com>
# Contributor: Lone_Wolf <lonewolf@xs4all.nl>
# Contributor: Marius Nestor <marius @ softpedia dot com>

# Warning: This package is BIG; you'll need at least 9 GiB free in
#   order to build version 1.1. (2 GiB for the downloads alone, about 3 GiB for the
#   unpacked game data and source, and 3.3 GiB for the finished package if
#   uncompressed)  'mv' is used over 'cp' in the package phase to save a bit
#   of space, but make sure there is enough room for everything.

# This PKGBUILD is based on fs2_open and fs2_open-data from AUR; the
# maintainers and contributors of those packages have been listed as
# contributors above.

pkgname=diaspora-sa
pkgver=1.1.1
pkgrel=1
pkgdesc="Diaspora: Shattered Armistice is a single and multiplayer space fighter combat game set in the reimagined Battlestar Galactica universe."
url="http://diaspora.hard-light.net/"
arch=('i686' 'x86_64')
license=('CCPL' 'custom:fs2_open')
depends=('libjpeg' 'libpng' 'libtheora' 'libvorbis' 'lua51' 'mesa' 'openal' 'sdl' 'jansson')
optdepends=('wxlauncher: a launcher for fs2_open-based games')
install=diaspora-sa.install
source=('http://diaspora.fs2downloads.com/Diaspora_R1_Linux.tar.lzma'
        'Diaspora_R1_Patch_1.1.tar.lzma::https://copy.com/8wo3AQnYu0bj%2FDiaspora_R1_Patch_1.1.tar.lzma?download=1'
        'http://diaspora.fs2downloads.com/Diaspora_R1_Patch_1.1.1.tar.lzma'
        'osapi_unix.patch'
        'increase_joy_buttons_fixed.patch'
        'diaspora-sa'
        'diaspora-sa.conf')

md5sums=('22b55ae9bc9366ccbeb1642cd50dc3f8'
      	 '423182f96a97e657da0e27b708b48abf'
      	 'd73890efd3e86b336d96b339e0a59a46'
         '783d5ab68a0ce4d26ee415e8fefbc762'
         '892cee11520d6e258eb17e897f98c1c9'
         '23fded0ca796c79a66127c26b22afe3c'
         '63383810fe6b92b9c3230410e127dfa8')
# This package is about 3 GiB uncompressed and takes a while to recompress for
# not too much space savings; the following PKGEXT disables compression of the
# package.  Add .xz or similar to the end of PKGEXT to compress the package.
PKGEXT=".pkg.tar"


build() {
  local SOURCE="$srcdir/Diaspora_R1_Linux/Diaspora/"

  msg "Removing unneeded files..."
  rm -r $SOURCE/resources $SOURCE/wxlauncher

  # 1.1 patch
  msg "Applying patch 1.1"
  #tar -xf $srcdir/Diaspora_R1_Patch_$pkgver/Patch_Files.$pkgver.tar -C $SOURCE
  tar -xf $srcdir/Patch_Files.1.1.tar -C $SOURCE

  # 1.1.1 patch
  msg "Applying patch 1.1.1"
  tar -xf $srcdir/Patch_Files.1.1.1.tar -C $SOURCE

  msg "Applying other patchces"
  cd $SOURCE/fs2_open
	# Changes default video settings for better mod compatability
	patch -Np0 -i "$srcdir/osapi_unix.patch"

	# Increases hard limit of joystick buttons for better use with HOTAS etc.
	patch -Np0 -i "$srcdir/increase_joy_buttons_fixed.patch"


  # (2) Build the game engine (fs2_open)
  msg "Building..."
  cd $SOURCE/fs2_open
	LDFLAGS="-l:liblua.so.5.1 $LDFLAGS" CXXFLAGS="-I/usr/include/lua5.1 $CXXFLAGS" ./autogen.sh --enable-speech
  make


  # (4) Set up the Diaspora launcher profile (for wxlauncher)
  cd $SOURCE
  sed "s@^folder=.*\$@folder=/opt/$pkgname@;s@pro00099.template.ini@@" pro00099.template.ini > pro00099.ini


  # Grab command-line options from pro00099.ini for our runner script.
  cd $srcdir
  FS2_OPTS="$(sed -n 's@^flags=\(.*\)$@\1@p' $SOURCE/pro00099.ini | tr -d '\r\n')"
  sed -i "s@\\(FS2OPTS=\\).*@\\1\"$FS2_OPTS\"@" $srcdir/diaspora-sa.conf
}

package() {
  local SOURCE="$srcdir/Diaspora_R1_Linux/Diaspora/"

  cd $SOURCE

  #install -D -m644 -t "$pkgdir/opt/$pkgname/" *.vp *.ini *.png *.bmp data
  install -m644 -d "$pkgdir/opt/$pkgname/"
  mv *.vp *.png *.bmp mod.ini pro00099.ini data "$pkgdir/opt/$pkgname/"

  #install -D -m644 -t "$pkgdir/usr/share/doc/$pkgname/" *.pdf *.txt *.rtf
  install -m644 -d "$pkgdir/usr/share/doc/$pkgname/"
  mv *.pdf *.txt *.rtf "$pkgdir/usr/share/doc/$pkgname/"

  # Fix permissions (since 'mv' won't set them)
  chmod -R 644 "$pkgdir/opt/$pkgname/" "$pkgdir/usr/share/doc/$pkgname/"
  find "$pkgdir/opt/$pkgname/" "$pkgdir/usr/share/doc/$pkgname/" -type d -exec chmod 755 '{}' \;

	install -D -m755 $srcdir/diaspora-sa "$pkgdir/usr/bin/diaspora-sa"
	install -D -m644 $srcdir/diaspora-sa.conf "$pkgdir/usr/share/$pkgname/diaspora-sa.conf"

	install -D -m644 fs2_open/COPYING "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
	install -D -m755 fs2_open/code/fs2_open_3.7.1 "$pkgdir/opt/$pkgname/fs2_open_diaspora"
}

# vim:set ts=2 sw=2 et:
