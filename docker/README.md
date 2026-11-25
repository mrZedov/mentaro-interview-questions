# Docker interview questions

[Explore this topic on Mentaro](https://www.mentaro.app/interview-questions/docker) · [Start interactive practice on Mentaro](https://www.mentaro.app/login?mode=signup&redirect=/onboarding)

## Foundations

### 1. How does an image differ from a container?

<details>
<summary>Show answer</summary>

An image contains the packaged filesystem layers and runtime defaults. A container runs from that image with its own process environment and writable layer unless configured otherwise.

**Explanation:** Two containers started from the same image can have different environment variables and writable files. Replacing an image tag does not migrate an already running container or preserve changes in its writable layer.

</details>

### 2. Do Linux containers normally boot their own kernel?

<details>
<summary>Show answer</summary>

No. They isolate processes through facilities provided by the host kernel, such as namespaces and cgroups. Docker Desktop may put Linux containers inside a Linux VM on a non-Linux host.

**Explanation:** A container can have a private process namespace even though its system calls execute on the shared host kernel. On a desktop VM, the relevant kernel is the VM’s Linux kernel, not the host operating system kernel.

</details>

### 3. How does publishing a port differ from EXPOSE?

<details>
<summary>Show answer</summary>

EXPOSE documents intended ports; it does not bind a host port by itself. Publishing creates a host-to-container mapping, and the bind address controls the host interfaces that expose it.

**Explanation:** EXPOSE does not start a listener or make it reachable. For a local-only service, bind the published port to loopback; the application also needs to listen on an interface inside the container that the forwarding path can reach.

**Example:**

```bash
docker run --rm -p 127.0.0.1:8080:80 example-web
```

</details>

### 4. What persists when a container is removed and recreated?

<details>
<summary>Show answer</summary>

Mounted volumes or bind mounts can persist independently. Data written only to the container's writable layer is removed with that container.

**Explanation:** Recreating an application container leaves a named database volume in place if you reattach it. That is persistence, not a backup: deleting the volume or losing the host can still destroy the data.

</details>

### 5. How does an image digest differ from a tag?

<details>
<summary>Show answer</summary>

A digest identifies specific content, while a tag is a movable name. Pin a digest when an exact deployment must be reproducible and update the pin intentionally.

**Explanation:** A tag can be repointed at new bytes without changing the deployment file. A digest pin makes updates explicit; maintain a process to review and replace the pin rather than leaving a known-outdated dependency indefinitely.

</details>

## Applied

### 6. Why copy dependency manifests before copying source code?

<details>
<summary>Show answer</summary>

The dependency install layer can be reused while manifests stay the same. Copying changing application files earlier invalidates that layer on otherwise unrelated edits.

**Explanation:** The dependency step can stay cached after an edit to an application file only if its inputs have not changed. Copy the lockfile along with the manifest so changing resolved versions invalidates that layer.

</details>

### 7. How can a multi-stage build reduce image size?

<details>
<summary>Show answer</summary>

Compile in a stage containing the toolchain, then copy only runtime artifacts into a clean final stage. Check that the copied binary still has the libraries it needs.

**Explanation:** Compile with a toolchain in one stage and copy the runnable artifact into a fresh stage. Confirm its architecture, dynamic libraries and runtime files match the final stage; a small image that cannot start is not a useful optimization.

</details>

### 8. How do ENTRYPOINT and CMD work together in exec form?

<details>
<summary>Show answer</summary>

ENTRYPOINT specifies the executable and CMD can supply its default arguments. Arguments supplied when starting the container replace CMD by default without replacing the entrypoint.

**Explanation:** Treat ENTRYPOINT as the command and CMD as its default arguments. Runtime arguments replace the defaults as a group rather than appending to them. Exec form also avoids inserting a shell in front of the executable.

**Example:**

```dockerfile
ENTRYPOINT ["node", "cli.js"]
CMD ["--help"]
```

For an image named cli, docker run cli --version runs node cli.js --version instead of node cli.js --help.

</details>

### 9. When would you use a bind mount instead of a named volume?

<details>
<summary>Show answer</summary>

Use a bind mount when the container needs a specific host path, such as checked-out source during development. A named volume is managed by Docker and avoids coupling the container to a chosen host directory.

**Explanation:** A bind mount lets a developer edit files on the host and see them immediately in the container. A named volume fits data that the deployment should manage independently of the developer’s working-directory layout.

</details>

### 10. How should a build receive a short-lived package registry credential?

<details>
<summary>Show answer</summary>

Use a build secret mount for the single step that needs it. Avoid putting the value in ARG, ENV or a layer, and verify that the build step does not copy it into its output.

**Explanation:** Supply the registry credential only in the build step that needs it. Do not echo it to output or copy the mounted file into an artifact: a temporary mount cannot prevent a build command from persisting its contents.

</details>

### 11. What does a .dockerignore file protect beyond build performance?

<details>
<summary>Show answer</summary>

It excludes matching files from the build context, reducing what is sent to the builder and what a broad COPY can include. It is not a substitute for secret mounts or reviewing final image contents.

**Explanation:** Exclude directories such as local dependencies or build outputs that should not enter the build. Check patterns against the actual build context; a file ignored in Git is not automatically excluded from the Docker context.

</details>

## Advanced scenarios

### 12. A process is healthy inside the container but unreachable through a published port. What do you check?

<details>
<summary>Show answer</summary>

Confirm the mapping uses the actual container port. Check that the process listens on a reachable container interface rather than only on loopback, then inspect host routing and firewall policy.

**Explanation:** Separate failures inside and outside the container. Verify the listening address and container port first, then test the host binding. A server bound to 127.0.0.1 inside the container can pass a local probe while remaining unreachable through a forwarded port.

</details>

### 13. Why can two containers not reach each other through localhost?

<details>
<summary>Show answer</summary>

Each container generally has its own loopback interface. Put them on the intended shared network and connect using the peer's service name and container port.

**Explanation:** Peer names resolve on an appropriate shared network, but localhost still names the caller itself. Use the target’s internal service port for container-to-container traffic rather than routing through an unnecessarily published host port.

</details>

### 14. Why does a container fail to shut down gracefully?

<details>
<summary>Show answer</summary>

Check what runs as PID 1 and whether it receives and forwards the stop signal. Use exec form or a wrapper that handles signals, and test that the process drains before forced termination.

**Explanation:** Check whether the main process handles SIGTERM or whether a shell wrapper absorbs it. Prefer exec form or make a necessary wrapper forward signals and wait. Run a stop test with enough time for an active request to drain.

</details>

### 15. Why does deleting build files in a later layer not reclaim their image size?

<details>
<summary>Show answer</summary>

Earlier layers still contain the bytes. Avoid copying unnecessary files, remove transient data within the same layer, or keep those files in a separate build stage.

**Explanation:** Layer history is additive: a later deletion hides an earlier file without erasing the bytes in the earlier layer. Keep temporary files within one build step or omit the build stage from the runtime image.

</details>

### 16. An image works on a laptop but fails with an exec format error on a server. Where do you look?

<details>
<summary>Show answer</summary>

Check the image platform and the architecture of copied executables. Build and test for the target platform; a multi-platform manifest does not fix a wrong binary inside one variant.

**Explanation:** Check the architecture of both the image variant selected by the runtime and the executable placed inside it. A build can accidentally include a binary compiled for the builder even if the published image manifest says otherwise.

</details>

### 17. Why can mounting a directory make files from the image seem to disappear?

<details>
<summary>Show answer</summary>

The mount covers the image contents at that path for the lifetime of the container. Check whether the mount point overlaps required files, then mount only the data directory or initialize it explicitly.

**Explanation:** If an empty bind-mounted host directory covers a populated application path, the process sees the empty directory. Inspect mounts before rebuilding the image, and choose a dedicated data path so it does not cover runtime dependencies.

</details>

## Official references

- [Docker: Build cache](https://docs.docker.com/build/cache/)
- [Docker: Networking](https://docs.docker.com/engine/network/)

Original questions and answers by [Mentaro](https://www.mentaro.app/), licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
