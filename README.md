# org.freedesktop.Sdk.Extension.podmanhost

A minimal Flatpak SDK extension that exposes the host's
**Podman** binary inside Flatpak SDK environments
by symlinking `/app/host-spawn` to `/app/bin/podman` and `/app/bin/podman-compose`.

---

## 📦 Purpose

This extension allows Flatpak SDKs and development environments
(like GNOME Builder or Toolbox-like setups) to run
the host’s `podman` binary transparently.

Inside a Flatpak environment that includes this extension, commands like:

```bash
podman ps
podman build .
```

will execute using the host's podman through /app/host-spawn.

## 🛠️ Building the Extension

Make sure you have flatpak-builder and the Freedesktop SDK installed.

```
# Build the extension
flatpak-builder build-dir org.freedesktop.Sdk.Extension.podmanhost.yaml --force-clean

# Alternative build with flatpak
flatpak run org.flatpak.Builder build-dir --user --ccache --force-clean --install org.freedesktop.Sdk.Extension.podmanhost.yam

# Build and install the extension
flatpak-builder build-dir org.freedesktop.Sdk.Extension.podmanhost.yaml --force-clean --user --install

# (Optional) Install it locally
flatpak-builder --install --user build-dir org.freedesktop.Sdk.Extension.podmanhost.yaml
```

Removing the extension
```
flatpak uninstall org.freedesktop.Sdk.Extension.podmanhost
```

Test the extension
```
flatpak run --command=ls org.freedesktop.Sdk//24.08 /usr/lib/sdk/podmanhost
```
should show:
```
bin  enable.sh	install-sdk.sh	install.sh  manifest.json  share
```

```
# build as sdk extension and mount point manually
flatpak run \
  --filesystem=/usr/lib/sdk/podmanhost:ro \
  --env=PATH=/usr/lib/sdk/podmanhost/bin:$PATH \
  com.vscodium.codium
```

 Patch Codium’s Flatpak manifest locally



## 🧩 Using the Extension

You can enable this SDK extension in another Flatpak manifest (for example, your app or SDK container):

```yaml
sdk-extensions:
  - org.freedesktop.Sdk.Extension.podmanhost
```

Then, inside that environment, /app/bin/podman will resolve to /app/host-spawn, letting you call the host’s Podman.

## 🧹 Notes

    This does not include Podman itself — it only symlinks the host binary via /app/host-spawn.

    Ensure your Flatpak sandbox allows --talk-name=org.freedesktop.Flatpak or similar permissions to access /app/host-spawn.

