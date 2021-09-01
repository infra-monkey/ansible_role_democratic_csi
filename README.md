# Overview
This role will deploy democratic-csi to your kubernetes cluster using helm.
The role currently supports deployment of only one configuration for :
- freenas-iscsi
- freenas-nfs
- freenas-smb

# Requirements
The remote user must have the following python libraries installed:
- openshift

The following binairies must be available and executable:
- kubectl
- helm3

A kubeconfig file with permissions allowing to install the helm chart and the resources must be readable for the remote user.

The kubernetes nodes need to be prepared with the dependencies required by democratic-csi as explained [here](https://github.com/democratic-csi/democratic-csi#node-prep)

# Variables

## Default Variables

In the case of variables shared accross several releases like same freenas server for different releases (different protocols or pools for example) it is interesting to define only once those variables.
The list of default variables and values are defined in [defaults](defaults/main.yml)

## Inventory structure

```yaml
democratic_csi_api_protocol: https
democratic_csi_api_host: "192.168.1.2"
democratic_csi_api_port: "443"
democratic_csi_api_key: "long-random-api-key"
democratic_csi:
  release:
    - name: "fast-storage"
      driver: "freenas-nfs"
      nfs_host: "192.168.1.2"   
      zfs_datasetParentName: "/tank/k8s/nfs-fast
```

## Variables common to all releases

| Name  | Type | Required | Default Value | Description |
| ----- | ---- | -------- | ------------- | ----------- |
| name | string | yes | n.a. | The name of the helm release and also the name of the storage class. |
| driver | string | yes | n.a. | The csi driver to deploy. One of `freenas-nfs`, `freenas-smb`, `freenas-iscsi` |
| namespace | string | no | `democratic_csi_release_namespace` | Namespace where to deploy this release |
| api_protocol | string | no | `democratic_csi_api_protocol` | Protocol to use for api requests. Authorized values are `http` or `https`. Used only for the freenas drivers . |
| api_host | string | no | `democratic_csi_api_host` | Hostname for api requests. Used only for the freenas drivers with TrueNAS > 12.0-U3. |
| api_port | string | no | `democratic_csi_api_port` | Port for api requests. Used only for the freenas drivers with TrueNAS > 12.0-U3. |
| api_key | string | no | `democratic_csi_api_key` | Api key to use for api requests. Used only for the freenas drivers with TrueNAS > 12.0-U3. |
| api_allowInsecure | boolean | no | `democratic_csi_api_allowInsecure` | Allow insecure api requests with TrueNAS > 12.0-U3. |
| ssh_host | string | no | `democratic_csi_ssh_host` | Ssh remote host used for connecting to the NAS. |
| ssh_port | string | no | `democratic_csi_ssh_port` | Ssh remote port used for connecting to the NAS. |
| ssh_username | string | no | `democratic_csi_ssh_username` | Ssh remote user used for connecting to the NAS. |
| ssh_password | string | yes | n.a. | Ssh password used for connecting to the NAS. Mutually exclusive with `ssh_privatekey`. |
| ssh_privatekey | string | yes | n.a. | Ssh private key used for connecting to the NAS. Mutually exclusive with `ssh_password`. |
| zfs_sudoEnabled | boolean | no | `democratic_csi_zfs_sudoEnabled` | Enables the use of sudo for the commands. The ssh remote user must have sudo permissions. |
| zfs_path_zfs | string | no | `democratic_csi_zfs_path_zfs` | Path to the zfs command. |
| zfs_path_zpool | string | no | `democratic_csi_zfs_path_zpool` | Path to the zpool command. |
| zfs_path_sudo | string | no | `democratic_csi_zfs_path_sudo` | Path to the sudo command. |
| zfs_path_chroot | string | no | `democratic_csi_zfs_path_chroot` | Path to the chroot command. |

## Variables specific to nfs drivers

| Name  | Type | Required | Default Value | Description |
| ----- | ---- | -------- | ------------- | ----------- |
| sc_mountOptions | list | no | `democratic_csi_nfs_sc_mountOptions` | Mount option of the storage class. |
| sc_reclaimPolicy | string | yes | `democratic_csi_sc_reclaimPolicy` | Reclaim policy of the storage class. |
| sc_volumeBindingMode | string | no | `democratic_csi_sc_volumeBindingMode` | Binding mode of the storage class. |
| sc_allowVolumeExpansion | boolean | no | `democratic_csi_sc_allowVolumeExpansion` | Allow the storage class to extend the volume. |
| volumeSnapshotClasses | list | no | `democratic_csi_nfs_volumeSnapshotClasses` | Storage class for snapshots. |
| zfs_datasetParentName | string | no | `democratic_csi_zfs_datasetParentName` | Zfs parent dataset where to create the share.  |
| zfs_detachedSnapshotsDatasetParentName | string | no | `democratic_csi_zfs_detachedSnapshotsDatasetParentName` | |
| zfs_datasetEnableQuotas | boolean | no | `democratic_csi_zfs_datasetEnableQuotas` | Enable zfs quota on the shared dataset. |
| zfs_datasetEnableReservation | boolean | no | `democratic_csi_zfs_datasetEnableReservation` | Enable zfs reservation on the shared dataset. |
| zfs_datasetPermissionsMode | string | no | `democratic_csi_nfs_zfs_datasetPermissionsMode` |  |
| zfs_datasetPermissionsUser | string | no | `democratic_csi_nfs_zfs_datasetPermissionsUser` |  |
| zfs_datasetPermissionsGroup | string | no | `democratic_csi_nfs_zfs_datasetPermissionsGroup` |  |
| host | string | no | `democratic_csi_nfs_host` | IP or hostname of the nfs host to reach. |
| shareAlldirs | boolean | no | `democratic_csi_nfs_shareAlldirs` |  |
| shareAllowedHosts | list | no | `democratic_csi_nfs_shareAllowedHosts` |  |
| shareAllowedNetworks | list | no | `democratic_csi_nfs_shareAllowedNetworks` |  |
| shareMaprootUser | string | no | `democratic_csi_nfs_shareMaprootUser` |  |
| shareMaprootGroup | string | no | `democratic_csi_nfs_shareMaprootGroup` |  |
| shareMapallUser | string | no | `democratic_csi_nfs_shareMapallUser` |  |
| shareMapallGroup | string | no | `democratic_csi_nfs_shareMapallGroup` |  |

## Variables specific to iscsi drivers

| Name  | Type | Required | Default Value | Description |
| ----- | ---- | -------- | ------------- | ----------- |
| sc_mountOptions | list | no | `democratic_csi_nfs_sc_mountOptions` | Mount option of the storage class. |
| sc_reclaimPolicy | string | yes | `democratic_csi_sc_reclaimPolicy` | Reclaim policy of the storage class. |
| sc_volumeBindingMode | string | no | `democratic_csi_sc_volumeBindingMode` | Binding mode of the storage class. |
| sc_allowVolumeExpansion | boolean | no | `democratic_csi_sc_allowVolumeExpansion` | Allow the storage class to extend the volume. |
| sc_fsType | string | no | `democratic_csi_iscsi_sc_fsType` | Filesystem type to create by the storage class. |
| sc_detachedVolumesFromSnapshots | string | no | `democratic_csi_iscsi_sc_detachedVolumesFromSnapshots` |  |
| sc_detachedVolumesFromVolumes | string | no | `democratic_csi_iscsi_sc_detachedVolumesFromVolumes` |  |
| volumeSnapshotClasses | list | no | `democratic_csi_iscsi_volumeSnapshotClasses` | Storage class to use for snapshots. |
| zfs_datasetParentName | string | no | `democratic_csi_zfs_datasetParentName` | Zfs parent dataset where to create the share. |
| zfs_detachedSnapshotsDatasetParentName | string | no | `democratic_csi_zfs_detachedSnapshotsDatasetParentName` |  |
| zvolCompression | string | no | `democratic_csi_iscsi_zvolCompression` | Enable zfs compression on the create zvol. |
| zvolDedup | string | no | `democratic_csi_iscsi_zvolDedup` | Enable zfs deduplication on the create zvol. |
| zvolEnableReservation | boolean | no | `democratic_csi_zfs_datasetEnableReservation` | Enable reservation on the create zvol. |
| zvolBlockSize | string | no | `democratic_csi_iscsi_zvolBlockSize` | Block size of the zvol. |
| portal | string | no | `democratic_csi_iscsi_portal` | ip:port designation of the iscsi portal to connect to. |
| portals | list | no | `democratic_csi_iscsi_portals` | List of portals in case of distributed portals. |
| portal_namePrefix | string | no | `democratic_csi_iscsi_portal_namePrefix` | Prefix of the zvol name. |
| portal_nameSuffix | string | no | `democratic_csi_iscsi_portal_nameSuffix` | Suffix of the zvol name. |
| portal_PortalGroup | string | no | `democratic_csi_iscsi_portal_PortalGroup` |  |
| portal_InitiatorGroup | string | no | `democratic_csi_iscsi_portal_InitiatorGroup` |  |
| portal_GroupAuthType | string | no | `democratic_csi_iscsi_portal_GroupAuthType` |  |
| portal_GroupAuthGroup | string | no | `democratic_csi_iscsi_portal_GroupAuthGroup` |  |
| extentInsecureTpc | boolean | no | `democratic_csi_iscsi_extentInsecureTpc` |  |
| extentXenCompat | boolean | no | `democratic_csi_iscsi_extentXenCompat` | Enable Xen compatibility. |
| extentDisablePhysicalBlocksize | string | no | `democratic_csi_iscsi_extentDisablePhysicalBlocksize` |  |
| extentRpm | string | no | `democratic_csi_iscsi_extentRpm` | Rotation speed of the drives on the zfs storage. |
| extentAvailThreshold | string | no | `democratic_csi_iscsi_extentAvailThreshold` |  |
| interface | string | no | `democratic_csi_iscsi_interface` |  |

## Variables specific to samba drivers

| Name  | Type | Required | Default Value | Description |
| ----- | ---- | -------- | ------------- | ----------- |
| sc_mountOptions | list | no | `democratic_csi_smb_sc_mountOptions` | Mount option of the storage class. |
| sc_reclaimPolicy | string | yes | `democratic_csi_sc_reclaimPolicy` | Reclaim policy of the storage class. |
| sc_volumeBindingMode | string | no | `democratic_csi_sc_volumeBindingMode` | Binding mode of the storage class. |
| sc_allowVolumeExpansion | boolean | no | `democratic_csi_sc_allowVolumeExpansion` | Allow the storage class to extend the volume. |
| volumeSnapshotClasses | list | no | `democratic_csi_smb_volumeSnapshotClasses` | Storage class for snapshots. |
| zfs_datasetParentName | string | no | `democratic_csi_zfs_datasetParentName` | Zfs parent dataset where to create the share. |
| zfs_detachedSnapshotsDatasetParentName | string | no | `democratic_csi_zfs_detachedSnapshotsDatasetParentName` |  |
| zfs_datasetEnableQuotas | boolean | no | `democratic_csi_zfs_datasetEnableQuotas` | Enable zfs quota on the shared dataset. |
| zfs_datasetEnableReservation | boolean | no | `democratic_csi_zfs_datasetEnableReservation` | Enable zfs reservation on the shared dataset. |
| zfs_datasetPermissionsMode | string | no | `democratic_csi_smb_zfs_datasetPermissionsMode` |  |
| zfs_datasetPermissionsUser | string | no | `democratic_csi_smb_zfs_datasetPermissionsUser` |  |
| zfs_datasetPermissionsGroup | string | no | `democratic_csi_smb_zfs_datasetPermissionsGroup` |  |
| zfs_datasetPermissionsAcls | list | no | `democratic_csi_smb_zfs_datasetPermissionsAcls` |  |
| host | string | no | `democratic_csi_smb_host` | IP or hostname of the samba host to reach. |
| shareAllowedHosts | list | no | `democratic_csi_smb_shareAllowedHosts` | List of hosts allow to mount the share. |
| shareDeniedHosts | list | no | `democratic_csi_smb_shareDeniedHosts` | List of hosts denied to mount the share. |
| nameTemplate | string | no | `democratic_csi_smb_nameTemplate` | Template of the zfs dataset name. |
| namePrefix | string | no | `democratic_csi_smb_namePrefix` | Prefix of the zfs dataset name. |
| nameSuffix | string | no | `democratic_csi_smb_nameSuffix` | Suffix of the zfs dataset name. |


# Help welcome

I can test only on truenas storage. For zol target and nfs generic, any help in testing is welcome.
Any suggestion on the best defaults are welcome.

I would also welcome help in automating the test for the helm part...
