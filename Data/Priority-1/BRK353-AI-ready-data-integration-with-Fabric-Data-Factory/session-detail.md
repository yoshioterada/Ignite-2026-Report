# BRK353 AI-ready data integration with Fabric Data Factory

## 1. はじめに

このセッションは、Fabric Data Factory（Microsoft Fabric のデータ統合サービス群）の最新アップデートを、データの「統合・配布」「変換」「オーケストレーション」「AI 活用」「Azure Data Factory からの移行」の順にまとめて紹介する 45 分のブレークアウトです。発表の途中で、デモ（ナレーション付きの画面映像を含む）が複数回挟まれます。

扱われる内容は、Mirroring のセキュリティロール複製、Copy Job と Eventstream の連携、Dataflows Gen2 の Mapping Data Flow（MDF）GA、dbt job と dbt Fusion、パイプラインの新しいアクティビティ、Fabric Unified Copilot、そして Azure Data Factory（ADF）から Fabric への移行ルートです。AI のためのデータ基盤を Fabric Data Factory で整えたい方、ADF を使っていて Fabric への移行を検討している方に向いた内容です。

発表全体の主張は、**AI とエージェントを活かすにはデータ基盤の整備が出発点であり、Fabric Data Factory はそのための統合・変換・配布・オーケストレーションを同じプラットフォーム上で提供する**、というものです。

セッション URL: [https://ignite.microsoft.com/en-US/sessions/BRK353](https://ignite.microsoft.com/en-US/sessions/BRK353)

## 2. 発表者

| 氏名 | 所属 | 役職 |
| --- | --- | --- |
| VIDYA Shanmugam | Microsoft | Partner Director, Product Management |
| Wee hyong Tok | Microsoft | Partner Director, Product Management |

> 補足：冒頭で、一方の登壇者が自己紹介し、同僚をもう一人の登壇者として紹介しています（トランスクリプト上の綴りは音声認識の都合で、メタデータと異なります）。前半（統合・配布・変換）は最初の登壇者が話し、「オーケストレーションと AI への投資」の話は 2 人目の登壇者に交代して進みます。交代時の名前の呼び方は聞き取りが不明瞭なため、本書では 2 人目の登壇者を「後半の登壇者」と書きます。デモの一部は、登壇者が別のチームメンバー（Connie 氏、Shireen 氏）への謝辞を述べる形で紹介されています。

## 3. 何に関する発表か

Fabric Data Factory は、Microsoft のデータ統合製品群です。発表では、これを **unification（統合）、transformation（変換）、distribution（配布）、orchestration（オーケストレーション）** の 4 つの領域として整理し、それぞれに「AI-powered」「enterprise ready」「multi-cloud」という 3 つの投資の柱がかかっていると説明しています。

発表の流れは次のとおりです。

1. 全体像と数字、投資の 3 本柱
2. 統合と配布：shortcut、Mirroring、Copy Job、Eventstream、コネクタ
3. 変換：Dataflows Gen2、Mapping Data Flow、dbt job
4. オーケストレーション：パイプライン、Apache Airflow
5. AI の活用：Fabric Unified Copilot、外部の AI ツールとの連携
6. Azure Data Factory から Fabric Data Factory への移行

冒頭では、デモが 7 つあると予告されています。

## 4. 発表者が伝えたいメッセージ

最初の登壇者は、AI の力を引き出すにはデータから始める必要があり、データ資産を整えてサイロをなくすことが重要だと述べています。Fabric IQ でコンテキストを整え、それをエージェントで実行に移すことも、強固なデータ基盤に依存するという考えです。

もう一つの軸は、変換や取り込みの方法を「選べる」ことです。変換ではローコードの Dataflows から、pro code の dbt job、完全に制御したい開発者向けの Spark ノートブックまでを、同じワークスペース・同じデータのまま選べると説明しています。オーケストレーションでも、ローコードのパイプラインからコードファーストの Airflow までを選べるとしています。

また、冒頭で、コミュニティが投稿・投票する Fabric Ideas がロードマップと優先順位づけに反映されていると紹介し、今後もアイデアを投稿してほしいと呼びかけています。

## 5. 発表内容の詳細

### 5.1 全体像と数字

登壇者は、まず Fabric Ideas に触れ、利用者が投稿・投票したアイデアがロードマップと優先順位づけに反映されていると説明しています。そのうえで、Fabric Data Factory の規模として次の数字を挙げています。

| 項目 | 数値 |
| --- | --- |
| オーケストレーションの実行回数 | 月 250 億回（25 billion） |
| 移動されているデータ量 | 月 853 ペタバイト |
| Power Query のクリエイター数 | 2,400 万人 |
| データゲートウェイ数 | 55.5 万（555K） |

あわせて、Gartner の Magic Quadrant でリーダーに 6 年連続で位置づけられ、直近に発表された最新版では execution（実行力）の軸で 1 位だと紹介しています。

### 5.2 データの統合と配布

統合と配布は「同じコインの裏表」で、データを取り込み、ガバナンスの効いたデータを複数の場所へ出す関係だと説明されています。中心にあるのは、OneLake にデータを統合する **zero ETL** の考え方です。手段として 4 つが挙げられています。

| 手段 | 発表での説明 |
| --- | --- |
| Shortcut | 各クラウドのファイルストアを指すだけで、OneLake 上にデータを仮想化する |
| Mirroring | データベース全体を OneLake に継続的に複製する |
| Copy Job | ペタバイト規模のデータ移動サービス。マルチクラウドとオンプレミスで動作する |
| Eventstream | ストリーミングソースを接続し、転送中に変換ロジックを適用する |

**Mirroring の拡張**

- Google BigQuery の Mirroring を最近開始（BigQuery のデータを zero ETL で OneLake に複製）
- SharePoint と OneDrive の Mirroring が GA。SharePoint のファイルにあるリアルタイムの運用データが、分析で使えなかったという課題への対応として説明されています
- Dynamics 365 Business Central の Mirroring も開始。Power BI のダッシュボードや分析ソリューションで Business Central のデータを使えるようになります
- Extended Mirroring は数か月前に GA。変更データフィード（変更された行だけを移す仕組み）とビューの複製を提供します
- 今回新たに、**セキュリティロールの複製**を開始。これまで OneLake では、セキュリティロールを手作業で再設定する必要があったのに対し、メタデータとロールを継続的に複製できるようになったという説明です。Snowflake 向けが public preview で、Databricks や BigQuery なども順次対応予定、優先したいソースがあれば連絡してほしいとのことです

shortcut とミラーリングのソースは、GA のものが多数、public preview のものもあり、拡大が続いています。Databricks や Snowflake など業界の主要パートナーとのネイティブ統合もあるそうです。

**Copy Job**

Mirroring が使えないソースがあるため Copy Job がある、という位置づけです。バッチ、ストリーミング、CDC を有効にしたパターンに対応します。Ignite での発表は次のとおりです。

1. **Fast delta cloning**：最初の delta クローンの後は、変更分だけをリフレッシュし、コミットの一貫性を保ちます。テストで Fabric Lakehouse 全体が必要な場合は、ファイルとテーブルをまとめてクローンできます。ネイティブな Iceberg のクローンにも対応します
2. **ネイティブ SQL change feed**：注文の配送先変更や重複注文の削除といった挿入・更新・削除を、従来の SQL CDC を使わずに取り込みます。ソースへの読み取りと運用上のオーバーヘッドが減る、という説明です
3. **Horizon DB との統合**：Horizon DB（音声認識の都合で表記が確定できません）の運用テーブルを Fabric Lakehouse に読み込み、アナリストが作ったカスタマーセグメントのような結果を、Copy Job で Horizon DB に書き戻してアプリから使えるようにします。運用 DB への reverse ETL にあたります
4. **Fabric Activator との連携**：固定スケジュールの代わりに、監視している条件が満たされたときに Copy Job を起動できます。実行回数が減り、データも新鮮になると説明されています
5. **Eventstream との連携**：イベントを Copy Job のソースとして、また Copy Job から Eventstream のターゲットとしてデータを送れます。バッチとストリーミングをつなぐもので、Fabric Data Factory ならではの点だと強調されています

配布の面では、Copy Job は任意のソースから任意のターゲットへ、マルチクラウドとオンプレミスで動き、高スループット・低コストで、各実行にモニタリング、ガバナンス、監査が組み込まれていると説明されています。

**コネクタ**

データ移動はコネクタに強く依存するとして、**170 以上の組み込みコネクタ**と、数千のカスタムコネクタがあると紹介しています。

### 5.3 データの変換

OneLake に入ったデータをどう加工するかについて、同じプラットフォーム・同じガバナンス・同じ OneLake のまま扱えるため、新しいツールを買い足したりデータを再び動かしたりする必要がない、と説明されています。利用者に合わせた使い分けは次のとおりです。

| ツール | 位置づけ |
| --- | --- |
| Dataflows Gen2 | ビジネスアナリスト向けのローコード。Excel や Power BI で使われている Power Query が基盤 |
| DBT jobs | アナリティクスエンジニア向けの pro code。ネイティブなオーサリング、デバッグ、オーケストレーション |
| Spark ノートブック | データエンジニア・開発者が完全に制御したい場合 |

同じワークスペース・同じデータのまま、プロジェクトを移行せずにツールを切り替えられるという説明です。

**Dataflows Gen2**

改善点として、リフレッシュの高速化（copy の最適化）、新しい出力先、Monitoring hub と診断のネイティブ対応が挙げられています。出力先は、今年最も進捗が早かった領域だと述べられています。

- もともと Fabric ファミリー（Lakehouse、Warehouse）や Azure SQL DB には対応済み
- GA：Snowflake データベース、Excel ブック、Ignite 時点で PostgreSQL も
- プレビュー：リッチな Excel 書式設定とメール
- Ignite で開始：Amazon S3 と Google BigQuery というマルチクラウドの出力先

変換ロジックは出力先を意識せず、一度作れば Fabric の内外どこへでも出力できる、という考えです。

また、Power BI ユーザーから要望の多かった **モダンな Power Query エクスペリエンスが Power BI Desktop で利用可能**になったと説明されています。マルチステップのオーサリングや、クエリ折りたたみ（folding）インジケーターでの性能最適化が可能です。今後の Power Query の新機能は Power BI Desktop にも入り、Fabric、Excel、Power BI のどこでも同様の体験になるとのことです。

**Mapping Data Flow（MDF）の GA**

ADF の mapping data flows に慣れた方向けに、Fabric の Dataflows Gen2 で MDF が GA したと発表されています。Spark スケールの変換を、ノーコード/ローコードのビジュアルなドラッグ＆ドロップで作れる機能です。

**dbt job**

- ネイティブな dbt job が GA。dbt プロジェクトを Fabric に持ち込めば、インフラ構築、CLI、クラスター管理が不要で、サーバーレスの dbt Core ランタイムで動きます
- Fabric Lakehouse に対応（従来の Warehouse 対応を拡張）
- Variable library に対応し、同じ dbt プロジェクトを dev/test/prod に移せます
- Microsoft と dbt Labs の強いパートナーシップの成果として、**dbt Core V2（Fusion エンジン）の対応を今週 public preview で開始**。パースとコンパイルの高速化により、プロジェクトが大きくなったときの開発者の待ち時間が減る、という説明です

### 5.4 オーケストレーション

後半の登壇者が引き継ぎ、オーケストレーションは **パイプライン**と **Apache Airflow** の 2 本立てだと説明しています。Airflow はオープンソースコミュニティで最も急成長しているプロジェクトの一つで、コードファーストを好む声に応えて Fabric に持ち込み、ランタイムの運用を Fabric 側が担うサーバーレスにしたとのことです。DAG コードを Fabric のエディターで書く場合も、ビジュアルでパイプラインを作る場合も、エラーメッセージの理解と対処に AI を使えるようにしたい、という方針が語られています。

GA になった機能は次のとおりです。

- **Lakehouse maintenance アクティビティ**：ノートブックチームと作ったもので、vacuum や optimize などで Lakehouse の性能を保ちます
- **Outlook アクティビティ**：メール送信に自分の認証を使うのではなく、SPN とワークスペース ID に対応しました
- **Smart retries**：一時的な障害に備え、指数的なバックオフで再試行します（従来のリトライは期待どおりではなかったという説明です）

public preview の機能は次のとおりです。

- **Approval アクティビティ**：今年の早い時期に導入された、パイプラインに human in the loop を入れる機能です。今回、**agentic approval** のサンプルコードが GitHub で公開され、人とエージェントが協調して承認できるようになりました

次の 2 点は、提供状態の説明が異なります。

- **パイプライン依存関係**：あるパイプラインが別のパイプラインの実行後にのみ動くように、柔軟な依存関係を組めます（提供状態については、発表では明言されていません）。Ignite での新発表ではなく、**tumbling window trigger**（ADF にある仕組み）に向けた一歩だとしています
- **tumbling window trigger**：private preview が「もうすぐ」始まる、と述べられています

**モニタリング**

FabCon Barcelona で導入されたモダンな Monitoring hub に、リッチなパイプライン診断（低レベルのエラー詳細を含む）が加わりました。さらに、**パイプラインごとの CU 消費**が Ignite から見えるようになりました（コミュニティの要望が大きかった項目、とのことです）。

**Fabric action アクティビティ**

従来、パイプラインから Fabric の公開 API を呼ぶには、Web アクティビティで REST URL と認証ヘッダーを自分で組み立てる必要があり、各ステップでミスが起きやすいという課題がありました。Fabric action アクティビティは、Fabric にログインしていれば認証を引き受け、内部の Swagger（Swagger ファイルは公開されています）から URL とパラメーターを把握し、ドロップダウンで任意の Fabric API を呼べるようにするものです。

**Business action アクティビティ**

パイプラインから HubSpot、ServiceNow、Jira などの業務システムを呼ぶには、Logic Apps や Power Automate（トランスクリプトでは音声認識の誤りで別表記）といった別のツールを併用する必要があるという声がありました。そこで FabCon Barcelona で発表されたのが Business action アクティビティです。当時は約 40 のシステムに接続でき、現在は **80 以上**になり、今後数か月で 100 を超える見込みと述べられています。ドロップダウンにないシステムは、メールで要望を送ってほしいとのことです。

### 5.5 AI の活用

Fabric Data Factory を Fabric Unified Copilot と深く統合しており、Claude や GitHub Copilot CLI といった外部ツールからも、UI でできることを一通り操作できると説明されています。データの発見、移動、変換、オーケストレーションまで対話で扱えます。

AI との対話は「その場限り」になりがちなため、対話で作ったものを Dataflow やパイプラインとしてワークスペースに**永続化**し、確認や運用化に進められるようにした、という点が強調されています。また、Fabric skills に Airflow オーケストレーションと dbt 変換が preview として加わったとのことです。

### 5.6 Azure Data Factory から Fabric Data Factory への移行

ADF から Fabric への移行は「旅の第 1 部」で、FabCon Barcelona で出した **View in Fabric** が最初のステップです。名前に反して、閲覧だけでなく ADF の内容を Fabric 上で管理・運用でき、ワークロードは引き続き Azure 上で動いたまま、Fabric のモダンな体験を安全に試せる、と説明されています。Azure Data Factory の枠を超えて Fabric の機能も使えるようになり、TCO の最適化に役立つ可能性がある、とも述べられています。

View in Fabric では **readiness assessment** を実施できます。すべてのパイプラインについて、そのまま移行できるもの、できないものを確認し、移行できるものはボタン一つでネイティブな Fabric パイプラインに変換できます。移行は準備ができたときに行えばよく、旅の第 2 部は、おそらく数か月後に共有するとのことです。

パートナー支援として **Microsoft Certified Partners プログラム**も紹介されています。QR コードまたは URL から登録するとパートナーとマッチングされ、パートナーが環境にアクセスして無料の評価を行い、利用者が承認した計画に基づいて、進めるかどうかを利用者が決める流れです。

## 6. デモ・手順

デモが複数ありました。**ただし、録音は音声のみのため、操作はナレーションで語られた範囲だけを記します。** 画面の細部は音声からは確認できません。

### 6.1 Snowflake の Mirroring とセキュリティロールの複製

1. Snowflake で、customers、sales、orders などのテーブルとビューを確認します。セキュリティロール、ユーザー、権限が既にアクセスを制御しています
2. support group ロールを開き、付与されたユーザーを確認します。ユーザー Aaron は orders、products、sales の各テーブルにだけアクセスでき、すべてのテーブルは見られません
3. Fabric で新しい Snowflake の Mirror を作成します。複製するテーブルとビューを選択します。Snowflake のビューは現時点で GA で、データと一緒にミラーできます
4. 新しいオプション **replicate security rules** を、単一のトグルで有効にして複製を開始します
5. 結果を確認します。Snowflake と同じテーブルとビューがあり、Manage OneLake Security タブにレプリケートされたロールが表示されます。support group ロールには、Snowflake と同じテーブルと、Aaron を含む同じユーザーが引き継がれています
6. Aaron として Fabric にサインインし、SQL analytics endpoint を開くと、orders、products、sales は見えます。customers テーブルにアクセスしようとすると、拒否されます
7. 管理者のアカウントに戻ると、Aaron が見られなかった customers を含むすべてのテーブルとビューを確認できます

ナレーションでは、Fabric は単にセキュリティのメタデータをコピーするのではなく、既存のセキュリティモデルを維持し、実際に強制している点が重要だと述べられています。

### 6.2 Copy Job と Eventstream、Activator

シナリオは、Snowflake のデータウェアハウスでチケットを販売する Sports Fans Ticket という会社で、運用チームが高額予約のリアルタイム通知を求めているという設定です。

1. ワークスペースで新しい Copy Job を作成し、名前を「Snowflake Ticket Ingestion」にして Create
2. ソースに Snowflake を選び、server、warehouse、database などの接続情報を入力
3. fan event スキーマの ticket transactions テーブルを選択（チケット購入、ファン ID、イベント名、金額、タイムスタンプを持つテーブル）
4. 宛先に、ticket sales event stream という名前のストリームを選択（これが新機能）。Copy Job が Snowflake から読んだ行を、1 行ずつイベントとしてストリームに送ります
5. 増分モードを設定し、ウォーターマーク列に updated at を指定。実行のたびに新規・変更されたトランザクションだけが取り込まれ、重複や全件再ロードはありません
6. 保存して実行。Copy Job は 2 分ごとに Snowflake に接続し、新しいトランザクションを読み取ります
7. Eventstream 側では、Snowflake ソースの Copy Job がソースとして追加されており、データプレビューでファン ID、イベント名、チケットの種類、購入金額を持つ取引が届いていることを確認。データは Eventhouse の宛先にも流れています
8. curated な Eventstream を読む **Activator ルール**を追加し、ファンの支出が 1,200 ドルを超えるたびにアラートを出す設定にします。Teams のメッセージを、必要な情報を含むようにカスタマイズします
9. ルールを作成するとイベントストリームの宛先として表示され、数分後には Teams のアラートでコンシェルジュチームに VIP の購入が通知されます

### 6.3 Power BI Desktop のモダン Power Query エディター

約 1 分のデモとして紹介されました。ナレーションでは次の要素が紹介されました。クエリとその接続をまとめて見られる diagram view、列に集中できる schema view、常に手の届く位置にある列アクション、各変換を示す query folding インジケーター、ステップの評価方法を調べられる query plans、ダークモードへの対応、M コードを直接編集できる Query Script です。

### 6.4 Dataflows Gen2 の MDF transforms

1. 「Run MDF transforms」を選び、Spark スケールの変換をローコードのビジュアル操作で作ります
2. 既存の ADF や Synapse の mapping data flows は、対応している変換ロジックを保ったまま、ガイド付きの体験でアップグレードできます。Dataflow Gen2 内で新規作成することもできます
3. Dataflow Debug をオンにして、データのプレビューとロジックの検証をしながら作ります
4. ソースを追加し、変換を設定し、出力先を定義します。実行、スケジュール、または Fabric Pipeline の Dataflow アクティビティからのトリガーが可能です
5. 実行は、クラスターの起動を含めて約 5 分で成功。Monitoring Hub では、変換診断で規模が確認でき、1.5 億件（150 million）の orders と 1,500 万件（15 million）の customers を結合・集計して、5 分強で完了したことが示されました。各ストリームの詳細な実行情報も確認できます

### 6.5 dbt on Fabric（laptop から本番まで）

1. Fabric の Warehouse に 10 万行の注文明細（100,000 order lines）があるところから始めます
2. VS Code など既存のツールで、ローカルに dbt プロジェクトを作成します
3. GitHub Copilot がプロジェクトの readme をコンテキストにして、ソース、ステージングモデル、ファクトテーブル、sales mart、データテストを生成します。利用者が差分を確認してコミットし、リポジトリにプッシュします
4. プロジェクトを Azure DevOps または GitHub に同期したら、Fabric の dbt job をそのリポジトリに接続します。接続とブランチを選び、ターゲットの Warehouse とスキーマを選びます。リポジトリが単一の信頼できる情報源（single source of truth）です
5. 設定で、今 preview で使える最新の Fusion を選択します。Fusion ランタイムにより、dbt プロジェクトがより高速・効率的に動きます

### 6.6 Business action アクティビティ（Stripe）

Connie 氏によるデモとして紹介されています。

1. アクティビティのカタログで Stripe を検索し、顧客を取得します。必要な入力は顧客 ID の 1 項目だけです
2. このパイプラインを単独で実行すると、名前、メール、請求設定を含む顧客レコードが返ります
3. 同じアクティビティを実際のパイプラインの中で、重複しない顧客を順にループ処理し、顧客ごとに Stripe を呼び出します。すべての注文に顧客情報が付いた状態で返ります

### 6.7 Fabric Unified Copilot（データ統合）

Shireen 氏によるデモとして紹介されています。

1. Q4 の店舗パフォーマンスレポートを VP 向けに用意するアナリストという設定で、自然言語で依頼します。月次目標と実績の比較、ファイルにある店舗の注釈の取り込み、結果を Lakehouse のテーブルとして出力すること、です
2. ステップごとにガイドしてもらうか、フルの autopilot モードにするかを選べます。ここでは autopilot を選びます
3. Fabric IQ が計画を作り、関連する Lakehouse のテーブルとファイルを見つけ、必要な変換を特定します。Dataflow が使っているデータも確認できます
4. Copilot が Dataflow を作成し、検証用の準備をして出力をプレビューします
5. 検証中に、Lakehouse 接続での navigation key の不一致を検出します。Copilot は止まらず、Power Query の定義を開いて調査し、M のクエリロジックを更新して自動で修正します
6. 修正後のプレビューは 24 行を返し、Seattle が 10 月の目標を逃したこと、オンラインチャネルが四半期の多くの期間で目標を下回っていたことが分かります
7. Dataflow を保存してリフレッシュを開始し、完了後にはデータをプレビューでき、Lakehouse に具体化されます

### 6.8 View in Fabric

このセッションでは実演されていません。デモはオンラインで公開し、YouTube にも載せる予定だと述べられています。

## 7. 注意点・発表者の気付き

- 発表では、**Snowflake のセキュリティロール複製は public preview** と明言されています。Databricks、BigQuery などは今後の対応です
- tumbling window trigger は、今回の Ignite の発表ではなく、**private preview がもうすぐ始まる**段階です。パイプライン依存関係はその一歩手前の機能と位置づけられています
- Approval アクティビティと agentic approval は public preview です
- dbt Fusion（dbt Core V2）は public preview で、dbt job の設定から選びます
- ADF の View in Fabric は、第 1 部としてワークロードを Azure 上で動かし続けたまま Fabric を試すためのものです。ADF 側では引き続き実行され、移行は準備ができた時点で選べます
- リッチな Excel 書式設定とメールの出力先は preview です
- Copilot のデモは「氷山の一角」だと説明されています。登壇者は、うまくいった点・いかなかった点のフィードバックを共有してほしいと呼びかけています

## 8. 利用者にとっての利点

発表で語られた利点を整理します。

- **セキュリティロールの手作業が不要に**：Snowflake のロールとメタデータが継続的に複製され、Fabric 側でも同じ制限が強制されます
- **運用負荷とコストの低減**：ネイティブ SQL change feed により、ソースの読み取りと運用上のオーバーヘッドが減ります。Activator での起動により実行回数が減り、データも新鮮になります
- **新しいツールの追加が不要**：変換、取り込み、オーケストレーションを同じプラットフォーム、ガバナンス、OneLake で扱えます
- **性能と規模**：MDF は 1.5 億件の orders と 1,500 万件の customers の結合・集計を、クラスター起動を含めて約 5 分で完了しました（デモでの数値）
- **インフラ管理が不要**：dbt job はサーバーレスの dbt Core ランタイムで、CLI やクラスターの管理が要りません
- **ツールの集約**：Business action アクティビティで、Logic Apps や Power Automate などを併用せずに業務システムを呼べます。Fabric action アクティビティでは URL や認証ヘッダーの組み立てが不要です
- **トラブルシュートの高速化**：Monitoring hub の低レベルのエラー詳細で、原因特定と復旧が早くなると説明されています。パイプラインごとの CU 消費も見られます
- **ADF からの安全な移行**：readiness assessment と、ボタン一つでのパイプライン変換、パートナーによる無料の評価が用意されています

顧客名を挙げた導入事例は、発表では紹介されていません。

## 9. まとめ

このセッションは、Fabric Data Factory を「統合・配布」「変換」「オーケストレーション」の 3 段階と、AI の活用、ADF からの移行という流れで紹介したものです。統合では、Mirroring のセキュリティロール複製（Snowflake、public preview）と、Copy Job の新機能（fast delta cloning、ネイティブ SQL change feed、Horizon DB 統合、Activator および Eventstream との連携）が目玉でした。

変換では、Dataflows Gen2 の出力先拡大、MDF の GA、dbt job の GA と dbt Fusion の public preview が発表されています。オーケストレーションでは、Lakehouse maintenance、Outlook アクティビティ、Smart retries が GA に、Approval アクティビティの agentic approval が preview になりました。パイプライン依存関係は tumbling window trigger への一歩として紹介されています。Fabric action アクティビティ、Business action アクティビティ（80 以上のシステム）、パイプラインごとの CU 消費も紹介されています。

AI の面では、Fabric Unified Copilot や外部の AI ツールから対話でデータ統合を進め、成果物をワークスペースに永続化して運用できます。ADF を使っている場合は、View in Fabric と readiness assessment、そしてパートナープログラムが移行の入り口になります。
