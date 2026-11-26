# INV643 AI infrastructure from cloud to edge

## 1. はじめに

このセッションは、Microsoft Ignite の Innovation Session（約 60 分）です。前半は Azure CTO の Mark Russinovich 氏が **Azure の AI 基盤**を、後半は Windows + Devices の Pavan Davuluri 氏と Windows Product Leader の Navjot Virk 氏が **Windows を使った「hybrid intelligence」**を説明します。両者をつなぐテーマは、AI を「クラウドから端末のエッジまで」どこでも安全に、予算内で動かせるようにすることです。

発表が重ねて訴えたのは次の点です。

* AI エージェント向けの新しい基盤要素（Agent Grid、Context Cache、Agent Sandboxes など）が Azure に加わった
* 古いアプリケーションの移行・モダナイズをエージェントで自動化する **Azure Catalyst** が発表された
* Windows は、エージェントを隔離して動かす仕組み（MXE）、ローカルモデルとクラウドモデルの併用、Windows Server 2028 などで、AI 基盤の一部になる

画面を使ったデモが複数あります。6 章に、語られた順に整理しました。

> 注意：製品名やモデル名の一部は、音声認識の都合で表記が確定できません。該当箇所には、その旨を書いています。

セッション URL: [https://ignite.microsoft.com/en-US/sessions/INV643](https://ignite.microsoft.com/en-US/sessions/INV643)\
YouTube URL: [https://www.youtube.com/watch?v=I27UYrukuAw](https://www.youtube.com/watch?v=I27UYrukuAw)

## 2. 発表者

メタデータ上の登壇者は次の 2 名です。

| 氏名 | 所属 | 役職 |
| ---- | ---- | ---- |
| Pavan Davuluri | Microsoft | EVP, Windows + Devices |
| Mark Russinovich | Microsoft | Azure CTO, dCISO, Technical Fellow |

トランスクリプトには、メタデータの一覧に含まれない次の人物が登場します。

| 氏名 | 紹介・自己紹介の内容 | 登場する場面 |
| ---- | ---- | ---- |
| Navjot Virk | Windows Product Leader（Pavan 氏の紹介） | Windows のデモと hybrid intelligence の説明 |
| Josh Emde | PwC のチームの Global leader（Pavan 氏の紹介） | Pavan 氏とのトーク |
| Michael Draper | Adobe, Vice President of Consumer Trust（自己紹介） | 映像 |
| Ray Morgan | 氏名のみ。Codex についての発言 | 映像 |
| Prashant Ratanchandani | Meta Superintelligence Labs, VP of Engineering（自己紹介） | 映像 |
| Amjad Masad | 氏名のみ。Replit に関する文脈の発言 | 映像 |
| Devang Thakkar | Christie's, Global Head of Christie's Ventures and Innovation Labs（自己紹介） | 映像 |
| Nc Nagarajan、Grant Smith、Alric Olsen、Brian Walsh | 氏名のみ。Columbia の事例映像 | 映像 |

発表の前半は Mark 氏、後半は Pavan 氏が進行し、途中で Navjot 氏に交代します。

## 3. 何に関する発表か

テーマは「AI を本番で動かすための基盤」です。流れは次のとおりです。

1. Azure の AI 基盤と、新しいプリミティブの紹介（Mark 氏）
2. Foundry と AKS をつなぐデモ（Mark 氏）
3. Azure Catalyst によるアプリのモダナイズと、Columbia の事例映像（Mark 氏）
4. Confidential computing と、sovereign cloud のデモ、オンプレミスや現場で AI を動かす顧客例（Mark 氏）
5. Windows の信頼できる基盤: セキュリティ、更新、管理（Pavan 氏）
6. Windows Hybrid Intelligence: MXE、Windows 365 for Agents、Copilot のローカル機能（Navjot 氏）
7. デバイス、Windows 365、Windows Server 2028 の方針と、PwC の事例（Pavan 氏）

## 4. 発表者が伝えたいメッセージ

Mark 氏は、AI があらゆるものの前提を書き換えており、インフラも例外ではないと述べています。AI を支えるには、新しいプリミティブ、エージェントの封じ込め（Satya 氏の言葉として「エージェントは insider risk」）、スタックを上下から最適化する仕組み、オープンさ、ガバナンス、そしてクラウドからエッジまでどこでも動くことが必要という整理です。Azure は 1 つのシステムとして、シリコンからサービス、データまでの full stack で AI のために作られたと主張しています。

Pavan 氏のメッセージは、「素早く動くことと慎重に動くことを、どちらか選ばなくてよい」です。AI が攻撃者の助けにもなる状況で、セキュリティを最優先に進めつつ、AI をどこで動かすかを選べるようにするという立場です。最後に、Windows を「hybrid intelligence の OS」にすると述べています。具体的には、エージェントのための最も安全なプラットフォームであること、AI の予算を有効に使えること、環境全体をつながった AI インフラにすることです。

## 5. 発表内容の詳細

### 5.1 Azure の AI 基盤の強み

Mark 氏は、Azure が 2010 年代初頭に商用クラウドとして初めて HPC クラスターを提供し、その後、OpenAI のモデルの初期の訓練を担う世界最大級のクラウド AI スーパーコンピューターを構築した、と振り返っています。次の数値が紹介されました。

| 項目 | 内容 |
| ---- | ---- |
| microVM | 0.5 秒未満で起動。1 つではなく数千個を起動できる。翌日の Inside Azure の講演で、1 万個の sandbox が 1〜2 秒で起動するのを見せる予定 |
| Lustre | オープンソースのマネージドファイルシステム。モデルのチェックポイント保存で 642 GB/s |
| RDMA | Ignite で、パブリッククラウドとして初めて対応を発表。ストレージサーバーから仮想マシン（GPU メモリを含む）への低遅延・高帯域の接続。CPU が介在しなくなり、データを Azure のストレージから移すジョブで「95% の CPU 利用」を節約できると説明 |
| Azure integrated HSM | すべてのサーバーに載せるセキュリティモジュール。ハードウェアの鍵を保護し、認証情報、トークン、シークレットを AI モデルから遠ざける。1 秒あたり 25,000 回の署名操作に対応 |

### 5.2 Azure の新しいプリミティブ

Ignite で、基本要素（compute、storage、networking、silicon）の上に、次の新しいプリミティブを Azure の core layer として発表しています。

* **Agent Grid**: Agent Sandbox のネットワーク接続を完全に制御する。特定のサイトにはアクセスさせない、一部のサイトだけに許可する、といったポリシーを、sandbox のすぐ隣のネットワークで強制できる。
* **Context Cache**: モデルが新しい文脈を見るたびに再計算せずにすむよう、文脈のキャッシュを Azure のストレージに保存する。コストを抑え、地域全体での大規模推論を支える。エージェントが停止し、数分後に別のサーバーで起動する場合でも、素早く立ち上がれる。（Mark 氏の発言には "content cache" とも聞こえる箇所があります。）
* **Agent Sandboxes（Azure Container Instances）**: microVM を支える仕組み。サーバーレスのコンピュート sandbox である Azure Container Instances を、エージェント向けに拡張したもので、0.5 秒未満の起動を実現する。Agent Grid、Context Cache、Foundry と統合されている。
* **AKS AI Manager**: Azure Kubernetes Service の上で、AI やエージェントのワークロードを、オープンソースのプリミティブで効率よく動かす機能群。コンテナーの実行と推論、AKS で管理する GPU のフリート全体へのリソース割り当てを含む。

Mark 氏は、これにより Foundry（完全マネージドでサーバーレス。Foundry-hosted agents を含む）から、AKS（自分で好きに構成する）まで、一貫した選択肢が Azure に用意されると説明しています。両者の中間として、AKS 上の運用を Foundry に任せる形もあります。

### 5.3 Foundry と AKS（デモ）

工場の設備を診断するエージェントを、Foundry と AKS の組み合わせで動かす内容です。手順は 6.1 に書いています。結果として、Caldova 社の工場でラインの診断にかかる時間が、人手の 2 時間以上から 4 分に短縮されたと説明されています。

### 5.4 アプリケーションのモダナイズと Azure Catalyst

Mark 氏は、AI に備えるためのアプリケーションのモダナイズが緊急になっているとして、次の理由を挙げています。

* オンプレミスのインフラのコスト上昇（サプライチェーンのコストの高騰、特に GPU、スケール時の GPU の入手難）
* セキュリティ。エージェントによる攻撃が、侵入、永続的な足場の確保、ランサムウェア、情報の窃取を、エージェントの速度で行う。オンプレミスは、パッチ管理や古い OS・SQL データベースの面で、最も把握・管理が行き届かないことが多い
* クラウドネイティブでないと、アプリを agentic にしたり AI 機能を組み込んだりするのが難しい

一般的なモダナイズは複数のフェーズに分かれ、各フェーズで人が情報を集め、まとめ、レビューし、他のチームに渡します。小さなアプリでも、数か月から半年かかると述べています。そこで発表されたのが **Azure Catalyst** です。すでにある複数の部品を、エージェントでつないで、発見、計画の作成、実行、検証まで端から端まで完了させます。人が必要なときに介入できる前提です。デモは 6.2 にあります。

事例として **Columbia** の映像が流れました。各人の発言は次のとおりです。

* Nc Nagarajan: 顧客が店舗や EC で製品に触れる以上、期待の水準が上がった。設計、製造、組み立て、販売のあらゆる領域に、変革の機会がある。
* Grant Smith: エージェントは、アーキテクチャの案を多く出すのが得意。以前は 4 週間かけて調べていたものが、初日に 4〜5 案出て、翌週にはコードを書いている。
* Alric Olsen: ソリューションを作るほど技術的負債がたまる。AI により、それに取り組めるようになった。取引先の銀行がセキュリティ体制を更新した件では、AI がなければ数週間から数か月かかったものを、数日で実装を指示できた。
* Brian Walsh: Logic Apps に素早く変更を加え、同じ日にデプロイできた。

Mark 氏は、こうしたモダナイズツールを Microsoft 自身の IT 資産にも、多くの顧客にも使っていると述べています。顧客に共通する効果は、時間とコストの節約、性能の向上、アプリの「agent ready」化です。

### 5.5 Confidential computing

Mark 氏は、AI を自信を持って動かすために「どう動かすか」と「どこで動かすか」の制御が必要だと整理し、まず前者として confidential computing を取り上げています。

* **概念**: 保存時と転送時の暗号化に加え、「使用中」のデータを保護する第 3 の要素。ハードウェアの trusted execution environment が、処理中のデータの周囲にブラックボックスを作る。
* **Azure の実績**: 2017 年に、パブリッククラウドとして初めて confidential computing を提供した。compute（confidential VM）、コンテナー（confidential ACI）、データ（confidential data services）にわたる。

Ignite で拡張されたものは次のとおりです。

| 領域 | 内容 |
| ---- | ---- |
| Compute | AMD と Intel の最新世代プロセッサーに対応した confidential VM。自分で入れ子のワークロードを作り管理できる confidential nested virtual machines を含む |
| コンテナー | confidential ACI に加え、AKS の confidential nodes、AKS 上で自分で管理する Kata confidential containers |
| データ | confidential backup、confidential disk encryption、confidential postgres |

### 5.6 Sovereign cloud とオンプレミス・エッジ

「どこで動かすか」として、sovereign public cloud、sovereign private cloud、sovereignty を支えるパートナーエコシステムからなる sovereign cloud のポートフォリオが紹介されました。画面を使ったデモは 6.3 にあります。

AI をどこでも動かす必要がある理由として、Mark 氏は、遅延、セキュリティ、データ主権（データを置く場所の要件）、手持ちの計算資源を使うための経済性を挙げています。顧客の例は次のとおりです。

* **Rockwell Automation**: Azure AI、IoT Operations、Fabric、工場のシステムを組み合わせ、物理的な運用をつなぐ。エンタープライズの知能を得ながら、極めて低い遅延が必要な機器のすぐ隣で計算する。
* **Chevron**: Azure で、ドローン、ロボット、センサーをつなぐ。
* **Husqvarna**: Azure AI で運用を近代化し、工場の作業者が問題を速く解決できるようにする。

Mark 氏は、ここまでの内容を「シリコンから、プラットフォーム、AKS と Foundry を含む Azure のコアプリミティブ、sovereign cloud によるエッジまでの 1 つの接続されたシステム」と総括し、エンタープライズ規模で AI を運用する場所は Azure だと述べました。

### 5.7 Windows: 信頼できる基盤（Pavan 氏）

Pavan 氏は、Windows が組織の重要な業務を動かしている点に触れたうえで、技術リーダーは AI を採用するかどうかではなく、「エージェントを安全に、予算内でどう動かすか」を尋ねていると話しています。そして、次の話が続きます。

* **脅威の変化**: 同氏が言及した Microsoft の Digital Defense Report では、脆弱性の悪用が、かつては専門的な技能が必要だったのに、いまは 1 つのプロンプトで済むようになった。業界全体の新しい脆弱性は、今年、増える速さが倍になっている。
* **3 つの柱**: (1) 従業員のための信頼できる基盤と、IT による制御、(2) エージェントのための最も安全なプラットフォームの上に築く hybrid intelligence、(3) クラウドからエッジまでつながった AI インフラ。

**セキュリティ**

* **Windows endpoint security platform**: 従来、一部の保護機能は Windows のカーネルで動き、1 つの障害がデバイス全体に影響した。2024 年にセキュリティパートナーと共同で作り、昨年に導入し、**今日 GA** になった。セキュリティプロバイダーが重要な保護機能をカーネルモードからユーザーモードに移せる。
* **Windows Baseline Protection**（発表）: secure boot、仮想化ベースのセキュリティ、メモリ整合性などを、検証しやすい 1 つの状態にまとめる。ユーザーはデバイスのセキュリティ状態を把握しやすくなり、IT はフリート内のすべてのデバイスについて yes/no のシグナルを得られる。パートナーや開発者には、自分のアプリが動くデバイスが要件を満たしているという確信を与える。
* **Adobe の発言（映像）**: Michael Draper 氏は、信頼はユーザーがソフトウェアをダウンロードし、インストールする瞬間から始まるとし、AI が関わることもある偽造・改ざん・悪意あるソフトウェアのリスクを指摘した。Adobe のアプリケーションの理解と、OS レベルの Windows の保護を組み合わせることで、リスクを早く特定し、より安全な体験を作れると述べた。
* **Windows Administrative Protection**（今日 GA）: ローカル管理者を標準ユーザー権限で動かし、必要なときだけ昇格させる。管理者権限が必要なタスクでは Windows Hello がユーザーを確認し、そのタスクの間だけ権限を与える。タスクが終われば権限も終わる。
* **脆弱性の発見と修正**: Microsoft は、Emdash のような AI エージェントで Windows のコードを大規模にスキャンしている。10 月だけで 1,100 件超の CVE の修正を出荷し、これは 2025 年全体より多いとのこと。（Emdash の表記は音声認識の都合で確定できません。）

**更新と管理**

* 修正は届いて初めて効果があるため、更新を素早く適用することが重要で、最速の顧客は 1 週間以内に大半のデバイスにパッチを当てている。
* Windows Autopatch の顧客は、オンプレミス管理の顧客より平均 40% 以上速く更新を配布している。利用は過去 1 年で 50% 超増え、1 億 5,000 万台超が登録されている。更新に関する制御、更新内容の可視性、月 1 回の再起動のみ、という改善が入る。
* **Intune Enterprise App Management**: 組織が配布するアプリに加え、ユーザーが配布したアプリでも、自動更新をプレビューできる。Windows Autopatch と同じ制御と期限の体験を使う。
* **宣言型管理（declarative management）**: Windows はこれに標準化する。Wi-Fi や VPN のポリシーが数分でデバイスに同期され、デバイスがオフラインでも、意図した状態を維持してドリフトから保護される。

### 5.8 Windows Hybrid Intelligence（Navjot 氏）

Navjot 氏は、AI を導入する組織が持つ 3 つの問い（エージェントは安全か、AI の予算は最大限に活きているか、従業員は最大の価値を得ているか）に、Windows Hybrid Intelligence が答えると位置づけています。

**(1) セキュリティ: Microsoft Execution Containers（MXE）**

* MXE は、先月 GA になった。エージェントに、隔離された Windows 環境を提供する。IT は Intune で、各エージェントがアクセスできるファイルとネットワークを定め、Windows がその方針を実行時に強制する。
* 先月の GA 以降（発言は "Since G8" と聞こえます）、Process、Session、WSL にわたってコンテナー機能を追加し、品質を改善し、エージェントの ID を改ざんされにくくした。
* MXE は Windows、Linux、macOS で動くが、OS に組み込まれているのは Windows だけで、エージェントがデータで何をできるかを最も深く制御できる、という説明。
* 現状は、よく作られたエージェントが封じ込めの種類を選ぶが、将来は、IT がポリシーを定めれば Windows が最適な封じ込めをその場で選ぶようになる。

デモの内容は 6.4 にあります。

導入側の証言は次のとおりです。

* **OpenAI Codex**: MXE がデフォルトで有効になった。映像の中で Ray Morgan 氏は、Microsoft Execution Containers を使った新しい sandbox モードにより、Windows 11 デバイスで、より速い設定、強いネットワーク強制、ファイルアクセスの細かな制御ができると述べた。
* **Meta**: Prashant Ratanchandani 氏（Meta Superintelligence Labs）は、同社の多くのチームが Muse code を含むエージェントを Windows で動かしていると話した。複数の sandbox 方式を試し、MXE が最適だった。Muse code は MXE をデフォルトで使っており、MXE は 30,000 台超の Windows デバイスに展開されている（その後に "a minute" と聞こえる語が続きますが、意味は聞き取れません）。

**(2) エージェント専用の PC と、governance**

* **Windows 365 for Agents**: エージェントに専用のクラウド PC を割り当てる。他の Windows PC と同様に管理できる。Microsoft Copilot Studio の computer-use ツールとして利用でき、**次に Microsoft Foundry に対応予定**で、Copilot credits の対象になる。Microsoft 自身の事業運用チームが、処理時間を 60% 節約している。
* **Agent 365 governance の Windows 対応**: IT が 1 度ポリシーを定めれば、管理対象のすべてのデバイスで、すべてのエージェントに Windows が適用する。

**(3) AI 予算を活かす: ローカルとクラウドの併用**

* **新しい Windows 検索**（プレビュー）: タスクバーの検索を、速く効率的で簡素なデザインにし、アプリ、設定、ファイルを素早く探せる。さらに、ウィンドウの整列、Do Not Disturb、VPN 接続、仮想デスクトップの作成など、数千のクイックアクションをタスクバーから実行できる。
* **Copilot+ PC 向けの Copilot（近日）**: Windows Hybrid Intelligence により 3 つの能力が加わる。ローカルの文脈（許可を得て、関連するファイルや最近のアクティビティを理解する）、ローカルのアクション（ファイルの整理からトラブルシューティングまで）、ローカルのエージェントとモデル（PC 上のモデルを優先し、必要なときだけクラウドのモデルを使う）。デモは 6.5 にあります。
* **GitHub Copilot と Copilot Fusion**: 開発者は AI トークンの最大の利用者であり、GitHub Copilot は各タスクを適切なクラウドモデルに振り分けられる。プレビュー提供の「Copilot Fusion」は、Windows 上の hybrid intelligence で、開発者向け PC で動くコーディングモデル MAI Code-Flash などのローカルモデルを使える。NVIDIA RTX Spark を搭載する PC で動き、AMD Ryzen AI Max シリーズ搭載 PC にも広がる。複数の Fortune 500 企業が、Windows 上のこの GitHub Copilot を試験運用中。（Copilot Fusion、MAI Code-Flash、RTX Spark の表記は、音声認識の都合で確定できません。）
* **エコシステム**: Hermes、OpenClaw、perplexity、Replit などの人気エージェントが、同じ機能を使って構築している。Replit は、Windows でローカル AI 体験を構築している。
* **Christie's の事例**: Christie's は、Replit のローカルエージェントを Windows で使い、デバイスの外に出ないプライベートなアートコレクションのデータを扱っている。映像で、Amjad Masad 氏は、エージェント型の開発では、データ主権、セキュリティ、ガバナンスが必要で、Windows アプリのローカル推論と、エンタープライズのポリシーに従う OS ネイティブな sandbox（MXE）でのエージェント実行で応えていると述べた。Devang Thakkar 氏（Christie's）は、機密性の高い情報を扱うため、機密データを手元に置いたまま AI を使えるローカル AI が重要だと話した。

Navjot 氏は、Copilot+ PC は、PC のローカルな文脈、ファイル、アクション、そして今後はローカルモデルを使えるため、手持ちのデバイスで多くを処理でき、AI の 1 ドルあたりの価値が上がると述べて、Pavan 氏に戻しました。

### 5.9 デバイス、Windows 365、Windows Server 2028（Pavan 氏）

**デバイスの選択肢**

Pavan 氏は、エージェントで野心が拡大するなか、hybrid intelligence は、仕事をどこで動かすかを選べることだと説明します。PC、データセンター、クラウドを 1 つのシステムとして Windows がつなぎ、モデルと計算資源を仕事に合わせられるという位置づけです。

| 用途 | 発表で挙がったデバイス |
| ---- | ---- |
| ナレッジワーカー | Copilot+ PC（Intel Core Ultra 搭載機が多い） |
| ビルダー | NVIDIA RTX Spark 搭載の Surface Laptop Ultra などで GitHub Copilot Fusion がローカルでコーディング処理を行う。AMD Ryzen AI Max シリーズにも拡張 |
| デバイス上でフロンティアモデルが必要なチーム | NVIDIA DGX Station for Windows（"true parameter models" と聞こえるが、表記は確定できません） |

AMD、Intel、NVIDIA、Qualcomm が、主要な PC メーカーのデバイスで各階層を支えるという説明です。

**Windows 365**

* 5 年前に Windows をクラウドに置いた製品。同じ Windows、同じアプリ、同じ ID で、動く場所だけが変わる。数万の組織が利用している。
* **Direct Stream**（発表）: Windows 365 の背後にあるクラウドストリーミング技術。クラウド PC を、要求の厳しい作業でもローカルのように感じさせる。課題のあるネットワークで信頼性が 300% 近く改善し、数秒で接続できる。NVIDIA と組み、2 台の 4K ディスプレイ、60 フレーム/秒で、日常の作業なら帯域を半分に抑えた GPU 対応クラウド PC が、来年初めにプレビュー提供される。
* Pavan 氏は、PC 価格が 40% 超上がり、IT 予算が厳しい状況で、Windows 365 は PC の寿命を延ばし、新しいハードウェアなしに数分で計算資源を提供できると述べ、PC の更新サイクルに合わせてクラウド PC を環境に加えることを勧めています。

**Windows Server 2028**

* **Windows Server 2028 が、今日からパブリックプレビュー**になりました（版名は発言どおりです）。
* ハードニングされたベースラインと、毎回安全に起動する trusted launch VM を備える。
* 初めて、サーバーを PC と並んで modern management で管理できる。エンドポイントのチームは Intune と Windows Autopatch、インフラのチームは Azure Arc を使える。
* サーバーは「自分たちが所有する知能のための token factory」になる。Microsoft Foundry を Windows Server 2028 に拡張し、1 つのフリートとして管理する。AMD MI350P と、GB300 上の NVIDIA DGX Station のサポートも追加する（時期は "G8" と聞こえますが、表記は確定できません）。
* NVIDIA Nemotron のようなオープンモデルでエージェントを作るチームは、Windows Server、またはデータの隣のクライアントで動かし、Azure に拡張し、Windows PC で体験する、という 1 本の道を持てる。
* 大量の AI 作業を hybrid intelligence に移すと、5 年間の TCO を半分以上削減できる、と述べている。

**2027 年に向けた助言**

今後 12〜18 か月のハードウェアの選択は、すべて AI の判断になり、次の更新までのトークンコストを左右するという考えです。出発点として次の 2 つが挙がりました。

1. Copilot+ PC と Windows 365 を 1 つの計画にまとめ、役割ごとに更新を計画する。
2. Windows Server 2028 を試し、データの隣で AI を動かす。

### 5.10 PwC の事例（Josh Emde 氏）

Pavan 氏は、PwC の digital workplace チームが、世界 130 か国超で 36 万台超のデバイスを管理していると紹介しました。Josh 氏の主な発言は次のとおりです。

* **将来像**: 電気は発電場所や配線を意識せず使える。AI も同じで、どこで動くかを意識せず、仕事の中にあるようになる。機密資料は端末に残り、重い思考はクラウドに出て、本人は選ばない、という例えで説明した。
* **デバイスの捉え方**: PC は、以前は配るだけのものだったが、今は AI を全員に届ける方法を決める場所で、戦略的な AI インフラになった。（エンドポイント数は、発言中に 36 万と 13 万の 2 通りが出ており、どちらが正しいかは聞き取りから確定できません。）
* **AI をデバイスに引き寄せる要因**: データ主権、セキュリティ、速度、コストの 4 つ。将来は、エッジ、データセンター、プライベート／パブリッククラウドにまたがる hybrid AI アーキテクチャだが、出発点は、すでに全員の手元にあるデバイス。
* **ペルソナ別のハードウェア**: AI リサーチャーとして顧客の文脈を集めて資料を作るコンサルタントと、定型作業を片付けるコーディング支援が必要な開発者では、必要なハードウェアとモデルの大きさが違う。標準は同じで、ペルソナごとにハードウェアとソフトウェアのスタックを意図的に変える。
* **ほかの IT リーダーへの助言**: AI が現実になるのは、ユーザーのペルソナ、ハードウェア、触れるソフトウェアの 3 つがそろったとき。どれが欠けても失敗する。仕事から始め、デバイスから始めない。ハードウェアは AI を可能にし、ソフトウェアは使えて管理できるものにする。
* **Windows に求めるもの**: 一貫性。ノート PC でもクラウド PC でも同じ体験があることが、パイロットを全社の採用に変える。
* **難しい点**: モデル、スキル、バージョン、ベンチマークの管理で、これから登る山だと話した。まず、何をデバイスで、何をクラウドで動かすかの配置を正しく決めることが必要で、それが制御できなければ一貫性を管理できないと考えている。そのため、1 回の展開ではなく戦略として扱っている。

Pavan 氏は、PwC は素早く、かつ安全に進めている例で、「選ばなくてよい」の実例だと結んでいます。

## 6. デモ・手順

このセッションには、画面を使ったデモが複数あります。**音声だけでは画面の細部は分かりません。** 以下は、ナレーションで語られた範囲です。

### 6.1 Foundry と AKS で、工場の診断エージェントを動かす（Mark 氏）

画面は、Caldova 社（Mark 氏の言う "our favorite company"）の Foundry です。

1. Caldova の Foundry には、すでに AKS クラスターが 1 つある。platform engineering チームが、セキュリティ制御とオープンソースのコンポーネントを含めて設定したもの。
2. 工場のビューを開く。Line 7 が「注意が必要」と表示され、"Multiple stop and restart warnings recorded. Review current line signals and recent records." と出ている。直近の記録には何度も短い停止（microstop）があり、それぞれが時間とコストの損失になる。現状は、人が診断すると 2 時間以上かかる。
3. Foundry に AKS クラスターを追加する。platform engineering チームが AI ops チーム向けに用意した inference 用のクラスターを選んで、attach する。既存のクラスターの隣に表示される。
4. Foundry からモデルをデプロイする。オープンソースの Llama 3.3 70B を選ぶ。
5. デプロイ先の選択肢は、serverless API（Foundry がすべて管理）、自分の計算資源の持ち込み、AKS、provisioned throughput によるマネージドモデルの利用。ここでは AKS を選び、先ほど追加した inference クラスターにデプロイする。
6. 工場の現場にある、GPU とカメラを接続したノードを Foundry の体験に加える。**AKS Flex Node** を使い、オンプレミスのノードを AKS クラスターに接続して、Foundry のモデルをそのサーバーにデプロイする。
7. Foundry から AKS にデプロイした inference と、このカメラを使い、診断するエージェントを作る。結果は、診断が 2 時間から 4 分になった。画面には、工場の現場から AKS Flex Node 経由で届いた画像が表示される。

### 6.2 Azure Catalyst によるモダナイズ（Mark 氏）

画面は、Caldova Pharmaceutical の Azure Catalyst です。

1. 最初に、モダナイズ対象を把握する。インベントリは、Catalyst に集めさせるか、既存のものを取り込める。ここでは、ServiceNow の CMDB のインポートファイルをアップロードする。
2. Catalyst は、10 個のアプリケーション、25 台の仮想マシンなどのリソースを特定し、依存関係の分析も済ませる。何が一緒に移行すべきで、何が待てるかが分かる。
3. 検出リストに、見つかったアプリが並ぶ。一番上の **Caldova Asset Manager** に注目する。
4. 何をどの順で、移行する価値があるかを判断する。Catalyst は、オンプレミスのコストとクラウドのコストを見て、Azure Hybrid Benefit も考慮し、コスト、削減可能額、セキュリティ体制（.NET から .NET Core、新しい SQL、新しい Windows への移行で改善する）を示す。
5. Caldova の経営層が、Asset Manager から始めると決める。
6. platform engineering チームが、セキュリティ標準とコストガバナンスの標準を満たす landing zone を作り、Cloud Adoption Framework（CAF）と統合する。Catalyst はそれに沿って、Web 層を Azure App Service に、データ層を Azure SQL Database の Hyperscale elastic pool に、ファイルを Azure Files に割り当てる。
7. 実行を開始する。依存関係マップから、移行の手順（runbook）が作られる。
8. SQL 管理者が、データベースの構成に関与する。他の担当者と独立して、自分のやり方で作業しつつ、Catalyst がモダナイズ全体を追跡する。
9. アプリケーション開発者が、使い慣れた GitHub Copilot でモダナイズの方法を検討し、すでにある CI/CD で、.NET から .NET Core への移行などが動くことを検証する。
10. 完了し、Caldova Asset Manager が Azure 上にデプロイされて動いている。

Mark 氏は、これで数か月かかっていた作業が、**望めば日数**で済むと述べています。

### 6.3 Sovereign control panel と Azure Local（Mark 氏）

1. sovereign control panel を開く。Azure で動いているものを、主権の観点から見たビューで、データレジデンシー、保存時の暗号化、転送時の暗号化、主権に関する事業継続が表示される。
2. 事業継続の項目を掘り下げ、パブリッククラウドで動かしているワークロードを、自社データセンターのエアギャップ環境にある Azure Local で動かす。Azure Local のデプロイには複数のノードがある。Azure と同じサービスなので移行は簡単だと説明している。
3. ワークロードを Azure Local に移したあと、スクリプトを実行して、Azure Local をパブリック Azure から完全に切断する。画面では接続状況が取得できなくなり、自己完結した Azure のインスタンスになる。
4. 切断されたままでも、Foundry Local の機能は使える。Caldova では、Foundry でエージェントを作り、AI ヘルスリサーチアシスタント（clinical researcher investigator）を作る。
5. このエージェントは、クラウドから完全に切り離された Azure Local 上のアプリから利用できる。ローカルの SharePoint から文書を追加して、文脈を与えられる。
6. Foundry Local 上にあるため、Caldova の他のチームも、このリサーチアシスタントを自分のアプリに組み込める。

### 6.4 MXE によるエージェントの封じ込め（Navjot 氏）

Navjot 氏は、"Let me show you" と言い、IT セキュリティマネージャーの Maria という人物の場面を示します。

1. Maria のチームが作った Contoso エージェントは、専用の Session コンテナーで動く。タスクマネージャーでは、Entra に裏付けられた独自の ID を持つ専用のユーザーとして表示される。Maria が許可しない限り、Maria のファイルには触れない。したがって、Maria が何をしたか、エージェントが何をしたかが、監査可能な形で分かる。
2. Maria は新しいセキュリティベンダーを受け入れようとしている。エージェントに、Fabric と契約書（"Fabric and Contract" と聞こえた綴りのままです）の未解決の課題を要約し、返信を下書きするよう頼む。ファイル 1 つの簡単なタスクなので、契約書だけにアクセスできる Process コンテナーで動く。
3. 次に危険な作業として、疑わしいマルウェアで Windows のスキャンツールをテストするよう頼む。完全な隔離のため、エージェントは、ローカルの microVM を起動し、テストを実行し、結果を報告し、microVM を削除する。何かが突破されても、行き場がない。
4. Maria にとっては 1 つのシンプルなエージェントだが、IT にとっては、タスクごとに適切な保護が使われている。

### 6.5 Copilot の Windows Hybrid Intelligence（Navjot 氏）

> 補足：以下の 2 つは、音声からは、ライブ操作か録画かを判別できません。

**ヘッドセットの問題**

1. 重要な会議の直前にヘッドセットが動かない。調べる代わりに、タスクバーに問題を入力して Copilot を開く。
2. 許可を与えると、Copilot がシステム情報にアクセスし、PC を確認する。
3. Windows Hybrid Intelligence でその情報を推論し、オーディオが別のデバイスに設定されていることを見つける。数秒で会議に戻れ、IT にチケットは届かない。

Navjot 氏は、トラブルシューティング、診断、システムの健全性の領域で、サポートへの問い合わせの多くの理由に対応できると述べています。

**ダウンロードしたレポートの整理**

1. 数週間の間にダウンロードした調査レポートがバラバラにある。Copilot に、それらを OneDrive のフォルダーに移すよう頼む。
2. Copilot は、ローカルファイルへのアクセスの許可を求めて、ダウンロードフォルダーを検索し、探しているアナリストレポートを特定する。
3. OneDrive へのアクセスを許可すると、共有できる保存先を作り、特定したレポートをそのフォルダーに移す。操作は、本人の制御の下で行われる。

## 7. 注意点・発表者の気付き

* **オンプレミスの弱点**: Mark 氏は、オンプレミスのインフラは、パッチ管理や古い OS・SQL データベースの面で、最も把握・管理が行き届かないことが多いと述べています。一方でエージェントによる攻撃は、侵入から情報の窃取までを短時間で行うとも説明しています。
* **エージェントへの統制**: エージェントは insider risk（Satya 氏の言葉として紹介）であり、隔離と制御が必要だという前提です。発表では、Azure 側の Agent Grid と Agent Sandboxes、Windows 側の MXE が、それぞれ隔離と制御の手段として紹介されました。
* **人の介入**: Azure Catalyst では、自動化を進めても、人が必要なときにいつでも介入して制御できるようにしていると、繰り返し述べています。
* **MXE の現状**: 現状は、よく作られたエージェントが封じ込めの選択を行い、将来は、IT がポリシーを設定して Windows が選ぶようになるとのことです。
* **更新の適用**: 修正は、デバイスに届かなければ守ってくれません。Pavan 氏は、組織が更新を素早く適用することの重要性を強調しています。
* **ハードウェア計画**: 今後 12〜18 か月のハードウェアの判断は、すべて AI の判断になる、というのが Pavan 氏の注意喚起です。
* **PwC の気付き**: ペルソナ、ハードウェア、ソフトウェアのどれが欠けても AI は失敗する。デバイスではなく仕事から始める。管理の次の課題は、モデル、スキル、バージョン、ベンチマークの管理。
* **提供状況の違い**: GA（Windows endpoint security platform、Windows Administrative Protection、MXE）、プレビュー（新しい Windows 検索、Copilot Fusion、Windows Server 2028）、近日（Copilot+ PC 向けの Copilot の hybrid intelligence）、来年初めのプレビュー（NVIDIA 連携の GPU クラウド PC）が混在しています。

> 補足：価格や対応リージョン、詳細な制約は、発表では触れられていません。

## 8. 利用者にとっての利点

発表で語られた効果と事例は次のとおりです。

| 対象 | 語られた成果・利点 |
| ---- | ---- |
| Caldova（デモのシナリオ上の会社） | 工場のライン診断が 2 時間以上から 4 分に。モダナイズが数か月から、望めば数日に |
| Columbia | 調査に 4 週間かけていた設計が、初日に 4〜5 案、翌週にコードに。銀行パートナーの変更を、数週間〜数か月から数日、Logic Apps の変更は同じ日にデプロイ |
| Microsoft の事業運用チーム | Windows 365 for Agents で、処理時間を 60% 節約 |
| Windows Autopatch の顧客 | オンプレミス管理より平均 40% 以上速く更新を配布。1 億 5,000 万台超が登録 |
| Meta | Muse code を MXE 上で標準で動かし、30,000 台超の Windows デバイスに展開 |
| Windows 365（Direct Stream） | 課題のあるネットワークで信頼性が 300% 近く改善。GPU 対応クラウド PC は、来年初めにプレビュー予定 |
| hybrid intelligence 全般 | 大量の AI 作業を移すと、5 年間の TCO を半分以上削減できる（Pavan 氏の発言） |
| PwC | 役割別に、デバイス上とクラウドで AI の置き場所を決め、安全に素早く進めている |

> 補足：Caldova は、Mark 氏のデモで使われた会社名です。実在する顧客かどうかは、発表では説明されていません。

## 9. まとめ

Mark 氏の前半は、Azure の AI 基盤の全体像でした。microVM、Lustre、RDMA、integrated HSM という土台の上に、Agent Grid、Context Cache、Agent Sandboxes、AKS AI Manager が加わります。さらに、Foundry と AKS をつなぐ運用、アプリの移行を自動化する Azure Catalyst、confidential computing、sovereign cloud が紹介されました。

Pavan 氏と Navjot 氏の後半は、Windows の側から見た AI 基盤です。Windows は、エージェントを MXE で封じ込め、Windows 365 for Agents でエージェント専用の PC を与え、Agent 365 のポリシーをデバイスに適用します。ローカルモデルとクラウドモデルを使い分ける hybrid intelligence で、AI の予算を有効に使えるとも述べています。デバイス、Windows 365、Windows Server 2028 を 1 つのシステムとして計画することが、今後の助言でした。

開発者の観点では、エージェント向けの隔離（Agent Sandboxes、MXE）、AKS と Foundry の連携、GitHub Copilot のローカルモデル利用（Copilot Fusion）が、実装に近い話題です。いずれも発表の中では概要と短いデモにとどまっており、使い方の詳細は、各製品のドキュメントや個別のセッションで確認する必要があります。
