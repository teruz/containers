---

copyright:
  years: 2014, 2019
lastupdated: "2019-03-21"

keywords: kubernetes, iks, ibmcloud, ic, ks, kubectl

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




# 設定 CLI 及 API
{: #cs_cli_install}

您可以使用 {{site.data.keyword.containerlong}} CLI 或 API 來建立及管理 Kubernetes 叢集。
{:shortdesc}

## 安裝 CLI
{: #cs_cli_install_steps}

安裝必要 CLI 以在 {{site.data.keyword.containerlong_notm}} 中建立及管理 Kubernetes 叢集，並且將容器化應用程式部署至叢集。
{:shortdesc}

此作業包括安裝這些 CLI 及外掛程式的資訊：

-   {{site.data.keyword.Bluemix_notm}} CLI 0.8.0 版或更新版本
-   {{site.data.keyword.containerlong_notm}} 外掛程式
-   符合叢集 `major.minor` 版本的 Kubernetes CLI 版本
-   選用項目：{{site.data.keyword.registryshort_notm}} 外掛程式



<br>
若要安裝 CLI，請執行下列動作：



1.  安裝 [{{site.data.keyword.Bluemix_notm}} CLI ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](/docs/cli?topic=cloud-cli-ibmcloud-cli)，它是 {{site.data.keyword.containerlong_notm}} 外掛程式的必要條件。使用 {{site.data.keyword.Bluemix_notm}} CLI 來執行指令的字首是 `ibmcloud`。

    計劃要使用很多 CLI 嗎？請嘗試[啟用 {{site.data.keyword.Bluemix_notm}} CLI 的 Shell 自動完成（僅限 Linux/MacOS）](/docs/cli/reference/ibmcloud?topic=cloud-cli-shell-autocomplete#shell-autocomplete-linux)。
    {: tip}

2.  登入 {{site.data.keyword.Bluemix_notm}} CLI。系統提示時，請輸入您的 {{site.data.keyword.Bluemix_notm}} 認證。

    ```
    ibmcloud login
    ```
    {: pre}

    如果您具有聯合 ID，請使用 `ibmcloud login --sso` 來登入 {{site.data.keyword.Bluemix_notm}} CLI。請輸入使用者名稱，並使用 CLI 輸出中提供的 URL 來擷取一次性密碼。若沒有 `--sso` 時登入失敗，而有 `--sso` 選項時登入成功，即表示您有聯合 ID。
    {: tip}

3.  若要建立 Kubernetes 叢集，以及管理工作者節點，請安裝 {{site.data.keyword.containerlong_notm}} 外掛程式。使用 {{site.data.keyword.containerlong_notm}} 外掛程式來執行指令的字首是 `ibmcloud ks`。

    ```
    ibmcloud plugin install container-service
    ```
    {: pre}

    若要驗證已適當安裝外掛程式，請執行下列指令：

    ```
    ibmcloud plugin list
    ```
    {: pre}

    {{site.data.keyword.containerlong_notm}} 外掛程式會在結果中顯示為 **container-service**。

4.  {: #kubectl}若要檢視本端版本的 Kubernetes 儀表板，以及將應用程式部署至叢集，請[安裝 Kubernetes CLI ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。使用 Kubernetes CLI 來執行指令的字首是 `kubectl`。

    1.  下載與您計劃使用的 Kubernetes 叢集 `major.minor` 版本相符的 Kubernetes CLI `major.minor` 版本。現行 {{site.data.keyword.containerlong_notm}} 預設 Kubernetes 版本為 1.12.6。

        如果您使用的 `kubectl` CLI 版本未至少符合叢集的 `major.minor` 版本，則您可能會看到非預期的結果。請確定 Kubernetes 叢集和 CLI 版本保持最新。
        {: note}

        - **OS X**：[https://storage.googleapis.com/kubernetes-release/release/v1.12.6/bin/darwin/amd64/kubectl ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://storage.googleapis.com/kubernetes-release/release/v1.12.6/bin/darwin/amd64/kubectl)
        - **Linux**：  [https://storage.googleapis.com/kubernetes-release/release/v1.12.6/bin/linux/amd64/kubectl ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://storage.googleapis.com/kubernetes-release/release/v1.12.6/bin/linux/amd64/kubectl)
        - **Windows**：   [https://storage.googleapis.com/kubernetes-release/release/v1.12.6/bin/windows/amd64/kubectl.exe ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://storage.googleapis.com/kubernetes-release/release/v1.12.6/bin/windows/amd64/kubectl.exe)

    2.  **若為 OS X 及 Linux**：請完成下列步驟。
        1.  將執行檔移至 `/usr/local/bin` 目錄。

            ```
            mv /filepath/kubectl /usr/local/bin/kubectl
            ```
            {: pre}

        2.  確定 `/usr/local/bin` 列在 `PATH` 系統變數中。`PATH` 變數包含作業系統可在其中找到執行檔的所有目錄。`PATH` 變數中所列的目錄用於不同的用途。`/usr/local/bin` 是用來儲存軟體的執行檔，該軟體不是作業系統的一部分，並且已由系統管理者手動安裝。

            ```
            echo $PATH
            ```
            {: pre}

            CLI 輸出範例：

            ```
            /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
            ```
            {: screen}

        3.  使檔案成為可執行檔。

            ```
            chmod +x /usr/local/bin/kubectl
            ```
            {: pre}

    3.  **若為 Windows**：請將 Kubernetes CLI 安裝在與 {{site.data.keyword.Bluemix_notm}} CLI 相同的目錄中。當您稍後執行指令時，這項設定可為您省去一些檔案路徑變更。

5.  若要管理專用映像檔儲存庫，請安裝 {{site.data.keyword.registryshort_notm}} 外掛程式。您可以使用此外掛程式，在多方承租戶、高可用性並且由 IBM 所管理的可擴充專用映像檔登錄中設定您自己的名稱空間，以及儲存 Docker 映像檔，並將其與其他使用者共用。需要有 Docker 映像檔，才能將容器部署至叢集。執行登錄指令的字首是 `ibmcloud cr`。

    ```
    ibmcloud plugin install container-registry 
    ```
    {: pre}

    若要驗證已適當安裝外掛程式，請執行下列指令：

    ```
    ibmcloud plugin list
    ```
    {: pre}

    在結果中，外掛程式會顯示為 container-registry。

接下來，開始[使用 {{site.data.keyword.containerlong_notm}} 從 CLI 建立 Kubernetes 叢集](/docs/containers?topic=containers-clusters#clusters_cli)。

如需這些 CLI 的相關參考資訊，請參閱那些工具的文件。

-   [`ibmcloud` 指令](/docs/cli/reference/ibmcloud?topic=cloud-cli-ibmcloud_cli#ibmcloud_cli)
-   [`ibmcloud ks` 指令](/docs/containers?topic=containers-cs_cli_reference#cs_cli_reference)
-   [`kubectl` 指令 ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://kubernetes.io/docs/reference/kubectl/overview/)
-   [`ibmcloud cr` 指令](/docs/services/Registry?topic=registry-registry_cli_reference#registry_cli_reference)

<br />




## 在電腦上的容器中執行 CLI
{: #cs_cli_container}

不是在您的電腦上個別安裝每一個 CLI，而是您可以將 CLI 安裝至在您電腦上執行的容器。
{:shortdesc}

開始之前，請[安裝 Docker ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://www.docker.com/community-edition#/download)，以在本端建置並執行映像檔。如果您使用的是 Windows 8 或更早版本，則可以改為安裝 [Docker Toolbox ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://docs.docker.com/toolbox/toolbox_install_windows/)。

1. 從提供的 Dockerfile 建立映像檔。

    ```
    docker build -t <image_name> https://raw.githubusercontent.com/IBM-Cloud/kube-samples/master/install-clis-container/Dockerfile
    ```
    {: pre}

2. 在本端部署映像檔作為容器，並裝載磁區來存取本端檔案。

    ```
    docker run -it -v /local/path:/container/volume <image_name>
    ```
    {: pre}

3. 從互動式 Shell 開始執行 `ibmcloud ks` 及 `kubectl` 指令。如果您建立了要儲存的資料，請將該資料儲存至您所裝載的磁區。當您結束 Shell 時，容器就會停止。

<br />



## 配置 CLI 以執行 `kubectl`
{: #cs_cli_configure}

您可以使用 Kubernetes CLI 隨附的指令來管理 {{site.data.keyword.Bluemix_notm}} 中的叢集。
{:shortdesc}

支援 Kubernetes 1.12.6 中所有可用的 `kubectl` 指令與 {{site.data.keyword.Bluemix_notm}} 中的叢集搭配使用。建立叢集之後，使用環境變數將本端 CLI 的環境定義設定為該叢集。然後，您可以執行 Kubernetes `kubectl` 指令，在 {{site.data.keyword.Bluemix_notm}} 中使用您的叢集。


在可以執行 `kkbectl` 指令之前，請執行下列動作：
* [安裝必要的 CLI](#cs_cli_install)。
* [建立叢集](/docs/containers?topic=containers-clusters#clusters_cli)。
* 請確定已經具有[服務角色](/docs/containers?topic=containers-users#platform)來授與適當的 Kubernetes RBAC 角色，以便您可以使用 Kubernetes 資源。如果您只有服務角色而無平台角色，您需要叢集管理者為您提供叢集名稱和 ID，或提供**檢視者**平台角色來列出叢集。

若要使用 `kubectl` 指令，請執行下列動作：

1.  登入 {{site.data.keyword.Bluemix_notm}} CLI。系統提示時，請輸入您的 {{site.data.keyword.Bluemix_notm}} 認證。若要指定 {{site.data.keyword.Bluemix_notm}} 地區，請[包括 API 端點](/docs/containers?topic=containers-regions-and-zones#bluemix_regions)。

    ```
    ibmcloud login
    ```
    {: pre}

    如果您具有聯合 ID，請使用 `ibmcloud login --sso` 來登入 {{site.data.keyword.Bluemix_notm}} CLI。請輸入使用者名稱，並使用 CLI 輸出中提供的 URL 來擷取一次性密碼。若沒有 `--sso` 時登入失敗，而有 `--sso` 選項時登入成功，即表示您有聯合 ID。
    {: tip}

2.  選取 {{site.data.keyword.Bluemix_notm}} 帳戶。如果您被指派到多個 {{site.data.keyword.Bluemix_notm}} 組織，請選取在其中建立叢集的組織。叢集是組織特有的，但與 {{site.data.keyword.Bluemix_notm}} 空間無關。因此，您不需要選取空間。

3.  若要建立及使用非 default 資源群組中的叢集，請將目標設為該資源群組。若要查看每一個叢集所屬的資源群組，請執行 `ibmcloud ks clusters`。**附註**：您必須對資源群組具有[**檢視者**存取權](/docs/containers?topic=containers-users#platform)。
    ```
    ibmcloud target -g <resource_group_name>
    ```
    {: pre}

4.  若要在先前所選取 {{site.data.keyword.Bluemix_notm}} 地區以外的地區中建立或存取 Kubernetes 叢集，請將此地區設為目標。
    ```
    ibmcloud ks region-set
    ```
    {: pre}

5.  列出帳戶中的所有叢集，以取得叢集的名稱。如果您只有 {{site.data.keyword.Bluemix_notm}} IAM 服務角色，無法檢視叢集，請要求叢集管理者提供 IAM 平台**檢視者**角色，或是叢集名稱和 ID。

    ```
    ibmcloud ks clusters
    ```
    {: pre}

6.  將您建立的叢集設為此階段作業的環境定義。請在您每次使用叢集時，完成下列配置步驟。
    1.  讓指令設定環境變數，並下載 Kubernetes 配置檔。

        ```
        ibmcloud ks cluster-config <cluster_name_or_ID>
        ```
        {: pre}

        下載配置檔之後，會顯示一個指令，可讓您用來將本端 Kubernetes 配置檔的路徑設定為環境變數。

        範例：

        ```
        export KUBECONFIG=/Users/<user_name>/.bluemix/plugins/container-service/clusters/mycluster/kube-config-prod-dal10-mycluster.yml
        ```
        {: screen}

    2.  複製並貼上終端機中顯示的指令，以設定 `KUBECONFIG` 環境變數。

        **Mac 或 Linux 使用者**：不執行 `ibmcloud ks cluster-config` 指令及複製 `KUBECONFIG` 環境變數，您可以改為執行 `ibmcloud ks cluster-config --export <cluster-name>`。根據您的 Shell，您可以透過執行 `eval $(ibmcloud ks cluster-config --export) <cluster-name>)`  來設定 Shell。
        {: tip}

        **Windows PowerShell 使用者**：不要從 `ibmcloud ks cluster-config` 的輸出中複製並貼上 `SET` 指令，例如，您必須改為執行 `$env:KUBECONFIG = "C:\Users\<user_name>\.bluemix\plugins\container-service\clusters\mycluster\kube-config-prod-dal10-mycluster.yml"` 來設定 `KUBECONCONFIG` 環境變數。
        {:tip}

    3.  驗證已適當地設定 `KUBECONFIG` 環境變數。

        範例：

        ```
        echo $KUBECONFIG
        ```
        {: pre}

        輸出：
        ```
        /Users/<user_name>/.bluemix/plugins/container-service/clusters/mycluster/kube-config-prod-dal10-mycluster.yml
        ```
        {: screen}

7.  檢查 Kubernetes CLI 伺服器版本，驗證叢集已適當地執行 `kubectl` 指令。

    ```
    kubectl version  --short
    ```
    {: pre}

    輸出範例：

    ```
    Client Version: v1.12.6
    Server Version: v1.12.6
    ```
    {: screen}

您現在可以執行 `kubectl` 指令，以在 {{site.data.keyword.Bluemix_notm}} 中管理叢集。如需完整指令清單，請參閱 [Kubernetes 文件 ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://kubernetes.io/docs/reference/kubectl/overview/)。

**提示：**如果您使用的是 Windows，但未在與 {{site.data.keyword.Bluemix_notm}} CLI 相同的目錄中安裝 Kubernetes CLI，則必須將目錄切換至 Kubernetes CLI 安裝所在的路徑，才能順利執行 `kubectl` 指令。


<br />


## 更新 CLI
{: #cs_cli_upgrade}

建議您定期更新 CLI，以使用新特性。
{:shortdesc}

此作業包括更新這些 CLI 的資訊。

-   {{site.data.keyword.Bluemix_notm}} CLI 0.8.0 版或更新版本
-   {{site.data.keyword.containerlong_notm}} 外掛程式
-   Kubernetes CLI 1.12.6 版或更新版本
-   {{site.data.keyword.registryshort_notm}} 外掛程式

<br>
若要更新 CLI，請執行下列動作：



1.  更新 {{site.data.keyword.Bluemix_notm}} CLI。下載[最新版本 ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](/docs/cli?topic=cloud-cli-ibmcloud-cli)，並執行安裝程式。

2. 登入 {{site.data.keyword.Bluemix_notm}} CLI。系統提示時，請輸入您的 {{site.data.keyword.Bluemix_notm}} 認證。若要指定 {{site.data.keyword.Bluemix_notm}} 地區，請[包括 API 端點](/docs/containers?topic=containers-regions-and-zones#bluemix_regions)。

    ```
    ibmcloud login
    ```
    {: pre}

     如果您具有聯合 ID，請使用 `ibmcloud login --sso` 來登入 {{site.data.keyword.Bluemix_notm}} CLI。請輸入使用者名稱，並使用 CLI 輸出中提供的 URL 來擷取一次性密碼。若沒有 `--sso` 時登入失敗，而有 `--sso` 選項時登入成功，即表示您有聯合 ID。
    {: tip}

3.  更新 {{site.data.keyword.containerlong_notm}} 外掛程式。
    1.  從 {{site.data.keyword.Bluemix_notm}} 外掛程式儲存庫中安裝更新。

        ```
        ibmcloud plugin update container-service
        ```
        {: pre}

    2.  執行下列指令，並檢查已安裝的外掛程式清單，以驗證外掛程式安裝。

        ```
        ibmcloud plugin list
        ```
        {: pre}

        在結果中，{{site.data.keyword.containerlong_notm}} 外掛程式會顯示為 container-service。

    3.  起始設定 CLI。

        ```
        ibmcloud ks init
        ```
        {: pre}

4.  [更新 Kubernetes CLI](#kubectl)。

5.  更新 {{site.data.keyword.registryshort_notm}} 外掛程式。
    1.  從 {{site.data.keyword.Bluemix_notm}} 外掛程式儲存庫中安裝更新。

        ```
        ibmcloud plugin update container-registry 
        ```
        {: pre}

    2.  執行下列指令，並檢查已安裝的外掛程式清單，以驗證外掛程式安裝。

        ```
        ibmcloud plugin list
        ```
        {: pre}

        在結果中，登錄外掛程式會顯示為 container-registry。

<br />


## 解除安裝 CLI
{: #cs_cli_uninstall}

如果不再需要 CLI，則可以將它解除安裝。
{:shortdesc}

此作業包括移除這些 CLI 的資訊：


-   {{site.data.keyword.containerlong_notm}} 外掛程式
-   Kubernetes CLI
-   {{site.data.keyword.registryshort_notm}} 外掛程式

若要解除安裝 CLI，請執行下列動作：

1.  解除安裝 {{site.data.keyword.containerlong_notm}} 外掛程式。

    ```
    ibmcloud plugin uninstall container-service
    ```
    {: pre}

2.  解除安裝 {{site.data.keyword.registryshort_notm}} 外掛程式。

    ```
    ibmcloud plugin uninstall container-registry
    ```
    {: pre}

3.  執行下列指令，並檢查已安裝的外掛程式清單，以驗證已解除安裝外掛程式。

    ```
    ibmcloud plugin list
    ```
    {: pre}

    container-service 及 container-registry 外掛程式不會顯示在結果中。

<br />




## 使用 API 自動化進行叢集部署
{: #cs_api}

您可以使用 {{site.data.keyword.containerlong_notm}} API 來自動化進行 Kubernetes 叢集的建立、部署及管理。
{:shortdesc}

{{site.data.keyword.containerlong_notm}} API 需要標頭資訊，您必須在 API 要求中提供它，且它會視您要使用的 API 而變。若要判斷 API 所需的標頭資訊，請參閱 [{{site.data.keyword.containerlong_notm}} API 文件 ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://us-south.containers.cloud.ibm.com/swagger-api)。

若要向 {{site.data.keyword.containerlong_notm}} 進行鑑別，您必須提供以 {{site.data.keyword.Bluemix_notm}} 認證產生且包含建立叢集所在 {{site.data.keyword.Bluemix_notm}} 帳戶 ID 的 {{site.data.keyword.Bluemix_notm}} Identity and Access Management (IAM) 記號。取決於您向 {{site.data.keyword.Bluemix_notm}} 進行鑑別的方式，您可以在下列選項之間進行選擇，以自動建立 {{site.data.keyword.Bluemix_notm}} IAM 記號。

您也可以使用 [API Swagger JSON 檔案 ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://containers.cloud.ibm.com/swagger-api-json) 來產生可在自動化工作期間與 API 互動的用戶端。
{: tip}

<table>
<caption>ID 類型和選項</caption>
<thead>
<th>{{site.data.keyword.Bluemix_notm}} ID</th>
<th>我的選項</th>
</thead>
<tbody>
<tr>
<td>未聯合 ID</td>
<td><ul><li><strong>{{site.data.keyword.Bluemix_notm}} 使用者名稱及密碼：</strong>您可以遵循本主題的步驟，以完全自動建立 {{site.data.keyword.Bluemix_notm}} IAM 存取記號。</li>
<li><strong>產生 {{site.data.keyword.Bluemix_notm}} API 金鑰：</strong>如果不使用 {{site.data.keyword.Bluemix_notm}} 使用者名稱和密碼，您的另一個替代方案是<a href="/docs/iam?topic=iam-userapikey#create_user_key" target="_blank">使用 {{site.data.keyword.Bluemix_notm}} API 金鑰</a>。{{site.data.keyword.Bluemix_notm}} API 金鑰相依於要產生它們的 {{site.data.keyword.Bluemix_notm}} 帳戶。您無法將 {{site.data.keyword.Bluemix_notm}} API 金鑰與不同帳戶 ID 結合在相同的 {{site.data.keyword.Bluemix_notm}} IAM 記號中。若要存取使用您 {{site.data.keyword.Bluemix_notm}} API 金鑰根據帳戶以外之帳戶建立的叢集，您必須登入帳戶才能產生新的 API 金鑰。</li></ul></tr>
<tr>
<td>聯合 ID</td>
<td><ul><li><strong>產生 {{site.data.keyword.Bluemix_notm}} API 金鑰：</strong><a href="/docs/iam?topic=iam-userapikey#create_user_key" target="_blank">{{site.data.keyword.Bluemix_notm}} API 金鑰</a>相依於要產生它們的 {{site.data.keyword.Bluemix_notm}} 帳戶。您無法將 {{site.data.keyword.Bluemix_notm}} API 金鑰與不同帳戶 ID 結合在相同的 {{site.data.keyword.Bluemix_notm}} IAM 記號中。若要存取使用您 {{site.data.keyword.Bluemix_notm}} API 金鑰根據帳戶以外之帳戶建立的叢集，您必須登入帳戶才能產生新的 API 金鑰。</li><li><strong>使用一次性密碼：</strong>如果您使用一次性密碼來向 {{site.data.keyword.Bluemix_notm}} 進行鑑別，則無法完全自動建立 {{site.data.keyword.Bluemix_notm}} IAM 記號，因為擷取一次性密碼需要與 Web 瀏覽器進行手動互動。若要完全自動建立 {{site.data.keyword.Bluemix_notm}} IAM 記號，您必須改為建立一個 {{site.data.keyword.Bluemix_notm}} API 金鑰。</ul></td>
</tr>
</tbody>
</table>

1.  建立 {{site.data.keyword.Bluemix_notm}} IAM 存取記號。要求中包含的內文資訊會根據您使用的 {{site.data.keyword.Bluemix_notm}} 鑑別方法而有所不同。請取代下列值：
  - `<username>`：您的 {{site.data.keyword.Bluemix_notm}} 使用者名稱。
  - `<password>`：您的 {{site.data.keyword.Bluemix_notm}} 密碼。
  - `<api_key>`：您的 {{site.data.keyword.Bluemix_notm}} API 金鑰。
  - `<passcode>`：您的 {{site.data.keyword.Bluemix_notm}} 一次性密碼。執行 `ibmcloud login --sso`，並遵循 CLI 輸出中的指示，使用 Web 瀏覽器來擷取一次性密碼。

    ```
    POST https://iam.<region>.bluemix.net/oidc/token
    ```
    {: codeblock}

    範例：
    ```
    POST https://iam.ng.bluemix.net/oidc/token
    ```
    {: codeblock}

    若要指定 {{site.data.keyword.Bluemix_notm}} 地區，請[檢閱 API 端點中所使用的地區縮寫](/docs/containers?topic=containers-regions-and-zones#bluemix_regions)。

    <table summary-"Input parameters to retrieve tokens">
    <caption>取得記號的輸入參數</caption>
    <thead>
        <th>輸入參數</th>
        <th>值</th>
    </thead>
    <tbody>
    <tr>
    <td>Header</td>
    <td><ul><li>Content-Type:application/x-www-form-urlencoded</li> <li>Authorization: Basic Yng6Yng=<p><strong>附註</strong>：<code>Yng6Yng=</code> 等於使用者名稱 <strong>bx</strong> 及密碼 <strong>bx</strong> 的 URL 編碼授權。</p></li></ul>
    </td>
    </tr>
    <tr>
    <td>{{site.data.keyword.Bluemix_notm}} 使用者名稱和密碼的內文</td>
    <td><ul><li>`grant_type: password`</li>
    <li>`response_type: cloud_iam uaa`</li>
    <li>`username: <em>&lt;username&gt;</em>`</li>
    <li>`password: <em>&lt;password&gt;</em>`</li>
    <li>`uaa_client_ID: cf`</li>
    <li>`uaa_client_secret:`</li></ul>
    <strong>附註</strong>：新增未指定任何值的 <code>uaa_client_secret</code> 金鑰。</td>
    </tr>
    <tr>
    <td>{{site.data.keyword.Bluemix_notm}} API 金鑰的內文</td>
    <td><ul><li>`grant_type: urn:ibm:params:oauth:grant-type:apikey`</li>
    <li>`response_type: cloud_iam uaa`</li>
    <li>`apikey: <em>&lt;api_key&gt;</em>`</li>
    <li>`uaa_client_ID: cf`</li>
    <li>`uaa_client_secret:``</li></ul>
    <strong>附註</strong>：新增未指定任何值的 <code>uaa_client_secret</code> 金鑰。</td>
    </tr>
    <tr>
    <td>{{site.data.keyword.Bluemix_notm}} 一次性密碼的內文</td>
    <td><ul><li>`grant_type: urn:ibm:params:oauth:grant-type:passcode`</li>
    <li>`response_type: cloud_iam uaa`</li>
    <li>`passcode: <em>&lt;passcode&gt;</em>`</li>
    <li>`uaa_client_ID: cf`</li>
    <li>`uaa_client_secret:`</li></ul>
    <strong>附註</strong>：新增未指定任何值的 <code>uaa_client_secret</code> 金鑰。</td>
    </tr>
    </tbody>
    </table>

    API 輸出範例：

    ```
    {
    "access_token": "<iam_token>",
"refresh_token": "<iam_refresh_token>",
"uaa_token": "<uaa_token>",
"uaa_refresh_token": "<uaa_refresh_token>",
"token_type": "Bearer",
"expires_in": 3600,
"expiration": 1493747503
}

    ```
    {: screen}

    您可以在 API 輸出的 **access_token** 欄位中找到 {{site.data.keyword.Bluemix_notm}} IAM 記號。請記下 {{site.data.keyword.Bluemix_notm}} IAM 記號，以在接下來的步驟中擷取其他標頭資訊。

2.  擷取建立叢集所在的 {{site.data.keyword.Bluemix_notm}} 帳戶 ID。將 `<iam_token>` 取代為您在前一個步驟中擷取的 {{site.data.keyword.Bluemix_notm}} IAM 記號。

    ```
    GET https://accountmanagement.<region>.bluemix.net/v1/accounts
    ```
    {: codeblock}

    <table summary="取得 {{site.data.keyword.Bluemix_notm}} 帳戶 ID 的輸入參數">
    <caption>取得 {{site.data.keyword.Bluemix_notm}} 帳戶 ID 的輸入參數</caption>
    <thead>
  	<th>輸入參數</th>
  	<th>值</th>
    </thead>
    <tbody>
  	<tr>
  		<td>Headers</td>
  		<td><ul><li>`Content-Type：application/json`</li>
      <li>`Authorization: bearer &lt;iam_token&gt;`</li>
      <li>`Accept: application/json`</li></ul></td>
  	</tr>
    </tbody>
    </table>

    API 輸出範例：

    ```
    {
      "total_results": 3,
      "total_pages": 1,
      "prev_url": null,
      "next_url": null,
      "resources":
        {
          "metadata": {
            "guid": "<account_ID>",
            "url": "/v1/accounts/<account_ID>",
            "created_at": "2016-01-07T18:55:09.726Z",
            "updated_at": "2017-04-28T23:46:03.739Z",
            "origin": "BSS"
    ...
    ```
    {: screen}

    您可以在 API 輸出的 **resources/metadata/guid** 欄位中找到 {{site.data.keyword.Bluemix_notm}} 帳戶的 ID。

3.  產生包含 {{site.data.keyword.Bluemix_notm}} 認證及建立叢集所在帳戶 ID 的新 {{site.data.keyword.Bluemix_notm}} IAM 記號。將 `<account_ID>` 取代為您在前一個步驟中擷取的 {{site.data.keyword.Bluemix_notm}} 帳戶的 ID。

    如果您是使用 {{site.data.keyword.Bluemix_notm}} API 金鑰，則必須使用為其建立 API 金鑰的 {{site.data.keyword.Bluemix_notm}} 帳戶 ID。若要存取其他帳戶中的叢集，請登入此帳戶，並建立以此帳戶為基礎的 {{site.data.keyword.Bluemix_notm}} API 金鑰。
    {: note}

    ```
    POST https://iam.<region>.bluemix.net/oidc/token
    ```
    {: codeblock}

    範例：
    ```
    POST https://iam.ng.bluemix.net/oidc/token
    ```
    {: codeblock}

    若要指定 {{site.data.keyword.Bluemix_notm}} 地區，請[檢閱 API 端點中所使用的地區縮寫](/docs/containers?topic=containers-regions-and-zones#bluemix_regions)。

    <table summary-"Input parameters to retrieve tokens">
    <caption>取得記號的輸入參數</caption>
    <thead>
        <th>輸入參數</th>
        <th>值</th>
    </thead>
    <tbody>
    <tr>
    <td>Header</td>
    <td><ul><li>`Content-Type:application/x-www-form-urlencoded`</li> <li>`Authorization: Basic Yng6Yng=`<p><strong>附註</strong>：<code>Yng6Yng=</code> 等於使用者名稱 <strong>bx</strong> 及密碼 <strong>bx</strong> 的 URL 編碼授權。</p></li></ul>
    </td>
    </tr>
    <tr>
    <td>{{site.data.keyword.Bluemix_notm}} 使用者名稱和密碼的內文</td>
    <td><ul><li>`grant_type: password`</li>
    <li>`response_type: cloud_iam uaa`</li>
    <li>`username: <em>&lt;username&gt;</em>`</li>
    <li>`password: <em>&lt;password&gt;</em>`</li>
    <li>`uaa_client_ID: cf`</li>
    <li>`uaa_client_secret:``</li>
    <li>`bss_account: <em>&lt;account_ID&gt;</em>`</li></ul>
    <strong>附註</strong>：新增未指定任何值的 <code>uaa_client_secret</code> 金鑰。</td>
    </tr>
    <tr>
    <td>{{site.data.keyword.Bluemix_notm}} API 金鑰的內文</td>
    <td><ul><li>`grant_type: urn:ibm:params:oauth:grant-type:apikey`</li>
    <li>`response_type: cloud_iam uaa`</li>
    <li>`apikey: <em>&lt;api_key&gt;</em>`</li>
    <li>`uaa_client_ID: cf`</li>
    <li>`uaa_client_secret:``</li>
    <li>`bss_account: <em>&lt;account_ID&gt;</em>`</li></ul>
      <strong>附註</strong>：新增未指定任何值的 <code>uaa_client_secret</code> 金鑰。</td>
    </tr>
    <tr>
    <td>{{site.data.keyword.Bluemix_notm}} 一次性密碼的內文</td>
    <td><ul><li>`grant_type: urn:ibm:params:oauth:grant-type:passcode`</li>
    <li>`response_type: cloud_iam uaa`</li>
    <li>`passcode: <em>&lt;passcode&gt;</em>`</li>
    <li>`uaa_client_ID: cf`</li>
    <li>`uaa_client_secret:`</li>
    <li>`bss_account: <em>&lt;account_ID&gt;</em>`</li></ul><strong>附註</strong>：新增未指定任何值的 <code>uaa_client_secret</code> 金鑰。</td>
    </tr>
    </tbody>
    </table>

    API 輸出範例：

    ```
    {
      "access_token": "<iam_token>",
"refresh_token": "<iam_refresh_token>",
"uaa_token": "<uaa_token>",
"uaa_refresh_token": "<uaa_refresh_token>",
"token_type": "Bearer",
"expires_in": 3600,
"expiration": 1493747503
}

    ```
    {: screen}

    您可以在 **access_token** 中找到 {{site.data.keyword.Bluemix_notm}} IAM 記號，並且在 **refresh_token** 中找到重新整理記號。

4.  列出帳戶中的所有 Kubernetes 叢集。使用您在先前步驟中所擷取的資訊，以建置標頭資訊。

     ```
     GET https://containers.cloud.ibm.com/v1/clusters
     ```
     {: codeblock}

     <table summary="使用 API 的輸入參數">
         <caption>使用 API 的輸入參數</caption>
     <thead>
     <th>輸入參數</th>
     <th>值</th>
     </thead>
     <tbody>
     <tr>
     <td>Header</td>
     <td><ul><li>`Authorization: bearer <em>&lt;iam_token&gt;</em>`</li>
     <li>`X-Auth-Refresh-Token: <em>&lt;refresh_token&gt;</em>`</li></ul></td>
     </tr>
     </tbody>
     </table>

5.  請檢閱 [{{site.data.keyword.containerlong_notm}} API 文件 ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://containers.cloud.ibm.com/swagger-api)，以尋找所支援 API 的清單。

<br />


## 使用 API 重新整理 {{site.data.keyword.Bluemix_notm}} IAM 存取記號，並取得新的重新整理記號
{: #cs_api_refresh}

每個透過 API 發出的 {{site.data.keyword.Bluemix_notm}} Identity and Access Management (IAM) 存取記號都會在一個小時後到期。您必須定期重新整理存取記號，以確保 {{site.data.keyword.Bluemix_notm}} API 的存取權。您可以使用相同的步驟來取得新的重新整理記號。
{:shortdesc}

開始之前，請確定您有可用來要求新存取記號的 {{site.data.keyword.Bluemix_notm}} IAM 重新整理記號或 {{site.data.keyword.Bluemix_notm}} API 金鑰。
- **重新整理記號：**請遵循[使用 {{site.data.keyword.Bluemix_notm}} API 自動化叢集建立及管理處理程序](#cs_api)中的指示。
- **API 金鑰：**如下所示，擷取 [{{site.data.keyword.Bluemix_notm}} ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://cloud.ibm.com/) API 金鑰。
   1. 從功能表列中，按一下**管理** > **存取權 (IAM)**。
   2. 按一下**使用者**頁面，然後選取自己。
   3. 在 **API 金鑰**窗格中，按一下**建立 IBM Cloud API 金鑰**。
   4. 輸入 API 金鑰的**名稱**和**說明**，然後按一下**建立**。
   4. 按一下**顯示**來查看為您產生的 API 金鑰。
   5. 複製 API 金鑰，您可以用它來擷取新的 {{site.data.keyword.Bluemix_notm}} IAM 存取記號。

如果您想要建立 {{site.data.keyword.Bluemix_notm}} IAM 記號，或想要取得新的重新整理記號，請使用下列步驟。

1.  使用重新整理記號或 {{site.data.keyword.Bluemix_notm}} API 金鑰來產生新的 {{site.data.keyword.Bluemix_notm}} IAM 存取記號。
    ```
    POST https://iam.bluemix.net/identity/token
    ```
    {: codeblock}

    <table summary="新 IAM 記號的輸入參數">
     <caption>新 {{site.data.keyword.Bluemix_notm}} IAM 記號的輸入參數</caption>
    <thead>
    <th>輸入參數</th>
    <th>值</th>
    </thead>
    <tbody>
    <tr>
    <td>Header</td>
    <td><ul><li>`Content-Type: application/x-www-form-urlencoded`</li>
      <li>`Authorization: Basic Yng6Yng=`</br></br><strong>附註</strong>：<code>Yng6Yng=</code> 等於使用者名稱 <strong>bx</strong> 及密碼 <strong>bx</strong> 的 URL 編碼授權。</li></ul></td>
    </tr>
    <tr>
    <td>使用重新整理記號時的內文</td>
    <td><ul><li>`grant_type: refresh_token`</li>
    <li>`response_type: cloud_iam uaa`</li>
    <li>`refresh_token: <em>&lt;iam_refresh_token&gt;</em>`</li>
    <li>`uaa_client_ID: cf`</li>
    <li>`uaa_client_secret:`</li>
    <li>`bss_account: <em>&lt;account_ID&gt;</em>`</li></ul><strong>附註</strong>：新增未指定任何值的 <code>uaa_client_secret</code> 金鑰。</td>
    </tr>
    <tr>
      <td>使用 {{site.data.keyword.Bluemix_notm}} API 金鑰時的內文</td>
      <td><ul><li>`grant_type: urn:ibm:params:oauth:grant-type:apikey`</li>
    <li>`response_type: cloud_iam uaa`</li>
    <li>`apikey: <em>&lt;api_key&gt;</em>`</li>
    <li>`uaa_client_ID: cf`</li>
        <li>`uaa_client_secret:`</li></ul><strong>附註</strong>：新增未指定任何值的 <code>uaa_client_secret</code> 金鑰。</td>
    </tr>
    </tbody>
    </table>

    API 輸出範例：

    ```
    {
      "access_token": "<iam_token>",
"refresh_token": "<iam_refresh_token>",
"uaa_token": "<uaa_token>",
"uaa_refresh_token": "<uaa_refresh_token>",
"token_type": "Bearer",
"expires_in": 3600,
"expiration": 1493747503
}

    ```
    {: screen}

    您可以在 API 輸出的 **access_token** 欄位中找到新的 {{site.data.keyword.Bluemix_notm}} IAM 記號，並在 **refresh_token** 欄位中找到重新整理記號。

2.  使用前一個步驟中的記號，繼續使用 [{{site.data.keyword.containerlong_notm}} API 文件 ![外部鏈結圖示](../icons/launch-glyph.svg "外部鏈結圖示")](https://containers.cloud.ibm.com/swagger-api)。

<br />


## 使用 CLI 重新整理 {{site.data.keyword.Bluemix_notm}} IAM 存取記號，並取得新的重新整理記號
{: #cs_cli_refresh}

當您啟動新的 CLI 階段作業時，或如果現行 CLI 階段作業在 24 小時後到期，您必須執行 `ibmcloud ks cluster-config <cluster_name>`，來設定叢集的環境定義。當您使用這個指令來設定叢集的環境定義時，會下載 Kubernetes 叢集的 `kubeconfig` 檔案。此外，會發出 {{site.data.keyword.Bluemix_notm}} Identity and Access Management (IAM) ID 記號及重新整理記號，以提供鑑別。
{: shortdesc}

**ID 記號**：每個透過 CLI 發出的 IAM ID 記號都會在一個小時後到期。當 ID 記號到期時，會將重新整理記號傳送至記號提供者，以重新整理 ID 記號。您的鑑別會重新整理，而且您可以繼續針對叢集執行指令。

**重新整理記號**：重新整理記號每隔 30 天到期。如果重新整理記號過期，則無法重新整理 ID 記號，而且您無法在 CLI 中繼續執行指令。您可以執行 `ibmcloud ks cluster-config <cluster_name>`，來取得新的重新整理記號。這個指令也會重新整理您的 ID 記號。
