# BRK372 Where agents meet analytics: Build with SQL Database in Fabric

## 1. はじめに

このセッションは、Microsoft Fabric 上のマネージドな SQL データベース「SQL database in Fabric」が何で、どんな場面で使え、この 1 年で何が追加されたのかを、デモ付きで解説した 45 分の Breakout です。SQL Server や Azure SQL を使っている開発者・DBA が、Fabric 側で何を得られるのかを知るのに向いています。

前半は Microsoft の Anna Hoffman 氏が、架空の企業 Caldova のカフェテリアの食品廃棄アプリを題材に、Fabric apps から作ったアプリ、データベースのエディタ、mirroring、lakehouse と data agent、ソース管理までを順に見せます。後半はチェコの Uniwise の CTO である Lumir Hlavac 氏が、財務計画アプリ Targety を Fabric に載せた経験と学びを、サンタクロースを題材にしたデモとともに紹介します。

結論として、登壇者が最後に強調したのは「Azure SQL Database から全部 Fabric に移す必要はない」という点です。mirroring を有効にするだけでも多くの価値が得られ、新しいアプリを作る場合や Fabric 内でデータベースが必要な場合に SQL database in Fabric が有力な選択肢になる、という立場です。

セッション URL: [https://ignite.microsoft.com/en-US/sessions/BRK372](https://ignite.microsoft.com/en-US/sessions/BRK372)\
YouTube URL: [https://www.youtube.com/watch?v=oSrdkHz4bzg](https://www.youtube.com/watch?v=oSrdkHz4bzg)

## 2. 発表者

| 氏名 | 所属 | 役職 |
| ---- | ---- | ---- |
| Anna Hoffman | Microsoft | Principal Group Product Manager |
| Lumir Hlavac | Uniwise | CTO |

> 補足：Anna Hoffman 氏は冒頭で自己紹介しており、SQL チームの principal group product manager と話しています。Lumir 氏は途中で紹介を受けて登壇し、「CTO at Uniwise」と自己紹介しています（音声では「Lomir」などと聞こえます）。

## 3. 何に関する発表か

テーマは、運用系（トランザクション）データの置き場である SQL データベースを Fabric に持ち込むと、アプリ、AI エージェント、分析が 1 つの基盤で結びつく、という点です。

発表の流れは次のとおりです。

1. SQL database in Fabric の位置づけと、同じ SQL エンジンであること
2. Fabric apps で作ったアプリと、データベースの使われ方
3. この 1 年の新機能（エディタ、max vCore、vector index、Database Hub など）
4. 内部構成（Fleet Manager、serverless、OneLake への mirroring）
5. mirroring の仕組みと、他の SQL への適用（デモ）
6. lakehouse の shortcut、data agent と Microsoft 365 への公開（デモ）
7. ソース管理、移行、変更イベントのストリーミング
8. Uniwise の Targety の事例とデモ
9. 補足（Spark コネクタ、skills、Fabric IQ）とまとめ

題材の Caldova は、Ignite の複数のセッションに登場する Microsoft の架空の企業名で、世界中に拠点があり、各地のカフェテリアでメニューと取引を記録している、という設定です。AI でアプリを簡単に作って展開できる反面、アプリが増えすぎる（proliferation）ため、エンタープライズ向けの準備とガバナンスが必要になっている、という状況から始まります。

## 4. 発表者が伝えたいメッセージ

- Azure SQL Database、SQL Managed Instance、SQL database in Fabric、SQL Server は、どれも**同じ SQL エンジン**で、T-SQL もツールも共通です。クラウド向けのエンジンは常に更新され、数年ごとにそれを切り出したものが SQL Server の次期バージョンになる、という説明です。
- Fabric はもともと分析のプラットフォームでしたが、運用・トランザクションのワークロードを持ち込んで、データプラットフォームに変わりました。SaaS データベースを 1 つの capacity で他の体験と統合する、というのが狙いです。
- 設計の柱は 3 つで、simple（シンプル）、autonomous and secure（自律的で安全）、optimized for AI（AI 向けに最適化）です。ただし顧客から「完全な SaaS ではなく、ある程度のコントロールが欲しい」という声があったため、SaaS をデフォルトにしつつ PaaS 的に設定できる形に調整してきた、と説明しています。
- 登壇者は、ここ 2 年（昨年の Ignite で GA、その前の Ignite で存在を発表）の歩みを踏まえ、フィードバックに基づいて機能を追加してきたと述べ、フィードバックを求めて締めくくりました。

## 5. 発表内容の詳細

### 5.1 SQL database in Fabric の位置づけ

- 同じエンジンで、Azure SQL Database、SQL Managed Instance、SQL database in Fabric が動き、同じ T-SQL と同じツールが使える。
- 今週のキーノートで、HorizonDB も Fabric に入ったこと、Cosmos DB in Fabric もあることに触れています。
- Fabric apps（登壇者は Raven という名前でも呼んでいます。音声認識の都合で表記が確定できません）が今週 GA になりました。Fabric apps は、アプリの急増、特に社内アプリを管理しやすくするもので、Raven と GitHub Copilot などのエージェントを組み合わせて AI でアプリを開発し、デプロイすると Fabric のアイテムとして置かれる、という説明です。データベースが必要なら、裏で SQL database in Fabric が作られ、Postgres を使うように設定することもできます。
- Fabric apps は数か月前の public preview 以降、10 万を超えるアプリがデプロイされたという統計を読んだ、と述べています。
- Fabric のサイトでは、手順書ではなく「プロンプトをコピーする」だけで始められ、エージェントが必要な文脈をすべて得られる、と紹介しています。

### 5.2 主な利用シナリオ

登壇者の見立てでは、現状で最も多いのは分析の周辺です。

- 分析で DB が必要になる場面。以前は Azure でデータベースを別に作って管理する必要があったが、統合されて簡単になった。
- メタデータ駆動のパイプライン。
- reverse ETL。gold 層の後で、高い同時実行性と低レイテンシが必要な場面。
- Power BI の writeback。
- Fabric apps。

### 5.3 シンプルな統合体験

Azure ポータルで Azure SQL Database を作るときは、ハードウェア、tier、コア数、serverless か provisioned かなど、多くの選択が必要です。Fabric SQL は「名前を入れれば作る」方式で、裏側で次を担います。

- serverless、オートスケールを維持し、使われないときは一時停止する
- multi-AZ をデフォルトにする（選択の余地はない）
- Fabric 内と各種ツールでの Copilot による AI 支援
- T-SQL のサポート
- レポート、パイプラインなどすべてを 1 つの Fabric capacity で扱う

### 5.4 この 1 年の新機能

**作成・管理まわり**

- SSMS から作成できるようになった。
- collation への対応。REST API で作成時に指定できる（要望が多かった）。
- エディタの改善と、Fabric skills の一部として SQL database 用の skills を追加（管理側の作成・運用の支援）。
- **max vCore の設定**: 今週 GA。支出をより管理したいという要望に応えた設定。デフォルトの serverless は 0 から 32 コアで、メタデータ駆動のパイプライン用などでは 32 コアまで上がる必要がなく、小さい上限を勧めています。
- ALTER DATABASE SET コマンドの対応を拡充。最初は SaaS なので不要と判断したが、要望があって戻した。
- 削除したデータベースのポイントインタイムリストアが GA。一定期間は削除後も復元できる。

**検索・AI まわり（SQL エンジン側の機能）**

- ベクトルインデックスが GA。登壇者は「disk scan アルゴリズム」と述べています（音声認識の都合で表記が確定できません）。業界で最速だと考えている、と述べています。
- あいまい文字列一致の新しいアルゴリズム。すべての検索が AI を必要とするわけではない、という趣旨です。
- Regex の機能拡充。

**Database Hub と agent**

- **Database Hub** が今週 GA。Fabric のデータベースだけでなく、Arc 対応の SQL Server、Postgres、Cosmos DB、Azure SQL、Fabric SQL といった Microsoft 管理のデータベースをまとめて見られる入口で、Fabric capacity は不要です。Fabric SQL を使っていなくても確認する価値がある、と述べています。
- Copilot in Database Hub と Database Agents を導入。特にパフォーマンスとセキュリティで、リアクティブな AI から、プロアクティブで最終的には完全に自律的なものへ向かう方向を目指していると説明しています。

**セキュリティ**

- auditing が今年 GA（デフォルトでは有効でないが、簡単にオンにできる）。
- workspace レベルの private link が public preview。tenant レベルの private link は GA。

### 5.5 内部構成

SaaS なので意識する必要はないが、よく質問されるので、という前置きで説明されました。

1. 裏側では Azure SQL Database が動いている。
2. Fleet Manager が、すぐ払い出せる多数のホットなデータベースを持っているため、新しいデータベースを約 10〜20 秒で提供できる。
3. serverless で 0 から 32（デフォルト）までスケールする。
4. MDF と LDF ファイルを持つ通常のデータベースで、OneLake 上でネイティブに動くものではない。そのデータをすべて OneLake にミラーリングすることで、shortcut などが使える。

### 5.6 mirroring

mirroring は、データベースから OneLake へのほぼリアルタイムのレプリケーションで、データは Delta Parquet 形式で OneLake に置かれます。デプロイすると、次の 2 つのアイテムが見えます。

| アイテム | 役割 |
| ---- | ---- |
| SQL database | 読み書きできる本体 |
| SQL analytics endpoint | OneLake に複製されたデータ上のエンドポイント（ライブの DB ではない）。主に読み取り用だが、ビューなど一部の書き込みはできる |

- エディタで 2 つのエンドポイントを切り替えられる。
- SQL database in Fabric では mirroring がデフォルトで有効で、オフにも、テーブルを選ぶ選択的ミラーリングにもできる。デフォルトは全テーブルのミラーリング。
- mirroring は、他の Microsoft SQL データベース（Managed Instance、サポート対象の SQL Server、SQL Server 2025 など）でも設定でき、それほど難しくない。違いは、SQL database in Fabric ではデプロイ時に自動で設定される点。
- ストレージは capacity のサイズまで無料（いくつか但し書きあり）。

方式の違いは次のとおりです。

| 対象 | 方式 | 必要なもの |
| ---- | ---- | ---- |
| Azure SQL、SQL database in Fabric、SQL Server 2025 | change feed（SQL から Fabric へ押し出す） | 特になし（発表で触れた範囲） |
| 古いバージョンの SQL Server | CDC（Fabric が SQL から引く） | SQL Server Agent、オンプレミスのデータゲートウェイ |

> 注意：ゲートウェイは、パブリックエンドポイントを使うか SQL database in Fabric でない限り必要です。Azure SQL Database でも private link を使っている場合は設定が必要、と説明されました。

### 5.7 lakehouse、shortcut、data agent

Caldova のメイン取引システムは、今回作ったデータベースではなく **Azure SQL Database Hyperscale** にあるという設定で、そこにも SQL Server の mirroring を設定します。

- ミラーされたデータベースにも SQL analytics endpoint ができる。メニュー項目や、拠点ごとの日別の取引（何をいくつ提供したか）が含まれる。
- データがすべて OneLake に着地したら、lakehouse を作り、shortcut を設定する。shortcut はデータを動かさず、OneLake 内のデータへのポインタ。
- 移動なしで、notebook、data agent、SQL analytics endpoint、eventhouse endpoint などが使える。
- レポートで傾向を見ると、廃棄が最も多いのは金曜日だった（出社が少ないからではないか、特定のメニューを作りすぎているのではないか、という見立て）。
- レポートの代わりに **data agent** を作れる。instruction、例、サンプルクエリを与える。「どの拠点で廃棄が多く、何が原因か」と尋ねると、lakehouse の shortcut を使って、Barcelona が最も多いこと、金曜が主な問題であること、金曜の品目ごとの廃棄率などを返した。
- data agent は Microsoft 365 に公開でき、組織の他のメンバーが普段使う Copilot から使える。Teams でも使えるように設定できる。

### 5.8 ソース管理、移行、ストリーミング

- ワークスペースを GitHub または Azure DevOps のリポジトリにつなぐだけで同期される。変更を commit でき、リポジトリ側の更新をワークスペースに適用もできる（双方向）。Fabric の多くのアイテムがソース管理に対応し、deployment pipelines も使える。
- 既存の SQL のツール（SQL database projects、SqlPackage、VS Code の MSSQL 拡張、SSMS）が使える。SQL projects の GA サポートは約 1 か月前に発表。
- SQL projects に、インポート・エクスポートの API、pre/post デプロイスクリプトを追加。例えば dev データベースにサンプルデータを事前投入する post deployment script を設定できる。
- 移行: データウェアハウス向けの移行体験に加え、今年は SQL database in Fabric への移行体験も追加。DAC pack でスキーマを持ち込み、データの移動は複数の方法があり、Fabric Data Factory やデータパイプラインを使う方法を推奨している。
- **change event streaming**: Microsoft SQL 全体で発表。preview。モダンなデータストリーミングの方法で、RTI を使う場合は Fabric の Event Hubs（発話どおり）に流せる。

### 5.9 Uniwise の事例（Lumir Hlavac 氏）

**会社と製品**

- Uniwise はチェコを拠点とし、データと AI の Microsoft パートナーで、約 30 年の歴史があり、SQL 2000 から使っている。チームは財務の専門家、開発者、データエンジニア。
- 製品は Targety（音声では Targety / targeted などと聞こえます）。計画と財務レポート向けで、世界中で約 3,000 人が使っている。
- 新バージョンは Fabric ネイティブで、Fabric apps と SQL database in Fabric を使う。今年中に Azure Store（聞こえた表記）への移行と、Fabric workload としての提供を計画している。

**なぜ Fabric か**

Fabric 以前は、Azure SQL、Analysis Services、アプリケーションサービスなど多くの Azure サービスを、デプロイし、保護し、つなぎ合わせる必要がありました。Fabric に移すと、アプリ、データベース、分析がつながり、1 つの Fabric capacity ですべてを賄えるようになった、と述べています。

**アーキテクチャ**

1. 全体が Fabric capacity 内で動く。
2. ワークスペースは 2 つ（アプリ用とデータ用）。
3. クライアントが Web アプリと Excel の 2 種類あるため、API レイヤーで同じデータを提供する。
4. データはすべて SQL database in Fabric に保存し、選択したテーブルを OneLake にミラーして、レポート用の semantic model に使う。
5. その上に、計画プロセス全体を記述する ontology がある。

**SQL database の使い方**

- メインのトランザクション層。計画データ、計画フォームのメタデータ、監査ログを保存する。
- ビジネスロジックはすべてストアドプロシージャで、アプリはそれを実行する。
- mirroring でデータを OneLake に流す。

**AI の使い方**

- 利用者向け: 自然言語で計画や予測を作れる（例:「actual を 3% 増やして」「新車を 3 月に入れて」）。エージェントが計画を用意し、ユーザーが承認または却下する。AI skills と MCP サーバーも提供し、AI Foundry（発話どおり）や Copilot から接続して、他のアプリとのワークフローで使える。
- 開発プロセス: タスクを定義してリポジトリに保存し、エージェントがコーディング、別のエージェントがレビュー、CI とテストを実行し、人が承認してから Fabric などの環境にリリースする。同じコンポーネントがどこでも動くため、Fabric、Azure、さらにオンプレミスにも提供できる。

**学び（Lumir 氏のアドバイス）**

1. 実際のワークロードを計測する。SQL database in Fabric はデフォルトで 32 コアを使い、capacity を使い切って他のアイテムにリソースが回らなくなる。選択肢として 2、4、32 がある（音声のとおり）。
2. mirroring はレポートに必要なテーブルだけにする。
3. mirror されたデータは SQL database に書き戻されないため、データの保護は SQL database 以外の場所でも考える必要がある（発話は「mirror data are not mirrored from SQL database」。要旨をこう解釈しています）。
4. ワークスペースは 2 つに分ける。ユーザーにはアプリ用ワークスペースのアクセス権だけを与え、データベースにはアクセスさせない。
5. serverless は、使われないと眠る。アプリ側でタイムアウトとリトライを扱う必要がある。

Anna 氏の補足は、serverless は使われないと約 10〜15 分で一時停止し、再開に通常 10〜20〜30 秒かかるため、適切なリトライロジックが必要で、ウォームアップが必要ならウォームアップクエリも使える、というものです。

### 5.10 その他の補足

- **Spark コネクタ**: SQL database in Fabric だけでなく他の Microsoft SQL でも使えて、Spark の力でより高速に処理できる。
- **skills for Microsoft Fabric プラグイン**: 現状は管理・メンテナンス寄り。
- **Fabric IQ**: GitHub Copilot などのプラグイン経由で、ontology から得られる情報を、作業中の他の場所へ持ち込める。Lumir 氏が ontology と Fabric IQ をエージェント構築に使っていることにも触れています。
- 顧客企業の例がスライドに示され、主な使い道は AI アプリを作ること、運用ワークロードと分析ワークロードを 1 つにして洞察を得ること、と説明しています。社名は発話では読み上げられていません。

### 5.11 Q&A

このセッションでは質疑応答は行われていません。Anna 氏は「今日は質問を受けなかった」として、セッション終了後に後ろか廊下で質問を受けると述べています。

## 6. デモ・手順

デモは行われています。録音は音声のみのため、操作の細部は語られた範囲だけを書きます。一部（Microsoft 365 への公開、Santa のデモ）は、動画を再生していると受け取れる発話があります（"make sure it plays"、"Can we take a look?"）。ライブ操作か録画かは音声からは断定できません。

### 6.1 Anna 氏のデモ（Caldova の食品廃棄アプリ）

1. 廃棄ログ用アプリを見せる。拠点ごとにログインし、シフト終了時に廃棄した食品（例: クロワッサン 100 個）を記録する。Fabric apps で作ったもの。
2. Fabric のサイトで、アプリを公開した画面を見せ、プロンプトをコピーするだけで始められることを示す。
3. データベースのエディタをツアーする。このデータベースは Fabric app の一部なので、アプリが管理しているため触りすぎないよう案内が出る。クエリエディタ、Object Explorer、設定（sensitivity label、endorse）、compute の max vCore、好みのツールで開く機能、直近 24 時間のメトリクスとクエリ診断のガイダンスを持つパフォーマンスダッシュボード、Copilot（読み取り専用と、書き込み承認つきの agent モード）、レプリケーション、auditing。
4. ワークスペース画面で、アプリ、SQL database、その下の SQL analytics endpoint（mirroring）を見せる。
5. 既存の Azure SQL Database Hyperscale の mirror database と、その SQL analytics endpoint（メニュー項目、取引）を見せる。
6. lakehouse で shortcut を設定し（SQL database in Fabric 側と、mirroring された Hyperscale 側）、廃棄の傾向をレポートで確認。金曜が最も多い。
7. data agent を作り、instruction などの文脈を与えて「どの拠点で廃棄が多く、原因は何か」と質問し、Barcelona などの回答を得る。
8. data agent を Microsoft 365 に公開し、Copilot から使えることを示す。
9. ソース管理: ワークスペース設定の Git integration で、Azure DevOps の組織・プロジェクトなどを設定し、ブランチも扱えることを見せる。
10. セッション終盤の補足部分で、skills for Microsoft Fabric プラグインに「取得して、何に使えるか教えて」とプロンプトを出す例を見せる。デプロイ済みの内容を把握し、SQL skills の使い道を答えた。

### 6.2 Lumir 氏のデモ（Targety とサンタクロース）

Santa を「最新の顧客」とした、季節向けの内容です。

1. Santa がクリスマスの計画に Targety を使っている。Web アプリまたは Excel で計画でき、同じデータを使う。
2. 実績は workshops、sleigh（音声では "slide"）、reindeer から入り、データは SQL database に保存される。
3. 出発の 6 時間前の状況を確認すると、2 つの地域は問題がなく、一部のチームが遅れている（遅れの数は音声では「6,000」と聞こえます）。
4. Santa がエージェントに「達成できるか」と尋ねると、「このままでは無理」と回答し、計画案を提示する。シフトを南へ移し（音声では "move to our shifts to southern" と聞こえ、意味は不明瞭です）、4 台（音声では "4 slides"）を追加するというもの。
5. エージェントは案を用意するだけで、Santa が承認して 1 クリックで計画を変更する。すべてが緑になる。

## 7. 注意点・発表者の気付き

- Fabric SQL は serverless なので、アイドル後に再開で遅延がある。アプリ側でタイムアウトとリトライを実装すること。
- デフォルトの 32 コアは、capacity を使い切り得る。実ワークロードを計測し、max vCore を下げる。
- mirroring はデフォルトで全テーブル。Lumir 氏は、レポートに必要なテーブルだけを選ぶよう勧めています（Anna 氏は、選択的ミラーリングにも設定できると説明）。
- 古い SQL Server の mirroring は、SQL Server Agent とオンプレミスのデータゲートウェイが必要になる。
- SQL analytics endpoint は OneLake 上のレプリケーションデータで、ライブの DB ではない。
- Fabric app が管理しているデータベースは、手を加えすぎない。
- Azure SQL Database 利用者に、全面移行は勧めない（後述）。
- SaaS を既定にしつつ、顧客の要望で ALTER DATABASE SET や max vCore など PaaS 的な設定項目を追加してきた、という経緯が語られています。

## 8. 利用者にとっての利点

- 1 つの Fabric capacity でアプリ、データベース、分析をまとめられる。Uniwise は、以前の多数の Azure サービス（デプロイ、保護、接続が必要）を Fabric capacity 1 つに集約でき、「すべてがより簡単になった」と述べています。
- データベースを作るのに名前だけで済み、約 10〜20 秒で払い出される。
- mirroring で、移動なしに shortcut、notebook、data agent、レポートへ展開できる。ストレージは capacity のサイズまで無料（但し書きあり）。
- ソース管理が簡単にでき、deployment pipelines も使える。
- Uniwise の Targety は、同じコンポーネントで Fabric、Azure、オンプレミスのどこにも提供できる。
- 自然言語による計画作成や、MCP サーバー・AI skills による他アプリとの連携を、計画アプリの機能として提供できる。

## 9. まとめ

- SQL database in Fabric は、Azure SQL などと同じ SQL エンジンを使う SaaS 寄りのデータベースで、mirroring を既定で行って OneLake と結びつきます。
- この 1 年で、max vCore（GA）、削除済み DB の復元（GA）、auditing（GA）、ベクトルインデックス（GA）、Database Hub（GA）などが追加され、workspace レベルの private link と change event streaming は preview です。
- デモでは、Fabric apps で作ったアプリのデータを mirroring と shortcut で lakehouse につなぎ、data agent を作って Microsoft 365 へ公開する流れが示されました。
- Uniwise の事例は、serverless のリトライ、max vCore の調整、選択的 mirroring、ワークスペース分割という具体的な学びを残しました。
- 登壇者の結論は、Azure SQL Database の利用者は、まず mirroring で多くの価値を得られること、Fabric で新しく作る場合や、1 つの capacity、SaaS 指向、Fabric apps によるデータベース作成の民主化を求める場合に SQL database in Fabric を選ぶことです。「利用者のいる場所に合わせる」のが基本方針だと述べています。
- 資料は MSSQL decks に公開予定で、Anna 氏は YouTube チャンネル Data Exposed を毎週木曜に更新していると紹介しました。
