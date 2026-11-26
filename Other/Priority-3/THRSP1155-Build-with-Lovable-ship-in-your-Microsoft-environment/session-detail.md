# THRSP1155 Build with Lovable, ship in your Microsoft environment

## 1. はじめに

このセッションは、AI エージェントとのチャットでアプリを作れるサービス **Lovable** を、Microsoft 環境とつないで使う方法の紹介です。Lovable で作ったアプリを、Microsoft 365 テナントの内側に公開したり、自社の Azure サブスクリプションの中で動かしたりする方法が語られます。

発表の中心は、アプリの置き場所（デプロイ先）の選び方です。扱うデータの機密度に応じて、Lovable Cloud、Copilot Managed Runtime、bring your own cloud の「Deploy on Azure」という 3 つから選べる、という整理が示されます。画面を使ったデモはなく、スライドと口頭の説明が中心の約 18 分のセッションです。

このセッションを読むと、次のことが分かります。

- Lovable が Microsoft のどの機能（Microsoft Graph、Copilot Managed Runtime、Fabric、Entra、GitHub）とつながるのか
- データの機密度に応じて、アプリをどこで動かすのが向いているか
- Deploy on Azure（プライベートプレビュー）が、Azure 上にどのような構成を作るのか

セッション URL: [https://ignite.microsoft.com/en-US/sessions/THRSP1155](https://ignite.microsoft.com/en-US/sessions/THRSP1155)\
YouTube URL: [https://www.youtube.com/watch?v=-Eta4wOW55k](https://www.youtube.com/watch?v=-Eta4wOW55k)

## 2. 発表者

| 氏名 | 所属 | 役職 |
| --- | --- | --- |
| Malavika | Lovable | 発表では役職への言及なし |

> 補足：メタデータに登壇者の一覧はありません。冒頭で司会者が「Lovable の Malavika」と紹介しており、発表全体を Malavika 氏が 1 人で話しています。姓と役職は音声から確認できません。

## 3. 何に関する発表か

テーマは、**「速く作る」ことと「ガバナンスを効かせる」ことを両立させる**ための Lovable と Microsoft の連携です。Lovable は、アイデアを人が気に入るソフトウェアに変える最速の手段だと紹介されます。個人、小規模事業者、大企業まで利用者は幅広く、発表では子どもと一緒にゲームを作った親や、マーケティング用サイトを作った事業主、営業プロセスや HR の仕事の進め方を AI で変えようとしている大企業が例に挙がります。

発表の流れは次のとおりです。

1. Lovable の紹介と、Microsoft との 4 つの接点（Graph、Copilot Managed Runtime、Fabric API、Entra・GitHub など）
2. Microsoft 環境の上で何が作れるか（Supplier Statement Desk の例）
3. ガバナンスの考え方と、3 種類のデプロイ先
4. Deploy on Azure（bring your own cloud）の構成
5. 今後の予定と、チームにとっての意味のまとめ

## 4. 発表者が伝えたいメッセージ

中心にある主張は、Microsoft で VP 兼 GM（managed apps and agents business）を務める David Blyth 氏の言葉として紹介された「企業はスピードとガバナンスのどちらかを選ぶべきではない」という考えです（人名は音声認識の表記です）。Lovable を使えば、初期の AI スタートアップのような速さで出荷しながら、大企業が Microsoft に期待するセキュリティとガバナンスも得られる、というのが Lovable が Microsoft と組んだ理由として説明されます。

そのうえで、役割分担を次のように位置づけています。

- **Lovable は作成のレイヤー（creation layer）**：プロンプトでコードを作り、アプリを動かす場所になる
- **データは Microsoft の環境に残す**：データはネットワーク境界の内側にとどまる

つまり、Microsoft で管理されたデータや ID の基盤はそのままに、アプリを作る部分だけを Lovable に任せる、という考え方です。

## 5. 発表内容の詳細

### 5.1 Lovable の概要

発表者は、Lovable を「誰でも、アイデアを人に愛されるソフトウェアに最速で変えられる」サービスと紹介しています。AI エージェントとチャットしながらソフトウェアを作ります。

| 項目 | 発表で語られた数値・事例 |
| --- | --- |
| ビジネス利用 | 4 万以上の企業が業務で利用 |
| プロジェクト数 | Lovable で作られたプロジェクトは 7,000 万以上 |
| 利用者の幅 | 個人、solopreneur、小規模事業者から、Microsoft、Deutsche Telekom、NVIDIA などの大企業まで |

また、Microsoft がプラットフォームを開放したことで、最良の AI ソリューションを Microsoft のプラットフォームに組み込めるようになった、とも説明されています。

### 5.2 Microsoft との 4 つの接点

Lovable が連携する Microsoft 側のインターフェースとして、次のものが挙げられます。

1. **Microsoft Graph**：Microsoft Office 365 と統一された API です。Microsoft テナントの中にあるデータへ、OAuth で安全につなげます。
2. **Copilot Managed Runtime**：サードパーティと Microsoft 内部のアプリの両方が JavaScript ランタイムへ公開できる、オープンなプラットフォームです。Lovable は single-page app を公開でき、そのアプリは Microsoft Office 365 テナントの中に置かれ、ほかの Power App と同じ方法でガバナンスされます。
3. **Fabric API**：Fabric 上で開発する場合、lakehouse、データウェアハウス、データベースの前にエンドポイントを置きます。Fabric がスキーマを生成し、Lovable がそれを利用します。Lovable のアプリは、独自のバックエンドを作らずに Fabric のデータへクエリできます。
4. **そのほかの Microsoft の機能**：Entra、コード同期のための GitHub などです。Lovable のワークスペースを Entra の SSO の背後に置く、コードを GitHub に同期する、といった形でエコシステムにつなげられます。

Lovable を「作成のレイヤー」として使うときの具体的な連携は、次のように整理されます。

| 連携先 | 実現できること |
| --- | --- |
| Entra | サインインを制御できる |
| Power Apps プラットフォーム（Copilot Managed Runtime 経由） | ガバナンスがその場から組み込まれる |
| Azure へのデプロイ用ソリューション | アプリだけでなく、Lovable の data plane 全体を自社の Azure サブスクリプション内で動かせる |
| GitHub | コードを同期できる |

### 5.3 Microsoft 環境の上で何が作れるか

Microsoft エコシステム上のあらゆるものを土台にアプリが作れる、と発表者は述べています。例として次が挙がります。

- 前日のセッションで、発表者がデモしたアプリ。バックエンドが Excel のスプレッドシートだった（今回のセッションではこのデモは行っていません）
- Fabric をバックエンドにしたアプリ
- Dynamics の上に作る、エージェント型の営業ツール

Lovable が Microsoft のエコシステムに上乗せする価値は、次の 3 点とされます。

- **作る**：プロンプトでコードを作ることにおいて、先行するプラットフォームの 1 つであること
- **動かす**：Lovable のクラウドでホストできるほか、platform as a service として、自社のクラウドサブスクリプション内でもシームレスに動かせること
- **ガバナンスする**：citizen development（開発の専門家ではない現場の人が作るアプリ開発）に必要なガードレールだけを置けるよう、セキュリティとガバナンスの機能が組み込まれていること

### 5.4 例：Supplier Statement Desk

作成例として、**Supplier Statement Desk**（サプライヤー明細の突き合わせ）が紹介されます。Lovable で作られ、すべて Microsoft Office 365 環境の中に収まるアプリです。発表では、次の流れで説明されています。

1. 共有受信トレイ（Outlook）に、サプライヤーからの明細が届く。
2. Outlook コネクタがメールボックスを読む。権限は、管理者が承認した範囲に沿う。
3. アプリが、SharePoint、Excel コネクタ、Fabric 上にある買掛金（payables）と、明細の各行を照合する。
4. 未払いや不一致の請求書にフラグを立てる。承認キュー（approval queue）も組み込まれている。
5. Teams に接続して、Web アプリの外でも、Teams の中から人がアプリを操作できる。
6. アプリが、Excel や SharePoint などのデータソースへ書き込み（更新）を行う。

発表者は、Office 365 コネクタ、Entra でのアプリ構築、複数の画面（surface）を持てる Teams との接続、データウェアハウスの上に作れる Fabric といった形で、チームのいる場所に Lovable が合わせられると述べています。

作れるアプリの幅は広く、財務チームが Supplier Statement Desk を作る、法務チームが契約更新のプラットフォームを作る、といった例が挙がります。多くの場合は、メールやスプレッドシートのように、チームがよく知っているデータソースの上に作るシンプルな Web アプリから始め、時間をかけてデータベースを使う高度なアプリへ発展させる、という進め方だと説明されています。

> 補足：発表者は「ここに Supplier Statement Desk がある」と示して流れを説明しましたが、「操作してみます」といった発話はありません。画面で操作を見せたのではなく、図や画面の説明だったと読み取れます。スライドか画面表示かは音声からは確認できません。

### 5.5 ガバナンスの二層構造

ガバナンスは、Microsoft と Lovable の両方が提供します。

- **Microsoft 側**：ID とアクセス管理。さらに、アプリが Microsoft テナントにデプロイされるため、Microsoft エコシステムに組み込まれたガバナンス機能を使える。
- **Lovable 側**：Lovable 自身のセキュリティとガバナンス機能。

両者を組み合わせて、citizen developer に適切なガードレールを用意できる、という説明です。具体的な設定項目や画面については、発表では触れていません。

また、これらの連携の多くは現在一般提供（generally available）で、例外が後述の **Deploy on Azure**（bring your own cloud の提供形態）で、現時点ではプライベートプレビューです。

### 5.6 3 つのデプロイ先と使い分け

企業のお客様にとってデータセキュリティは最優先事項であり、扱うデータの種類によって適したデプロイ先が変わる、と発表者は説明します。

| デプロイ先 | 向いているケース | 発表者の説明 |
| --- | --- | --- |
| **Lovable Cloud** | シンプルなプロトタイプ。機密性の高いデータソースに触れない場合 | Lovable のクラウドに直接ホストできる。パブリックインターネット経由でパブリッククラウド上のコネクタにつなぐ顧客の多くにも向く、と説明されている |
| **Copilot Managed Runtime** | Office 365 上で作り、ネットワーク分離をより強めたい。データを Microsoft Office テナントの境界内にとどめたい | 使いやすく軽量。実装上の難しさは小さく、20 分で Copilot Managed Runtime を設定できる。IT 管理者にとっては、セキュリティと分離を少し強めたい顧客向けの中間的な選択肢 |
| **Deploy on Azure（bring your own cloud）** | 最も機密性の高いワークロード | Lovable の構築体験を、自社のクラウドサブスクリプションに置く |

Deploy on Azure では、アプリを動かすプレビュー環境とサンドボックス、アプリ本体、それを支えるデータベース、コンピュートのすべてを、自社のクラウド環境内の専用 VNet で動かせます。機密データを扱う社内ツールに特に有用で、データソース側とは VNet ピアリングでプライベートネットワークに直接つなげます。データは、社内の閉じたネットワークの境界内にとどまります。HR データやデータウェアハウスのような機密データに向く、と説明されています。

発表者は、この 3 つがそろっているので、アプリを動かす場所を自分で決められる、とまとめています。なお Copilot Managed Runtime の仕組みについては、詳細な説明を省いています（Lovable で作ってから 365 テナントに直接デプロイできる、というシームレスさの説明にとどまります）。

### 5.7 Deploy on Azure のアーキテクチャ

Deploy on Azure は bring your own cloud の提供形態で、citizen developer から見た構築体験は、Lovable Cloud 上で作ってホストする今の体験とほとんど同じになるよう設計されています。citizen developer がアプリをエンドツーエンドで簡単に作れるようにするためで、裏側では、すべてが顧客のクラウド環境の中で動きます。

構成要素は、発表で次のように語られています。

| 要素 | 内容 |
| --- | --- |
| data plane | 顧客のクラウド環境内の専用 VNet に起動する。Azure Kubernetes Service 上で動く |
| アプリ本体 | サーバーサイドレンダリングの TanStack アプリ（音声認識の表記は "TAN stack"） |
| 静的アセット | Blob storage に置く |
| 動的なコンピュート | Azure Functions で提供する |
| データベース | PostgreSQL on Azure database を同梱する |

これにより、Lovable の体験全体がプライベートネットワークの境界内で完結します。チームはこれまでと同じように作れるうえ、アプリとデータは社内ネットワークの境界の中にとどまります。

> 補足：発表者はネットワーク構成図のスライドを見せようとして、「ネットワークのスライドがない」と述べています。図は見せられていません。

### 5.8 今後の予定

今後は Lovable のセキュリティとガバナンス機能に投資し、Microsoft エコシステムとの連携を拡大していく、とされます。具体的には次のとおりです。

- Copilot エコシステムとの連携の強化
- Teams でのビルドの改善
- Dynamics 365 の Web アプリで、Dataverse のセキュリティルールに対するガバナンスの強化

### 5.9 チームにとっての意味（発表者のまとめ）

発表の最後に、Microsoft との連携が各チームにとって何を意味するかが整理されています。

| 発表で挙がった観点 | 意味 |
| --- | --- |
| 好きなツールで作って本番に出荷できる | Lovable で最良の作り方をしたうえで、アプリを Microsoft エコシステムに公開できる |
| アプリが Microsoft のガバナンスを継承する | Entra を使い、Graph を介して、アプリ利用者の権限でデータへ安全に接続できる |
| データのコピーは 1 つにしたい | Lovable のアプリは Fabric を GraphQL API 経由で読める。データをエクスポートしたり、2 つ目のウェアハウスを持ったりする必要がない |
| アクセス管理を 1 か所に集めたい | Entra ID で、Lovable のアプリを制御できる |

### 5.10 Q&A

司会者が質問を募りましたが、トランスクリプト上は質問は出ておらず、そのまま終了しています。

## 6. デモ・手順

このセッションでは、画面を使ったデモは行われていません（スライドと口頭の説明が中心です）。発表者は前日のセッションで Excel をバックエンドにしたアプリのデモを行ったと述べていますが、今回は再演していません。

発表内で説明された構成としては、5.4 の Supplier Statement Desk の流れと、5.7 の Deploy on Azure のアーキテクチャがあります。設定手順やコマンドは、発表では紹介されていません。

## 7. 注意点・発表者の気付き

- **Deploy on Azure はプライベートプレビュー**です。そのほかの連携の多くは一般提供だと説明されていますが、個々の機能ごとの提供状況は一覧では示されていません。
- デプロイ先は、扱うデータの機密度で選びます。機密性の高いデータを扱うなら、Lovable Cloud ではなく、ネットワーク分離ができる Copilot Managed Runtime、さらに HR データやデータウェアハウスのような機密データなら Deploy on Azure が向く、という整理です。
- Deploy on Azure でも、citizen developer から見た構築体験は Lovable Cloud とほぼ変わらない、という設計方針が語られています。
- Dataverse のセキュリティルールに対するガバナンス強化は、現時点では「今後の投資」であり、すでにできることとしては語られていません。

## 8. 利用者にとっての利点

発表で語られた利点は次のとおりです。

- **速度とガバナンスの両立**：初期の AI スタートアップのような速さで出荷しながら、大企業が Microsoft に期待するセキュリティとガバナンスを得られる。
- **データを Microsoft の環境内に残せる**：Copilot Managed Runtime や Deploy on Azure を選べば、データは Microsoft の環境やネットワーク境界の内側に残る。Fabric は GraphQL API 経由で読むため、データのエクスポートや 2 つ目のウェアハウスは不要。
- **導入が軽い**：Copilot Managed Runtime は 20 分で設定できると説明されている。
- **ID と権限の一元化**：Entra でサインインやアプリの管理を 1 か所にまとめられ、Graph 経由ではアプリ利用者の権限でデータに接続できる。
- **デプロイ先を選べる**：Lovable Cloud、Copilot Managed Runtime、Deploy on Azure から、データの機密度に合わせて選べる。

発表では、特定の顧客企業の導入成果（削減時間や金額）は示されていません。名前が挙がった利用企業は Microsoft、Deutsche Telekom、NVIDIA です。

## 9. まとめ

Lovable は、Microsoft 環境の中で使う「作成のレイヤー」です。Graph、Copilot Managed Runtime、Fabric API、Entra、GitHub を通じて Microsoft のデータとガバナンスにつながり、アプリは Lovable Cloud、Microsoft 365 テナント内の Copilot Managed Runtime、自社 Azure 内の Deploy on Azure（プライベートプレビュー）のどこにでも置けます。機密度が高いほど、データが境界の内側に残る後者の選択肢が向く、というのがこのセッションの整理です。
