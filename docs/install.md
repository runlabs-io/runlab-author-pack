# Install the pack

`trylab` is how you write and try a lab. The other programs in the pack support it. You run `./trylab`.

Download the latest pack and unpack it. Run every command in this manual from that directory.

https://github.com/runlabs-io/runlab-author-pack/releases/latest

The pack runs on a 64-bit PC with Linux. Your account needs access to `/dev/kvm`, because that's how Linux starts the lab machine.

## Setup

A lab disk is built with Packer. Packer is a prerequisite, and it is not in the download. Install it before you build a disk.

`./prereqs` installs Packer from HashiCorp's apt repository, along with the other packages a build needs. You can run that yourself now, or wait until `trylab` asks.

## What a launch needs

Before a lab starts, `trylab` checks that those programs are present. One of them is Firecracker, which runs the lab machine. The download does not include that binary. `./prereqs` writes it into this directory as `firecracker`.

If something is missing, `trylab` asks `Install them with ./prereqs?`. Answer yes and it runs `./prereqs`. Press Enter to leave them uninstalled.

You can run `./prereqs` yourself instead of waiting for that question. That is what it is for. It prints the name of each package before installing it, so you can see what it's doing. apt asks for your password.

If your account can't use `/dev/kvm`, `./prereqs` tells you so at the end.

## Secrets

The pack also includes `runlabctl`. It stores a secret so a lab can receive it without putting the value in the repository or on the disk. How that works is in [An API key](secret.md).

## Launch a lab

`./trylab <lab>` launches that lab in a microVM.

The machine boots from a disk image. `./packer/build.sh` builds that image: you point it at a lab, and it writes the disk. The `packer` directory is those build scripts. It is not a program.

You don't need to run `./packer/build.sh`. When the disk doesn't exist yet, `trylab` asks `Build it now?`. Press Enter to build it. The build needs a network connection and takes a while. When you later change something the disk is built from, `setup.sh`, files under `data/`, the disk size, or the login key, `trylab` rebuilds the disk. Editing the lesson does not.

## Starting the machine

Starting the microVM needs administrative privileges, for the lab network. The pack includes `elevate` so you don't run `sudo` every time. `./prereqs` installs that helper for your account and grants it those privileges. `trylab` uses it. You run `./trylab` as yourself.

`./prereqs` will not run if `elevate` is missing from the pack directory.

The program you use is `trylab`.

## The login key

The pack logs in to the lab machine as root, the administrator account on that machine. To do that it uses a key file named `ssh-key` in the pack directory, which the first disk build creates if it isn't already there.

## The shared disk

A disk holds the lab machine's files, stored as a single file on your computer, and each lab boots from a copy of one.

Labs that don't install their own software all share one disk, named `default`. The machine on that disk runs Ubuntu and comes with Docker already installed, along with curl and other everyday tools.

The first build also saves the kernel, the program the machine boots with, so later builds can reuse it.

## In the download

- `trylab`, the program you run
- `prereqs`, which installs Packer, Firecracker, and `elevate`
- `elevate`, which `prereqs` installs so `trylab` can start the lab network
- `runlabctl`, which stores secrets
- `packer/`, the build scripts `trylab` calls
- three example labs: `basics`, `docker`, and `nginx`
