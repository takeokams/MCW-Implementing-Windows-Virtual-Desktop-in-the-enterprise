![マイクロソフト クラウド ワークショップ](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "マイクロソフト クラウド ワークショップ")

<div class="MCWHeader1">
エンタープライズでの Windows Virtual Desktop の実装
</div>
<div class="MCWHeader2">
「ハンズオン ラボの前に」設定ガイド
</div>
<div class="MCWHeader3">
2020 年 9 月
</div>
このドキュメントに記載されている情報 (URL 等のインターネット Web サイトに関する情報を含む) は、将来予告なしに変更されることがあります。特に断りがない限り、ここで使用している会社、組織、製品、ドメイン名、電子メール アドレス、ロゴ、人物、場所、イベントの例は、架空のものであり、実在する会社、組織、製品、ドメイン名、電子メール アドレス、ロゴ、人物、場所、イベントなどとは一切関係ありません。お客様ご自身の責任において、適用されるすべての著作権関連法規に従ったご使用を願います。このドキュメントのいかなる部分も、米国 Microsoft Corporation の書面による許諾を受けることなく、その目的を問わず、どのような形態であっても、複製または譲渡することは禁じられています。ここでいう形態とは、複写や記録など、電子的な、または物理的なすべての手段を含みます。ただしこれは、著作権法上のお客様の権利を制限するものではありません。

マイクロソフトは、このドキュメントに記載されている内容に関し、特許、特許申請、商標、著作権、またはその他の無体財産権を有する場合があります。別途マイクロソフトのライセンス契約上に明示の規定のない限り、このドキュメントはこれらの特許、商標、著作権、またはその他の無体財産権に関する権利をお客様に許諾するものではありません。

製造元や製品の名前、URL は情報の提供のみを目的としており、マイクロソフトは、これらの製造元、またはマイクロソフトの技術での製品の使用について、明示的、黙示的、または法的にいかなる表示または保証も行いません。製造元または製品の使用は、マイクロソフトによるその製造元または製品の推奨を意味するものではありません。サード パーティのサイトへのリンクが提供されている場合があります。このようなサイトはマイクロソフトの管理下にはなく、マイクロソフトは、リンクされたサイトの内容またはリンクされたサイトに含まれるリンク、あるいはこのようなサイトの変更または更新について責任を負いません。マイクロソフトは、リンクされたサイトから受信された Web キャストまたは他のいかなる形態の転送にも責任を負いません。マイクロソフトは、これらのリンクを便宜のみを目的として提供しており、いかなるリンクの使用も、マイクロソフトによるサイトまたはそこに含まれる製品の推奨を意味するものではありません。

© 2020 Microsoft Corporation.All rights reserved.

Microsoft および <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> に記載されている商標は、Microsoft グループの商標です。その他すべての商標は、該当する各社が所有しています。

**目次**

<!-- TOC -->

- [エンタープライズでの Windows Virtual Desktop の実装のための「ハンズオン ラボの前に」設定ガイド](#エンタープライズでの-windows-virtual-desktop-の実装のためのハンズオン-ラボの前に設定ガイド)
    - [必要条件](#必要条件)
    - [ハンズオン ラボの前に](#ハンズオン-ラボの前に)
        - [タスク 1: Azure インフラストラチャと AD DS をデプロイする](#タスク-1-azure-インフラストラチャと-ad-ds-をデプロイする)
        - [タスク 2: ARM テンプレートをデプロイする](#タスク-2-arm-テンプレートをデプロイする)

<!-- /TOC -->
# エンタープライズでの Windows Virtual Desktop の実装のための「ハンズオン ラボの前に」設定ガイド

## 必要条件

Windows Virtual Desktop ワークスペースの設定を開始する前に、次の項目が用意されていることを確認します。

- Windows Virtual Desktop ユーザー用の Azure Active Directory のテナント ID。

- Azure Active Directory テナント内の全体管理者アカウント。
  
  - これは、顧客のために Windows Virtual Desktop ワークスペースを作成するクラウド ソリューション プロバイダー (CSP) の組織にも適用されます。もしお客様が CSP 組織に属している場合は、顧客の Azure Active Directory テナントの全体管理者としてサインインできる必要があります。
  
  - 管理者アカウントは、Windows Virtual Desktop ワークプレースの作成先の Azure Active Directory テナントから供給する必要があります。このプロセスでは、Azure Active Directory B2B (ゲスト) アカウントはサポートされません。
  
  - 管理者アカウントは、職場または学校アカウントである必要があります。

- Azure サブスクリプション。
  
  - 4 台の 4 コア サーバーを構築するのに十分な数のクォータ コア。
  
  - 新規または既存の Azure Active Directory テナントのための Azure Active Directory 全体管理者アカウントへのアクセス許可。
  
  - すべての Azure サブスクリプションに対する所有者の権限。

## ハンズオン ラボの前に

所要時間: 45 分

### タスク 1: Azure インフラストラチャと AD DS をデプロイする

このタスクでは、カスタムの Azure Resource Manager (ARM) テンプレートを活用して、WVD に必要な Active Directory ドメイン サービスのインフラストラクチャをデプロイします。既に AD DS 環境と AD Connect を構成している場合は、**段階的ハンズオン ラボ、「演習 4: AD DS で Azure AD Connect を構成する」に進むことができます。**

1. ARM テンプレートへのリンク: [GitHub からの AAD ハイブリッド ラボの ARM テンプレート](https://github.com/PeterR-msft/M365WVDWS/tree/master/AAD-Hybrid-Lab)

この ARM テンプレートによって、次のリソースがプロビジョニングされます。

- 仮想ネットワーク
  
  - 1 サブネット
  
  - 1 ネットワーク セキュリティ グループ (NSG)
    
    - AD トラフィックを許可、RDP 受信トラフィックを許可、DMZ アクセスを制限。
  
  - ドメイン コントローラーを指すよう構成された DNS

- 仮想マシン
  
  - Active Directory ドメイン サービスのインストールと設定
  
  - テスト ユーザーをドメインに作成
  
  - Azure AD Connect をインストールし、構成前の状態
  
  - RDP を介してリモート管理できるパブリック IP アドレスの割り当て

### タスク 2: ARM テンプレートをデプロイする

> **ヒント**: マイクロソフト社内では、異なるテナント ディレクトリが利用可能です。このため、GitHub 上でホストされた ARM テンプレートのデプロイメントの途中でスタックすることは珍しくありません。混乱とデプロイ エラーを回避するために、GitHub から ARM テンプレートをデプロイする場合は、**InPrivate ブラウザー ウィンドウを開いて**ください。

1. "InPrivate" または "Incognito" モードを使用してブラウザーを開くと、次の ARM テンプレートにナビゲートされます。[GitHub からの AAD ハイブリッド ラボの ARM テンプレート](https://github.com/PeterR-msft/M365WVDWS/tree/master/AAD-Hybrid-Lab)

2. デプロイメントの前に、ARM テンプレートを使用するための GitHub ページ上の記載を確認してください。

3. \[クイック スタート\] ページで、\[Deploy to Azure\] をクリックします。これにより、カスタム デプロイのための Azure Portal の新しいブラウザー タブが開きます。 
   
   ![GitHub 内の \[Deploy to Azure\] ボタンのスクリーンショット](images/1.png "[Deploy to Azure] ボタン")

4. プロンプトが表示される場合は、Azure サブスクリプションの所有者であるアカウントでサインインするようにしてください。

5. 必要な ARM テンプレート パラメーターを入力します。パラメーターの詳細については、以下の例を参照してください。
   
   - 新しい**リソース グループ**を作成する
   - **リージョン**を選択する
   - **管理者パスワード**を作成する
   - **(Mycompany).local** などの **AD ドメイン名**を作成する
   - **"mywvd"** などの WVD の **Customupnsuffix** を作成する
   - **既定のユーザー パスワード**を作成する
   - \[Review + create\] を選択する
   
   > 注: これらの入力内容は、ステップバイステップ ハンズオン ラボを実施する際に参照する必要があるため、必ず保存するようにしてください。
   
   ![この画像は、WVD ドメイン コントローラーのセットアップのためのカスタム テンプレートに入力する値を示しています。](images/wvdcustomdeployment.png "Windows Virtual Desktop カスタム デプロイ テンプレート")

6. 使用条件に同意して、\[作成\] をクリックします。
   
   デプロイメントが進行中です。このプロセスが完了するまでに平均 30 分かかります。デプロイの進行状況を監視して、問題がないことを必ず確認してください。右上隅にある**通知**ベルをクリックして、\[Deployment in progress...\] をクリックすると、進行状況を監視できます。
   
   > 注: 自動化によって、作業をシンプルにして再現性も高くすることができますが、失敗する場合もあります。ARM テンプレートのデプロイ中に障害が発生した場合は、障害を確認し、リソース グループを削除して、ARM テンプレートを再試行してください。エラーが発生する場合は調整を行います。
   
   ARM テンプレートがデプロイされたら、ステータスが完了に変わります。この時点で、ドメイン コントローラーで RDP 接続の準備が整っています。

このハンズオン ラボを実行する "前に"、指定されているすべてのステップに従う必要があります。