# fdroidserver FlatPak
Flatpak playground

## How to build and install

0. Prereqs:
    * Install Flatpak https://flatpak.org/setup/
    * Add Flathub repo `flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo`
    * restart
    * install the freedesktop runtime `flatpak install flathub org.freedesktop.Platform//1.6 org.freedesktop.Sdk//1.6`
1. Copy json to some empty place
2. `flatpak-builder --install fdroidserver fdroidserver.json`
3. Flatpak is built and installed now, play with it `flatpak run org.fdroid.fdroidserver`
4. For debugging, dive inside the sandbox with `flatpak run --command=sh org.fdroid.fdroidserver`

## Steps taken so far

* Used [Flatpak PIP Generator](https://github.com/flatpak/flatpak-builder-tools/tree/master/pip) to generate module for fdroidserver (`python3 flatpak-pip-generator fdroidserver`)
* It wasn't installing without babel and cffi, so added them as modules as well
* Added placeholder for where env vars will be, added the network permission
* Added [openjdk runtime extension](https://github.com/flathub/org.freedesktop.Sdk.Extension.openjdk9), but we need truly an sdk https://github.com/flathub/org.freedesktop.Sdk.Extension.openjdk9/issues/3
