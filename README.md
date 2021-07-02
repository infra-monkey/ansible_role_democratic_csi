# Overview
This role will deploy democratic-csi to your kubernetes cluster using helm.
The role currently supports deployment of only one configuration for :
- freenas-iscsi
- freenas-nfs

Node-manual will not be covered in this role.

# Requirements
The remote user must have the following python libraries installed:
- openshift

The following binairies must be available and executable:
- kubectl
- helm3

A kubeconfig file with permissions allowing to install the helm chart and the resources must be readable for the remote user.

The kubernetes nodes need to be prepared with the dependencies required by democratic-csi as explained [here](https://github.com/democratic-csi/democratic-csi#node-prep)

# Variables

TODO !
| Name  | Type | Required | Default Value | Description |
| ----- | ---- | -------- | ------------- | ----------- |
| var | type | no | n.a. | description |

# Help wanted

I can test only on truenas storage. For zol target and nfs generic, any help in testing or developing is welcome.

I would also welcome help in automating the test for the helm part...
