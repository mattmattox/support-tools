# Rancher v2.x logs-collector

This logs collector project was created to collect logs from Linux Kubernetes nodes. It is designed to be used in the following environments for troubleshooting support cases:
- [RKE2 clusters](https://docs.rke2.io/)
- [RKE1 clusters](https://rancher.com/docs/rke/latest/en/)
- [K3s clusters](https://docs.k3s.io/)
- [Custom clusters](https://docs.ranchermanager.rancher.io/pages-for-subheaders/use-existing-nodes)
- [Infrastructure provider clusters](https://ranchermanager.docs.rancher.com/how-to-guides/new-user-guides/launch-kubernetes-with-rancher/use-new-nodes-in-an-infra-provider)
- [Kubeadm clusters](https://kubernetes.io/docs/reference/setup-tools/kubeadm/)

> Note: This script may not collect all necessary information when run on nodes in a [Hosted Kubernetes Provider](https://ranchermanager.docs.rancher.com/how-to-guides/new-user-guides/kubernetes-clusters-in-rancher-setup/set-up-clusters-from-hosted-kubernetes-providers) cluster.

## Usage

The script needs to be downloaded and run directly on the node, using the `root` user or `sudo`.

Output will be written to `/tmp` as a tar.gz archive named `<hostname>-<date>.tar.gz`, the default output directory can be changed with the `-d` flag.

### Download and run the script
* Save the script as: `rancher2_logs_collector.sh`

  Using `wget`:
    ```bash
    wget --backups https://raw.githubusercontent.com/rancherlabs/support-tools/master/collection/rancher/v2.x/logs-collector/rancher2_logs_collector.sh
    ```
  Using `curl`:
    ```bash
    curl -OLs https://raw.githubusercontent.com/rancherlabs/support-tools/master/collection/rancher/v2.x/logs-collector/rancher2_logs_collector.sh
    ```
 
* Run the script:
  ```bash
  sudo bash rancher2_logs_collector.sh
  ```

### Optional: Download and run the script in one command
  ```bash
  curl -Ls rnch.io/rancher2_logs | sudo bash
  ```
  > Note: This command requires `curl` to be installed, and internet access from the node.

## Flags

```
Rancher 2.x logs-collector
  Usage: rancher2_logs_collector.sh [ -d <directory> -s <days> -e <days> -r <k8s distribution> -p -f ]

  All flags are optional

  -c    Custom data-dir for RKE2 (ex: -c /opt/rke2)
  -d    Output directory for temporary storage and .tar.gz archive (ex: -d /var/tmp)
  -s    Start day of journald and docker log collection, # of days relative to the current day (ex: -s 7)
  -e    End day of journald and docker log collection, # of days relative to the current day (ex: -e 5)
  -S    Start date of journald and docker log collection. (ex: -S 2022-12-05)
  -E    End date of journald and docker log collection. (ex: -E 2022-12-07)
  -r    Override k8s distribution if not automatically detected (rke|k3s|rke2|kubeadm)
  -p    When supplied runs with the default nice/ionice priorities, otherwise use the lowest priorities
  -f    Force log collection if the minimum space isn't available
  -o    Obfuscate IP addresses and hostnames
```

## Scope of collection

Collection includes the following areas, the logs collector is designed to gather necessary diagnostic information while respecting privacy and security concerns. A detailed list is maintained in [collection-details.md](./collection-details.md).

- Related OS logs and configuration:  
  - Network configuration - interfaces, iptables
  - Disk configuration - devices, filesystems, utilization
  - Performance - resource usage, tuning 
  - OS release and logs - versions, messages/syslog
- Related Kubernetes object output, kubectl commands, and pod logs
  - Related CRD objects
  - Output from kubectl for troubleshooting
  - Pod logs from related namespaces

The scope of collection is intentionally limited to avoid sensitive data, use minimal resources and disk space, and focus on the core areas needed for troubleshooting.

IP addresses and hostnames are collected and can assist with troubleshooting, however these can be obfuscated when adding the `-o` flag for the log collection script.

Note, if additional verbosity, debug, or audit logging is enabled for the related Kubernetes and OS components, these logs can be included and may contain sensitive output. 
