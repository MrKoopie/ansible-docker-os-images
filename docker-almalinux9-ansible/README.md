# AlmaLinux 9 Ansible Test Image

AlmaLinux 9 Docker container for Ansible playbook and role testing.

## Available Versions

This Dockerfile supports building multiple Ansible versions using build arguments:

  - **Latest Ansible**: Default build (no build args needed)
  - **Ansible core 2.16**: Specify version with `--build-arg ANSIBLE_VERSION="ansible-core~=2.16.0"`
  - **Any version**: Use any pip-compatible version string

The images are lightweight and designed for basic validation of Ansible playbooks.

## How to Build

Build the image locally with your preferred Ansible version:

  1. [Install Docker](https://docs.docker.com/engine/installation/).
  2. `cd` into this directory.
  3. Build your preferred version:
     - **Latest Ansible**: `docker build -t almalinux9-ansible:latest .`
     - **Ansible core 2.16**: `docker build --build-arg ANSIBLE_VERSION="ansible-core~=2.16.0" -t almalinux9-ansible:core-2.16 .`
     - **Specific version**: `docker build --build-arg ANSIBLE_VERSION="ansible-core==2.15.5" -t almalinux9-ansible:2.15.5 .`

## How to Use

  1. [Install Docker](https://docs.docker.com/engine/installation/).
  2. Run a container from the image you built (use your chosen tag):
     ```bash
     docker run --detach --privileged \
       --volume=/sys/fs/cgroup:/sys/fs/cgroup:rw \
       --cgroupns=host \
       almalinux9-ansible:latest
     ```

     To test Ansible roles, add a volume mount: `--volume=$(pwd):/etc/ansible/roles/role_under_test:ro`

  3. Use Ansible inside the container:
     ```bash
     # Check version
     docker exec --tty [container_id] env TERM=xterm ansible --version

     # Syntax check a playbook
     docker exec --tty [container_id] env TERM=xterm ansible-playbook /path/to/ansible/playbook.yml --syntax-check
     ```

## Author

Maintained by Daniel Koop.

Based on work by [Jeff Geerling](https://www.jeffgeerling.com/), author of [Ansible for DevOps](https://www.ansiblefordevops.com/).
