# Manage Refreshable Access Tokens

In TeamCity, there are multiple entities whose functionality requires accessing VCS hosting providers: [VCS roots](configuring-vcs-roots.md), [Commit Status Publishers](commit-status-publisher.md), [Pull Requests features](pull-requests.md), and so on. These entities typically offer multiple VCS authentication options:

* Regular username/login credentials — being the most vulnerable, this authentication method is gradually deprecated by major VCS hosting providers.
* Personal access tokens (PATs) — issued on the VCS provider's side and then copied into a TeamCity object. These tokens grant the same or fewer permissions as those of the issuing user.
* Refreshable tokens — personal or non-personal tokens. To acquire this token, TeamCity communicates with a VCS provider using an existing OAuth (or OAuth-like) [connection](configuring-connections.md).

This topic explains how to issue, assign, and manage refreshable tokens.

## Supported Connections

To issue a token, TeamCity communicates with a VCS hosting provider using a connection configured in project [Connections](configuring-connections.md) settings. Currently, refreshable access tokens are supported for the following connections:

* [GitHub App](configuring-connections.md#github-app) (both regular and manifest-based connections)
* [GitLab.com and GitLab CE/EE](configuring-connections.md#GitLab)
* [](configuring-connections.md#Bitbucket+Cloud)
* [](configuring-connections.md#Bitbucket+Server+and+Data+Center)
* [Azure DevOps OAuth 2.0](configuring-connections.md#Connecting+to+Azure+DevOps)
* [JetBrains Space](configuring-connections.md#connect-to-jetbrains-space)


## Token Management

The **VCS Auth Tokens** page provides centralized access to all refreshable tokens issued for this project and its subproject. To open this page:

1. <include from="common-templates.md" element-id="open-project-settings"/> To view all tokens used on this server, open the Root project settings.
2. <include from="common-templates.md" element-id="open-project-settings-tab"><var name="tab-name" value="VCS Auth Tokens"/></include>

<img src="dk-vcs-auth-tokens-overview.png" width="706" alt="VCS Auth Tokens Overview"/>

This page allows you to find tokens by their names, related connections, and users who issued them. Without any filters applied, the page shows 10 most recently tokens.

The **Show inherited tokens** checkbox allows you to view tokens issued in parent projects but available in the currently viewed project.

To issue a new token, click the **Generate new token**. In a dialog that pops up, specify the token name and a TeamCity connection.

<img src="dk-generate-token-dialog.png" width="464" alt="Generate new token"/>

GitHub App connections support granular per-repository token access. To generate such token, enter repository names without user/organization names (`myRepo` instead of `myUser/myRepo`). Note that repositories must be hosted under the same account to which a corresponding GitHub App was installed.

Project **VCS Auth Tokens** pages display only the tokens available for each project. If you create a new token for project `A` and restrict its scope to subprojects `A.2` and `A.3`, TeamCity will notify you that the current project will not have access to this token.
{id="token-project-scopes"}

## How to Create and Assign Refreshable Tokens

You do not need to manually set up refreshable tokens for each object that requires access to VCS providers. For example, when you create a new project or configuration from an existing connection (by clicking a **From &lt;Connection_name&gt;** tile on the New Project/Configuration page), TeamCity will automatically generate a token and pass it to the related VCS root.

<img src="dk-githubchecks-from-connection.png" width="706" alt="Create a project from connection"/>

Similarly, when setting up build features like [](commit-status-publisher.md), you may opt for employing default VCS root authorization method instead of issuing new tokens.

<img src="dk-publisher-share-root-creds.png" width="706" alt="Share root credentials"/>

To assign a token manually, navigate to authentication settings of a build feature or a root. Here you can generate a new or use an existing token.

<procedure title="Generate new token" type="steps">
<step>
Click the <b>Generate new</b> button.
<img src="dk-generate-new-token.png" width="706" alt="Generate new token"/>
</step>
<step>
Enter the token name and choose a TeamCity connection that should be used to communicate with the VCS provider. The <a href="#token-project-scopes">project scope</a> will be automatically limited to this project and its subprojects. 
</step>
<step>Click <b>Save</b> to exit the setup.</step>
</procedure>

<procedure title="Choose an existing token" type="steps">
<step>
Click a pencil icon to enable the **Token** text box.
<img src="dk-edit-ref-token.png" width="706" alt="Edit button"/>
</step>
<step>
Click the <b>git token list</b> link to open the <a href="#Token+Management">VCS Auth Tokens</a> page in a new tab.
</step>
<step>
On the <b>VCS Auth Tokens</b> page, locate a token you wish to use and click the <b>Copy ID</b> button.
</step>
<step>Return back to the edited object and paste the token ID.</step>
<step>Click <b>Save</b> to exit the setup.</step>
</procedure>