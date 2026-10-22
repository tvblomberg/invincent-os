# invincent-os &nbsp; [![bluebuild build badge](https://github.com/tvblomberg/invincent-os/actions/workflows/build.yml/badge.svg)](https://github.com/tvblomberg/invincent-os/actions/workflows/build.yml)

My personal Fedora atomic image: [wayblue](https://github.com/wayblueorg/wayblue) Hyprland plus the packages, services and Flatpaks I use, built daily with [BlueBuild](https://blue-build.org/).

Everything that used to be layered with `rpm-ostree install` lives in [`recipes/recipe.yml`](recipes/recipe.yml) instead:

- **Packages:** ghostty, zsh, neovim, bat, btop, fd, ripgrep, swappy, gtk-murrine-engine, podman-compose, libvirt/qemu/virt-manager, tailscale, rpmrebuild
- **Services:** tailscaled, libvirtd
- **Flatpaks:** installed on first boot if missing (system scope, Flathub)

User configuration lives in the dotfiles repo, not here.

## Rebuilding a machine

1. Install [wayblue](https://github.com/wayblueorg/wayblue) Hyprland.
2. Rebase to the unsigned image first, to get the signing key and policy installed, then reboot:
   ```
   rpm-ostree rebase ostree-unverified-registry:ghcr.io/tvblomberg/invincent-os:latest
   systemctl reboot
   ```
3. Rebase to the signed image and reboot again:
   ```
   rpm-ostree rebase ostree-image-signed:docker://ghcr.io/tvblomberg/invincent-os:latest
   systemctl reboot
   ```
4. Restore the dotfiles and run their bootstrap.

## Changing the image

Edit `recipes/recipe.yml` and push. GitHub Actions builds and signs a new image (also daily at 06:00 UTC); `rpm-ostree upgrade` or `os-update` picks it up.

## Verification

Images are signed with [cosign](https://github.com/sigstore/cosign):

```bash
cosign verify --key cosign.pub ghcr.io/tvblomberg/invincent-os
```

The private key is stored as the `SIGNING_SECRET` repository secret and in my password manager; it is never committed.
