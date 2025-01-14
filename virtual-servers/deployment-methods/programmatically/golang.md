---
description: >-
  An example implementation in Golang of a kubernetes client to interact with a
  Virtual Server resource on CoreWeave Cloud.
---

# Golang

The provided Golang example illustrates the following:

1. Spinning up a [Virtual Server struct (`VirtualServer`)](https://github.com/coreweave/virtual-server/blob/master/api/v1alpha1/virtualserver\_types.go#L68).
2. Building a Service and PVC to be used as a FloatingIP and Additional Filesystem respectively.
3. Removing an existing Virtual Server.
4. Creating a new Virtual Server. (The instance is started automatically.)
5. Waiting for Virtual Server ready status.
6. Stopping the instance, then waiting until it is completely stopped.
7. Deleting the Virtual Server once it is completely stopped.

**View the example on GitHub:**

{% embed url="https://github.com/coreweave/kubernetes-cloud/tree/master/virtual-server/examples/go" %}

## Build & Run

A `makefile` is provided within the [Golang example](https://github.com/coreweave/kubernetes-cloud/tree/master/virtual-server/examples/go) with the following directives:

| Directive | Description                           |
| --------- | ------------------------------------- |
| `install` | Download the module dependencies.     |
| `run`     | Build and run the example executable. |
| `clean`   | Remove the executable.                |

## Implementation

The go [example](../../../virtual-server/examples/go/main.go) employs the [v1alpha1](https://pkg.go.dev/github.com/coreweave/virtual-server/api/v1alpha1) package of CoreWeave's VirtualServer in order to interface with the Virtual Server resource on CoreWeave Cloud. The Virtual Server struct is prepared using helper functions provided by package [v1alpha1](https://pkg.go.dev/github.com/coreweave/virtual-server/api/v1alpha1). Additionally, a PersistentVolumeClaim and a Service are created. They are then allocated to the Virtual Server as an additional file system and floating IP respectively. Once prepared, a simple [sigs](https://github.com/kubernetes-sigs/controller-runtime) client is used to Create, Get, and Delete the Virtual Server.

### Client

The example application ([main.go](../../../virtual-server/examples/go/main.go)) builds Kubernetes client using the credentials from either a Kubernetes config file or service account mount. Additional information on building a config can be found at [config](https://pkg.go.dev/sigs.k8s.io/controller-runtime/pkg/client/config).

### Persistent Volume Claim

A [PersistentVolumeClaim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) (PVC) is created and assigned to the Virtual Server as an additional file system. The PVC will then be accessible from within the Virtual Server as a mounted file system. The example function `buildPVC` illustrates the creation of a PVC struct.The Kubernetes client is then used to deploy the PVC to the cluster.

### Service

A [Service](https://kubernetes.io/docs/concepts/services-networking/service/) is created and assigned to the Virtual Server as a floating IP service. The service must be `type: LoadBalancer`, and have been assigned a load balancer IP in order to be used as a floating IP service. If the service is valid, the IP of the service will be assigned to the Virtual Server as a floating IP.

{% hint style="info" %}
**Note**

This example also illustrates creating a floating service when the `FLOATING_SERVICE_NAME` environment variable is specified. The function `buildFloatingIPService` illustrates the creation of a Service struct. The Kubernetes client is then used to deploy the Service to the cluster.
{% endhint %}

### Virtual Server

Using utility functions provided by [v1alpha1](https://pkg.go.dev/github.com/coreweave/virtual-server/api/v1alpha1), the application creates a VirtualServer struct, and then deploys the Virtual Server to the cluster using the Kubernetes client.

{% hint style="info" %}
**Note**

For long-running, fine-grained and event-driven control of Virtual Servers, a [sigs controller](https://pkg.go.dev/sigs.k8s.io/controller-runtime/pkg/builder) can be implemented for Virtual Servers.
{% endhint %}
