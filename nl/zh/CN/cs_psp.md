---

copyright:
  years: 2014, 2019
lastupdated: "2019-03-21"

keywords: kubernetes, iks 

subcollection: containers

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:download: .download}

# 配置 pod 安全策略
{: #psp}

通过 [pod 安全策略 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)，可以配置策略以授权谁可以在 {{site.data.keyword.containerlong}} 中创建和更新 pod。

**为什么要设置 pod 安全策略？**</br>
作为集群管理员，您希望控制集群中发生的情况，尤其是影响集群安全性或就绪性的操作。pod 安全策略可以帮助控制有特权容器、根名称空间、主机联网和端口、卷类型、主机文件系统、Linux 许可权（例如，只读或组标识）等的使用。

通过 `PodSecurityPolicy` 许可控制器，在[授权策略](#customize_psp)之后，才能创建 pod。设置 pod 安全策略可能会产生意外的副作用，因此请确保在更改策略后测试部署。要部署应用程序，用户和服务帐户必须全部由部署 pod 所需的 pod 安全策略授权。例如，如果是使用 [Helm](/docs/containers?topic=containers-integrations#helm_links) 安装的应用程序，那么 Helm Tiller 组件会创建 pod，因此您必须具有正确的 pod 安全策略授权。

要尝试控制有权访问 {{site.data.keyword.containerlong_notm}} 的用户？请参阅[分配集群访问权](/docs/containers?topic=containers-users#users)以设置 {{site.data.keyword.Bluemix_notm}} IAM 和基础架构许可权。
{: tip}

**缺省情况下设置了任何策略吗？我可以添加哪些策略？**</br>
缺省情况下，{{site.data.keyword.containerlong_notm}} 将 `PodSecurityPolicy` 许可控制器配置为使用[用于 {{site.data.keyword.IBM_notm}} 集群管理的资源](#ibm_psp)，您无法删除也无法修改这些资源。您也无法禁用该许可控制器。

缺省情况下，pod 操作不会锁定。取而代之的是，集群中有两个基于角色的访问控制 (RBAC) 资源，用于授权所有管理员、用户、服务和节点创建有特权 pod 和无特权 pod。用于[混合部署](/docs/containers?topic=containers-hybrid_iks_icp#hybrid_iks_icp)的 {{site.data.keyword.Bluemix_notm}} Private 包随附更多支持可移植性的 RBAC 资源。

如果要阻止特定用户创建或更新 pod，可以[修改这些 RBAC 资源或创建您自己的资源](#customize_psp)。

**策略授权是如何运作的？**</br>
您以用户身份直接创建 pod，而不是使用控制器（如部署）创建 pod 时，系统会根据您有权使用的 pod 安全策略来验证您的凭证。如果没有策略支持 pod 安全需求，那么不会创建 pod。

使用资源控制器（如部署）创建 pod 时，Kubernetes 会根据服务帐户有权使用的 pod 安全策略来验证该 pod 的服务帐户凭证。如果没有策略支持 pod 安全需求，控制器会成功执行，但不会创建 pod。

有关常见错误消息，请参阅[因 pod 安全策略导致 pod 部署失败](/docs/containers?topic=containers-cs_troubleshoot_clusters#cs_psp)。

## 定制 pod 安全策略
{: #customize_psp}

要阻止未授权的 pod 操作，您可以修改现有 pod 安全策略资源或创建自己的资源。您必须是集群管理员才能定制策略。
{: shortdesc}

**可以修改哪些现有策略？**</br>
缺省情况下，集群包含以下 RBAC 资源，这些资源支持集群管理员、已认证的用户、服务帐户和节点使用 `ibm-privileged-psp` 和 `ibm-restricted-psp` pod 安全策略。这些策略允许用户创建和更新有特权和无特权（受限）pod。

|名称|名称空间|类型|用途|
|---|---|---|---|
|`privileged-psp-user`|cluster-wide|`ClusterRoleBinding`|支持集群管理员、已认证的用户、服务帐户和节点使用 `ibm-privileged-psp` pod 安全策略。|
|`restricted-psp-user`|cluster-wide|`ClusterRoleBinding`|支持集群管理员、已认证的用户、服务帐户和节点使用 `ibm-restricted-psp` pod 安全策略。|
{: caption="可以修改的缺省 RBAC 资源" caption-side="top"}

可以修改这些 RBAC 角色，以在策略中除去或添加管理员、用户、服务或节点。

开始之前：
*  [登录到您的帐户。将相应的区域和（如果适用）资源组设定为目标。设置集群的上下文](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)。
*  了解如何使用 RBAC 角色。有关更多信息，请参阅[使用定制 Kubernetes RBAC 角色授权用户](/docs/containers?topic=containers-users#rbac)或 [Kubernetes 文档 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#api-overview)。
* 确保您对所有名称空间具有 [{{site.data.keyword.Bluemix_notm}} IAM **管理者**服务访问角色](/docs/containers?topic=containers-users#platform)。

修改缺省配置后，可能会阻止重要的集群操作，例如 pod 部署或集群更新。请在其他团队不依赖的非生产集群中测试您的更改。
{: important}

**修改 RBAC 资源**：
1.  获取 RBAC 集群角色绑定的名称。
    ```
    kubectl get clusterrolebinding
    ```
    {: pre}

2.  将集群角色绑定下载为可在本地编辑的 `.yaml` 文件。

    ```
    kubectl get clusterrolebinding privileged-psp-user -o yaml > privileged-psp-user.yaml
    ```
    {: pre}

    您可能希望保存现有策略的副本，以便在修改后的策略产生意外结果时可以还原到原始策略。
    {: tip}

    **示例集群角色绑定文件**：

    ```yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
      creationTimestamp: 2018-06-20T21:19:50Z
      name: privileged-psp-user
      resourceVersion: "1234567"
      selfLink: /apis/rbac.authorization.k8s.io/v1/clusterrolebindings/privileged-psp-user
      uid: aa1a1111-11aa-11a1-aaaa-1a1111aa11a1
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: ibm-privileged-psp-user
    subjects:
    - apiGroup: rbac.authorization.k8s.io
      kind: Group
      name: system:masters
    - apiGroup: rbac.authorization.k8s.io
      kind: Group
      name: system:nodes
    - apiGroup: rbac.authorization.k8s.io
      kind: Group
      name: system:serviceaccounts
    - apiGroup: rbac.authorization.k8s.io
      kind: Group
      name: system:authenticated
    ```
    {: codeblock}

3.  编辑集群角色绑定 `.yaml` 文件。要了解哪些内容可以编辑，请查看 [Kubernetes 文档 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)。示例操作：

    *   **服务帐户**：您可能希望授权服务帐户，以便部署只能在特定名称空间中执行。例如，如果策略的作用域限定为允许在 `kube-system` 名称空间内执行操作，那么可能会执行许多重要操作，例如集群更新。但是，不会再对其他名称空间中的操作进行授权。

        要将策略的作用域限定为允许在特定名称空间中执行操作，请将 `system:serviceaccounts` 更改为 `system:serviceaccount:<namespace>`.
        ```yaml
        - apiGroup: rbac.authorization.k8s.io
          kind: Group
          name: system:serviceaccount:kube-system
        ```
        {: codeblock}

    *   **用户**：您可能希望除去所有已认证用户使用特权访问权部署 pod 的授权。请除去以下 `system:authenticated` 条目。
        ```yaml
        - apiGroup: rbac.authorization.k8s.io
          kind: Group
          name: system:authenticated
        ```
        {: codeblock}

4.  在集群中创建修改后的集群角色绑定资源。

    ```
    kubectl apply -f privileged-psp-user.yaml
    ```
    {: pre}

5.  验证资源是否已修改。

    ```
    kubectl get clusterrolebinding privileged-psp-user -o yaml
    ```
    {: pre}

</br>
**删除 RBAC 资源**：
1.  获取 RBAC 集群角色绑定的名称。
    ```
    kubectl get clusterrolebinding
    ```
    {: pre}

2.  删除要除去的 RBAC 角色。
    ```
    kubectl delete clusterrolebinding privileged-psp-user
    ```
    {: pre}

3.  验证 RBAC 集群角色绑定是否不再存在于集群中。
    ```
    kubectl get clusterrolebinding
    ```
    {: pre}

</br>
**创建您自己的 pod 安全策略**：</br>
要创建您自己的 pod 安全策略资源并通过 RBAC 授权用户，请查看 [Kubernetes 文档 ![外部链接图标](../icons/launch-glyph.svg "外部链接图标")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)。

确保已修改现有策略，以便您创建的新策略不会与现有策略相冲突。例如，现有策略允许用户创建和更新有特权 pod。如果创建的策略不允许用户创建或更新有特权 pod，那么现有策略与新策略之间的冲突可能会导致意外结果。

## 了解用于 {{site.data.keyword.IBM_notm}} 集群管理的缺省资源
{: #ibm_psp}

{{site.data.keyword.containerlong_notm}} 中的 Kubernetes 集群包含以下 pod 安全策略和相关的 RBAC 资源，以允许 {{site.data.keyword.IBM_notm}} 正确管理集群。
{: shortdesc}

缺省 `PodSecurityPolicy` 资源指的是 {{site.data.keyword.IBM_notm}} 设置的 pod 安全策略。

**注意**：不能删除或修改这些资源。

|名称|名称空间|类型|用途|
|---|---|---|---|
|`ibm-anyuid-hostaccess-psp`|cluster-wide|`PodSecurityPolicy`|用于创建完整主机访问权 pod 的策略。|
|`ibm-anyuid-hostaccess-psp-user`|cluster-wide|`ClusterRole`|允许使用 `ibm-anyuid-hostaccess-psp` pod 安全策略的集群角色。|
|`ibm-anyuid-hostpath-psp`|cluster-wide|`PodSecurityPolicy`|用于创建主机路径访问 pod 的策略。|
|`ibm-anyuid-hostpath-psp-user`|cluster-wide|`ClusterRole`|允许使用 `ibm-anyuid-hostpath-psp` pod 安全策略的集群角色。|
|`ibm-anyuid-psp`|cluster-wide|`PodSecurityPolicy`|用于创建任何 UID/GID 可执行 pod 的策略。|
|`ibm-anyuid-psp-user`|cluster-wide|`ClusterRole`|允许使用 `ibm-anyuid-psp` pod 安全策略的集群角色。|
|`ibm-privileged-psp`|cluster-wide|`PodSecurityPolicy`|用于创建有特权 pod 的策略。|
|`ibm-privileged-psp-user`|cluster-wide|`ClusterRole`|允许使用 `ibm-privileged-psp` pod 安全策略的集群角色。|
|`ibm-privileged-psp-user`|`kube-system`|`RoleBinding`|支持集群管理员、服务帐户和节点在 `kube-system` 名称空间中使用 `ibm-privileged-psp` pod 安全策略。|
|`ibm-privileged-psp-user`|`ibm-system`|`RoleBinding`|支持集群管理员、服务帐户和节点在 `ibm-system` 名称空间中使用 `ibm-privileged-psp` pod 安全策略。|
|`ibm-privileged-psp-user`|`kubx-cit`|`RoleBinding`|支持集群管理员、服务帐户和节点在 `kubx-cit` 名称空间中使用 `ibm-privileged-psp` pod 安全策略。|
|`ibm-restricted-psp`|cluster-wide|`PodSecurityPolicy`|用于创建无特权（或受限）pod 的策略。|
|`ibm-restricted-psp-user`|cluster-wide|`ClusterRole`|允许使用 `ibm-restricted-psp` pod 安全策略的集群角色。|
{: caption="不能修改的 IBM pod 安全策略资源" caption-side="top"}
