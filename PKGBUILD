pkgname="emacs-nox-opt-jit-git"
pkgver=28.0.50.147641
pkgrel=1
pkgdesc="GNU Emacs. Development native-comp branch & cli only"
arch=('x86_64')
ur="https://www.gnu.org/software/emacs/"
license=('GPL3')
depends=('jansson' 'libotf' 'libgccjit' 'gpm' 'dbus' 'gnutls')
makedepends=('git' 'clang' 'lld' 'llvm' 'polly')
provides=('emacs' 'emacs-seq')
conflicts=('emacs' 'emacs26-git' 'emacs-27-git' 'emacs-git' 'emacs-seq')
replaces=('emacs' 'emacs26-git' 'emacs-27-git' 'emacs-git' 'emacs-seq')
source=("emacs-git::git://git.savannah.gnu.org/emacs.git#branch=feature/native-comp")
md5sums=('SKIP')


pkgver() {
    cd "$srcdir/emacs-git"

    printf "%s.%s" \
           "$(grep AC_INIT configure.ac | \
           sed -e 's/^.\+\ \([0-9]\+\.[0-9]\+\.[0-9]\+\?\).\+$/\1/')" \
           "$(git rev-list --count HEAD)"
}

prepare() {
    cd "$srcdir/emacs-git"
    [[ -x configure ]] || ( ./autogen.sh git && ./autogen.sh autoconf )
}


build() {
    cd "$srcdir/emacs-git"

    CC="/usr/bin/clang" CXX="/usr/bin/clang++" CFLAGS="-march=native -O3 -pipe -fno-plt -pthreads -ffast-math -flto -ftls-model=initial-exec -fuse-linker-plugin -fuse-ld=lld -Xclang -load -Xclang /usr/lib/LLVMPolly.so -mllvm -polly" CXXFLAGS="$CFLAGS" LD="/usr/bin/lld" AR="/usr/bin/llvm-ar" AS="/usr/bin/llvm-as" ./configure --prefix=/usr --sysconfdir=/etc --libexecdir=/usr/lib --localstatedir=/var --mandir=/usr/share/man --with-modules --enable-link-time-optimization --without-rsvg --without-lcms2 --without-x --without-x-toolkit --without-cairo --without-sound --without-imagemagick --without-xft --with-json --without-harfbuzz --without-m17n-flt --without-xaw3d --without-xim --without-xdbe --with-dbus --without-gsettings --without-selinux --without-gnutls --with-native-compilation --without-mailutils --without-pop --without-kerberos --without-kerberos5 --without-hesiod --without-mail-unlink --without-compress-install --without-toolkit-scroll-bars

    make NATIVE_FULL_AOT=1
}

package() {
    cd "$srcdir/emacs-git"

    make DESTDIR="$pkgdir/" install

    mv "$pkgdir"/usr/bin/{ctags,ctags.emacs}

    find "$pkgdir"/usr/share/emacs/ | xargs chown root:root
    rm -rf "$pkgdir"/var/games
    rm -rf "$pkgdir"/usr/share/man/man1/ctags.1.gz
}
