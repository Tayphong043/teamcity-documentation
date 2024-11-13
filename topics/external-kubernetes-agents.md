# External Kubernetes Agents

TeamCity allows you to offload building tasks to a Kubernetes (K8s) cluster.

Key benefits this integration brings to the table include:

* Pods can run [TeamCity agent Docker images](agent-docker-images.md), which eliminates the need to periodically update your build agents.
* K8s acts as an orchestrator that starts and winds down pods, and assigns queued builds to them. This means you also no longer need to control agents lifecycle.
* Each build step runs in a separate pod, which allows you to specify different [containers](container-wrapper.md) for individual builds.


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
7. The TeamCity K8s executor collects a list of build steps with their parameters, generates a pod definition, and submits it to K8s cluster.
8. The K8s cluster allocates pods required to run a build and starts it.


## YAML Configuration

???

## Special Notes and Limitations

* Currently, a project can use only one Kubernetes integration. We expect to support multiple executors per project (along with a mechanism to prioritize them) in future release cycles.
