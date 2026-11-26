# THR897 Cloud management for every Windows Server

## 1. はじめに

このセッションは、データセンター、支店、マルチクラウド、エッジに分散した Windows Server を、どう一貫して管理するかを扱う 25 分のシアターセッションです。管理の中心に据えられているのは、サーバーを **Azure Arc** に接続（Arc enable）する方法です。

内容は大きく 5 つに分かれます。Windows Admin Center の Virtualization Mode（WAC V mode）と、そこから VM を Azure Arc にまとめて登録するデモ、Arc で使える Defender for Cloud と Azure Monitor、Windows Server 2025 の **Hotpatching** とそのデモ、Arc 経由の ESU（Extended Security Updates）の月額支払い、そして今回発表された **Windows Server と Intune のプレビュー**です。Levi Strauss と World Bank の事例も紹介されました。

セッション URL: [https://ignite.microsoft.com/en-US/sessions/THR897](https://ignite.microsoft.com/en-US/sessions/THR897)\
YouTube URL: [https://www.youtube.com/watch?v=HCahGhu2k5U](https://www.youtube.com/watch?v=HCahGhu2k5U)

## 2. 発表者

| 氏名 | 所属 | 役職 |
|---|---|---|
| Theo Tran | Microsoft | Product Manager |
| Cindy Wan | Microsoft | Program Manager |

冒頭の司会の紹介で、2 人は Windows Server の PM とされ、続けて「Theo Tran」「Cindy Wan」と名乗っています。Defender と Azure Monitor の説明を終えた Theo 氏が「ここから Cindy に渡す」と述べ、Cindy 氏が自己紹介をして Hotpatching の説明を始めます。それ以外の部分は、どちらが話しているかを示す発言がないため、本文では「登壇者」と書きます。

## 3. 何に関する発表か

登壇者は、Windows Server が重要なインフラの基盤として進化してきたことを述べたうえで、次の進化を **hybrid intelligence**（AI の推論を、アプリやデータ、業務のある場所に近づけること）と位置づけています。Windows Server と Azure を組み合わせて、事業にとって最適な場所でワークロードと AI を動かせるようにする、という考え方です。

そのうえで、次期の大きなリリースである **Windows Server 2028** が preview 段階にあり、Windows Server Insider program から利用できると紹介しました。管理面では、分散した環境を、運用のサイロを作らずに、一貫して管理・保護できるようにすることが重点だと説明されています。このセッションでは、個々のサーバーや仮想化基盤の詳細な管理を担う Windows Admin Center と、クライアントとサーバーをまとめて管理する Microsoft Intune・Azure Arc を取り上げると、冒頭で紹介されました。

> 補足：「Windows Server 2028」は、トランスクリプトでそのように発話されています。

発表の流れは次のとおりです。

1. WAC Virtualization Mode と、Azure Arc への一括登録（デモ）
2. Azure Arc とは何か
3. Arc で使える Defender for Cloud と Azure Monitor
4. Hotpatching（デモ）と Azure Update Manager
5. Software Assurance の特典と、Arc 経由の ESU
6. 事例（Levi Strauss、World Bank）
7. Windows Server と Intune のプレビュー
8. 持ち帰ってほしいこと

## 4. 発表者が伝えたいメッセージ

発表の主張は、**Azure Arc を使えば、サーバーが物理か仮想か、オンプレミスか他社クラウドかにかかわらず、Azure のサービスで一貫して管理・保護でき、しかも多くの機能が追加費用なしで使える**という点にあります。登壇者は、サーバーをデータセンターに置いたままで、パッチ適用や保護の方法を現代化できる、という言い方で締めくくっています。

## 5. 発表内容の詳細

### 5.1 Windows Admin Center Virtualization Mode

**WAC V mode**（Windows Admin Center Virtualization Mode）は、Windows Server Hyper-V 向けの新しい仮想化管理コンソールで、**今週 GA になったばかり**です。物理サーバーやクラスターのデプロイ、ネットワーク構成、ストレージ構成の管理に特化して作られています。

- ダウンロードしてインストールし、ホストマシンの管理を始めるまでに 5 分かからないと、登壇者は冗談交じりに話しています。「満足できなければ返金」と言いつつ、実際は**無料で、Windows Server のライセンスに含まれます**。
- ストレージは、SAN、NAS、HCI など幅広く対応します。
- VLAN と SDN の仮想ネットワークをサポートします。
- VM の変換（conversion）が統合されています。
- VM を Azure Arc に登録して、統合管理に移行する体験が用意されています。

WAC V mode の詳細は、1:30 から別の theater セッションで扱われると案内されました（会場名の聞き取りは不明瞭です）。

### 5.2 Azure Arc とは

登壇者は会場に「Azure Arc を知っている人は？」と挙手を求めたうえで、知らない人向けに説明しました。有料のサービスなのか、機能なのか、と問いかけつつ、**Azure Arc は「コネクター」や「橋」と考えるのが最もわかりやすい**と述べています。

- Azure の外で動いているサーバーを、Azure につなぐ仕組みです。データセンター、支店、AWS や Google などのマルチクラウド、物理でも仮想でも対象です。
- **Azure Arc 自体は完全に無料**で、費用は一切かかりません。Arc エージェントをインストールして Azure に登録するだけです。
- 登録後は、事業に必要な Azure サービスを選んで、必要なものだけ使い始めます。
- 例として Defender、Monitor、Sentinel、Policy、Update Manager が挙げられ、Arc enable したサーバーでこれらが使えるようになります。

今週、何人かの顧客と話したなかで、「すべてが正常に、しかも安全に動いていると、どう確認できるのか」という声が繰り返し出ているとのことです。登壇者は、Arc がその可視性を与え、たとえば 3 つの大陸にまたがる 10,000 台のサーバーに Defender を展開するような操作が、Arc のおかげで可能になると説明しています。この可視性と制御は、Windows Server だけでなく、**Linux と SQL Server にも**適用できます。

### 5.3 Defender for Cloud

**Defender for Cloud は、Arc enabled servers で最も使われている機能**です。理由は、セキュリティが Microsoft、Windows Server、Azure、そして利用者にとって最大の関心事だからだと説明されています。登壇者は、最近「178 の組織が攻撃を受けた」という記事を読んだと述べ、AI によって攻撃が頻繁かつ複雑になっていると話しました（記事の出典などは発表では触れていません）。

10,000 台を管理しているとき、全台にエンドポイント保護が入っているか、1 台でも欠けていないか、どう確認するのかという問いに対して、Defender for Cloud を有効にすることで次のことができます。

- 全体のセキュリティ態勢（security posture）を確認する。
- エンドポイント保護を有効にする。
- 推奨事項を、critical、high、medium、low に分けて見る。
- 物理・仮想を問わず、Azure の外で動くあらゆる場所を横断して可視化する。

### 5.4 Azure Monitor

**Azure Monitor は 2 番目に使われている Arc 有効機能**です。ハイブリッドとマルチクラウドのコンピュートに対して、一貫した監視体験を提供します。

サーバーアプリケーションは単一の実行ファイルではなく、フロントエンド、ロジックを担う中間層、バックエンドのデータベースからなる多層構造が普通です。Azure Monitor は、この依存関係を示す **application map** を作り、問題を素早く見つけて対処できるようにします。さらに次の機能が紹介されました。

- インフラレベルの問題を、VM やコンテナーなどの insights と関連づける。
- スマートアラートと自動アクションで、大規模に運用化する（事後対応でなく先手の対応）。
- Log Analytics で、より深くトラブルシューティングや診断をする。
- **ダッシュボード**で可視化する。上司から「状況はどうか、KPI は、目標を達成できているか」と聞かれたときに使える。ストレージチームやネットワークチームがそれぞれ見たい指標のダッシュボードを作れ、作るのが簡単なため、チーム間で共有されやすいと説明されています。

ここで登壇者は「Defender for Cloud が Arc enabled servers の 1 位、Azure Monitor が 2 位」と整理しています。

### 5.5 Hotpatching

ここから Cindy 氏が話します。Arc を使うと、Windows Server 2025 で顧客からの要望が最も多かった機能である、**Arc-enabled Windows Server Hotpatching** が使えます。

Hotpatching は Windows Server 2022 で導入された機能ですが、当時は Azure でしか利用できませんでした。**すでにメモリ上で動いている Windows のコードを書き換える**ため、対象となるセキュリティ更新では、サーバーの再起動も、プロセスやサービスの再起動も要りません。アプリケーションは動き続け、パッチを適用した瞬間からワークロードは保護されます。同じ技術は、Azure 自身のサーバーフリートへのパッチ適用に何年も使われてきたとのことです。

> 注意：baseline 更新では、引き続き再起動が必要です。頻度はおおむね**四半期に 1 回**です。差は、毎月のセキュリティ更新ごとに再起動の時間枠を確保する必要がなくなる点です。登壇者は、年 12 回の再起動が年 4 回になる、と説明しています。

Windows Server 2022 Azure edition で導入したとき、「自社データセンターのサーバーでも使えるか」という声が出続けており、Windows Server 2025 では可能になりました。条件と手順は次のとおりです。

1. 対象となるサーバー（Standard または Datacenter エディション）を用意します。物理サーバー、データセンター内の VM、他社クラウド上の VM のいずれでも構いません。
2. そのサーバーを Azure Arc に接続します。
3. Azure portal で Hotpatching を有効にします。**追加費用はなし**です。

なお、Windows Server 2025 Azure edition で使う場合も、追加費用はないとのことです。

### 5.6 Azure Update Manager

2 台であればビルド番号の変化を目で確認できますが、200 台では「どのマシンにまだセキュリティ更新が必要か」を知る仕組みが要ります。そこで **Azure Update Manager** が紹介されました。

- Azure 上のサーバーと Arc 経由で接続したサーバー、Windows と Linux の両方について、不足している更新を一か所で確認できる。
- メンテナンスウィンドウに合わせて、パッチ適用を自動化・スケジュールできる。
- 組み込みの role-based access control で、スケジュールを変更できる人を制御できる。
- 適用後に、実際に何がインストールされたかを確認できる。

### 5.7 Software Assurance の特典と ESU

Hotpatching 以外にも無料のものがあります。**Software Assurance をお持ちのお客様には、20 を超える追加の Azure サービスが含まれています**。例として Windows Admin Center、Arc の Best Practices Assessment、Azure Update Manager、Azure Change Tracking Inventory などが挙げられました（読み上げの一部は音声認識の都合で表記が確定できません）。追加費用なしで利用でき、Azure portal で特典を有効化する必要があると案内されています。

Arc は、**ESU のコスト削減**にも使えます。登壇者が挙げた例は次のとおりです。

- Windows Server 2016 は 2027 年 1 月にサポート終了を迎えます（発話では「in January 2027, which is just in a month」）。1,000 台が残っていて ESU が必要だとします。
- 1 年分を前払いで購入する方法のほかに、Arc 経由で Azure portal から登録し、**月額で支払う**方法があります。
- 6 か月後に 1,000 台のうち 500 台をアップグレードしたら、ライセンスのコア数を、残り 500 台分に減らせます。800 台をアップグレードしたなら、200 台分だけで済みます。
- 移行が進むにつれて、残りの月の請求額が下がっていきます。

### 5.8 事例

**Levi Strauss** は、Arc を移行の最中に使った例として紹介されました。

- 古い ERP と SQL Server のシステムをモダナイズする必要がありつつ、数百の小売店舗の稼働も止められない状況でした。
- Arc で、オンプレミスのサーバーを Azure Update Manager に取り込み、移行中のパッチ適用と ESU を一か所で扱えるようにしました。
- Azure Migrate により、サーバー 1 台あたりの移行作業が 2〜3 時間短縮されました。
- Azure SQL Managed Instance では、レイテンシが半分になり、ピーク IOPS が 60% 増えました。

**World Bank** の課題は、130 を超える拠点のチームが、サーバーの状況を共有して見られるようにすることでした。

- SQL Server が、自社データセンター、Azure、AWS で動いていました。
- Arc でサーバーを棚卸しし、バックアップとパッチ適用を簡素化し、どのシステムが移行できる状態かを評価しました。
- セキュリティチームは、同じ SQL 環境に Microsoft Defender for Cloud と Microsoft Purview を適用でき、データベースが Azure に移るのを待たずに可視化・保護できました。

### 5.9 Windows Server と Intune（プレビュー）

すでに Microsoft Intune で Windows クライアントを管理しているチームから、Windows Server も Intune で管理したいという要望があり、**Windows Server and Intune のプレビュー**が発表されました。構成、コンプライアンス、アプリ、更新といった、おなじみのワークフローを、対象となる**永続的な（persistent）Windows Server**で試せます。Intune でエンドポイントデバイス群を管理していて、Windows Server は 1〜2 台だけという顧客に向く、と説明されています。

登壇者が示したワークフローは次のとおりです。

1. 対象のサーバーを Microsoft Entra ID に参加させ、Intune に登録（enroll）します。
2. 登録されると、Intune admin center に、ほかの Windows デバイスと並んで表示されます。
3. デバイスのページで、構成の状態、コンプライアンスの状態、インストールされているアプリを確認します。
4. 同じコンソールから、ポリシーを割り当て、アプリや更新を配布し、結果を確認し、サポートされているリモートアクションを実行します。
5. そのサーバーから保護されたアプリにサインインする場合は、**conditional access** で、サーバーがコンプライアンスを満たしていることを条件にできます。

サーバーと PC を同じ場所で管理しても、同じ設定にする必要はありません。例えば、サーバーには日曜夜のメンテナンスウィンドウを設定し、ノート PC は通常のスケジュールのままにする、といった使い分けができます。現在は **Windows Server Insider Preview** で利用でき、Azure Arc や Windows Admin Center を置き換えるものではなく、それらと並ぶ選択肢だと位置づけられています。

## 6. デモ・手順

**デモは 2 つあります。** どちらも画面を使ったデモですが、録音は音声のみで、画面の細部（ボタンの配置や入力値）は語られた範囲しか分かりません。

### 6.1 WAC V mode から Azure Arc への一括登録

1. Azure 認証から始めます。
2. 画面左側にあるクラスターで動いているホストマシンを、すべて Azure Arc に登録するのが目的です。理由は、セキュリティ、ヘルス監視、ガバナンスなどを、オンプレミス、エッジ、クラウドを通じて一貫した方法で管理するためだと述べています。
3. Azure Arc ツールを開くと、管理対象の VM と、それぞれの Arc の状態が一覧で表示されます。
4. すべてのリソースを選択し、「onboard to Azure Arc」を選びます。
5. サブスクリプション、リソースグループ、リージョンの情報の入力を求められます。さらにクラウド環境、プライベートエンドポイントかパブリックエンドポイントか、プロキシサーバー、タグも指定できます。
6. 内容を確認して submit すると、V mode がマシンを一括で Arc に登録します。
7. オンプレミスで管理していた同じ VM が、Azure portal にも表示され、Azure のサービスを使えるようになります。

登壇者は、このデモは**早送りしておらず、全体で 1 分 30 秒だった**と述べています。

### 6.2 Hotpatching のデモ

1. 2 台の VM を用意します。1 台はクライアントアプリ、もう 1 台はチャットボットサーバーを動かしています。アプリケーションを動かしたまま、両方に live で hotpatch を適用します。
2. 最初に、両方のマシンの Windows のビルドを確認します（左右とも同じ番号で、数字は音声認識の都合で確定できません）。
3. 左側の OS に hotpatch の適用を開始します。チャットには中断がなく、チャットボットサーバーもバックエンドも動き続け、アプリを止めることもユーザーに再接続を求めることもありません。
4. 完了後に、再びバージョンを確認します。左も右も同じ新しいバージョンになっています（発話では 2308 と聞こえます）。両方のサーバーに更新が入りました。
5. ユーザーの視点では、一続きの会話が途切れなかったと説明されています。

## 7. 注意点・発表者の気付き

- Hotpatching でも、baseline 更新では再起動が必要です（おおむね四半期に 1 回）。
- Hotpatching の対象は「対象となる（eligible）」セキュリティ更新です。Arc 経由で使えるのは、Standard または Datacenter エディションの Windows Server 2025 です。
- Intune での Windows Server 管理は**プレビュー**で、対象は eligible な persistent Windows Server です。Windows Server Insider Preview から利用します。
- Software Assurance の特典は、Azure portal で有効化する必要があります。
- 登壇者は Arc の機能に不足があれば、フィードバックがほしいと述べています。

## 8. 利用者にとっての利点

- Arc 自体は無料で、Windows Admin Center、Azure Update Manager、Hotpatching などは追加費用なしで使えるとされています（Software Assurance の特典を含む）。
- Hotpatching により、再起動が年 12 回から年 4 回に減ります。
- ESU を月額払いにでき、アップグレードに合わせて請求を減らせます。
- Levi Strauss：サーバー 1 台あたりの移行作業で 2〜3 時間の短縮、Azure SQL Managed Instance でレイテンシ半減、ピーク IOPS 60% 向上。
- World Bank：130 超の拠点でサーバーの共通ビューを持ち、データベースの移行前から Defender for Cloud と Purview で保護。
- WAC V mode の Arc への一括登録は、1 分 30 秒で行えました。

## 9. まとめ

登壇者は、持ち帰ってほしいこととして次の 5 点を挙げています。

1. 自分のチームが運用している workload を 1 つ思い浮かべ、Azure のマネージドサービスとして動かしたほうが楽かを考える。そうであれば、それがモダナイズの出発点になる。
2. データセンターに残す必要があるなら、セキュリティと性能のために Windows Server 2025 へのアップグレードを計画する。
3. 古いサーバーのアップグレードに時間が必要なら、Arc 経由で ESU を月額で支払う。
4. すでに Intune で Windows デバイスを管理しているなら、Windows Server Insider Program に参加して、サーバー向けプレビューを試す。
5. Arc をまだ試していなければ、Windows Server 2025 のテストマシンを 1 台 Arc に接続して Azure portal で確認する。パッチ適用が課題なら Hotpatching を、セキュリティが優先なら Defender for Cloud の推奨事項を、Azure Policy での設定確認や Azure Monitor でのヘルス確認も試す。

Azure Arc の価値は、各ワークロードを本来の場所に置いたまま、パッチ適用と保護の方法を現代化できる点にある、というのが登壇者の結論です。
