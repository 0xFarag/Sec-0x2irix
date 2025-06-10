# Docker

**Definition**: Open-source tool for running applications inside containers. **Purpose**: Offers isolated, lightweight environments that consume fewer resources than VMs.

#### Docker Architecture

* **Docker Client**: Sends commands to daemon.
* **Docker Daemon(Server)**: Manages containers, images, volumes, networks, logs, and resources.

#### Key Components

* **Images**: Read-only templates with app + dependencies.
* **Containers**: Running instances of images, isolated and executable.
* **Volumes**: Used for persistent storage.
* **Docker Compose**: Manages multi-container apps via YAML config.
* **Docker Desktop**: GUI version with Kubernetes support.

#### Docker Privilege Escalation Techniques

1. **Shared Directories**:
   * Access to host directories like `.ssh/`
   * Can read sensitive files (e.g., private keys).
2.  **Docker Socket Exploitation**:

    * If `/var/run/docker.sock` is writable, attackers can:
      * Spin up privileged containers
      * Mount host filesystem (`/`) to access full system

    ```bash
    docker -H unix:///var/run/docker.sock run -v /:/mnt --rm -it ubuntu chroot /mnt bash
    ```
3.  **Membership in docker group**:

    * Grants full control over Docker daemon
    * Can run containers with host root access

    ```bash
    id  # check if user is in docker group
    ```
