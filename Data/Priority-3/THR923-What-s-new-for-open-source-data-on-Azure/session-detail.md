# THR923 What's new for open source data on Azure?

## 1. はじめに

このセッションは、Azure のオープンソース系データベースのマネージドサービスについて、この週の新情報を約 25 分で駆け足に紹介するものです。2 人の登壇者が交代で、PostgreSQL と MySQL（Tenille 氏）、DocumentDB・Redis（Marco 氏）を担当します。

目玉は、**Azure HorizonDB の一般提供（GA）** です。Postgres 互換のクラウドネイティブなデータベースで、コンピュートとストレージを分離し、AI 機能を最初から備えます。そのほかに、PostgreSQL 向けの AI 支援運用「database agent」（プレビュー）、Azure Database for PostgreSQL のストレージ強化と Oracle 移行ツール、MySQL の新機能、Azure DocumentDB の VS Code 拡張の GA や Azure Arc 対応、Azure Managed Redis の Redis 8 対応が語られます。最後に、続きを学べる関連セッションと、新しい外部公開のロードマップが案内されます。

発表者は、画面を見せながら HorizonDB の作成手順と database agent の画面を説明しています（6 章）。

セッション URL: [https://ignite.microsoft.com/en-US/sessions/THR923](https://ignite.microsoft.com/en-US/sessions/THR923)

## 2. 発表者

| 氏名 | 所属 | 役職 |
| --- | --- | --- |
| Tenille Lawrence | Microsoft Azure | Senior Product Marketing Manager（Azure のオープンソース・データベースのマネージドサービス） |
| Marco Hadi | Microsoft Azure | Senior Technical Product Marketing Manager（Azure DocumentDB。Azure Cosmos DB にも関与） |

> 補足：メタデータに登壇者の一覧は無く、表は冒頭の自己紹介の内容に基づいています。氏名は音声認識の都合で表記が揺れており、最初の自己紹介では "Tania Lawrence"、その後は "Tenille" "Tanil" "Sunil" などと聞こえます。ここでは "Tenille Lawrence" と書きましたが、綴りは確定できません。Marco 氏の姓（"Hadi"）も聞こえたままの表記です。Marco 氏は自己紹介で、DocumentDB を担当し、Azure Cosmos DB にも関わると述べています。

## 3. 何に関する発表か

テーマは、「オープンソースのデータベースを Azure のマネージドサービスで使う」ことに関する最新情報です。Tenille 氏は冒頭で、次の 3 点を扱うと説明しました。

1. Azure のオープンソース・マネージドサービスの新機能
2. それらが、どんな「ジョブ」（やりたいこと）の実現を助けるか
3. 詳しい情報の入手先（25 分しかないため）

発表の流れは次のとおりです。

1. 数字で見る Microsoft のオープンソースへの貢献と、なぜオープンソースか（Tenille 氏）
2. Microsoft のオープンソースのポートフォリオ全体（Marco 氏）
3. Azure HorizonDB（GA）、Microsoft Fabric HorizonDB、database agent
4. Azure Database for PostgreSQL の新機能と Oracle 移行
5. Azure Database for MySQL の新機能
6. Azure DocumentDB と Azure Managed Redis（Marco 氏）
7. 関連セッションとロードマップの案内

Tenille 氏の担当範囲は、Azure Database for MySQL、Azure Database for PostgreSQL、Azure HorizonDB です。リリースの狙いは 2 つの「ジョブ」にまとめられています。**AI で速く構築・革新すること**と、**すでに動いているワークロードを拡張し、性能を高めること**です。

## 4. 発表者が伝えたいメッセージ

最初のメッセージは、Microsoft はオープンソースのプロジェクトに実際に貢献している、という点です。Tenille 氏は 2 つの数字で示しました。Postgres のコアプロジェクトに貢献している Microsoft のメンバーは **20 人**、今月リリースされる **Postgres 19** で Microsoft のチームが書いたコミットは **4,098 件**です（発話では "Postgres 19"）。マネージドサービスのラッパーを作るだけでなく、上流のプロジェクトにも貢献し、得られた学びや知見を上流に戻している、と説明しています。

2 つ目は、開発者と企業が求めるものの違いです。開発者は、無料で始められる、ポータブル、ベンダーロックインが無い、イノベーションに取り組むコミュニティがある、といった点でオープンソースを支持してきました。企業も今ではオープンソースを求めており、さらに「どうパッチを当てるのか」「サービスが止まらないようにするには」「最新の技術が得られるか」を知りたがる、と述べています。Azure のマネージドサービスは、オープンソースの開放性・柔軟性・革新性と、Azure の信頼性・セキュリティ・回復性を組み合わせたものだ、という位置づけです。

3 つ目は、Marco 氏が強調した「デプロイの自由」です。お客様は常にパブリッククラウドへ展開したいわけではなく、オンプレミスやエッジで動かしたいことがあります。そのため、クラウドのマネージドサービスにも、ハイブリッドやマルチクラウドにもデプロイできるように投資している、という説明でした。

## 5. 発表内容の詳細

### 5.1 Microsoft のオープンソースへの関わり（Marco 氏）

Marco 氏は、Microsoft の貢献は Postgres だけでないと述べ、次の例を挙げました。

| 領域 | 内容 |
| --- | --- |
| Linux Foundation | 多くのプロジェクトに貢献。Azure DocumentDB は、Linux Foundation が運営するオープンソースの DocumentDB プロジェクトに基づく |
| CNCF（Cloud Native Computing Foundation） | Kubernetes と Kubernetes オペレーターに貢献。監視、デプロイ、トラブルシューティングの機能を追加している |
| Linux on Azure | Azure のお客様のコンピュートコアの **3 分の 2 以上** が Linux で動いている |
| 開発者ツール | VS Code、GitHub のほか、.NET フレームワークのオープンソース化（Linux でも動く）、TypeScript への貢献など。Python、Rust、Go など、挙げられた言語やフレームワークが Azure と製品でファーストパーティとして扱われるよう努めている |

DocumentDB は、MongoDB 互換のオープンソースのデータベースで、Microsoft が生み出し、**昨年 Linux Foundation に寄贈**したものです。MongoDB の API を使い続けたいが、構築方法にもっと自由が欲しい開発者のエコシステムが育っている、と説明しています。MySQL と Redis のエコシステムも Microsoft がサポートしている、とのことです。

データベースのポートフォリオとしては、Postgres のほか、Azure DocumentDB、Azure Managed Redis、Azure Database for MySQL があります。これらを、セキュリティ統制を備え、常に最新で、脆弱性があれば修正され、企業が望む規模に拡張できる「エンタープライズ対応」にするための投資だと述べています。「前日に GA した HorizonDB を知っているか」と会場に尋ねており、Postgres を Azure 上でよりスケーラブルで高性能にするものと位置づけています。

### 5.2 Azure HorizonDB（GA）

HorizonDB は、**フルクラウドネイティブな PostgreSQL**で、「アーキテクチャ上の回避策なしに、現代の AI ワークロードに向けて作られた」と紹介されました。説明された特徴は次のとおりです。

- **コンピュートとゾーン冗長ストレージの分離**：コンピュートはワークロードに合わせて独立してスケールでき、共有ストレージはワークロードに応じて自動的に拡張される
- **回復性を内蔵**：コミットは 1 ミリ秒未満（発話は "less than one millisecond commit"）、フェイルオーバーは 5 秒
- **性能**：このアーキテクチャにより、セルフマネージドの Postgres に比べて **8 倍高速**
- **AI の標準装備**：デプロイ時点で AI パイプラインと AI モデル管理を備え、AI 関数をデータの近くであるデータベース内に置ける
- **リーダーエンドポイント**：専用のリーダーエンドポイントを最大 15 個持てる（Decision Point ゲームの説明の中で語られた）

作成の手順（Azure portal の画面を見せながらの説明）は 6 章にまとめています。

**Decision Point（ゲーム）**：今週公開された、Postgres 向けのゲームです。シナリオを体験でき、VS Code のサンドボックスで、組み込まれた AI 機能を試せると説明されました。例として、HorizonDB に支えられた VS Code でのミックスクエリの設定、変更時に埋め込みを更新する「on change embedding」の AI パイプライン、スケールの構成（上述の専用リーダーエンドポイント）が挙げられました。クラスターをまだ作る準備ができていない人に向けたものです。

### 5.3 Microsoft Fabric HorizonDB（プレビュー）

Microsoft Fabric の中の**サーバーレスの Postgres** で、プレビューとして公開されました。トランザクションデータを Fabric の分析に近づけます。Fabric 上にあるので OneLake にアクセスでき、分析とトランザクションの両方のデータが要る新しいアプリで、ミックスクエリを 1 つの場所で実行できます。

- 数秒で使える（コンピュートの構成が不要）
- 最初から 4 テラバイトのストレージが付く
- 5 分間アイドルになると停止する（コンピュートリソースを無駄にしない）

### 5.4 database agent（プレビュー）

PostgreSQL の **AI 支援運用**の機能で、プレビューとして公開されました。VS Code でデータベースに対してエージェントを有効にすると、次の動きをします。

1. データベースを自動で監視する
2. 問題を検出する
3. 完全なインシデントレポートを出す
4. 修復アクションを推奨する

セキュリティと信頼が重要であるため、**エージェントは承認を得て動く**設計です。修正案を推奨するところまでがエージェントで、実際に変更を実行するかどうかは、そのデータベース・ワークロードの持ち主であるエンジニアが「はい」とクリックして決めます。問題が起きた場合に備えて**ロールバック**の選択肢もあります。これらは、Postgres データベースを支える **VS Code の Copilot agent mode** によって実現されると説明されています。画面の内容は 6 章を参照してください。

### 5.5 Azure Database for PostgreSQL

こちらも 2 つのジョブ（大規模での運用、既存ワークロードのモダナイズ）に沿って整理されています。

**大規模運用の強化**

- 追加のコンピュートオプション（柔軟性と性能の向上）
- **Ultra Disk ストレージ**（プレビュー）：性能向上
- **SSD v2 ストレージの自動拡張**：ストレージの上限に近づくと自動で拡張するため、その都度構成する手間が不要。現在は Premium SSD v2 向けで、Ultra Disk が GA したら同じ機能を提供する
- **強化されたバースタブル層**：負荷の波が大きく、常に安定した性能が要らないワークロード向け。本番を支えないものに無駄な費用をかけたくない場合に選べる

エンジニアの運用負荷を減らし、データベースをより回復性・性能の高い状態に保つ狙いだと説明しています。

**モダナイズ（Oracle 移行）**

- **AI 支援の移行ツール**（Oracle ワークロード向け）：アプリとスキーマの移行を支援する
- **Azure Data Migration Service**：データの移動には、Oracle の移行を支えるオフラインのデータ移行を用意した

これで Azure Database for PostgreSQL でも Azure HorizonDB でも、Azure 上の Postgres の利点を得られます。

**事例（グローバルな銀行）**：古い Oracle のワークロードを Azure 上の Postgres に移す必要がありました。Azure チームの支援と AI 支援ツールにより、移行にかかる時間が大幅に短縮されました。変更内容をレビューして評価し、そのワークロードを本番の PostgreSQL にデプロイできたということです。得られた教訓は、AI が移行の手作業とリスクを減らせる、というものです（具体的な社名や数値は発表では語られていません）。この事例は明日の朝のセッションで、お客様本人に質問しながら深掘りできると案内されています。

### 5.6 Azure Database for MySQL

MySQL も引き続き強く支援する、と前置きされたうえで、2 つが紹介されました。

- **Premium SSD v2**：MySQL ワークロード向けの新しいストレージオプション
- **Rename server**：サーバーを復元したり新しいサーバーに移行したりせずに、サーバー名を変更できる機能。性能向上に加え、モダナイズの際の運用上の障害を減らす

### 5.7 Azure DocumentDB（Marco 氏）

DocumentDB は、MongoDB の API と互換性のあるオープンソースのプロジェクトで、現在は Linux Foundation が運営し、Azure DocumentDB でサポートされます。会場には MongoDB（MongoDB Atlas を含む）を使っている人がいました。MongoDB のワークロードがあるお客様は、オープンソースの選択肢を検討し、Azure DocumentDB に移行する動きがあると述べています。

**開発者のワークフロー**

- ラップトップで始める。Windows Subsystem for Linux、Ubuntu（発話では "24.4 or 26.4"）、VS Code、AI 支援ツール、Docker、エージェントキット（agent kits）、オープンソースのフレームワークを使う
- Docker のコマンド 1 つで DocumentDB のインスタンスを起動できる。公式リポジトリから Docker イメージを取得して起動する（"a simple Docker command here" と画面を指す発話があり、コマンドの内容は読み上げられていません）
- **DocumentDB extension for Visual Studio Code**：データベースの作成、スキーマの閲覧、JSON ドキュメントの確認、スキーマの変更といったデータベース関連の作業を VS Code から行える
- ラップトップで作ったアプリを Azure で試すには、Azure Portal で Azure DocumentDB を検索し、作成のワークフローを進める。その中で**無料ティア**を選ぶ。無料ティアは**ずっと無料**で、**25 ギガバイト**のストレージが付き、プロトタイプや小さなワークロード向け
- 既存の MongoDB ワークロードには、**Azure DocumentDB Migration Assistant for VS Code**。既存ワークロードの評価から、オンラインまたはオフラインの実際の移行までを統合している。評価では、アプリに起きうる問題などの詳細なレポートが得られる。コードを確認したほうがよい場面もある

**今回の新情報**

| 項目 | 内容 |
| --- | --- |
| DocumentDB extension for VS Code | プレビューが数か月続き、**GA** になった。VS Code 環境へのインストールを推奨 |
| クラスターのプロビジョニング | コンピュートノード、ストレージノード、ネットワークなどを用意するため、以前は例えば 10 分かかっていたが、分単位より短くなり、ほぼ即時になった（発話は "less than minutes"）。対象は M10〜M30 の低位の構成。新しい MongoDB 互換クラスターをすぐに立てたいときに便利 |
| Azure Arc 対応 | **DocumentDB enabled by Azure Arc** がパブリックプレビュー。Azure のコントロールプレーンを Azure 外の場所にも持ち込み、任意の環境で動く DocumentDB クラスターを管理・制御できる |
| Azure Local | DocumentDB が Azure Local でも**パブリックプレビュー**。Azure Arc 版と Azure Local 版は組み合わせても別々でも使え、どちらにも依存しない |

オンプレミスに置きたい理由は、データをアプリケーションやユーザーの近くに置きたいなど、お客様によってさまざまだ、と説明しています。

### 5.8 Azure Managed Redis（Marco 氏）

Redis は、お客様がインメモリのキャッシュにだけ使うことが多いものですが、**Redis 8 のサポートがパブリックプレビュー**になり、Azure Managed Redis が「AI ready」になったと説明されました。ベクトル機能に対応し、ベクトル検索、セマンティック検索、RAG アプリケーションの構築や、ベクトルを高速にキャッシュしたいだけのアプリにも使えます。Azure 上で動くので、安心して使えるとのことです。

また **Azure Managed Redis の agent kit** が紹介されました。agent kit は、開発中に使う AI アシスタントに追加できるスキルのようなものです。ベストプラクティスとガイダンスを開発環境に持ち込むもので、ダウンロードして GitHub Copilot やクラウド環境などにインストールすれば、推奨事項やベストプラクティスが得られます。

### 5.9 次のステップ（関連セッション・ロードマップ）

発表では、次の深掘りセッションが案内されました。

- AI 支援の移行で Postgres への移行を加速する（明日の午前。Tenille 氏が前述の銀行の事例で言及）
- フリートのモダナイゼーションで得た教訓（ブレークアウト）
- 仕事に合ったデータベースエンジンの選び方（ブレークアウト。パートナー 3 社が参加するパネルディスカッション。聞こえた名称は "database SOS meeting" ですが、音声認識の都合で正式名称が確定できません）
- MongoDB 移行のブレークアウト（明日の午後。Marco 氏が特に楽しみにしている）

Tenille 氏は、スケジュールに少なくとも 1 つは入れるよう勧めています。また、**新しい外部向けロードマップ**を公開したことが案内されました。四半期ごとの計画を見られ、アップグレードやモダナイズを計画している人が、Azure のデータベースのポートフォリオ全体でのリリース予定を把握するのに使えます。さらに、直接話したい人は、翌日（会場が再開したあと）に Microsoft Azure エリアの expert meetup ブースに来てほしいと案内して、セッションは終わります。

> 補足：このセッションでは、質疑応答の時間は設けられていません（時間切れの案内とブースへの誘導で終わっています）。

## 6. デモ・手順

このセッションでは、スライドに加えて、画面を見せながらの説明が 2 箇所あります（"Just taking a look here at how easy it is to provision..." "this is what that new experience looks like" といった発話に基づきます）。録画かライブかは発話からは分かりません。画面の細部は音声からは確認できず、語られた範囲だけを書きます。

### 6.1 HorizonDB クラスターの作成（Azure portal）

発話の順序は次のとおりです。

1. 新しい HorizonDB クラスターには、ストレージとコンピュートのリソースが含まれる
2. Azure portal から、リソースグループを入力する
3. クラスターに名前を付け、リージョンを選ぶ
4. コンピュートを構成し、ゾーン冗長ストレージを選ぶ。画面では **16 vCores** を選んでいる（最大 192 まで選べると説明）
5. Microsoft Entra ID は最初から組み込まれており、セキュリティを高める
6. AI 機能（AI モデル管理、AI パイプライン）を、すぐに有効にできる。クラスターを使い始めた時点で、これらは構成済み
7. プライベートエンドポイントを作成できる
8. 「review and save」をクリックすると、新しい HorizonDB クラスターが作られる

### 6.2 database agent の画面（VS Code）

1. VS Code に、パフォーマンスのダッシュボードがある。発話は "It's been up for 30 days" で、30 日が表示期間か稼働期間かは音声からは確認できません。CPU やメモリの使用率、ネットワークの状況、スパイクや潜在的なインシデントが見える。従来はここから調べ回って原因を突き止める必要があった
2. 「database agent」のタブに、エージェントがすでにまとめたレポートが並んでいる
3. レポートを開くと、そのインシデントに関連するすべてのイベントと、詳細が見られる。イベントのタイムラインもある
4. レポートの最後に、**推奨アクション**がある。推奨する変更、なぜ安全か、ロールバックの方法、その変更で期待される結果が示される
5. 判断に迷ったら、「スパークル」のアイコンをクリックして Copilot Chat を開き、エージェントに質問できる。「この変更は何に影響するか」「こうしたらどうなるか」などと問い合わせられる
6. 発表者は、エージェントのせいで何かが壊れることは避けたいので、ユーザーが主導権を握れるようにしている、と述べています

### 6.3 DocumentDB のデモ的な紹介

Docker で DocumentDB を起動するコマンドと、無料ティアの選択画面（"briefly showed up there"）が、スライドや画面で短く示されたと発話から分かります。コマンドや画面の内容は読み上げられていません。

## 7. 注意点・発表者の気付き

- 発表されたものには、**GA、パブリックプレビュー、プレビュー**が混在しています。GA は HorizonDB と DocumentDB extension for VS Code。プレビューは Microsoft Fabric HorizonDB、database agent、Ultra Disk ストレージ、DocumentDB の Azure Arc 対応と Azure Local 対応、Azure Managed Redis の Redis 8 対応です
- SSD v2 の自動拡張は、現在は Premium SSD v2 向けで、Ultra Disk が GA したら同様の機能が提供される、と説明されています
- DocumentDB のクラスター高速プロビジョニングは、M10〜M30 の低位の構成で対応しています
- database agent は、人間の承認とロールバックを前提にしています。「ユーザーが主導権を握る」ことが強調されました
- DocumentDB の Azure Arc 対応と Azure Local 対応は、互いに依存せず、組み合わせても別々でも使えます
- 25 分のセッションのため詳細は語られておらず、深掘りは関連セッションで行うよう案内されています

## 8. 利用者にとっての利点

発表で語られた利点は次のとおりです。

- **HorizonDB**：コンピュートとストレージの分離、ゾーン冗長ストレージ、1 ミリ秒未満のコミットと 5 秒のフェイルオーバー、セルフマネージドの Postgres と比べて 8 倍の性能。AI パイプラインと AI モデル管理が標準で付く
- **Microsoft Fabric HorizonDB**：サーバーレスで数秒で使え、OneLake と連携して分析とトランザクションを 1 か所で扱える。5 分のアイドルで停止し、無駄を省く
- **database agent**：監視、検出、レポート、推奨までを自動化し、実行は承認制、ロールバックもあり、調査の手間を減らせる
- **Azure Database for PostgreSQL**：ストレージの自動拡張、バースタブル層、Oracle 移行の AI 支援。事例では、グローバルな銀行が古い Oracle ワークロードの移行時間を大幅に短縮した
- **MySQL**：Premium SSD v2、サーバー名の変更を移行なしで行える
- **DocumentDB**：Docker と VS Code で手元から始められ、無料ティア（25 GB、ずっと無料）で Azure で試せる。MongoDB からの移行は評価から本番までを Migration Assistant が支援する。Arc や Azure Local でオンプレミスにも広げられる
- **Azure Managed Redis**：Redis 8 でベクトル検索やセマンティック検索、RAG アプリに対応

## 9. まとめ

このセッションは、Microsoft が Postgres のコア（Microsoft の貢献者 20 人、Postgres 19 で 4,098 コミット）や DocumentDB の Linux Foundation への寄贈などでオープンソースに貢献していることを示したうえで、この週の新機能を並べたものです。中心は、GA した Azure HorizonDB（8 倍の性能、コミット 1 ミリ秒未満、フェイルオーバー 5 秒、最大 192 vCores）と、承認とロールバックを備えた database agent（プレビュー）です。

あわせて、Azure Database for PostgreSQL（Ultra Disk、自動拡張、Oracle 移行の AI 支援ツール）、MySQL（Premium SSD v2、サーバー名変更）、Azure DocumentDB（VS Code 拡張の GA、高速プロビジョニング、Azure Arc と Azure Local）、Azure Managed Redis（Redis 8、ベクトル検索、agent kit）の更新が紹介されました。詳細は関連セッションと、公開されたばかりの外部ロードマップで確認するよう案内されています。
