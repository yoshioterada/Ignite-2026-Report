# BRK413 Govern, secure, and operate anywhere with Azure Arc

## 1. はじめに

このセッションは、Azure Arc を使って、オンプレミス、他のクラウド、エッジに分散したサーバー、Kubernetes、データベースを、Azure の管理・ガバナンス・セキュリティの仕組みでまとめて運用する方法を紹介するものです。構成は、「分散した資産を 1 か所に集める（インベントリ）」「一貫した運用を大規模に行う」「アプリとデータを Arc で動かす」の 3 部です。各部で最新の発表内容を紹介し、Megan McCrory 氏が 3 つのデモを行います。

後半では、**Database Hub in Microsoft Fabric の一般提供（GA）**が発表されました。Arc に接続した SQL Server を含むデータベースを、フリートとして 1 か所で運用するためのものです。

セッション URL: [https://ignite.microsoft.com/en-US/sessions/BRK413](https://ignite.microsoft.com/en-US/sessions/BRK413)\
YouTube URL: [https://www.youtube.com/watch?v=PJAIviKe8pY](https://www.youtube.com/watch?v=PJAIviKe8pY)

## 2. 発表者

| 氏名 | 所属 | 役職 |
| ---- | ---- | ---- |
| Vlad Joanovic | Microsoft | Product manager |
| Meagan McCrory | Microsoft | Product Manager |

冒頭の自己紹介から、次のことが分かります。

- Vlad 氏：Microsoft のコントロールプレーンと管理サービスを担当するプロダクトマネージャーで、Microsoft に 27 年在籍しています。スライドを使った説明を担当しています。
- Megan 氏（トランスクリプトの綴りは "Megan McCrory"）：Azure Arc チームのプロダクトマネージャーで、Microsoft に 12 年在籍しています。3 つのデモを担当しています。

## 3. 何に関する発表か

Azure Arc は、Azure のコントロールプレーンである Azure Resource Manager（ARM）を、Azure の外にあるリソースにまで広げるための仕組みです。発表の流れは次のとおりです。

1. Arc が必要になる背景と、仕組み（ARM をコントロールプレーンとして使う）
2. デモ 1：Arc によるインベントリ（Configuration Manager、GCP との接続、リソースの検索、Essential Machine Management）
3. デモ 2：日々の運用チェックリスト（監視、拡張機能の失敗、Kubernetes のアップグレード、アプリの配布、TPM、ホットパッチ、CIS ベンチマーク、Defender）
4. SQL Server と Arc、Database Hub in Fabric
5. デモ 3：Database Hub とデータベースエージェント
6. 関連リソースの案内と Q&A

## 4. 発表者が伝えたいメッセージ

「インフラは分散しているが、コントロールプレーンは分散させるべきではない」（セッション概要）というのが主題です。Vlad 氏は、ARM で 1 つのコントロールプレーンと運用モデルを持てば、運用、セキュリティ、構成のデータを大規模に照会して対処でき、脆弱性やコンプライアンスのずれ（drift）を速く解消できると説明しています。

背景として、Gartner の予測が紹介されています。

| 予測 | 内容 |
| ---- | ---- |
| エンタープライズが管理するデータ | 現在の 27% から 50% へ増える |
| クロスクラウド統合（2027 年） | 50% の組織が求める |

Vlad 氏は、顧客が直面している課題として、ツールとチームのサイロが非効率とリスクを生むことを挙げています。リソースが動的なクラウドでは、リアルタイムに更新されるインベントリ（顧客が CMDB と呼ぶもの）が必要です。また、オンプレミスはあるチームが、クラウドは別のチームが管理しているといった組織の課題もあります。そこで、運用の手間（toil）を減らし、セキュリティを高めるために、1 組の標準と 1 組のシステムが必要だという考えです。

## 5. 発表内容の詳細

### 5.1 Arc の仕組み

Azure Resource Manager は、Azure の「グローバルなコントロールプレーン」で、Azure 上のあらゆるリソースの作成、更新、構成を 1 つの管理エンドポイントで行えるようにしたものです。Arc はこれを Azure の外に広げます。Azure 外のサーバーや Kubernetes クラスターなどのリソースに対して、Arc が「コネクターとなるインフラ」として、他のエンドポイントとやりとりします。アカウントとアクセス権が必要で、その環境と Azure の間のネットワーク接続も必要です。これによってリソースが検出され、最新の状態に保たれます。

Azure と同じ概念を、管理対象のすべてに使えます。

- インベントリと整理：Azure Resource Graph、management groups、tags、そして Ignite で GA になった **service groups**
- 変更の適用：共通のアップデート管理、構成の適用と強制（Azure、他のクラウド、オンプレミスのすべての Kubernetes クラスター）
- セキュリティと可観測性：共通の Entra ID によるアクセス、Privileged Identity Management（常時付与のアクセスをなくす）、Defender、Azure Monitor
- デプロイ：Bicep、deployment stacks、Terraform

利用状況として、Vlad 氏は 45,000 を超える顧客が Azure（文脈上 Arc）を利用し、2,000 万を超えるリソースを管理していると紹介しています。活用場面としては、支店、店舗、製造や倉庫などの OT 環境で接続したいマシンが挙げられています。

### 5.2 インベントリ（デモ 1 の内容）

Megan 氏は、Caldova Pharmaceutical Company（トランスクリプト上の綴り）の担当者という設定で、「環境をすべて把握し、安全、準拠、監視された状態を保つ責任がある」立場からデモを行います。デモの手順は 6.1 に記載します。ここでは、関連する発表内容を整理します。

| 機能 | 状況 | 内容 |
| ---- | ---- | ---- |
| On-prem connector（Configuration Manager） | 提供状況は明言されていません（Q&A の質問では "coming" と表現） | Configuration Manager のサイトサーバーを Azure に接続し、管理下のマシンをインベントリとして確認できる（inventory only モード） |
| Machine inventory | プレビュー | サーバーと VM に加え、パスワード、グループ、証明書、アプリケーション、サービスを追跡できる。Arc-enabled server と Azure VM が対象 |
| Multi-cloud connector（GCP） | 数日前に GA | GCP のリソースを読み取り専用で Azure に反映する。AWS コネクターも作成可能 |
| Azure Copilot と ARM MCP server | 利用例を紹介 | 自然言語でインベントリを照会する |
| Essential Machine Management（EMM） | 紹介 | サブスクリプション単位で管理サービスをまとめて有効化する |

マルチクラウドコネクターで接続すると、次のことができると説明されています。

- インベントリ：ストレージアカウント、VM、ネットワークなどのメタデータが Azure Resource Graph に反映される。
- GCP や AWS の VM、Kubernetes クラスターを Arc enable する（どちらも現在利用可能）。
- ライセンスの大規模な適用：ESU（拡張セキュリティ更新）の対象マシンを検出し、最初から ESU ライセンスを構成できる。
- セキュリティベースライン：他のパブリッククラウド上のマシンが、割り当てたベースライン（CIS や Azure compute baselines）に準拠しているか確認できる。

**Essential Machine Management（EMM）** は、サブスクリプションを登録すると、その中の Azure VM と Arc-enabled server（現在のマシンも、明日デプロイされるマシンも）に、次のサービスをまとめて有効にします。

- Azure Monitor
- Policy と Machine Configuration
- Azure Update Manager
- Change Tracking and Inventory

料金は次のとおりです。

| 対象 | 料金 |
| ---- | ---- |
| Azure VM | 無料 |
| Arc-enabled server | 1 サーバーあたり月 $9 |
| 例外 | Defender for Server Plan 2 を使う場合は追加費用なし。Software Assurance や Azure PayGo などのライセンス特典があれば、Azure benefits を有効化して、EMM が有効にするサービスを含め追加費用なしで利用できる |

### 5.3 運用・ガバナンス・セキュリティ（デモ 2 の内容）

Vlad 氏は、インベントリは最初の一歩で、次は何かが壊れたときに見つけて素早く直すことだと位置づけ、Megan 氏に引き継ぎます。デモ 2 では、Copilot のプロンプトで作った「運用チェックリスト」に沿って確認が進みます（手順は 6.2）。取り上げられた新しい機能は次のとおりです。

| 機能 | 状況・内容 |
| ---- | ---- |
| Arc-enabled Kubernetes の upgrade insights | プレビュー。アップグレード前のプレチェックのように、互換性の問題を確認できる |
| VM applications（サードパーティアプリの配布） | Azure VM では以前から対応しており、Arc でも利用可能に。Azure Policy で大規模に適用できる |
| TPM-backed identity | プレビュー |
| Auto agent upgrade | Arc エージェントの自動アップグレード。Azure Policy でサブスクリプション全体に適用できる |
| Hotpatch | Windows Server 2025 で無料で利用可能。ダウンタイムなしでセキュリティ更新を適用できる |
| Linux 向け CIS ベンチマーク（Machine Configuration） | Azure security baselines と CIS。L1 と L2 のサーバープロファイルに対応。修復（remediation）モードと監査のみのモードがある |

Megan 氏は、これらによって、以前は AWS や GCP のポータル、SCCM に個別にログインしていた作業を、Azure で一元的に行えるようになると説明しています。顧客事例として、Levi's が Arc を使っている顧客の 1 つに挙げられています（具体的な成果は触れられていません）。

### 5.4 SQL Server と Arc

Vlad 氏は、Arc が多くの Microsoft サービスの土台になっているとして、まず SQL Server を取り上げます。Azure Virtual Desktop でも Arc が基盤の役割を果たし、サーバーに Azure Virtual Desktop Agent extension をインストールして AVD に参加させられると述べています。

Arc は、データセンター、エッジ、クラウドのどこにある SQL Server にも Azure の管理を拡張します。現在、**100 万を超える SQL Server インスタンス**が Arc 経由で接続されています。接続すると、次の効果があります。

- SQL Server の情報が 1 か所に集まる。
- サーバーと同じセキュリティとガバナンスのツール（Defender、Policy、Entra、Update Manager）をデータベースにも適用できる。
- 大規模な監視ができ、可用性グループやフェールオーバークラスターインスタンスといったビジネス継続性の機能が引き続き動作する。
- すべての SQL Server が見える単一のビュー、組み込みのパフォーマンス監視、ベストプラクティスの自動チェックが得られる。
- Defender と自動パッチ適用でセキュリティを保てる。ESU も有効にできる。SQL Server 2016 は今年 7 月に延長サポートが終了しました。
- モダナイズの際、Arc が継続的に SQL Server を評価し、Azure 上の移行先とサイズの推奨を提示する。移行判断を推測ではなくデータに基づいて行える。
- 課金面では、Software Assurance を前払いではなく、時間単位で支払う選択肢がある（任意）。

### 5.5 Database Hub in Microsoft Fabric（GA）

数百から数千のデータベースを管理する顧客の課題として、Vlad 氏は次の 3 点を挙げています。

1. **データベースの健全性**：シグナルが分散し、環境をまたいで複数のエンジンがあるため、顧客が影響を感じてから問題が表面化する。
2. **大規模な監視**：1 つのデータベースで機能した方法が、100 のデータベースでは通用しない。
3. **セキュリティとガバナンス**：データベースが増えるほど攻撃面が広がり、ポリシー、ドリフト、パッチ適用が不統一になり、コンプライアンスが手作業でコストがかかる。

その解決策が **Database Hub in Microsoft Fabric** で、今日 GA が発表されました。データベース全体を 1 か所で運用できます。対象には、Azure SQL、VM 上の SQL Server、Fabric databases、Cosmos、Postgres などの各種データベースサービス、そして Arc で接続したすべての SQL Server（他のクラウドやオンプレミスで動いているものを含む）が含まれます。

> 注意：データベースを移動する必要はありません。Fabric に移行されるものはなく、Database Hub は Azure Resource Manager と Azure Resource Graph、既存のサブスクリプション、リソースグループ、権限をそのまま使います。

Database Hub が束ねる 3 つの要素は次のとおりです。

- **フリート管理**：全体を 1 つのビューで見て整理する。
- **オブザーバビリティ**：全体の健全性とパフォーマンスを監視する。
- **データベースエージェント**：AI が、フリート全体の問題の発見と修正を支援する。

Vlad 氏は、データベースエージェントを「データの常時稼働アシスタント」と位置づけ、Vlad 氏が "my VP" と呼ぶ Brendan Burns 氏の「エージェントは疲れず、飽きない」という言葉を紹介しています。IT で最も多い答えは「状況による（it depends）」で、エージェントは、負荷特性などによる違いの細部を見ていくのに向いているという説明です。

このエージェントによって、管理を事後対応から事前対応へ（shift left）、そして時間をかけて、より自律的なものへと進めていきます。エージェントに何を許可し何を許可しないかは、利用者が決めます。ガイド付きのアクション（guided actions）が提供され、DB の専門家はアラートの調査に追われる時間が減り、事業を前に進める仕事に時間を使えるようになります。

デモの手順は 6.3 に記載しています。デモの後、Megan 氏は、データベースエージェントのプレビューは **Azure SQL Database 向けに public preview** であること、SSMS と Azure に組み込まれたネイティブなツールでガイド付きアクションを実行できることを説明しています。

### 5.6 関連リソースの案内

- 過去 6〜12 か月の更新をまとめたスライド（細部は 1 つずつ説明されていません）。
- Azure Arc のブログ（QR コード）。機能の説明とドキュメントへのリンクがある。
- Arc Server Forum：毎月開催され、今後の予定を聞けるほか、ロードマップに意見を反映できる。参加登録は冒頭と同じ QR コード。
- ラボ：翌朝にもう 1 回ある。今日説明した機能の多くを試せる。ARM MCP server も含まれる。
- ブース：Private cloud, hybrid and edge ブースに、その日の午後と翌日に在席する。

デモで使ったダッシュボードを作ったプロンプトは、セッションの最後に共有すると Megan 氏が述べています。

### 5.7 Q&A

**質問 1：SCCM コネクターの導入は Azure Arc Gateway に対応していますか。**
回答：対応しています。サイトサーバーへの接続時に Azure Arc Gateway を利用できます。

**質問 2：現在は限定的に Arc を使っており、SCCM をやめて Update Manager と Arc-enabled server に移行したい。ただしサードパーティの更新を簡単に管理する方法がない。Intune のブースで、Update Manager の代わりに、Intune が Windows Server 2025 の Arc-enabled server のサードパーティ更新に、1 月ごろ対応するかもしれないと聞いたが本当か。**

回答の要点は次のとおりです。

- 現在、多くの Config Manager 環境が、サーバーとクライアントの両方を管理している。Arc は、Azure に慣れた顧客がサーバーを Arc に接続し、Config Manager や Intune 型のエンドポイント管理から外していくために作った。
- Azure と Config Manager を使っていても、別々のチームで、片方のチームは Azure サブスクリプションの取得も認められておらず、知識もない場合がある。そのため Intune チームと協力して、サーバーを Intune にも接続できるようにしている。
- それでも、Intune ではできないことが多いため、Arc への接続を勧めたい。Arc では、infrastructure as code を使えて、サーバーを単なるサーバーから、コンピュートファブリックの一部へと進められる。将来は、他の環境のサーバーも Azure に接続し、Kubernetes クラスターのようにアプリをデプロイできる状態を目指している。
- Intune は、サーバーをクライアントのような「エンドポイント」として管理する。Intune にクライアントを集約していて、Config Manager に残っているのがサーバーだけの顧客は、まずサーバーを Arc に接続するのが第一の選択肢。それが負担の大きい作業になる場合は、Intune への接続も考えられる。Intune が、サーバーでもクライアントと同じエンドポイント管理を可能にする方向で取り組んでいる。
- サードパーティ更新がいつ対応するかについては、「Intune チームに直接確認が必要」とされ、1 月かどうかについて、発表では確認も否定もされていません。

## 6. デモ・手順

このセッションでは 3 つのデモが行われました。録音は音声のみのため、画面の細部は音声からは確認できず、以下は語られた範囲に基づきます。

### 6.1 デモ 1：インベントリ（Megan 氏）

1. Azure portal の検索バーで「Arc」を入力し、**Azure Arc Center** を開きます。
2. 「all resources」タブで、すでに登録されているリソースを確認します。Azure Local サーバー、Kubernetes クラスター（AWS 上、GCP 上のものを含む）、Azure Arc servers（AWS、GCP、Windows、Linux に対応）が見えます。
3. 「supported environments」の下の **On-prem connectors** で、Configuration Manager（SCCM）のコネクターを確認します。1 つ接続済みです。作成する場合は create ボタンから、サイトサーバーの詳細を入力して、Microsoft Configuration Manager のサイトサーバーを Azure に接続します。
4. 作成済みのリソース（リソースタイプはサイトサーバー）を開くと、エージェントの接続状態が connected で、リンクされたデバイスが 4 台あります。4 台を開くと、サイトサーバーが管理しているマシンの一覧が見えます。SCCM で VM を新規に立てると、自動的にコネクター側にも表示されます。マシンを開くと inventory only モードで、更新の配信などはサイトサーバーが制御します。
5. **Machine inventory**（プレビュー）を開くと、SCCM 由来のサーバーと VM に加え、パスワード、グループ、証明書、実行中のアプリケーション、サービスが見えます（"passwords" と聞こえますが、音声認識の都合で表記が確定できません）。
6. 同じ supported environments の **Multi-cloud connectors** で、create ボタンから AWS コネクターまたは GCP コネクターを作れることを示します。すでに GCP コネクターを作成済みで、GCP プロジェクト番号が表示され、インベントリ（読み取り専用）が有効です。
7. **Resources** タブで、収集されたリソースを確認します。接続されたクラスター、複数のマシン、ストレージアカウントがあります。ファイルサーバーを開くと、接続された Arc マシンで、状態は connected です。更新の配信など、他のパブリッククラウドで通常行う作業を Azure から行えます。
8. **Resource Graph Explorer** で KQL を書き、「ハイブリッドコンピューターのマシンと Azure VM を kind で並べ替える」クエリを実行します。AWS、GCP、Azure で動いている数が得られます。
9. Copilot（ARM MCP server に対応した Azure Copilot）を開き、「環境ごとに、Azure VM と Arc-enabled server は何台あるか」と質問した結果を示します（実行済みのもの）。Copilot は「環境」が何を指すか尋ね返し、「kind フィールドを使って」と指示すると、AWS、GCP、VMware、SCVMM、HCI などごとの台数を、すべてのサブスクリプションについて返しました。さらに「Caldova のサブスクリプションだけに絞って」と指示すると、そのデータも取得できました。
10. Copilot のプロンプトで作ったダッシュボードを示します。現在は Megan 氏のマシン上でローカルにホストされており、Azure に公開することもできます。Arc サーバー数、Kubernetes クラスター数、接続状況、Azure Advisor の推奨が表示され、接続済みのマシンだけに絞り込むこともできます。
11. 最後に **Essential Machine Management**（compute infrastructure の検索から「monitoring and operations」の下にあります）を示します。サブスクリプションを登録すると、そこにある（これから追加される）すべてのマシンに、Azure Monitor、Policy と Machine Configuration、Update Manager、Change Tracking and Inventory が有効になります。

### 6.2 デモ 2：運用チェックリスト（Megan 氏）

デモ 1 で作ったダッシュボードから始め、Copilot のプロンプトで作った「運用チェックリスト」の項目を順に確認します。

1. **パフォーマンス監視の逸脱**：Copilot が ARM MCP server を使い、全マシンの CPU 使用率とメモリ使用率を表示します。EMM で監視が有効になっているため、データが得られます。約 30 分前にネットワークのスパイクがあり、調査が必要ですが、同僚に引き継ぐかもしれないとして、先に進みます。
2. **EMM の失敗やポリシー違反**：EMM ダッシュボードで、Caldova のサブスクリプションを確認します。ポリシー関連の問題を抱えるマシンが多数あり、拡張機能が失敗している（またはインストールされていない）マシンが 11 台あります。そこから失敗の一覧を開き、Ubuntu Web のマシンを選ぶと、MDE Linux 拡張機能が失敗しています。
   **Get Copilot help** を押すと、Copilot が「MDE エージェントが正常でないため、Defender for Endpoint を構成できなかった」というエラーを見つけ、考えられる理由、トラブルシューティングの推奨、最も可能性の高い根本原因、次に確認すべき最速の手順を示します。これは後で戻るメモとして残します。
3. **Arc-enabled Kubernetes のアップグレード insights**：Arc ポータルの Kubernetes クラスターで、クラスターを選んで upgrade insights を有効にします。有効にした後、upgrade insights（プレビュー）で、アップグレード前に注意すべき点（プレチェックのようなもの）を確認します。3 つの問題があり、component compatibility に critical なものがあります。複数のクラスターで CoreDNS コンポーネントに互換性がなく、アップグレードすると壊れます。そのため、アップグレードは保留して、CoreDNS の修正を先に行うと説明しています。
4. **アプリのデプロイ（サードパーティアプリの配布）**：PHP（Ubuntu）を最新版にしたい、という場面です。Azure portal のマシンのページで、OS でフィルターし（Ubuntu 20.04 が多い）、4 台のマシンを選んで「add applications」を押します。ギャラリーに追加済みの PHP Ubuntu のアプリを選び、最新版を指定します。この 4 台が成功したら、残りの環境にも展開できます。Azure Policy を使えば大規模にも適用できます。
5. **TPM の構成と Arc エージェントの自動アップグレード**：Arc ポータルで 1 台のマシンを開き、エージェントの状態を確認します。TPM-backed identity（プレビュー）はすでに有効です。auto agent upgrade は未有効なので、このマシンで有効にします。さらに Azure Policy の Arc のカテゴリに「Configure Azure Arc servers to enable automatic upgrades」があり、サブスクリプション全体に割り当てることで、すべての Arc サーバーに適用できます。
6. **ホットパッチ**：Windows Server 2025 向けのホットパッチ（無料）を、このマシンで有効にします。Windows 側のセキュリティ更新がダウンタイムなしで適用されます。
7. **Linux の CIS ベンチマーク（Machine Configuration）**：Machine Configuration のポータルの security baselines で、Windows と Linux の Azure security baselines と CIS が見られます。Linux の CIS を選んで「modify settings」を押し、Ubuntu を選んで next に進みます。設定を変更でき、設定ごとに remediation モード（誤っていれば自動修正）か、監査のみかを選べます。例として、会社の要件で SSHD の最大リトライ回数（現在のしきい値は 4）を 2 に変更します。変更後にポリシー割り当てを作成し、管理グループ、サブスクリプション、リソースグループのいずれかのスコープに割り当てます。
8. **Defender の状態**：Defender ポータル（security.microsoft.com）を開き、assets の cloud で、AWS の資産、オンプレミスの Azure（Arc）の資産、GCP のインスタンスを確認します。cloud security overview で、セキュリティ態勢が 40% と表示されています。重大な修復とアラートを確認して投資すべきだという流れで、デモを終えます。

### 6.3 デモ 3：Database Hub とデータベースエージェント（Megan 氏）

1. Azure portal で、すでにデプロイ済みの SQL データベースを開き、**Database Hub で開きます**。
2. 概要の画面で、セキュリティに影響する問題、パフォーマンスと最適化に関する問題を確認します。
3. 新しい **Manage agent** から、データベースエージェントの設定を開きます。このデータベースでは、すでに有効（active）です。SQL データベースのインスタンスは現在 1 つですが、複数ある場合は大規模に有効化できます。
4. エージェントに追跡させる項目を設定します。blocking と deadlock、長時間実行クエリのクエリパフォーマンス、インデックスのチューニング、リソース圧迫（CPU、メモリ、高 IO）です。これらを有効にして適用します。
5. **Agent preview** を開くと、エージェントが検出した 3 件が表示されます。blocking の問題（音声では "blockchain" と聞こえますが、文脈上は blocking のことです）、CPU 高騰、長時間実行クエリです。
6. blocking の問題を開くと、head blocker のクエリが確認できます。現在ブロックされているものを見られます。Azure と SQL Server Management Studio（SSMS）が統合されており、SSMS でクエリを直接開いて詳細を確認できます。また、発生した時刻（タイミング）と、head blocker のクエリの出どころも表示されます。
7. analysis ページで、過去 24 時間の履歴を確認します。いつ検出されたか、何件か、まだ問題が続いているかが分かります。Megan 氏は、次のステップとして、検出されたクエリを実行して blocking の問題をさらに調べる、と述べています。
8. Database Hub では、SQL、Postgres、Cosmos DB などすべてのデータベースが 1 か所に表示されることを示します。

## 7. 注意点・発表者の気付き

- **提供状況の違い**：Database Hub in Fabric は GA、GCP コネクターは数日前に GA、service groups は Ignite で GA です。Machine inventory、Kubernetes の upgrade insights、TPM-backed identity はプレビュー、データベースエージェントは Azure SQL Database 向けの public preview です。
- **Database Hub では何も移行されません**：既存の ARM、Resource Graph、サブスクリプション、権限をそのまま使います。
- **EMM の課金**：Azure VM は無料ですが、Arc-enabled server は 1 台あたり月 $9 です。Defender for Server Plan 2 の利用時や、Software Assurance、Azure PayGo のライセンス特典がある場合は、追加費用なしで使えます。
- **アップグレード前の確認**：デモでは、upgrade insights で CoreDNS の非互換が見つかり、アップグレードを保留する流れが示されました。
- **オンプレミス接続の前提**：Arc では、接続先の環境から Azure へのネットワーク接続と、アカウントとアクセス権が必要です。
- **Configuration Manager コネクターの位置づけ**：マシンは inventory only モードで、更新の配信などはサイトサーバーが引き続き制御します。
- **サードパーティ更新と Intune**：Q&A で、Intune による対応時期は発表者からは明言されていません（5.7 を参照）。
- **エージェントの権限**：データベースエージェントに何を許可し、何を許可しないかは、利用者が決められます。

## 8. 利用者にとっての利点

- **1 つのコントロールプレーン**：オンプレミス、他のクラウド、Azure のリソースを、同じ Azure の概念（Resource Graph、management groups、tags、service groups）で扱えます。
- **インベントリの一元化**：Megan 氏は、以前は AWS や GCP のポータル、SCCM に個別にログインしていたが、現在は Azure に集約されたと説明しています。
- **運用負荷の軽減**：EMM により、監視、ポリシー、更新管理、変更追跡をサブスクリプション単位でまとめて有効にできます。Azure Policy により、自動アップグレードやアプリの配布を大規模に適用できます。
- **セキュリティの向上**：ホットパッチによるダウンタイムなしの更新、CIS ベンチマークの適用と自動修復、Defender との連携。
- **SQL Server**：100 万を超えるインスタンスが接続されており、ESU、モダナイズの推奨、時間単位の Software Assurance などを利用できます。
- **データベースの運用**：Database Hub が、データベース全体を 1 か所で扱い、データベースエージェントが問題の検出とガイド付きの対応を支援します。DB の専門家は、アラートの調査に費やす時間を減らせます。

## 9. まとめ

Azure Arc は、ARM をコントロールプレーンとして、Azure の外にあるサーバー、Kubernetes、データベースまで同じ方法で管理できるようにします。このセッションでは、Configuration Manager や GCP との接続によるインベントリ、EMM による管理サービスの一括有効化、そして CIS ベンチマーク、ホットパッチ、Defender による運用とセキュリティを、デモで確認しました。

データベースの面では、Arc に接続された 100 万を超える SQL Server インスタンスを土台に、GA となった Database Hub in Microsoft Fabric が、データベースを移動せずにフリートとして運用する手段を提供します。データベースエージェントは、Azure SQL Database 向けの public preview です。

登壇者は最後に、Arc Server Forum への参加、ブースへの来訪、翌日のラボへの参加を呼びかけています。
