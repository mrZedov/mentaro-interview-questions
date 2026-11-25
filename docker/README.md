# Docker interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/docker) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. How does an image differ from a container?

<details>
<summary>Show answer</summary>

An image contains the packaged filesystem layers and runtime defaults. A container runs from that image with its own process environment and writable layer unless configured otherwise.

</details>

### 2. Do Linux containers normally boot their own kernel?

<details>
<summary>Show answer</summary>

No. They isolate processes through facilities provided by the host kernel, such as namespaces and cgroups. Docker Desktop may put Linux containers inside a Linux VM on a non-Linux host.

</details>

### 3. How does publishing a port differ from EXPOSE?

<details>
<summary>Show answer</summary>

EXPOSE documents intended ports; it does not bind a host port by itself. Publishing creates a host-to-container mapping, and the bind address controls the host interfaces that expose it.

</details>

### 4. What persists when a container is removed and recreated?

<details>
<summary>Show answer</summary>

Mounted volumes or bind mounts can persist independently. Data written only to the container's writable layer is removed with that container.

</details>

### 5. How does an image digest differ from a tag?

<details>
<summary>Show answer</summary>

A digest identifies specific content, while a tag is a movable name. Pin a digest when an exact deployment must be reproducible and update the pin intentionally.

</details>

## Applied

### 6. Why copy dependency manifests before copying source code?

<details>
<summary>Show answer</summary>

The dependency install layer can be reused while manifests stay the same. Copying changing application files earlier invalidates that layer on otherwise unrelated edits.

</details>

### 7. How can a multi-stage build reduce image size?

<details>
<summary>Show answer</summary>

Compile in a stage containing the toolchain, then copy only runtime artifacts into a clean final stage. Check that the copied binary still has the libraries it needs.

</details>

### 8. How do ENTRYPOINT and CMD work together in exec form?

<details>
<summary>Show answer</summary>

ENTRYPOINT specifies the executable and CMD can supply its default arguments. Arguments supplied when starting the container replace CMD by default without replacing the entrypoint.

</details>

### 9. When would you use a bind mount instead of a named volume?

<details>
<summary>Show answer</summary>

Use a bind mount when the container needs a specific host path, such as checked-out source during development. A named volume is managed by Docker and avoids coupling the container to a chosen host directory.

</details>

### 10. How should a build receive a short-lived package registry credential?

<details>
<summary>Show answer</summary>

Use a build secret mount for the single step that needs it. Avoid putting the value in ARG, ENV or a layer, and verify that the build step does not copy it into its output.

</details>

### 11. What does a .dockerignore file protect beyond build performance?

<details>
<summary>Show answer</summary>

It excludes matching files from the build context, reducing what is sent to the builder and what a broad COPY can include. It is not a substitute for secret mounts or reviewing final image contents.

</details>

## Advanced scenarios

### 12. A process is healthy inside the container but unreachable through a published port. What do you check?

<details>
<summary>Show answer</summary>

Confirm the mapping uses the actual container port. Check that the process listens on a reachable container interface rather than only on loopback, then inspect host routing and firewall policy.

</details>

### 13. Why can two containers not reach each other through localhost?

<details>
<summary>Show answer</summary>

Each container generally has its own loopback interface. Put them on the intended shared network and connect using the peer's service name and container port.

</details>

### 14. Why does a container fail to shut down gracefully?

<details>
<summary>Show answer</summary>

Check what runs as PID 1 and whether it receives and forwards the stop signal. Use exec form or a wrapper that handles signals, and test that the process drains before forced termination.

</details>

### 15. Why does deleting build files in a later layer not reclaim their image size?

<details>
<summary>Show answer</summary>

Earlier layers still contain the bytes. Avoid copying unnecessary files, remove transient data within the same layer, or keep those files in a separate build stage.

</details>

### 16. An image works on a laptop but fails with an exec format error on a server. Where do you look?

<details>
<summary>Show answer</summary>

Check the image platform and the architecture of copied executables. Build and test for the target platform; a multi-platform manifest does not fix a wrong binary inside one variant.

</details>

### 17. Why can mounting a directory make files from the image seem to disappear?

<details>
<summary>Show answer</summary>

The mount covers the image contents at that path for the lifetime of the container. Check whether the mount point overlaps required files, then mount only the data directory or initialize it explicitly.

</details>

## Official references

- [Docker: Build cache](https://docs.docker.com/build/cache/)
- [Docker: Networking](https://docs.docker.com/engine/network/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
