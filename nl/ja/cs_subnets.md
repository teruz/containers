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



# クラスター用のサブネットの構成
{: #subnets}

Kubernetes クラスターにサブネットを追加して、ロード・バランサー・サービス用の使用可能なポータブル・パブリック IP アドレスまたはポータブル・プライベート IP アドレスのプールを変更します。
{:shortdesc}

## カスタムまたは既存の IBM Cloud インフラストラクチャー (SoftLayer) サブネットを使用したクラスターの作成
{: #subnets_custom}

標準クラスターを作成すると、サブネットが自動的に作成されます。 ただし、自動的にプロビジョンされるサブネットを使用する代わりに、ご使用の IBM Cloud インフラストラクチャー (SoftLayer) アカウントから既存のポータブル・サブネットを使用したり、削除したクラスターのサブネットを再利用したりできます。
{:shortdesc}

このオプションは、クラスターの削除や作成が行われても変わらない静的 IP アドレスを保持する場合や、より大きな IP アドレス・ブロックを注文する場合に利用してください。 代わりに、独自のオンプレミス・ネットワーク・サブネットを使用して、クラスター・ロード・バランサー・サービス用の追加のポータブル・プライベート IP アドレスを取得することを希望する場合は、[ユーザー管理サブネットをプライベート VLAN に追加することによるポータブル・プライベート IP の追加](#user_managed)を参照してください。

ポータブル・パブリック IP アドレスは、月単位で課金されます。 クラスターをプロビジョンした後にポータブル・パブリック IP アドレスを削除した場合、短時間しか使用していなくても月額料金を支払う必要があります。
{: note}

開始前に、以下のことを行います。
- [アカウントにログインします。 該当する地域とリソース・グループ (該当する場合) をターゲットとして設定します。 クラスターのコンテキストを設定します](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)。
- 不要になったクラスターのサブネットを再使用する場合は、不要なクラスターを削除します。 新規クラスターをすぐに作成します。これは、サブネットを再利用しない場合、サブネットが 24 時間以内に削除されるためです。

   ```
   ibmcloud ks cluster-rm --cluster <cluster_name_or_ID>
   ```
   {: pre}

IBM Cloud インフラストラクチャー (SoftLayer) ポートフォリオにある、カスタム・ファイアウォール・ルールまたは使用可能な IP アドレスが設定された既存のサブネットを使用するには、以下のようにします。

1. 使用するサブネットの ID と、そのサブネットが存在する VLAN の ID を取得します。

    ```
    ibmcloud ks subnets
    ```
    {: pre}

    次の例の出力では、サブネット ID が `1602829`、VLAN ID が `2234945` です。
    ```
    Getting subnet list...
    OK
    ID        Network             Gateway          VLAN ID   Type      Bound Cluster
    1550165   10.xxx.xx.xxx/26    10.xxx.xx.xxx    2234947   private
    1602829   169.xx.xxx.xxx/28   169.xx.xxx.xxx   2234945   public
    ```
    {: screen}

2. 特定した VLAN ID を使用して、[クラスターを作成](/docs/containers?topic=containers-clusters#clusters_cli)します。 新しいポータブル・パブリック IP サブネットと新しいポータブル・プライベート IP サブネットが自動的に作成されないようにするため、`--no-subnet` フラグを指定します。

    ```
    ibmcloud ks cluster-create --zone dal10 --machine-type b2c.4x16 --no-subnet --public-vlan 2234945 --private-vlan 2234947 --workers 3 --name my_cluster
    ```
    {: pre}
    `--zone` フラグについて VLAN がどのゾーンに存在するのかを思い出せない場合、`ibmcloud ks vlans --zone <zone>` を実行して、その VLAN が特定のゾーンに存在するかどうかを確認できます。
    {: tip}

3.  クラスターが作成されたことを確認します。 ワーカー・ノード・マシンがオーダーされ、クラスターがセットアップされて自分のアカウントにプロビジョンされるまでに、最大 15 分かかります。

    ```
    ibmcloud ks clusters
    ```
    {: pre}

    クラスターが完全にプロビジョンされると、**State** が `deployed` に変わります。

    ```
    Name         ID                                   State      Created          Workers    Zone      Version     Resource Group Name
    mycluster    aaf97a8843a29941b49a598f516da72101   deployed   20170201162433   3          dal10     1.12.6      Default
    ```
    {: screen}

4.  ワーカー・ノードの状況を確認します。

    ```
    ibmcloud ks workers --cluster <cluster_name_or_ID>
    ```
    {: pre}

    次のステップに進む前に、ワーカー・ノードが作動可能になっている必要があります。 **State** が `normal` に変わり、**Status** は `Ready` です。

    ```
    ID                                                  Public IP        Private IP     Machine Type   State      Status   Zone     Version
    prod-dal10-pa8dfcc5223804439c87489886dbbc9c07-w1    169.xx.xxx.xxx   10.xxx.xx.xxx  free           normal     Ready    dal10      1.12.6
    ```
    {: screen}

5.  サブネット ID を指定してクラスターにサブネットを追加します。 クラスターでサブネットを使用できるようにすると、使用できるすべての使用可能なポータブル・パブリック IP アドレスが含まれた Kubernetes の構成マップが作成されます。 そのサブネットの VLAN が配置されたゾーンに Ingress ALB が存在しない場合、そのゾーンにパブリック ALB とプライベート ALB を作成するために、1 つのポータブル・パブリック IP アドレスと 1 つのポータブル・プライベート IP アドレスが自動的に使用されます。 そのサブネットからの他のすべてのポータブル・パブリック IP アドレスとポータブル・プライベート IP アドレスは、アプリのロード・バランサー・サービスの作成に使用できます。

  ```
  ibmcloud ks cluster-subnet-add --cluster <cluster_name_or_id> --subnet-id <subnet_ID>
  ```
  {: pre}

  コマンド例:
  ```
  ibmcloud ks cluster-subnet-add --cluster mycluster --subnet-id 807861
  ```
  {: screen}

6. **重要**: 同じ VLAN 上の別のサブネットにあるワーカー間の通信を可能にするには、[その同じ VLAN 上のサブネット間のルーティングを有効にする](#subnet-routing)必要があります。

<br />


## 既存のポータブル IP アドレスの管理
{: #managing_ips}

デフォルトでは、4 つのポータブル・パブリック IP アドレスと 4 つのポータブル・プライベート IP アドレスを使用して、[ロード・バランサー・サービスを作成する](/docs/containers?topic=containers-loadbalancer)ことによって、単一アプリをパブリック・ネットワークまたはプライベート・ネットワークに公開することができます。 ロード・バランサー・サービスを作成するには、使用できる正しいタイプのポータブル IP アドレスが少なくとも 1 つ必要です。 使用できるポータブル IP アドレスを表示することも、使用されているポータブル IP アドレスを解放することもできます。
{: shortdesc}

### 使用可能なポータブル・パブリック IP アドレスの表示
{: #review_ip}

クラスター内のすべてのポータブル IP アドレス (使用中と使用可能の両方) をリスト表示するには、次のコマンドを実行します。
{: shortdesc}

```
kubectl get cm ibm-cloud-provider-vlan-ip-config -n kube-system -o yaml
```
{: pre}

ロード・バランサーの作成に使用できるポータブル・パブリック IP アドレスだけをリスト表示するには、次の手順を使用できます。

開始前に、以下のことを行います。
-  `default` 名前空間に対する[**ライター**または**管理者**の {{site.data.keyword.Bluemix_notm}} IAM サービス役割](/docs/containers?topic=containers-users#platform)があることを確認してください。
- [アカウントにログインします。 該当する地域とリソース・グループ (該当する場合) をターゲットとして設定します。 クラスターのコンテキストを設定します](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)。

使用可能なポータブル・パブリック IP アドレスをリスト表示するには、以下のようにします。

1.  `myservice.yaml` という Kubernetes サービス構成ファイルを作成します。このファイルでは、ダミーのロード・バランサー IP アドレスを使用して `LoadBalancer` タイプのサービスを定義します。 以下の例では、ロード・バランサー IP アドレスとして IP アドレス 1.1.1.1 を使用します。 `<zone>` を、使用可能な IP が存在するかどうかを確認するゾーンに置き換えてください。

    ```
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: myservice
      name: myservice
      namespace: default
      annotations:
        service.kubernetes.io/ibm-load-balancer-cloud-provider-zone: "<zone>"
    spec:
      ports:
      - port: 80
        protocol: TCP
        targetPort: 80
      selector:
        run: myservice
      sessionAffinity: None
      type: LoadBalancer
      loadBalancerIP: 1.1.1.1
    ```
    {: codeblock}

2.  クラスター内にサービスを作成します。

    ```
    kubectl apply -f myservice.yaml
    ```
    {: pre}

3.  サービスを検査します。

    ```
    kubectl describe service myservice
    ```
    {: pre}

    Kubernetes マスターが、指定されたロード・バランサー IP アドレスを Kubernetes 構成マップで見つけることができないため、このサービスの作成は失敗します。 このコマンドを実行すると、エラー・メッセージと、クラスターで使用可能なパブリック IP アドレスのリストが表示されます。

    ```
    Error on cloud load balancer a8bfa26552e8511e7bee4324285f6a4a for service default/myservice with UID 8bfa2655-2e85-11e7-bee4-324285f6a4af: Requested cloud provider IP 1.1.1.1 is not available. The following cloud provider IP addresses are available: <list_of_IP_addresses>
    ```
    {: screen}

<br />


### 使用されている IP アドレスの解放
{: #free}

ポータブル IP アドレスを使用しているロード・バランサー・サービスを削除することによって、使用されているポータブル IP アドレスを解放できます。
{:shortdesc}

開始前に、以下のことを行います。
-  `default` 名前空間に対する[**ライター**または**管理者**の {{site.data.keyword.Bluemix_notm}} IAM サービス役割](/docs/containers?topic=containers-users#platform)があることを確認してください。
- [アカウントにログインします。 該当する地域とリソース・グループ (該当する場合) をターゲットとして設定します。 クラスターのコンテキストを設定します](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)。

ロード・バランサーを削除するには、以下のようにします。

1.  クラスターで使用可能なサービスをリスト表示します。

    ```
    kubectl get services
    ```
    {: pre}

2.  パブリック IP アドレスまたはプライベート IP アドレスを使用しているロード・バランサー・サービスを削除します。

    ```
    kubectl delete service <service_name>
    ```
    {: pre}

<br />


## ポータブル IP アドレスの追加
{: #adding_ips}

デフォルトでは、4 つのポータブル・パブリック IP アドレスと 4 つのポータブル・プライベート IP アドレスを使用して、[ロード・バランサー・サービスを作成する](/docs/containers?topic=containers-loadbalancer)ことによって、単一アプリをパブリック・ネットワークまたはプライベート・ネットワークに公開することができます。 4 つを超えるパブリック・ロード・バランサーまたは 4 つのプライベート・ロード・バランサーを作成するには、クラスターにネットワーク・サブネットを追加することによって、ポータブル IP アドレスをさらに取得することができます。
{: shortdesc}

クラスターでサブネットを使用できるようにすると、このサブネットの IP アドレスは、クラスターのネットワーキングの目的で使用されるようになります。 IP アドレスの競合を回避するため、1 つのサブネットは必ず 1 つのクラスターでのみ使用してください。 あるサブネットを複数のクラスターで使用したり、同時に他の目的で {{site.data.keyword.containerlong_notm}}の外部で使用したりしないでください。
{: important}

ポータブル・パブリック IP アドレスは、月単位で課金されます。 サブネットをプロビジョンした後にポータブル・パブリック IP アドレスを削除した場合、短時間しか使用していなくても月額料金を支払う必要があります。
{: note}

### サブネットをさらにオーダーしてポータブル IP を追加する
{: #request}

ロード・バランサー・サービス用にさらにポータブル IP を取得できます。この取得は、IBM Cloud インフラストラクチャー (SoftLayer) アカウントで新規サブネットを作成し、指定したクラスターでそのサブネットを使用できるようにすることで行います。
{:shortdesc}

開始前に、以下のことを行います。
-  クラスターに対する[**オペレーター**または**管理者**の {{site.data.keyword.Bluemix_notm}} IAM プラットフォーム役割](/docs/containers?topic=containers-users#platform)があることを確認してください。
- [アカウントにログインします。 該当する地域とリソース・グループ (該当する場合) をターゲットとして設定します。 クラスターのコンテキストを設定します](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)。

サブネットを注文するには、以下のようにします。

1. 新しいサブネットをプロビジョンします。

    ```
    ibmcloud ks cluster-subnet-create <cluster_name_or_id> <subnet_size> <VLAN_ID>
    ```
    {: pre}

    <table>
    <caption>このコマンドの構成要素について</caption>
    <thead>
    <th colspan=2><img src="images/idea.png" alt="アイデア・アイコン"/> このコマンドの構成要素について</th>
    </thead>
    <tbody>
    <tr>
    <td><code>cluster-subnet-create</code></td>
    <td>クラスターのためにサブネットをプロビジョンするコマンド。</td>
    </tr>
    <tr>
    <td><code><em>&lt;cluster_name_or_id&gt;</em></code></td>
    <td><code>&lt;cluster_name_or_id&gt;</code> をクラスターの名前または ID に置き換えます。</td>
    </tr>
    <tr>
    <td><code><em>&lt;subnet_size&gt;</em></code></td>
    <td><code>&lt;subnet_size&gt;</code> を、ポータブル・サブネットから追加する IP アドレスの数に置き換えます。 受け入れられる値は 8、16、32、64 です。 <p class="note"> サブネットのポータブル IP アドレスを追加する場合、3 つの IP アドレスはクラスター内ネットワークの確立のために使用されます。 これらの 3 つの IP アドレスは、アプリケーション・ロード・バランサーでは、あるいはロード・バランサー・サービスの作成には使用できません。 例えば、8 個のポータブル・パブリック IP アドレスを要求する場合は、そのうちの 5 個を、アプリをパブリックに公開するために使用できます。</p> </td>
    </tr>
    <tr>
    <td><code><em>&lt;VLAN_ID&gt;</em></code></td>
    <td><code>&lt;VLAN_ID&gt;</code> を、ポータブル・パブリック IP アドレスまたはポータブル・プライベート IP アドレスの割り振り先となるパブリック VLAN またはプライベート VLAN の ID に置き換えます。 既存のワーカー・ノードが接続されているパブリック VLAN またはプライベート VLAN を選択する必要があります。 ワーカー・ノードのパブリック VLAN またはプライベート VLAN を確認するには、<code>ibmcloud ks worker-get --worker &lt;worker_id&gt;</code> コマンドを実行します。 <その VLAN が含まれているゾーンと同じゾーンにサブネットがプロビジョンされます。</td>
    </tr>
    </tbody></table>

2. クラスターにサブネットが正常に作成されて追加されたことを確認します。 サブネットの CIDR は **Subnet VLANs** セクションにリストされます。

    ```
    ibmcloud ks cluster-get --showResources <cluster_name_or_ID>
    ```
    {: pre}

    次の例の出力では、2 番目のサブネットがパブリック VLAN の `2234945` に追加されています。
    ```
    Subnet VLANs
    VLAN ID   Subnet CIDR          Public   User-managed
    2234947   10.xxx.xx.xxx/29     false    false
    2234945   169.xx.xxx.xxx/29    true     false
    2234945   169.xx.xxx.xxx/29    true     false
    ```
    {: screen}

3. **重要**: 同じ VLAN 上の別のサブネットにあるワーカー間の通信を可能にするには、[その同じ VLAN 上のサブネット間のルーティングを有効にする](#subnet-routing)必要があります。

<br />


### ユーザー管理サブネットをプライベート VLAN に追加することによるポータブル・プライベート IP の追加
{: #user_managed}

ロード・バランサー・サービス用にさらにポータブル・プライベート IP を取得できます。この取得は、クラスターでオンプレミス・ネットワークからのサブネットを使用できるようにすることで行います。
{:shortdesc}

代わりに IBM Cloud インフラストラクチャー (SoftLayer) アカウントで既存のポータブル・サブネットを再利用する場合は、[カスタムまたは既存の IBM Cloud インフラストラクチャー (SoftLayer) サブネットを使用したクラスターの作成](#subnets_custom)を参照してください。
{: tip}

要件:
- ユーザー管理のサブネットを追加できるのは、プライベート VLAN のみです。
- サブネットの接頭部の長さの限度は /24 から /30 です。 例えば、`169.xx.xxx.xxx/24` は 253 個の使用可能なプライベート IP アドレスを示し、`169.xx.xxx.xxx/30` は 1 つの使用可能なプライベート IP アドレスを示します。
- サブネットの最初の IP アドレスは、サブネットのゲートウェイとして使用する必要があります。

開始前に、以下のことを行います。
- 外部サブネットとのネットワーク・トラフィックの出入りのルーティングを構成します。
- プライベート・ネットワーク Virtual Router Appliance またはクラスター内で実行されている strongSwan VPN サービスのいずれかと、オンプレミス・データ・センター・ネットワーク・ゲートウェイとの間に VPN 接続があることを確認してください。 詳しくは、[VPN 接続のセットアップ](/docs/containers?topic=containers-vpn)を参照してください。
-  クラスターに対する[**オペレーター**または**管理者**の {{site.data.keyword.Bluemix_notm}} IAM プラットフォーム役割](/docs/containers?topic=containers-users#platform)があることを確認してください。
- [アカウントにログインします。 該当する地域とリソース・グループ (該当する場合) をターゲットとして設定します。 クラスターのコンテキストを設定します](/docs/containers?topic=containers-cs_cli_install#cs_cli_configure)。


オンプレミス・ネットワークからサブネットを追加するには、以下のようにします。

1. クラスターのプライベート VLAN の ID を表示します。 **Subnet VLANs** セクションを見つけます。 **User-managed** フィールドで、_false_ となっている VLAN ID を見つけます。

    ```
    ibmcloud ks cluster-get --showResources <cluster_name>
    ```
    {: pre}

    ```
    Subnet VLANs
  VLAN ID   Subnet CIDR         Public   User-managed
  2234947   10.xxx.xx.xxx/29    false    false
  2234945   169.xx.xxx.xxx/29  true    false
    ```
    {: screen}

2. プライベート VLAN に外部サブネットを追加します。 ポータブル・プライベート IP アドレスがクラスターの構成マップに追加されます。

    ```
    ibmcloud ks cluster-user-subnet-add <cluster_name> <subnet_CIDR> <VLAN_ID>
    ```
    {: pre}

    例:

    ```
    ibmcloud ks cluster-user-subnet-add mycluster 10.xxx.xx.xxx/24 2234947
    ```
    {: pre}

3. ユーザー提供のサブネットが追加されていることを確認します。 **User-managed** フィールドは _true_ です。

    ```
    ibmcloud ks cluster-get --showResources <cluster_name>
    ```
    {: pre}

    次の例の出力では、2 番目のサブネットがプライベート VLAN の `2234947` に追加されています。
    ```
    Subnet VLANs
    VLAN ID   Subnet CIDR       Public   User-managed
    2234947   10.xxx.xx.xxx/29  false    false
    2234945   169.xx.xxx.xxx/29 true     false
    2234947   10.xxx.xx.xxx/24  false    true
    ```
    {: screen}

4. [同じ VLAN 上のサブネット間のルーティングを有効にします](#subnet-routing)。

5. プライベート・ネットワークを介してアプリにアクセスするために、[プライベート・ロード・バランサー・サービス](/docs/containers?topic=containers-loadbalancer)を追加するか、または[プライベート Ingress ALB](/docs/containers?topic=containers-ingress#private_ingress) を有効にします。 追加したサブネットからのプライベート IP アドレスを使用するには、サブネット CIDR からの IP アドレスを指定する必要があります。 使用しない場合は、IP アドレスは IBM Cloud インフラストラクチャー (SoftLayer) サブネット、またはプライベート VLAN 上のユーザー提供のサブネットからランダムに選択されます。

<br />


## サブネットのルーティングの管理
{: #subnet-routing}

1 つのクラスターに複数の VLAN がある場合、同じ VLAN 上に複数のサブネットがある場合、または複数ゾーン・クラスターがある場合は、IBM Cloud インフラストラクチャー (SoftLayer) アカウントに対して[仮想ルーター機能 (VRF)](/docs/infrastructure/direct-link?topic=direct-link-overview-of-virtual-routing-and-forwarding-vrf-on-ibm-cloud#customer-vrf-overview) を有効にして、ワーカー・ノードがプライベート・ネットワーク上で相互に通信できるようにする必要があります。 VRF を有効にするには、[IBM Cloud インフラストラクチャー (SoftLayer) のアカウント担当者に連絡してください](/docs/infrastructure/direct-link?topic=direct-link-overview-of-virtual-routing-and-forwarding-vrf-on-ibm-cloud#how-you-can-initiate-the-conversion)。 VRF の有効化が不可能または不要な場合は、[VLAN スパンニング](/docs/infrastructure/vlans?topic=vlans-vlan-spanning#vlan-spanning)を有効にしてください。この操作を実行するには、**「ネットワーク」>「ネットワーク VLAN スパンニングの管理」**で設定する[インフラストラクチャー権限](/docs/containers?topic=containers-users#infra_access)が必要です。ない場合は、アカウント所有者に対応を依頼してください。 VLAN スパンニングが既に有効になっているかどうかを確認するには、`ibmcloud ks vlan-spanning-get` [コマンド](/docs/containers?topic=containers-cs_cli_reference#cs_vlan_spanning_get)を使用します。

VLAN のスパンニングも必要になる、次の各シナリオを検討してください。

### 同一 VLAN 上の 1 次サブネット間のルーティングを有効にする
{: #vlan-spanning}

クラスターを作成すると、プライマリーのパブリック・サブネットとプライベート・サブネットはパブリック VLAN とプライベート VLAN 上にプロビジョンされます。プライマリー・パブリック・サブネットは `/28` で終わり、14 個のワーカー・ノード用パブリック IP を提供します。プライマリー・プライベート・サブネットは `/26` で終わり、最大 62 個のワーカー・ノード用プライベート IP を提供します。
{:shortdesc}

同じ VLAN 上の同じ場所に単一の大規模クラスターまたは複数の小規模クラスターを配置することで、ワーカー・ノード用に初期提供される 14 個のパブリック IP と 62 個のプライベート IP を超過する場合があります。パブリック・サブネットまたはプライベート・サブネットがワーカー・ノードの上限数に達すると、同じ VLAN 内のもう 1 つのプライマリー・サブネットが注文されます。

同一 VLAN 上のこれらの 1 次サブネット内の各ワーカーが通信できるようにするには、VLAN スパンニングをオンにする必要があります。 手順については、[VLAN スパンの有効化または無効化](/docs/infrastructure/vlans?topic=vlans-vlan-spanning#vlan-spanning)を参照してください。

VLAN スパンニングが既に有効になっているかどうかを確認するには、`ibmcloud ks vlan-spanning-get` [コマンド](/docs/containers?topic=containers-cs_cli_reference#cs_vlan_spanning_get)を使用します。
{: tip}

### ゲートウェイ・アプライアンス用のサブネットのルーティングの管理
{: #vra-routing}

クラスターを作成すると、そのクラスターが接続されている VLAN 上にポータブル・パブリック・サブネットとポータブル・プライベート・サブネットがオーダーされます。 これらのサブネットは、Ingress ネットワーク・サービスとロード・バランサー・ネットワーク・サービスに IP アドレスを提供します。
{: shortdesc}

ただし、[Virtual Router Appliance (VRA)](/docs/infrastructure/virtual-router-appliance?topic=virtual-router-appliance-about-the-vra#about-the-vra) などの既存のルーター・アプライアンスが存在する場合は、そのクラスターが接続されている VLAN から新しく追加されたポータブル・サブネットはそのルーター上で構成されません。 Ingress ネットワーク・サービスまたはロード・バランサー・ネットワーク・サービスを使用するには、[VLAN スパンニングの有効化](/docs/infrastructure/vlans?topic=vlans-vlan-spanning#vlan-spanning)によって、各ネットワーク・デバイスが同じ VLAN 上の異なるサブネット間でルーティングできるようにする必要があります。

VLAN スパンニングが既に有効になっているかどうかを確認するには、`ibmcloud ks vlan-spanning-get` [コマンド](/docs/containers?topic=containers-cs_cli_reference#cs_vlan_spanning_get)を使用します。
{: tip}
