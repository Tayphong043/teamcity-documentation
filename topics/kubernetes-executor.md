# Executor Mode: Agentless Kubernetes Integration


<include from="setting-up-teamcity-for-kubernetes.md" element-id="k8s-integration-types"/>


This article explains the native integration approach. To learn about the traditional integration instead, refer to the [](setting-up-teamcity-for-kubernetes.md) topic.

> This feature is currently in experimental stage and may change in future release cycles.
> 
{style="warning"}

## How It Works

1. Create a [Kubernetes connection](configuring-connections.md#Kubernetes) that will allow TeamCity to access your K8s cluster.
2. In project settings, go to the **Cloud Profiles** section and click **Create New Profile**.
3. Click the **Kubernetes** tile under the "Offload your tasks to external agents" section.
4. Set your K8s integration as follows:
    * **Connection** — choose a connection created in step 1.
    * **Server URL** — enter your TeamCity server URL or leave empty to use the URL specified on the server's [Global Settings](configuring-server-url.md) page.
    {instance="tc"}
    * **Server URL** — enter your TeamCity server URL or leave empty to use the default server URL.
    {instance="tcc"}
    * **YAML Config** — choose a required pod configuration. See the [](#YAML+Configuration) section for more information.
    * **Maximum number of builds** — enter the cluster capacity. When this capacity is reached, new builds will remain queued unless currently ongoing builds are finished.
    * **Parameters** — enter the list of [parameters](configuring-build-parameters.md) (in the `name=value` format) that should be present on K8s containers. These parameters will be matched to [explicit agent requirements](agent-requirements.md) of queued builds. As a result, you can specify which builds can be run in your K8s cluster.
        
        For example, add the `k8s=yes` value to this field in order to offload builds with the `equals("k8s", "yes")` agent requirement.
        
        > Parameters written to pods are not accessible to builds running on them. That is, you cannot access these parameters within build processes (for example, by echoing their values) or view them on the [Build Results](build-results-page.md#Parameters+Tab) page or via the `/app/rest/builds/id:<Int32>?fields=properties(property)` REST API requests. 
        > 
        > These parameters are used solely to match executors with configuration requirements.
        > 
        {style="note"}
   
5. In your build configuration settings, specify [agent requirements](agent-requirements.md) and [step containers](container-wrapper.md) if needed.
6. Trigger a new build.
7. The TeamCity K8s executor collects a list of build steps with their parameters, generates a pod definition, and submits it to K8s cluster. Each build step runs in a separate pod, which allows you to specify different [containers](container-wrapper.md) for individual builds.
8. The K8s cluster allocates pods required to run a build and starts it.


## Cluster Permissions

Make sure the TeamCity user is allowed to perform writing operations in the Kubernetes namespace. Your Kubernetes user role must be configured with the following permissions:

* Pods: `get`, `create`, `list`, `delete`.
* Pod templates: `get`, `list`
* Namespaces: `get`, `list` — to allow TeamCity to suggest the namespaces available on your server.

The following sample illustrates all required permissions configured via [Kubernetes RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/):

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: teamcity:executor
rules:
  - apiGroups: [""]
    resources: [namespaces]
    verbs: ["get", "list"]
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "create", "delete"]
  - apiGroups: [""]
    resources: [podtemplates]
    verbs: ["get","list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: teamcity:executor
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: teamcity:executor
subjects:
  # proper RoleBinding subject depends on your Authentication strategy
  # use one of examples below

  # if you use OIDC/Certificate auth strategies
  - kind: User
    name: teamcity

  # if you use Service account
  - kind: ServiceAccount
    name: teamcity
```

## YAML Configuration

The `podtemplates list` [permission](#Cluster+Permissions) enables TeamCity to access the list of [pod templates](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/#pod-templates) stored in your cluster (under the same namespace as specified in the selected [Kubernetes connection](configuring-connections.md#Kubernetes). The retrieved templates are displayed in the **YAML Configuration** drop-down menu.

The sample template below launches pods that have 2GB of memory and run builds inside the latest [TeamCity agent docker image](agent-docker-images.md).

<include from="setting-up-teamcity-for-kubernetes.md" element-id="k8s-template-sample"/>

## Special Notes and Limitations

* Currently, a project can use only one Kubernetes integration. We expect to support multiple executors per project (along with a mechanism to prioritize them) in future release cycles.