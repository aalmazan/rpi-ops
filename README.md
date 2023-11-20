# rpi-ops

My personal Ansible playbooks and other devops-related things for setting up/maintaining Raspberry
Pi machines, specifically to run [K3S](https://k3s.io/).

For other details on the hardware used take a look
at [the article here](https://blog.aalmazan.com/posts/2023-11-kubernetes-rpi-pt-1.html#raspberry-pi-setup).

## Initial setup (on each RPi node)

These are just quick instructions to get started. More details will be added if there's interest,
but these are mainly for my personal use and to keep track of changes.

1. [Raspberry Pi Imager](https://www.raspberrypi.com/documentation/computers/getting-started.html#installing-the-operating-system)
2. After flashing add file:
    * `touch /boot/ssh` (empty file)
3. Edit `/boot/config.txt`:
    * `echo "arm_64bit=1" >> /boot/config.txt`
4. iptables config for Kubernetes?

```
   sudo iptables -F
   sudo update-alternatives --set iptables /usr/sbin/iptables-legacy
   sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy
   sudo reboot
```

5. Add control groups to `/boot/cmdline.txt`
    * More info: https://devicetests.com/enabling-memory-cgroup-ubuntu-kubernetes
    * `cgroup_memory=1 cgroup_enable=memory cgroup_enable=cpuset`

## Ansible

1. Install via `apt install ansible`
2. Install `sshpass` if you want to use ssh passwords with ansible
    * `sudo apt install sshpass`

### Running Ansible

```bash
ansible-playbook -i ansible/inventory ansible/site.yml --user=pi
```

## After nodes are up and running with K3S

* To have remote access via `kubectl` to the cluster, copy `/etc/rancher/k3s/k3s.yaml` to your
  local `~/.kube/config`,
    * Or set your terminal `KUBECONFIG` envvar to point to a local copy.
    * Note that you will also need to edit `k3s.yaml` file to point to the master node's IP.