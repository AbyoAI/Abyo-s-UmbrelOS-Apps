# VoxelDash One — Umbrel app package

## Install into a custom Umbrel app store

Copy the `voxeldash-one` directory into the root of your Umbrel app-store
repository, alongside its other app directories. Commit and push the change,
then add/update that app store in Umbrel.

## Open the app

After installation, open:

- `http://umbrel.local:7867`

On first launch, complete VoxelDash's administrator setup and use a strong,
unique password.

## Important networking note

This package uses host networking because VoxelDash's managed Minecraft
servers need to bind/listen on the Umbrel host network. With host networking,
Docker `ports:` mappings are intentionally omitted. The dashboard listens on
port 7867.

Host networking bypasses Umbrel's normal app-proxy authentication layer.
Protect the VoxelDash account with a strong password and do not expose this
port directly to the public internet without an appropriate secure access
layer.

## Before publishing to a public app store

The image is pinned to the upstream `1.2.0` release tag, but not yet to a
multi-architecture image digest. For a public Umbrel App Store submission,
verify that the image supports both `linux/amd64` and `linux/arm64`, obtain
the manifest-list digest, and pin the image as:

`ghcr.io/gnmyt/voxeldash-one:1.2.0@sha256:<multiarch-digest>`

Then run the Umbrel app-store lint checks.
