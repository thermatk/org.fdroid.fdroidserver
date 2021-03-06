# fdroidserver FlatPak
Flatpak playground

## How to build and install

0. Prereqs:
    * Install Flatpak https://flatpak.org/setup/
    * Add Flathub repo `flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo`
    * restart
    * install the freedesktop runtime `flatpak install flathub org.freedesktop.Platform//1.6 org.freedesktop.Sdk//1.6`
    * install jdk extension `flatpak install flathub org.freedesktop.Sdk.Extension.openjdk9`
1. Copy json to some empty place
2. `flatpak-builder --install fdroidserver fdroidserver.json`
3. Flatpak is built and installed now, play with it `flatpak run org.fdroid.fdroidserver`
4. For debugging, dive inside the sandbox with `flatpak run --command=sh org.fdroid.fdroidserver`

## Steps taken so far

* Used [Flatpak PIP Generator](https://github.com/flatpak/flatpak-builder-tools/tree/master/pip) to generate module for fdroidserver (`python3 flatpak-pip-generator fdroidserver`)
* It wasn't installing without babel and cffi, so added them as modules as well
* Added placeholder for where env vars will be, added the network permission
* Added [openjdk runtime extension](https://github.com/flathub/org.freedesktop.Sdk.Extension.openjdk9), but we [need truly an sdk]( https://github.com/flathub/org.freedesktop.Sdk.Extension.openjdk9/issues/3)
* Added full jdk, also [PR to upstream](https://github.com/flathub/org.freedesktop.Sdk.Extension.openjdk9/pull/7)
* As for 32-bit libs [required by fdroidserver](https://f-droid.org/en/docs/Installing_the_Server_and_Repo_Tools/), those are the same requirements as for [Android Studio](https://developer.android.com/studio/troubleshoot#linux_libraries). Yet while they are being [explicitly added in an older flatpak](https://github.com/endlessm/android-studio-flatpak/blob/master/com.google.AndroidStudio.json.in), in the [newest they aren't](https://github.com/flathub/com.google.AndroidStudio/blob/master/com.google.AndroidStudio.json). Let's assume it works, unless it breaks
* Since Virtualbox requires kernel modules, let's skip it and go QEMU/KVM route? This should work, because the emulator in the aforementioned Android Studio flatpak works. [Also](https://github.com/flatpak/flatpak/commit/c9c35133c3a0041c7bf9269791a2caebfb04ddf0)
* Vagrant in a separate flatpak for now, using [PKGBUILD from Arch as inspiration](https://git.archlinux.org/svntogit/community.git/tree/trunk/PKGBUILD?h=packages/vagrant)
* All Ruby gems had to be added manually, until someone writes a generator
* To compile native gems, need to add `CPATH=/usr/include`
* Also, because of broken /usr/lib/ruby/2.2.0/x86_64-linux-gnu/rbconfig.rb shipped in the runtime, need to do some crazy patch inside a gem `RbConfig::MAKEFILE_CONFIG['CC'] = ENV['CC'] if ENV['CC']`
* Added libvirt and its dependencies, simply as a lib. Fixed some errors like these https://github.com/flatpak/flatpak/issues/469
* Added QEMU. Chose targets according to https://wiki.qemu.org/Hosts/Linux, KVM seems to be on by default
* Added vagrant-libvirt and vagrant-mutate plugins. For some reason they don't get listed by Vagrant, but seem to work
