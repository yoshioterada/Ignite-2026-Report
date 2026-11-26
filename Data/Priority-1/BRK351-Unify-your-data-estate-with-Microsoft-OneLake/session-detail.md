# BRK351 Unify your data estate with Microsoft OneLake

## 1. はじめに

このセッションは、Microsoft Fabric の土台である **OneLake** の最新機能と今後の方向性を、5 つの柱（ガバナンス、エンタープライズ対応、データ資産の統合、オープンなアクセス、AI 向けデータレイク）に沿って紹介するものです。

発表の主張は一貫していて、「モデルやエージェントは差別化の要因ではなく、差別化になるのは自社のデータとコンテキストである。そのデータを信頼できる形で 1 つのレイクにまとめよう」というものです。OneLake catalog の Govern と Govern skills、ライフサイクル管理、shortcut transformations、Fabric IQ sharing、カタログ経由の書き込み、Azure Databricks の既定ストレージとしての OneLake、semantic views といった新機能が、実演（録画ビデオを含む）を交えて語られました。TomTom の担当者が登壇して事例を話す場面もあります。

セッション URL: [https://ignite.microsoft.com/en-US/sessions/BRK351](https://ignite.microsoft.com/en-US/sessions/BRK351)\
YouTube URL: [https://www.youtube.com/watch?v=Q4iKrf0Cu54](https://www.youtube.com/watch?v=Q4iKrf0Cu54)

## 2. 発表者

| 氏名 | 所属 | 役職 |
| ---- | ---- | ---- |
| Dipti Borkar | Microsoft | Vice President, Microsoft IQ & OneLake |
| Josh Caplan | Microsoft | Director of Product Management |
| Roy Hasson | Microsoft | Sr. Director and GPM, Microsoft OneLake |

> 補足：トランスクリプトでは、冒頭で Dipti Borkar 氏が自己紹介し、その後 Josh 氏、Roy 氏が紹介されて登壇します。Josh 氏の姓は音声認識で「Kaplan」と表記されますが、メタデータの綴り（Caplan）に従います。また、TomTom の Rama 氏（「VP of product and maps」と紹介され、自身は solution architecture と strategy チームを率いていると述べています）が登壇して事例を話しますが、この方はメタデータの登壇者一覧には含まれていません。

## 3. 何に関する発表か

OneLake は、Fabric の全ワークロードのデータをオープンな形式で保存する、テナントに 1 つのデータレイクです。発表では「データのための OneDrive」と表現されています。Fabric は Microsoft のデータプラットフォームで、Data Factory のパイプライン、Spark とウェアハウス、SQL・Cosmos DB・Postgres などのデータベース、Real-Time Intelligence（eventhouse）、Power BI、そして Fabric IQ（ontology と各種 data agent）が、すべて OneLake の上で動きます。

発表の流れは次のとおりです。

1. OneLake の位置づけと規模（Dipti 氏）
2. データ ガバナンス：OneLake catalog、Govern、policies、lineage、OneLake security（Dipti 氏）
3. エンタープライズ対応のストレージ機能と、データ資産の統合（shortcuts、mirroring、shortcut transformations、Fabric IQ sharing）（Josh 氏、TomTom 事例）
4. オープンなアクセス：Delta と Iceberg、カタログのフェデレーション、書き込み、Azure Databricks 連携（Roy 氏）
5. AI 向けデータレイク：セマンティクスの層、table read API、Foundry、semantic views（Roy 氏）

## 4. 発表者が伝えたいメッセージ

発表の核になっているのは、次の考え方です。

- **モデルやエージェントではなく、自社のデータとコンテキストが差別化になる。** そのデータはロックインされない形で 1 つのレイクに統合し、レイクの層でセキュリティとガバナンスを効かせる。その上で、任意のアプリ、分析、エージェントを動かす。
- **OneLake の基本原則は「open」と「single copy」。** あらゆる層でこの原則を適用すると述べています。既存のデータ資産は、移行（migration）ではなく「ゼロコピー・ゼロ ETL」で接続するのが基本です（「migration という言葉は私たちの辞書にない」と冗談めかして語られました）。ただし、クローズドソースのシステムは複製せざるを得ないので、mirroring を使います。
- **データだけでなく、コンテキスト（IQ）も一緒に共有する。** ontology を共有することで、エージェントが正しい答えにたどり着く、という事例が紹介されました。

## 5. 発表内容の詳細

### 5.1 OneLake の位置づけと規模

OneLake は、あらゆるクラウド、あらゆるデータベース、オンプレミス、SharePoint、他のデータプラットフォームのデータを統合するもので、多くはゼロ ETL です。データは Delta と Iceberg のオープン形式でネイティブに保存されます。レイクが複数あるのではなく、OneDrive と同じようにテナントに OneLake が 1 つあるだけで、データを仮想化して持ち込むことで、データ資産の単一のビューが得られると説明されています。

Fabric、Foundry、Excel、Teams など Microsoft のエコシステムが OneLake と深く統合されているほか、「オープン」という言葉どおり、Databricks、Snowflake、Salesforce など任意のプラットフォームからも使えます。Salesforce の Data 360 とのパートナーシップにも触れられました。データアクセス、データ共有、データ移動、データ運用などの分野で、OneLake と統合するパートナーのエコシステムが広がっているそうです。

発表で示された規模の数字は次のとおりです。

| 項目 | 数値 |
| ---- | ---- |
| Fabric の顧客数 | 4 万以上（Fortune 500 の 90% 超） |
| OneLake への 1 日あたりのインタラクション | 1,100 億 |
| アクティブな shortcut | 1,500 万 |
| SaaS 体験の裏にあるアクティブなストレージアカウント | 400 万 |
| ネイティブデータの伸び | 前年比 300% 超 |
| OneLake catalog を使う組織 | 24 万以上（Power BI の全顧客がアクセス権を持ち、その約半数が利用） |
| catalog 内のアーティファクト（アイテム） | 3 億 5,000 万以上 |

OneLake の課金は**オンデマンド**で、Foundry、Databricks、Snowflake などから使うだけなら、Fabric のキャパシティ全体を買う必要はなく、サーバーレスな使い方ができる、とも説明されました。

### 5.2 ガバナンスと探索：OneLake catalog

Dipti 氏は、AI を本番運用するうえで最も重要な層が「データとガバナンス」だと位置づけています。信頼できるデータとコンテキストをモデルとエージェントに渡すためです。その中心が OneLake catalog で、探索（discovery）とキュレーション、ガバナンス、OneLake security が 1 つに集まっています。catalog のデータは、S3 や Snowflake 由来のものも含め、Excel の OneLake hub から開くこともできると説明されました。

catalog の主な機能は次のとおりです。

| 機能 | 状況・内容 |
| ---- | ---- |
| Explore（探索） | GA 済み。新たに、きめ細かい検索・探索と、ビジネスコンテキスト（説明、データのプレビューなど）が加わった。「ビジネスカタログ」としての役割 |
| Govern | GA。ポリシー、ラベル、タグ、キャパシティなどを 1 か所で扱う。新機能として **Govern skills**（Copilot やエージェントから呼び出せるスキル）を発表 |
| Policies | public preview。テナント／キャパシティ単位で、特定の機能を使えるワークスペースを限定するなどの管理者ポリシー |
| Lineage | きめ細かい（granular）エンドツーエンドの lineage を発表。**coming soon**で、private preview の参加用 QR コードが示された |
| OneLake security | Fabric 向けは **GA**。レイクの層でのきめ細かいデータセキュリティ。Foundry も OneLake security に対応 |

OneLake security の例として、「Dipti にある地域の売上テーブルへのアクセス権が無ければ、エージェントに尋ねてもそのデータは返ってこない」という説明がありました。きめ細かいセキュリティはこのために必要、という位置づけです。

OneLake catalog は「catalog of catalogs」と位置づけられ、追加機能が必要な場合のために、他社のパートナーと連携しています。今回、データガバナンスの **preferred partner として Atlan** を発表しました。Iceberg shortcuts と Atlan で管理されるメタデータを組み合わせて、高度なガバナンス機能に拡張できる、と説明しています。

### 5.3 エンタープライズ対応：保存と運用の機能（Josh 氏）

ここから Josh 氏が登壇します。OneLake にデータを持ち込む方法は 2 つで、既存のデータ資産に接続する方法と、OneLake にネイティブに保存する方法です。Fabric 内のものは自動的にオープン形式で OneLake に保存され、Snowflake と Databricks のサービスからも、データを OneLake にネイティブに保存して読み出せます。管理、課金、監視、セキュリティを 1 か所に集約できる、というのが利点です。OneLake が公開する **Azure storage API** を使えば、Azure Storage で動くサービスからも読み書きできます。

その上で、次の機能が紹介されました。

**ストレージの階層化とライフサイクル管理**
- 最も容量が大きいのは bronze 層（生のデータ）で、加工して silver、gold に進めたあとは、再処理やコンプライアンスのために残されることが多く、ほとんど触られません。
- ライフサイクル管理では、ルールを設定できます。一定期間アクセスされない、あるいは一定期間経過したデータを、より安価なストレージ階層に自動で移せます。
- 既定は hot で、使われなくなると cool や cold に移ります。保存の価格は下がり、アクセスの価格は少し上がります。

**可観測性（observability）**
- ストレージサイズの詳細（何が容量を使っているか）、診断イベント、誰が何を使ったかなどの詳細なレポートが、OneLake に保存されたデータで使えます。shortcut 経由でアクセスされるデータでも、多くが使えます。
- 拡張診断イベントは「coming soon ではなく、すでに利用可能」と述べています。
- これらは**ホスト型の MCP サーバー**経由でも利用でき、エージェントが直接データを扱えます。

**ネットワークとセキュリティ**
- Outbound access protection：データがレイクから持ち出されるかどうか（できるかどうか）を制御できる、データレイクとしては唯一の機能だと述べています。
- IP ファイアウォール、private link、resource instance rules（private link を用意しなくても Azure サービスをつなげる）
- 独自の暗号化キー（BYOK）：保存データは常に自分のキーで暗号化され、必要なら Microsoft からのアクセスをいつでも取り消せる

Josh 氏は「OneLake に置くかどうか迷っているなら、準備はできている」と述べています。

### 5.4 データ資産の統合：shortcuts と mirroring

**既存 shortcut の改善**
- **Dataverse shortcuts**（最も人気のあるものの一つ）：最初の読み込みが 70% 速くなり、増分更新はすべて 2 倍超速くなった。Power Platform や Dynamics での変更が、数秒で OneLake に現れ、Fabric や Databricks など、どこからでも使える。
- **SharePoint shortcuts**：最も伸びの速い shortcut ソースと説明されています。SharePoint はもともと、ファイルを開いて編集して閉じる使い方を想定した製品です。モデル学習や Spark での変換では、すべてを高速に開いて閉じるため、SharePoint が絞り込み（throttling）に遭います。この分析的な呼び出しに耐えるよう改善し、**SharePoint の RU を 67% 削減**したと述べています。

**新しいソース**
- 一般提供（GA）：Azure Monitor、Google Lakehouse Runtime Catalog、Dremio、AWS Glue Catalog、Aviva Connect（聞こえた綴りのままです。音声認識の都合で表記が確定できません）
- public preview：Salesforce Data 360、および Microsoft Dynamics 系のソース（名称は聞き取れません）

**shortcut transformations**
- shortcut は、データソースへのポインタです。呼び出しが来るとソースへ自動でリダイレクトする、仮想化された仕組みです。ただし、見えているデータがそのまま欲しい形とは限らず、最後の変換のためにパイプラインを作る必要がありました。
- shortcut transformations は、その最後の変換を自動で行い、新しいデータが来ても最新に保ちます。例えば CSV ファイルを、Power BI、Databricks、Fabric から使える Delta Lake または Iceberg のテーブルに変換できます。shortcut の作成時に右クリックして「transform to table」を選ぶだけです。
- ファイル形式の変換に加え、現在は複数の **AI transforms** があると説明されています。

### 5.5 Fabric IQ sharing

次に、ほかの組織が自分のデータやコンテキストを持ち込めるようにする機能として、最近発表した **Fabric IQ sharing** が紹介されました。同じブランドと見た目で、パートナーとのテナント間でも、自社のチーム内でも、データの移動も複製もなしに共有できます。

- 現在は **public preview** で、企業内なら今日から使える。
- launch partner が提供するデータは、そのパートナーの顧客であればテナントに取り込める。
- 完全にセルフサービスで、誰でも提供者（provider）になれる。
- **今回発表された機能拡張**：当初は shortcut による「データだけ」の共有でしたが、**Fabric ontology も IQ share に含められる**ようになり、データと一緒にコンテキストを共有できる。受け取った側では、その上でエージェントを動かせる。

### 5.6 事例：TomTom

TomTom の Rama 氏が登壇しました。TomTom はアムステルダムに本社を置き、自動車、企業、政府、開発者に向けて位置情報技術のサービスと製品を提供していると説明しています。TomTom のパートナー企業（名称は音声認識の都合で確定できません）は、道路事業者（「Rd. operators」と聞こえます）向けに、料金徴収、交通管理、渋滞の警告などのソリューションを提供しています。

解決しようとしている課題は道路工事です。Rama 氏は、道路協議会（Roads Council）が 2024 年に公表した数字として、**工事区間で 850 人の死亡と 4 万 2,000 件の事故**があったと述べています（この部分は音声が乱れており、文意は聞き取れる範囲です）。この解決策として Fabric DataShare（IQ sharing）を使った、と説明しました。

内容は次のとおりです。TomTom は、地図データと ontology、交通データを OneLake catalog に公開しました。受け手のパートナー企業は、その交通管理システムの側で受信します。流れは、動画のナレーションで語られており、詳細は 6.3 に整理します。ポイントは次の 2 点です。

- **ontology あり／なしの比較**：生のテーブルだけで Airport Boulevard について尋ねると、エージェントは **124 セグメント**しか見つけられません。共有された ontology を使うと **406 セグメント**すべてが見つかります。ontology が、生のテーブルに無いビジネスの文脈と関係を与えるためです。
- **従来との違い**：Rama 氏は、従来はデータを届けるたびに ETL とダウンロードが必要で、地図の更新から顧客側の反映までのサイクルが非常に長かったと説明しています。Fabric IQ sharing では、コピーなしで顧客のテナントにデータがすぐ届きます。ontology を共有できる点が、おそらく最大の差別化要因だとも述べています。

ビデオのあと、Rama 氏の前に話した登壇者（発言者は文面からは特定できません）は、「データはテナントの外に出ず、顧客やパートナーが任意のエージェントで使え、コンテキストも一緒に付いてくる」とまとめています。

### 5.7 オープンなアクセス（Roy 氏）

ここから Roy 氏が登壇しました。OneLake は、Power BI、Excel、Teams、Copilot といった Microsoft ツールに加えて、商用・オープンソースを問わず自分のツールをつなげる、オープンで相互運用可能な基盤だと説明されます。方法は、オープンなテーブル形式と、カタログのフェデレーション（他のカタログとの連携）です。

- **Delta Lake と Apache Iceberg の両方をサポート**し、OneLake が両者を仮想化します。片方で保存すれば、もう片方に変換されるので、どちらの形式を選ぶか悩む必要がありません。
- カタログのフェデレーション：AWS Glue、Google Lakehouse Runtime Catalog、Aviva Connect（綴りは音声認識の都合で確定できません）への GA サポート。いずれも標準的な Iceberg REST API で動き、データのコピーは不要です。
- **OneLake table API**：Apache Iceberg ベースの API と、Unity catalog 向けの API を提供する。Databricks や Snowflake などが同じ API でデータを探索・アクセスできる。
- **カタログ経由の書き込み（今回 GA を発表）**：これまでは、カタログに接続して使えるものを読むだけでしたが、書き込みもできるようになりました。Snowflake、ClickHouse、自己管理の Spark、DuckDB などが table API でデータを書けます。仕組みは credential vending で、アクセス制御は OneLake security だけで定義します。エンジンが読み書きを要求すると、カタログが一時的な認証情報を返し、エンジンは OneLake に直接書き込みます。カタログとストレージの両方でアクセス制御を設定する必要はありません。
- **Azure Databricks の既定ストレージとして OneLake を設定できる**：パイプラインはそのままに、ADLS の管理されたストレージではなく、自分の制御する OneLake に書き込めます。Power BI のダッシュボード、Microsoft IQ、Copilot からも使えるようになります。「今日から使える」と述べられました。

### 5.8 AI 向けデータレイク

shortcuts、mirroring、フェデレーションで統合したデータを、Copilot や Fabric の data agent などの利用につなげる段階です。Roy 氏は 2 段階で説明しています。

**第 1 段階：コンテキストを作る**
- semantic model を使う。すでに 3,000 万以上の semantic model が使われている。
- または、最近リリースされた **ontology agent** を使う。データを調べ、自動でコンテキストを構築する。
- こうしたコンテキストは Fabric IQ に読み込まれ、Fabric IQ の ontology によって、Copilot などのエージェントが、利用できるデータを理解し、その使い方を判断し、バインディングで物理データを読めるようになる。

**第 2 段階：安全にアクセスさせる**
- エージェントは、ローカルのマシンや、誰かが作った任意の場所で動くことがあり、信頼できない場合もあります。そこで、エージェントに安全にデータを読ませるための単一の API として、新しい **table read API** が紹介されました。
- 特定のデータセットに、列レベルと行レベルのセキュリティを含むアクセス制御ポリシーを定義します。その API を信頼できないエージェントやツールに渡し、`select * from customers` のようなクエリを送ると、裏側でポリシーを適用して、許可された行だけを返します。「ポリシーを渡さず、エージェントを信頼せず、こちらが代わりに正しく処理する」という考えです。
- **AI Search や Foundry**で OneLake のデータを取り込む際にも、OneLake security が使える。これまでは広い権限を与えて取り込む必要があったが、きめ細かいアクセス制御が効くようになり、ナレッジベースの構築やモデルの学習に使える。

### 5.9 Semantic views

最後に、新たに発表された **semantic views** が紹介されました。semantic model に近いものですが、セマンティック情報を YAML 形式の**オープンな標準**で定義できます。Roy 氏は、Apache の OSI（incubating）の形式だと説明しています（名称は音声認識の都合で確定できません）。業界の semantic model 間の互換性を作る手段としての位置づけです。実演は 6.5 に整理します。

### 5.10 Q&A

Q&A のやり取りはトランスクリプトに含まれていません（Roy 氏は時間が押しているとして一部のデモを省く旨を述べ、最後はスライドを残して終了しました）。

## 6. デモ・手順

このセッションには 5 つのデモがあります。そのうち 4 つ（Govern、TomTom、Databricks、semantic views）は、ナレーション付きまたは自動再生の録画映像とみられ、Josh 氏の shortcut transformations のデモだけが、本人が操作しながら説明する形でした。画面の細部は音声からは確認できません。以下は、語られた範囲に限ります。

### 6.1 Govern のデモ（ナレーション付きビデオ）

1. Govern は、Fabric のガバナンスを 1 か所にまとめ、データ資産の管理、ポリシーの設定、機能の構成、必要な対応ができる。Govern はガバナンスのシグナルを継続的に分析し、注意が必要な箇所を示す。
2. ある組織（名称は音声認識の都合で確定できません）で、機密性の高いデータのガバナンスの欠落を指摘するレコメンデーションが表示される。
3. どこに注力すべきかを知るため、Copilot に調査を依頼する。Copilot は資産全体のシグナルを分析し、ガバナンス上のホットスポットをすぐに特定する。
4. 新しい **policies** の体験で、外部共有、アイテム作成、ワークスペース管理などのポリシーを定義できる。例として、customer-managed keys などの機密性の高いワークスペース設定を、指定した管理者だけが変更できるようにする。
5. **Policy Center** で、すべてのポリシーと適用先を一覧できる。
6. 今回発表した **Govern skills** を、Copilot で使い、ガバナンスのヘルスチェックを実行する。Govern がギャップを特定して優先度を付け、推奨アクションを示す。
7. 推奨の一つとして、複数のワークスペースがビジネスドメインに割り当てられていない点が挙がる。Copilot に、すべてのワークスペースへのドメイン割り当てを、理由付きで提案させる。
8. 内容を確認して、推奨を適用する。

Dipti 氏は、Copilot を使いながら、ユーザーの承認のもとでガバナンスを進められる、とコメントしています。

### 6.2 shortcut transformations のデモ（Josh 氏）

Josh 氏が画面を操作しながら説明しました。語られた順は次のとおりです。

1. Fabric のワークスペースで、同じワークスペース内の lakehouse を開く。lakehouse は構造化データも非構造化データも扱える。
2. 新しい shortcut を作成し、Salesforce のデータを取り込む。Salesforce は shortcut の 1 つのソースにすぎない。Salesforce の接続情報を入力すると、Salesforce のデータが画面に見える。数クリックで shortcut を作成する。
3. データはテーブルとして表示されるが、コピーではなく、Salesforce にあるデータへの参照である。新しいデータは自動で反映される。
4. 次に、SharePoint にある非構造化のメールのファイルを shortcut で取り込む。今回は、非構造化ファイルであることが検出され、追加のオプションが表示される。
5. **sentiment analysis（感情分析）の transformer** を追加する。各ファイルに対してポジティブかネガティブかを判定し、ファイルごとに 1 行のテーブルにする。
6. こうして構造化テーブルが 2 組になり、結合できる。Fabric の任意のエンジン、Snowflake、Databricks でもテーブルとして使える。Salesforce のデータ（Salesforce から出ない）と SharePoint の非構造化ファイルを、1 つのクエリで結合して 1 つの結果を得られる。

Josh 氏は、新しいデータが来ると変換が起動し、データのコピーは 2 重にならないと説明しています。

### 6.3 TomTom の Fabric IQ sharing のデモ（ナレーション付きビデオ）

登場する組織名は、音声認識で「Caps」「Capture」などと揺れています。以下では、受け手側の組織を「受信側（CAPS と聞こえる）」とします。

1. 受信側が OneLake catalog を開き、TomTom を選ぶと、TomTom のポータルに移る。
2. Orbis の地図と、その ontology、交通のサンプルを選んで、Fabric に取り込む。
3. ほどなく、TomTom のデータと ontology が受信側のテナントで使える。コピーも、構築するパイプラインもない。
4. 新しい点として、TomTom が地図の ontology を共有している。ontology は道路網を、すべてのエンティティと相互の関係を持つ、1 つの接続されたシステムとしてモデル化している。
5. 生のテーブルだけで Airport Boulevard を尋ねると 124 セグメント、共有された ontology を使うと 406 セグメント（前述）。
6. 共有データとつながったコンテキストを使い、ノートブックで、TomTom の過去の交通データを各道路閉鎖に当てはめ、渋滞が発生する時期を予測して、その予測を公開する。
7. 受信側の Roadworks signal planner（Fabric の operations agent）が、予測を TomTom の道路網と突き合わせ、承認されたシグナルを交通管理システムに送る。交通管理システムがドライバーに、渋滞の可能性を警告する。
8. 2 つ目の operations agent が、共有データと ontology を使い、交通が計画から外れた瞬間に通知する。

### 6.4 Azure Databricks で OneLake を既定ストレージにするデモ（Roy 氏）

1. Databricks のコンソールで、テーブルを開く。
2. 詳細を開くと、データが OneLake に保存されていることが分かる（そうでなければ、異なる URL が表示される）。
3. Fabric に切り替えると、テーブルが OneLake catalog に自動で反映されている。それを開いてクエリできる。
4. Databricks 側では、新しく何かを設定する必要はなかった。Databricks を OneLake に向ける設定をしただけだ、と説明している。

### 6.5 semantic views のデモ（ナレーション付きビデオ。GitHub Copilot）

1. GitHub Copilot で、地域の営業チームが再利用できるビジネス指標が、Fabric の OneLake にすでにあるかを尋ねる。
2. Copilot が、ガバナンスされた finance の lakehouse にある semantic view を見つける。DAX の指標と明確な説明を持つ、軽量な YAML 定義（オープン標準ベース）。
3. 指標を作り直す代わりに、再利用を依頼する。Copilot は、OneLake の shortcut（既存と同じ、ガバナンスされた shortcut のフロー）で finance の semantic view を regional sales の lakehouse に取り込む。コピーも、ずれていく 2 つ目の定義もない。
4. 不足している部分だけを依頼する。Copilot は lakehouse のテーブルを読み、新しい semantic view を作成して、finance の shortcut の隣に置く。
5. 公開して、2 つの定義をマージする。すべてのレポートとエージェントが、同じ定義を使える。
6. Power BI の regional performance レポートで検証する。Pacific Northwest の net revenue は 220 万ドル、185 万ドルから 19.1% 増で、GitHub Copilot が確認した内容と一致する。
7. Microsoft Copilot の Contoso sales advisor（エージェント）に同じ指標を尋ねても、同じ答えが得られる。レポートもエージェントも regional sales の semantic view を使うため。

発表では、「一度定義した指標を、オープンな形式で、どこでも使える」ことが価値だと説明されました。

## 7. 注意点・発表者の気付き

- 各機能の提供状況が混在しています。GA、public preview、coming soon（granular lineage は private preview の参加を募集中）が混ざっているので、利用前に確認が必要です。table read API の提供状況は、発表では明示されていません。
- shortcut 経由でも、データはソースのシステムに残ります。SharePoint のように、もともと分析的なアクセスを想定していないソースでは、スロットリングの問題があり、改善によって RU を削減した、という説明でした。
- ストレージの階層化では、アクセスのコストが少し上がるトレードオフがあります。
- エージェントは信頼できない場合があるため、エージェント側にポリシーを渡さず、データ層（OneLake security、table read API）で制御する、というのが発表の設計思想です。
- クローズドソースのシステムは shortcut ではなく、mirroring でレプリケートする必要があると述べられています。

## 8. 利用者にとっての利点

発表で語られた効果は次のとおりです。

| 利点 | 発表での説明 |
| ---- | ---- |
| データ移動の削減 | ゼロコピー・ゼロ ETL。shortcut とフェデレーションで、既存の資産を移行せずに接続する |
| 1 か所での管理 | 管理、課金、監視、セキュリティを OneLake に集約。Snowflake や Databricks のデータも同じ場所に置ける |
| コスト | ストレージの階層化で保存コストを下げる。OneLake はオンデマンド課金で、Fabric キャパシティの購入が不要な使い方もできる |
| 速度 | Dataverse shortcut は初回読み込み 70% 高速化、増分更新は 2 倍超。SharePoint は RU を 67% 削減 |
| セキュリティ | OneLake security による行・列レベルの制御。BYOK、private link、outbound access protection |
| 共有の簡素化 | TomTom の事例：ETL やダウンロードの長い更新サイクルが、コピーなしで即時にアクセスできる形に。ontology により、エージェントの回答が 124 から 406 セグメントに改善 |
| 指標の一元化 | semantic view で一度定義した指標を、レポートとエージェントで同じ答えとして使える |

## 9. まとめ

OneLake は、Fabric のすべてのワークロードのデータを、Delta と Iceberg のオープン形式で 1 つのレイクに保存する基盤です。発表は、ガバナンス（Govern、policies、lineage、OneLake security）、エンタープライズ対応（ライフサイクル管理、ネットワーク保護、BYOK）、統合（shortcuts、mirroring、shortcut transformations、Fabric IQ sharing）、オープンなアクセス（カタログ経由の書き込み、Databricks の既定ストレージ）、AI 向けデータレイク（ontology、table read API、semantic views）の順で、それぞれの新機能と提供状況を示しました。

全体を貫くのは、「データとコンテキストを、コピーせず、ロックインせず、ガバナンスを効かせて 1 か所に集める」という考え方です。事例では、TomTom が ontology ごとデータを共有し、エージェントの回答の網羅性が上がることを示しました。
