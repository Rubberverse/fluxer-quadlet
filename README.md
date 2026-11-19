fyi: still not instantly usable out of the box as whole .env is missing and I have not finished making it more generic in terms of configuration.

# README

This is my new improved attempt at spinning up a reliable, easy to maintain Quadlet configuration for [Fluxer]().
It's based on my old stack that's visible on [my Homelab repository](https://github.com/MrRubberDucky/Homelab/2-WAN/Fluxer) but it irons out some big issues that it had and instead opts for easier management at the cost of network and some user isolation between containers. It makes use of pod functionality of Podman which is very similar to Kubernetes pods.

You can read more about Podman pod functionality in [this redhat blog](https://developers.redhat.com/blog/2019/01/15/podman-managing-containers-pods)

This will be accompanied by a guide that will explain more which will be hosted on my main site in docs section - [Rubberverse Docs](https://rubberverse.xyz/docs)

## Requirements

Podman is a 'rolling-release' software, it's not meant to be stuck on a 'stable' version however some maintainers may choose to pin and implement security patches themselves for the version of their own choice. You'll need to manually build Podman from source, use Alvistack OBS repository on Debian, or move to Fedora Server for full support. It's not my own whimsy doing requirements like this as I use a lot of features that are just not supported in older versions of `quadlet-systemd-generator` which is bundled with every Podman release.

May need to extend user namespaces range.

- systemd (any)
- Unprivileged user (no access to `sudo` etc.)
- Podman v5.8.2 and above minimum with no support from me, Podman v6 and above recommended and fully supported
- 8GB of RAM and 60GB+ of storage

## Difference between this and official deployment

Tools such as `rexctl` and `podman-compose` are incompatible with this deployment. It is purely a Quadlet-centric deployment that utilizes power of both systemd and Podman to give you a comfy experience - container logs appear in journald, containers can be managed by using systemctl while all Podman functionality is retained.

**This however requires a modern version of Podman m.in. v5.8.2 and above.** Older versions don't support custom directory structures that are done here and some keys so `quadlet-systemd-generator` will fail while generating the services.

- `public.ecr.aws` docker.io mirrors are used since they have public 100GB/month of pulls limit and are open to everyone
- Quadlet instead of `podman-compose`
- NATS uses scratch image instead of alpine image
- SeaweedFS is tweaked for single-node operation by default and uses [Aikido](https://www.aikido.dev/) hardened image (mostly as they're the only ones mirroring to ECR AWS)
- Different healthcheck endpoint for Meilisearch
- Some memory limits are tighter and have specific extras set that may or may not impact performance (yolo)
- Core containers (Postgres, Valkey, Seaweedfs, Meilisearch) run as non-root user inside container and so does NATS
- Internal network is used for containers that don't need internet access (all of CORE containers)
- Bind mounts to host directories are used to make it easier to backup things (and not accidentally wipe them by resetting podman if you ever run into a problem needing such an option)
- Containers always update to latest version, no need for watchtower, rexctl or other tools to do that for you

It's customized to my own liking and needs pretty much.

## Did you even test this?

It's what runs Mallardling so I guess I did. Right now even. Tomorrow too. In a wee-the answer will still be the same. If it does not work for you then make an issue.
