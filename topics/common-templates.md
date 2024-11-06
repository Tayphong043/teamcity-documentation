# Darwin Information Typing Architecture

## Project and Configuration Settings

<snippet id="open-project-settings">Open project settings. To do so, navigate to the required project and click the <b>Edit</b> button in the top right corner.</snippet>


## Project Features

<snippet id="create-new-connection">Navigate to the **Connections** group and click **Add Connection**. Note that connections can be used only in their parent projects and their subprojects. If you want a connection to be available globally, add it to the **Root** project.</snippet>

<snippet id="test-and-save-connection">Click <b>Test connection</b> to verify TeamCity can access your resources, and save your new conneciton.</snippet>


## K8S

<snippet id="kubernetes-settings-api-server-url">Specify the URL of the <a href="https://kubernetes.io/docs/concepts/overview/components/#kube-apiserver">Kubernetes API server</a>.</snippet>

<snippet id="kubernetes-settings-certificate-authority">Enter the content of the <a href="https://kubernetes.io/docs/concepts/cluster-administration/certificates/">CA certificate</a> for your cluster.</snippet>


<snippet id="kubernetes-settings-namespace">Specify a required <a href="https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/">Kubernetes namespace</a>. Leave empty to use the <a href="https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/#viewing-namespaces">default namespace</a>.</snippet>

<snippet id="kubernetes-settings-auth-strategy">

Select the required authentication strategy. Depending on the selected strategy, the set of additional options will vary. Refer to the <a href="https://kubernetes.io/docs/reference/access-authn-authz/authentication/#authentication-strategies">Kubernetes documentation</a> for details on available options.

<note>The <b>Token</b> strategy accepts any token types supported by Kubernetes.</note>

</snippet>