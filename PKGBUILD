# Maintainer: Matt C <mdc028[at]bucknell[dot]edu>
# Arch Contributor: Sébastien Luttringer
# Arch Contributor: Tom Gundersen <teg@jklm.no>

pkgname=filesystem
pkgver=2022.01.24
pkgrel=1
pkgdesc='Base Crystal Linux filesystem'
arch=('any')
license=('GPL')
url='https://getcryst.al'
depends=('iana-etc' 'zsh')
backup=('etc/crypttab' 'etc/fstab' 'etc/group' 'etc/gshadow' 'etc/host.conf'
        'etc/hosts' 'etc/issue' 'etc/ld.so.conf' 'etc/motd' 'etc/nsswitch.conf'
        'etc/passwd' 'etc/profile' 'etc/resolv.conf' 'etc/securetty'
        'etc/shadow' 'etc/shells')

source=('git+https://git.tar.black/crystal/filesystem-src' 'git+https://git.tar.black/crystal/branding')
md5sums=('SKIP')

package() {

        mkdir -p ${pkgdir}/usr/share/pixmaps
        cp branding/logos/crystal-logo-minimal.png ${pkgdir}/usr/share/pixmaps/crystal-logo.png
        cp branding/logos/crystal-logo-promo.png ${pkgdir}/usr/share/pixmaps/crystal.png

        cd "$pkgdir"

        # setup root filesystem
        for d in boot dev etc home mnt usr var opt srv/http run; do
        install -d -m755 $d
        done
        install -d -m555 proc
        install -d -m555 sys
        install -d -m0750 root
        install -d -m1777 tmp
        # vsftpd won't run with write perms on /srv/ftp
        # ftp (uid 14/gid 11)
        install -d -m555 -g 11 srv/ftp

        # setup /etc and /usr/share/factory/etc
        install -d etc/{ld.so.conf.d,skel,profile.d} usr/share/factory/etc
        for f in fstab group host.conf hosts issue ld.so.conf motd nsswitch.conf \
        passwd resolv.conf securetty shells profile; do
        install -m644 "$srcdir"/$f etc/
        install -m644 "$srcdir"/$f usr/share/factory/etc/
        done
        ln -s ../proc/self/mounts etc/mtab
        for f in gshadow shadow crypttab; do
        install -m600 "$srcdir"/$f etc/
        install -m600 "$srcdir"/$f usr/share/factory/etc/
        done

        touch etc/arch-release
        install -m644 "$srcdir"/locale.sh etc/profile.d/locale.sh

        install -Dm644 "$srcdir"/os-release usr/lib/os-release
        install -Dm644 "$srcdir"/os-release etc/os-release
        #install -Dm644 "$srcdir"/arch-release etc/arch-release

        # setup /var
        for d in cache local opt log/old lib/misc empty; do
        install -d -m755 var/$d
        done
        install -d -m1777 var/{tmp,spool/mail}

        # allow setgid games (gid 50) to write scores
        install -d -m775 -g 50 var/games
        ln -s spool/mail var/mail
        ln -s ../run var/run
        ln -s ../run/lock var/lock

        # setup /usr hierarchy
        for d in bin include lib share/{misc,pixmaps} src; do
        install -d -m755 usr/$d
        done
        for d in {1..8}; do
        install -d -m755 usr/share/man/man$d
        done

        # add lib symlinks
        ln -s usr/lib lib
        [[ $CARCH = 'x86_64' ]] && {
        ln -s usr/lib lib64
        ln -s lib usr/lib64
        }

        # add bin symlinks
        ln -s usr/bin bin
        ln -s usr/bin sbin
        ln -s bin usr/sbin

        # setup /usr/local hierarchy
        for d in bin etc games include lib man sbin share src; do
        install -d -m755 usr/local/$d
        done
        ln -s ../man usr/local/share/man

        # setup systemd-sysctl
        install -D -m644 "$srcdir"/sysctl usr/lib/sysctl.d/10-arch.conf

        # setup systemd-sysusers
        install -D -m644 "$srcdir"/sysusers usr/lib/sysusers.d/arch.conf

        # setup systemd-tmpfiles
        install -D -m644 "$srcdir"/tmpfiles usr/lib/tmpfiles.d/arch.conf

        # setup systemd.environment-generator
        install -D -m755 "$srcdir"/env-generator usr/lib/systemd/system-environment-generators/10-arch
}

# vim:set ts=2 sw=2 et:
