# Maintainer: Joan Figueras <ffigue at gmail dot com>
# Contributor: Torge Matthies <openglfreak at googlemail dot com>
# Contributor: Jan Alexander Steffens (heftig) <jan.steffens@gmail.com>

##
## The following variables can be customized at build time. Use env or export to change at your wish
##
##   Example: env _microarchitecture=98 use_numa=n use_tracers=n makepkg -sc
##
## Look inside 'choose-gcc-optimization.sh' to choose your microarchitecture
## Valid numbers between: 0 to 99
## Default is: 0 => generic
## Good option if your package is for one machine: 98 (Intel native) or 99 (AMD native)
if [ -z ${_microarchitecture+x} ]; then
  _microarchitecture=15
fi

## Disable NUMA since most users do not have multiple processors. Breaks CUDA/NvEnc.
## Archlinux and Xanmod enable it by default.
## Set variable "use_numa" to: n to disable (possibly increase performance)
##                             y to enable  (stock default)
if [ -z ${use_numa+x} ]; then
  use_numa=y
fi

## For performance you can disable FUNCTION_TRACER/GRAPH_TRACER. Limits debugging and analyzing of the kernel.
## Stock Archlinux and Xanmod have this enabled. 
## Set variable "use_tracers" to: n to disable (possibly increase performance)
##                                y to enable  (stock default)
if [ -z ${use_tracers+x} ]; then
  use_tracers=y
fi

# Unique compiler supported upstream is GCC
## Choose between GCC and CLANG config (default is GCC)
## Use the environment variable "_compiler=clang"
if [ "${_compiler}" = "clang" ]; then
  _compiler_flags="CC=clang HOSTCC=clang LLVM=1 LLVM_IAS=1"
fi

# Choose between the 4 main configs for EDGE branch. Default x86-64-v2 which use CONFIG_GENERIC_CPU2:
# Possible values: config_x86-64 / config_x86-64-v2 (default) / config_x86-64-v3 / config_x86-64-v4
# This will be overwritten by selecting any option in microarchitecture script
# Source files: https://github.com/xanmod/linux/tree/5.17/CONFIGS/xanmod/gcc
if [ -z ${_config+x} ]; then
  _config=config_x86-64-v3
fi

# Compress modules with ZSTD (to save disk space)
if [ -z ${_compress_modules+x} ]; then
  _compress_modules=n
fi

# Compile ONLY used modules to VASTLY reduce the number of modules built
# and the build time.
#
# To keep track of which modules are needed for your specific system/hardware,
# give module_db script a try: https://aur.archlinux.org/packages/modprobed-db
# This PKGBUILD read the database kept if it exists
#
# More at this wiki page ---> https://wiki.archlinux.org/index.php/Modprobed-db
if [ -z ${_localmodcfg} ]; then
  _localmodcfg=n
fi

# Tweak kernel options prior to a build via nconfig
if [ -z ${_makenconfig} ]; then
  _makenconfig=n
fi

### IMPORTANT: Do no edit below this line unless you know what you're doing

pkgbase=linux-xanmod-rt-rog
_major=6.0
pkgver=${_major}.7
_branch=6.x
_rt=14
xanmod=1
pkgrel=${xanmod}
pkgdesc='Linux Xanmod - Latest Mainline Realtime Kernel'
url="http://www.xanmod.org/"
arch=(x86_64)

license=(GPL2)
makedepends=(
  bc cpio kmod libelf perl tar xz
)
if [ "${_compiler}" = "clang" ]; then
  makedepends+=(clang llvm lld python)
fi
options=('!strip')
_srcname="linux-${pkgver}-xanmod${xanmod}"

source=("https://cdn.kernel.org/pub/linux/kernel/v${_branch}/linux-${_major}.tar."{xz,sign}
	"https://github.com/xanmod/linux/releases/download/${pkgver}-rt${_rt}-xanmod${xanmod}/patch-${pkgver}-rt${_rt}-xanmod${xanmod}.xz"
        "config"
        "choose-gcc-optimization.sh"
        
        #"sys-kernel_arch-sources-g14_files-0004-5.17+--more-uarches-for-kernel.patch"::"https://raw.githubusercontent.com/graysky2/kernel_compiler_patch/master/more-uarches-for-kernel-5.17+.patch"
        #"sys-kernel_arch-sources-g14_files-0005-lru-multi-generational.patch"
        
        
        0001-acpi-proc-idle-skip-dummy-wait.patch
        #0001-ACPI-x86-s2idle-Move-_HID-handling-for-AMD-systems-i.patch
        
        0001-platform-x86-asus-wmi-Convert-all-attr-show-to-use-s.patch
        #0002-ACPI-x86-s2idle-If-a-new-AMD-_HID-is-missing-assume-.patch
        0002-platform-x86-asus-wmi-Use-kobj_to_dev.patch
        #0003-ACPI-x86-s2idle-Add-module-parameter-to-prefer-Micro.patch
        0003-platform-x86-asus-wmi-Document-the-dgpu_disable-sysf.patch
        #0004-ACPI-x86-s2idle-Add-a-quirk-for-ASUS-TUF-Gaming-A17-.patch
        0004-platform-x86-asus-wmi-Document-the-egpu_enable-sysfs.patch
        #0005-ACPI-x86-s2idle-Add-a-quirk-for-ASUS-ROG-Zephyrus-G1.patch
        0005-platform-x86-asus-wmi-Document-the-panel_od-sysfs-at.patch
        #0006-ACPI-x86-s2idle-Add-a-quirk-for-Lenovo-Slim-7-Pro-14.patch
        
        
        
        0006-platform-x86-asus-wmi-Refactor-disable_gpu-attribute.patch
        #0007-ACPI-x86-s2idle-Add-a-quirk-for-ASUSTeK-COMPUTER-INC.patch
        0007-platform-x86-asus-wmi-Refactor-egpu_enable-attribute.patch
        #0008-ACPI-x86-s2idle-Fix-a-NULL-pointer-dereference.patch
        0008-platform-x86-asus-wmi-Refactor-panel_od-attribute.patch
        #0009-ACPI-x86-s2idle-Add-another-ID-to-s2idle_dmi_table.patch
        0009-platform-x86-asus-wmi-Simplify-some-of-the-_check_pr.patch
        #0010-platform-x86-asus-wmi-Support-the-hardware-GPU-MUX-o.patch
        
        
        
        
        0011-platform-x86-asus-wmi-Adjust-tablet-lidflip-handling.patch
        0012-platform-x86-asus-wmi-Add-support-for-ROG-X13-tablet.patch
        0013-platform-x86-asus-wmi-Simplify-tablet-mode-switch-pr.patch
        0014-platform-x86-asus-wmi-Simplify-tablet-mode-switch-ha.patch
        #0017-asus-wmi-Implement-TUF-laptop-keyboard-LED-modes.patch
        #0018-asus-wmi-Implement-TUF-laptop-keyboard-power-states.patch
        0019-HID-amd_sfh-Add-keyguard-for-ASUS-ROG-X13-tablet.patch
        0020-asus-wmi-Modify-behaviour-of-Fn-F5-fan-key.patch
        0021-asus-wmi-Support-the-GPU-fan-on-TUF-laptops.patch
        0001-asus-wmi-Expand-support-of-GPU-fan-to-read-RPM-and-l.patch
        #0001-platform-x86-asus-wmi-Add-safety-checks-to-dgpu-egpu.patch
        0001-platform-x86-asus-wmi-Add-support-for-ROG-X16-tablet.patch
        
        
        "sys-kernel_arch-sources-g14_files-0047-asus-nb-wmi-Add-tablet_mode_sw-lid-flip.patch"
        "sys-kernel_arch-sources-g14_files-0048-asus-nb-wmi-fix-tablet_mode_sw_int.patch"
        "sys-kernel_arch-sources-g14_files-0049-ALSA-hda-realtek-Add-quirk-for-ASUS-M16-GU603H.patch"
)
        #"patch-${pkgver}-xanmod${xanmod}.xz::https://sourceforge.net/projects/xanmod/files/releases/stable/${pkgver}-xanmod${xanmod}/patch-${pkgver}-xanmod${xanmod}.xz/download"
validpgpkeys=(
    'ABAF11C65A2970B130ABE3C479BE3E4300411886' # Linux Torvalds
    '647F28654894E3BD457199BE38DBBDC86092693E' # Greg Kroah-Hartman
)

# Archlinux patches
_commit="ec9e9a4219fe221dec93fa16fddbe44a34933d8d"
_patches=()
for _patch in ${_patches[@]}; do
    #source+=("${_patch}::https://git.archlinux.org/svntogit/packages.git/plain/trunk/${_patch}?h=packages/linux&id=${_commit}")
    source+=("${_patch}::https://raw.githubusercontent.com/archlinux/svntogit-packages/${_commit}/trunk/${_patch}")
done

sha256sums=('5c2443a5538de52688efb55c27ab0539c1f5eb58c0cfd16a2b9fbb08fd81788e'
            'SKIP'
            '394cd4fbd74f73f6982e112ae1759a79eb870b9531f89ee00dc648383586da7c'
            '05d8119dcf06b20d585cef21b5560c8e53c6c97b6fddef7f0afe8c545734b64a'
            'dda2e928f3b02c28e71d4e99f90b499b4c99a265d30fceec7dc1dd7082afc285'
            '40e4c300be6681ab3b30042eb4bb5981081ce029b2bdd4773a38b4a9f65e943e'
            'a51b11bd64915454981aeb0aa680efb508a24a6df552caa17cd01e31a776a7e5'
            '02424be0d9a42d8b19a5ed0a3a108b35dc31a9e2938efa9cf81f52a682e39f63'
            '113289ef6a4e813bae02487a4cf961f821e000a195e25ff4ffcb56eedd42b12b'
            'f7b1f4d3c461f010c89507ff378df8232ea5d4bfcda600c5a06e8e09128d5291'
            '625e5f05b3fbdccedb1bf48b42422cd8de1f9bb3f621b37c569bd1081ba52e43'
            '4eca79899c890d07f20b79534730d415853e1ec80b38b9b6a30301e5806961f3'
            '7e575f72423e01b74716e1fc21a3364f4cac29366b48caa0cda3504f75071ad3'
            '7057f33c63880a540f5dcba0945140f9dccb2a6de6715eb5853d65c5447c1736'
            'd6f06dfee9d7910208c3851e730ccb79bf349b5ea5f8ab74f994489c679c40e3'
            '2bbb9725cfdd834553ef30f81609bd9b6c4f6a71caf8da1c65eb413b68d3d98a'
            '92e63800f0ec71456345cd7152dac44996ea24e4bc24a3a5a07c6e8e29d4ab6e'
            'c47962c06c85fcaa8c8cc5a9bbd8eff56968caeb4e777ef99f2bbda731398718'
            '87d3df1c0eac4b7d301b2dc375f9e754429ca394c9ff09218a00ab1462627e24'
            'd45e2ae1d21b1dc8e0de94a4fa58e9a53d72306843f87d3cc49f5f641399d8e3'
            '6907a750183e1f06c4b15014f078bcd15cb4ea498fd9d4c5c4c4d1955dac69d3'
            'abd1d8def25f7fae0d5590c7fd36ea4dcc479d16d268615d8b3d95c8e8446d44'
            '9efcef7aab8094ed3596cba872527d6e838c760ba4f4c5b7b1586a963b71d576'
            'ddcd442b780bffde86c51373d6e24c77ecc890375cd0be361d2de9f26826f845'
            '15e912a66e4bbce1cf0450f1dc6610653df29df8dd6d5426f9c1b039490436c8'
            'e9e4b03b836e1a86a2a5dc70b0d5512348eb19742f83bee794a3ab7d91bd41cf'
            '982a31e47d3d586789e1b3cdda25f75e3b71d810e7494202089b8f2cef7c0ef9')

export KBUILD_BUILD_HOST=${KBUILD_BUILD_HOST:-archlinux}
export KBUILD_BUILD_USER=${KBUILD_BUILD_USER:-makepkg}
export KBUILD_BUILD_TIMESTAMP=${KBUILD_BUILD_TIMESTAMP:-$(date -Ru${SOURCE_DATE_EPOCH:+d @$SOURCE_DATE_EPOCH})}

prepare() {
  cd linux-${_major}

  # Apply VFIO fix
  echo Applying experimental VFIO patch.
  wget https://raw.githubusercontent.com/Kinsteen/win10-gpu-passthrough/main/pat_patch.diff
  mv pat_patch.diff ..
  patch -p0 < ../pat_patch.diff arch/x86/mm/pat/memtype_interval.c

  # Apply Xanmod patch
  patch -Np1 -i ../patch-${pkgver}-rt${_rt}-xanmod${xanmod}

  msg2 "Setting version..."
  scripts/setlocalversion --save-scmversion
  echo "-$pkgrel" > localversion.10-pkgrel
  echo "${pkgbase#linux-xanmod-edge}" > localversion.20-pkgname

  # Archlinux patches
  local src
  for src in "${source[@]}"; do
    src="${src%%::*}"
    src="${src##*/}"
    [[ $src = *.patch ]] || continue
    msg2 "Applying patch $src..."
    patch -Np1 < "../$src"
  done

  # Applying configuration
  cp -vf ../config .config
  # enable LTO_CLANG_THIN
  if [ "${_compiler}" = "clang" ]; then
    scripts/config --disable LTO_CLANG_FULL
    scripts/config --enable LTO_CLANG_THIN
  fi

  # CONFIG_STACK_VALIDATION gives better stack traces. Also is enabled in all official kernel packages by Archlinux team
  scripts/config --enable CONFIG_STACK_VALIDATION

  # Enable IKCONFIG following Arch's philosophy
  scripts/config --enable CONFIG_IKCONFIG \
                 --enable CONFIG_IKCONFIG_PROC

  # User set. See at the top of this file
  if [ "$use_tracers" = "n" ]; then
    msg2 "Disabling FUNCTION_TRACER/GRAPH_TRACER only if we are not compiling with clang..."
    if [ "${_compiler}" = "gcc" ]; then
      scripts/config --disable CONFIG_FUNCTION_TRACER \
                     --disable CONFIG_STACK_TRACER
    fi
  fi

  if [ "$use_numa" = "n" ]; then
    msg2 "Disabling NUMA..."
    scripts/config --disable CONFIG_NUMA
  fi

  # Compress modules by default (following Arch's kernel)
  if [ "$_compress_modules" = "y" ]; then
    scripts/config --enable CONFIG_MODULE_COMPRESS_ZSTD
  fi

  # Let's user choose microarchitecture optimization in GCC
  # Use default microarchitecture only if we have not choosen another microarchitecture
  if [ "$_microarchitecture" -ne "0" ]; then
    ../choose-gcc-optimization.sh $_microarchitecture
  fi

  # This is intended for the people that want to build this package with their own config
  # Put the file "myconfig" at the package folder (this will take preference) or "${XDG_CONFIG_HOME}/linux-xanmod/myconfig"
  # If we detect partial file with scripts/config commands, we execute as a script
  # If not, it's a full config, will be replaced
  for _myconfig in "${SRCDEST}/myconfig" "${HOME}/.config/linux-xanmod/myconfig" "${XDG_CONFIG_HOME}/linux-xanmod/myconfig" ; do
    if [ -f "${_myconfig}" ] && [ "$(wc -l <"${_myconfig}")" -gt "0" ]; then
      if grep -q 'scripts/config' "${_myconfig}"; then
        # myconfig is a partial file. Executing as a script
        msg2 "Applying myconfig..."
        bash -x "${_myconfig}"
      else
        # myconfig is a full config file. Replacing default .config
        msg2 "Using user CUSTOM config..."
        cp -f "${_myconfig}" .config
      fi
      echo
      break
    fi
  done

  ### Optionally load needed modules for the make localmodconfig
  # See https://aur.archlinux.org/packages/modprobed-db
  if [ "$_localmodcfg" = "y" ]; then
    if [ -f $HOME/.config/modprobed.db ]; then
      msg2 "Running Steven Rostedt's make localmodconfig now"
      make ${_compiler_flags} LSMOD=$HOME/.config/modprobed.db localmodconfig
    else
      msg2 "No modprobed.db data found"
      exit 1
    fi
  fi

  make ${_compiler_flags} olddefconfig

  make -s kernelrelease > version
  msg2 "Prepared %s version %s" "$pkgbase" "$(<version)"

  if [ "$_makenconfig" = "y" ]; then
    make ${_compiler_flags} nconfig
  fi

  # save configuration for later reuse
  cat .config > "${SRCDEST}/config.last"
}

build() {
  cd linux-${_major}
  make ${_compiler_flags} all
}

_package() {
  pkgdesc="The Linux kernel and modules with Xanmod patches"
  depends=(coreutils kmod initramfs)
  optdepends=('crda: to set the correct wireless channels of your country'
              'linux-firmware: firmware images needed for some devices')
  provides=(VIRTUALBOX-GUEST-MODULES
            WIREGUARD-MODULE
            KSMBD-MODULE
            NTFS3-MODULE)

  cd linux-${_major}
  local kernver="$(<version)"
  local modulesdir="$pkgdir/usr/lib/modules/$kernver"

  msg2 "Installing boot image..."
  # systemd expects to find the kernel here to allow hibernation
  # https://github.com/systemd/systemd/commit/edda44605f06a41fb86b7ab8128dcf99161d2344
  install -Dm644 "$(make -s image_name)" "$modulesdir/vmlinuz"

  # Used by mkinitcpio to name the kernel
  echo "$pkgbase" | install -Dm644 /dev/stdin "$modulesdir/pkgbase"

  msg2 "Installing modules..."
  make INSTALL_MOD_PATH="$pkgdir/usr" INSTALL_MOD_STRIP=1 modules_install

  # remove build and source links
  rm "$modulesdir"/{source,build}
}

_package-headers() {
  pkgdesc="Headers and scripts for building modules for the $pkgdesc kernel"
  depends=(pahole)

  cd linux-${_major}
  local builddir="$pkgdir/usr/lib/modules/$(<version)/build"

  msg2 "Installing build files..."
  install -Dt "$builddir" -m644 .config Makefile Module.symvers System.map \
    localversion.* version vmlinux
  install -Dt "$builddir/kernel" -m644 kernel/Makefile
  install -Dt "$builddir/arch/x86" -m644 arch/x86/Makefile
  cp -t "$builddir" -a scripts

  # required when STACK_VALIDATION is enabled
  install -Dt "$builddir/tools/objtool" tools/objtool/objtool

  # required when DEBUG_INFO_BTF_MODULES is enabled
  if [ -f "$builddir/tools/bpf/resolve_btfids" ]; then install -Dt "$builddir/tools/bpf/resolve_btfids" tools/bpf/resolve_btfids/resolve_btfids ; fi

  msg2 "Installing headers..."
  cp -t "$builddir" -a include
  cp -t "$builddir/arch/x86" -a arch/x86/include
  install -Dt "$builddir/arch/x86/kernel" -m644 arch/x86/kernel/asm-offsets.s

  install -Dt "$builddir/drivers/md" -m644 drivers/md/*.h
  install -Dt "$builddir/net/mac80211" -m644 net/mac80211/*.h

  # https://bugs.archlinux.org/task/13146
  install -Dt "$builddir/drivers/media/i2c" -m644 drivers/media/i2c/msp3400-driver.h

  # https://bugs.archlinux.org/task/20402
  install -Dt "$builddir/drivers/media/usb/dvb-usb" -m644 drivers/media/usb/dvb-usb/*.h
  install -Dt "$builddir/drivers/media/dvb-frontends" -m644 drivers/media/dvb-frontends/*.h
  install -Dt "$builddir/drivers/media/tuners" -m644 drivers/media/tuners/*.h

  # https://bugs.archlinux.org/task/71392
  install -Dt "$builddir/drivers/iio/common/hid-sensors" -m644 drivers/iio/common/hid-sensors/*.h

  echo "Installing KConfig files..."
  find . -name 'Kconfig*' -exec install -Dm644 {} "$builddir/{}" \;

  msg2 "Removing unneeded architectures..."
  local arch
  for arch in "$builddir"/arch/*/; do
    [[ $arch = */x86/ ]] && continue
    echo "Removing $(basename "$arch")"
    rm -r "$arch"
  done

  msg2 "Removing documentation..."
  rm -r "$builddir/Documentation"

  msg2 "Removing broken symlinks..."
  find -L "$builddir" -type l -printf 'Removing %P\n' -delete

  msg2 "Removing loose objects..."
  find "$builddir" -type f -name '*.o' -printf 'Removing %P\n' -delete

  msg2 "Stripping build tools..."
  local file
  while read -rd '' file; do
    case "$(file -bi "$file")" in
      application/x-sharedlib\;*)      # Libraries (.so)
        strip -v $STRIP_SHARED "$file" ;;
      application/x-archive\;*)        # Libraries (.a)
        strip -v $STRIP_STATIC "$file" ;;
      application/x-executable\;*)     # Binaries
        strip -v $STRIP_BINARIES "$file" ;;
      application/x-pie-executable\;*) # Relocatable binaries
        strip -v $STRIP_SHARED "$file" ;;
    esac
  done < <(find "$builddir" -type f -perm -u+x ! -name vmlinux -print0)

  msg2 "Stripping vmlinux..."
  strip -v $STRIP_STATIC "$builddir/vmlinux"
  
  msg2 "Adding symlink..."
  mkdir -p "$pkgdir/usr/src"
  ln -sr "$builddir" "$pkgdir/usr/src/$pkgbase"
}

pkgname=("${pkgbase}" "${pkgbase}-headers")
for _p in "${pkgname[@]}"; do
  eval "package_$_p() {
    $(declare -f "_package${_p#$pkgbase}")
    _package${_p#$pkgbase}
  }"
done

# vim:set ts=8 sts=2 sw=2 et:
