# BRK426 Red Hat on Azure: Advancing unified hybrid cloud together

## 1. はじめに

このセッションは、Azure 上で Red Hat の製品（Azure Red Hat OpenShift、Red Hat Enterprise Linux、Ansible Automation Platform など）を使っている、あるいは使おうとしている人に向けた、Microsoft と Red Hat の共同セッションです。

発表の出発点は「hybrid は、クラウドへ行く途中の一時的な状態ではなく、運用モデルそのものだ」という考え方です。そのうえで、(1) Goethe-Institut による Azure Red Hat OpenShift（以下 ARO）の導入事例、(2) Microsoft と Red Hat の共同ビジョン、(3) Ignite で発表された新機能とそのデモ、(4) 仮想化のパフォーマンス検証結果、の順に進みます。

Ignite での発表の中心は、ARO の **hosted control planes（HCP）**、脆弱性対応サービスの **Lightwell**、そして **direct virtualization**（private preview が間もなく開始）の 3 つです。

セッション URL: [https://ignite.microsoft.com/en-US/sessions/BRK426](https://ignite.microsoft.com/en-US/sessions/BRK426)\
YouTube URL: [https://www.youtube.com/watch?v=YJq2Ho2cMT8](https://www.youtube.com/watch?v=YJq2Ho2cMT8)

## 2. 発表者

| 氏名 | 所属 | 役職 |
| --- | --- | --- |
| Campbell Vertesi | Microsoft | Partner CTO lead, Infrastructure partners |
| Immo Goltz | Goethe-Institut e.V. | Systems Engineer |
| Oren Kashi | Red Hat | Senior Principal Product Manager |

3 人とも、トランスクリプトの中で自己紹介または紹介があり、発言者を特定できます。Campbell 氏が全体の司会と共同ビジョン・パフォーマンス検証・Q&A を担当し、Immo 氏は Campbell 氏の質問に答える対談形式で Goethe-Institut の事例を話し、Oren 氏は新機能の紹介とデモを担当しています。

> 補足：トランスクリプト上では Immo 氏の名前が「Imo Golz」と書き起こされていますが、本ドキュメントではメタデータの綴りを使っています。Oren 氏は発表の中で、Azure Red Hat OpenShift の product owner の一人として紹介されています。

## 3. 何に関する発表か

### 3.1 テーマと背景

Campbell 氏は冒頭で、hybrid を「クラウドへ移行する途中の一時的な状態」と捉える人が多いと述べたうえで、Microsoft 側の考えは違うと説明しました。hybrid のまま長く、場合によっては恒久的に運用するケースが多くあり、移行中であっても長期間 hybrid になります。つまり hybrid は、きちんと使いこなすべき運用モデルです。これが、Red Hat との partnership の新しい焦点だと説明しています。

### 3.2 発表の流れ

1. hybrid とは何か、Red Hat on Azure の現状構成
2. Goethe-Institut の導入の道のり（うまくいった点と、苦労した点）
3. Microsoft と Red Hat の共同ビジョン（3 つの柱）
4. 新機能の発表とデモ（ARO の HCP、Lightwell による脆弱性修復）
5. 仮想化のパフォーマンス検証（boosted KVM と direct virtualization）
6. 参考リソースと Q&A

## 4. 発表者が伝えたいメッセージ

発表全体を通した主張は次の 3 点です。

- **hybrid の痛みの源は、複数のプラットフォームをまたいで運用することにある。** データセンターとクラウドの併用に限らず、オンプレミスだけ、Azure だけの環境でも、プラットフォームが複数あれば hybrid の痛みは生じます。
- **技術よりも、人と運用の層が難しい。** Goethe-Institut の事例でも、ワークロードの移行そのものより、チームの説得、依存関係の調整、組織の責任分担のほうが難所だったと語られています。
- **だからこそ Microsoft と Red Hat は、共同で「unified（統合）」「specialized（それぞれの強みを活かす）」「AI is hybrid」の 3 本柱で取り組んでいる。** HCP、Lightwell、direct virtualization、統合サポートは、その具体的な成果として紹介されました。

## 5. 発表内容の詳細

### 5.1 hybrid の痛みと Red Hat on Azure の構成

Campbell 氏は、業界の調査で「エンタープライズの 85% くらい（something like 85%）が hybrid アーキテクチャで運用している」とされ、企業が不満として挙げるのは multi-vendor の複雑さや hybrid 運用の複雑さだと述べました。ただし痛みの主因は、データセンターとクラウドの有無ではなく、「プラットフォームをまたいで運用すること」にあります。Azure 上で Red Hat を動かす場合も、プラットフォームは 2 つあり、両者を統合していても、2 つであること自体が hybrid の痛みの源になります。

また、すべてをオンプレミスで動かしても、すべてを Azure で動かしても hybrid になり得ると話しています。さらに、プラットフォームの外側にあるワークロードを「存在しないことにする」やり方を、**dark hybrid** と呼んでいました。しかしそれらのワークロードは、リスクやガバナンスの対象として実在し続けます。

続いて、スライドで次の構成が示されました。

| 区分 | 内容 |
| --- | --- |
| Azure と直接統合されている Red Hat 製品 | Red Hat Enterprise Linux、Azure Red Hat OpenShift（OpenShift の Azure ファーストパーティ実装で、共同でサポート・運用される）、Ansible Automation Platform |
| Azure と直接は重ならない・統合されない Red Hat 側の構成要素 | Red Hat の monitoring stack、identity と console の stack、Satellite、ACM、Insights |
| Azure 側で統合される領域 | identity（ARO と RHEL で managed identity が使える）、Azure NetApp Files（ANF）のサポートなど |

Campbell 氏は、統合されている領域は重要な部分に絞られており、それ以外では利用者が独自の「glue（つなぎの仕組み）」を維持する必要がある領域もあると指摘しています。

### 5.2 Goethe-Institut の事例（Immo 氏との対談）

#### 組織とデジタルサービスの規模

Goethe-Institut は、ドイツの global cultural institute で、ドイツ語教育と文化交流を世界で推進しています。150 を超える institute を 100 か国で運営し、そのうち 6 つが米国にあります。語学の certificate は、ドイツへの移住手続きでも広く知られているとのことです。

デジタル面では、昨年の web サイト訪問数が 1 億 6,000 万件（160 million）、デジタル学習プラットフォームへの訪問が約 630 万件（6.3 million）でした。Web サイト、学習プラットフォーム、オンライン試験サービスの多くは、外部チームが構築しています。Campbell 氏は、これらがキャッシュされたページ表示ではなく、ログイン済みユーザー向けにページを生成するインタラクティブで重い処理であることを補足していました。目標は、サービスを安定・信頼でき・スケール可能に保つこと、そして **「プラットフォームの下回りではなく、サービスに集中する」** ことです。

#### 移行の道のり

出発点はオンプレミスで、物理サーバー、のちに VM でした。CentOS の複数バージョン（聞こえた表記は「3 CentOS versions」）を使い、多くの言語・フレームワーク・データベースを管理していました。プロジェクトごとに独自のチームが運用し、新しいプロジェクトが増えるたびに運用の複雑さが増したそうです。

そこで次のように進めました。

1. クラウド基盤として Azure を選んだ
2. 新規アプリケーションで Azure Kubernetes Service（AKS）から開始した
3. その後 ARO に移った。理由は、ニーズにより合っていて、turnkey で運用が簡単だから。開発者は標準化・分離された環境、高可用性、スケーリングを得られ、プラットフォームに使う時間を減らしてアプリケーションに時間を使えると説明されています。

#### プラットフォームの運用方法

- **すべてをコードで管理する。** 環境の定義は Git に置き、手作業のサーバー設定はありません。OpenShift GitOps が構成を適用し、災害対策（disaster recovery）にも同じ構成を使っています。開発者は developer GitOps で自分のワークロードをデプロイします。その範囲は各自の namespace の中に収まっているという説明でした（該当部分の聞き取りが不明瞭で、細部は確定できません）。
- **セキュリティも同じ方式。** プラットフォームの構成は Git にあるため、一貫性・再現性・監査性があり、外部チームが運用していても成り立つとのことです。ドイツのコンテナー・Kubernetes 向けセキュリティのフレームワークに従っています（フレームワーク名は音声からは確定できませんが、のちに BSI という名称が登場します）。
- クラスターの構築には Ansible を使い、Kyverno と Red Hat Advanced Cluster Security が policy as code を enforce します。
- イメージは一度だけビルドし、ステージごとに昇格（promote）させます。
- シークレットは Git に入れません。Azure Key Vault に保管し、External Secrets Operator が配布します。
- マネージドサービスの方が運用負荷を下げられる部分には Azure のサービスを使っています。データには Azure Database for MySQL と PostgreSQL です。

#### ストレージが最も難しかった点

最も難しかったのはストレージでした。Moodle は多数の小さなファイルへの高速なアクセスを必要とし、ページ表示に最大 30 秒かかっていたそうです。そこで Azure NetApp Files と Trident operator を使い、全ノードが同じ高速ストレージを使えるようにした結果、ページはほぼ瞬時に表示されるようになりました。

#### 成果

| 項目 | 内容 |
| --- | --- |
| 開始時期と現在 | 2020 年に 1 つのパイロットプロジェクトで開始。現在は 50 のプロジェクトが ARO 上にある（聞こえた表現は「50 points on error」。Campbell 氏が後で「50 projects を移した」と言及） |
| 容量 | 12 CPU・48 GB メモリから、約 250 CPU とメモリ（メモリ量は「TB」としか聞き取れず、数値は不明瞭）へ |
| Moodle（過去 12 か月） | 530 万訪問、1 億ページビュー、アクティブな学習者 約 30 万人、同時利用 1,000 人超 |

Immo 氏は、これはもはやサイドプロジェクトではなく、**組織にとって重要なインフラ（critical infrastructure）** だと述べています。

#### 今後の計画

- developer GitOps 上のアプリケーションを増やす
- policy as code を強化し、コンプライアンスのレポートと enforcement を行う
- Advanced Cluster Security による脆弱性の可視化を深める
- observability の強化とコスト管理の改善
- 語学学習・試験向けの AI の検証と、運用・開発チーム向けの OpenShift Lightspeed の検証

#### 難しかった点（Campbell 氏が聞きたかった部分）

Campbell 氏は「ここからが自分にとって面白い部分」として、難しかった点を尋ねました。Immo 氏が挙げたのは次のとおりです。

1. チームごとに運用モデルや独自の設定が違い、標準化に時間がかかった。これに対する答えが、GitOps、policy as code、シークレット管理、イメージスキャン付きの中央レジストリを組み込んだ **一つの標準環境** だった。
2. チームの説得。まず「コンテナーが進むべき道だ」と、次に「何年も問題なく動いているアプリでも、モダナイズする価値がある」と納得してもらう必要があった。
3. 移行の調整。アプリ同士が依存し合っており、一つを移すと別のものが壊れる可能性がある。ファイアウォール、ルーティング、DNS を移行前にすべて整えて、証明する必要があった。
4. 責任の共有。一つのプラットフォーム上で、自分の担当部分だけでなくエンドツーエンドの体験に全員が責任を持つ。新しい考え方だが、実際の利点でもある。

Campbell 氏はこれを受けて、難所は技術ではなく人と組織と運用だと整理しました。OpenShift virtualization では、数百、ときには 1,000 を超える VM を 1 日で移行する人もいるため、ワークロード移行そのものの技術面は簡単な場合があるとも述べています。

#### 移行後も続く運用

Immo 氏は、移行に成功しても終わりではなく、要件の変化に合わせてプラットフォームが進化し続けると説明しました。例として、最初は public cluster で始めたものの、のちに BSI が、セキュリティ要件を満たすために private cluster を推奨したことを挙げています。現在は Azure Application Service（聞こえた表記のまま）経由でアプリケーションを公開しており、OpenShift と Azure のツールを並行して管理する必要があります。

Goethe-Institut 自身は hybrid とは呼んでいませんが、「方向性はクラウドであっても、完全に移行した後も OpenShift と Azure の 2 つのプラットフォームを管理している」と述べています。ここが、Microsoft と Red Hat が共同で取り組んでいる領域です。「一言でまとめるなら」と求められた最後の一言は、プラットフォームそのものではなく、その上で動くものに集中する、という趣旨でした（聞こえた表記は "We don't find the platform anymore. We focus on what runs on it."）。事例の詳細は、スライドの QR コードから開く公開済みの customer story で読めます。

### 5.3 Microsoft と Red Hat の共同ビジョン（3 つの柱）

Campbell 氏は、狙っている hybrid の痛みを踏まえ、3 つの柱を紹介しました。

**1. Unified（統合）**
どのプラットフォーム上のワークロードでも、どのツールを使っていても、一つの control system で扱えるようにするという考えです。オンプレミスの VM も Azure のコンテナーも、ガバナンス、ポリシー、セキュリティ、運用で同じ経路をたどれるべきだと説明しています。

**2. Specialized（それぞれの環境の強みを活かす）**
各環境が最も得意なことをするという考えです。顧客がよく苦労するパターンとして、オンプレミスの OpenShift の標準や運用モデルを、クラウドの ARO にもそのまま当てはめるケースを挙げました。同じシステムなので問題ない場面も多いものの、例えば静的 IP を identity の基盤にするような使い方は、クラウドでも可能ですが意味が薄く、クラウドの価値を引き出せないとのことです。逆に、クラウドのモデルをオンプレミスに写そうとするのも難しく、良い結果になりません。Azure の強み、Red Hat の強み、オンプレミスの強みをそれぞれ使うべきだと述べています。

**3. AI is hybrid**
実世界のエンタープライズで影響の大きいワークロードの多くは、本質的に hybrid なエコシステムの一部だという考えです。例として飛行機を挙げています。機内でデータ処理をしても、センサーデータのすべてをクラウドに送って推論を続けるほどの太い回線はありません。そのため機内でデータを絞り込み、クラウドは MLOps サイクルの一部を担います。商業や産業にも同様のパターンがあるとのことで、AIOps のライフサイクル（聞こえた表記のまま）で利用者が感じる「継ぎ目（seams）」に取り組むことに焦点を当てていると説明しました。

### 5.4 Ignite で発表された新機能（Oren 氏）

Oren 氏は、Goethe-Institut の話にあった「重要なのはアプリケーションで、プラットフォームの配管作りではない」という結びが、自分たちの取り組みに重なると述べてから、Ignite での発表内容を紹介しました。

#### 5.4.1 ARO with hosted control planes（HCP）

HCP は、クラスターを再設計して control plane と worker node を分離したものです。control plane は service subscription 側で動き、worker node は利用者自身の subscription で動き続けます。

| 観点 | 内容 |
| --- | --- |
| コスト | control plane 分のコンピュートのインフラコストを負担しなくてよい。標準構成の基本クラスターでは、control plane 用に少なくとも 8 vCPU のマシン 3 台が必要で、クラスターが大きく忙しくなれば 16 や 32 vCPU に拡大が必要になることもある。HCP では代わりに一定額（flat）の control plane fee になる（金額は発表では触れられていません） |
| セキュリティ | control plane と data plane の分離がより強くなる。認証情報は managed identity のみ（標準クラスターでは長期間有効な認証情報である service principal か managed identity を選べるが、HCP では managed identity しか使えない）。control plane の更新も自動化され、常に最新で安全なパッチになる |
| 柔軟性 | control plane と worker node で異なるバージョンを使える。worker node 同士でも異なるバージョンを持てる |
| デプロイ速度 | 標準構成で約 45 分程度かかっていたのが、クラスター全体で 15〜20 分程度になる |
| ネットワーク | 既定の OVN ではなく、独自の CNI を持ち込める |
| OpenShift 5 | HCP では OpenShift 5 を利用でき、AI-powered operations、より長いサポートライフサイクル、virtualization の強化が得られる |

#### 5.4.2 仮想化（OpenShift virtualization on ARO）

hypervisor チームがパフォーマンスを中心に多くの改善を行っています。Campbell 氏とは「1 年以上」共同で作業してきたと話しています。

- **v7 SKU のサポート**など。さらに direct virtualization を最初に使える場所になる（詳細は 5.6）
- **ストレージの選択肢の拡大**：Azure NetApp Files と Everpure（綴りは聞こえたまま。音声認識の都合で表記が確定できません）
- **VM へのアクセスの課題への対処**：クラウドのプラットフォームは、コンテナー基盤が提供する内部の overlay network を自動では認識しません。そこで FRR による BGP ルーティングと Azure Route Server を使って、カスタムサブネットのプレフィックスを動的に広告（advertise）する方式を実装しました。これを簡単にする **BGP Cloud Operator** もリリース済みです。
- **サポートの一本化**：VM の OS が何であっても、サポートの窓口は 1 本になりました。Azure または ARO 上で動いていれば、Windows、RHEL、その他の承認済み（endorsed）ディストリビューションのいずれでも、電話するのは 1 つの番号です。

#### 5.4.3 セキュリティ

- **Lightwell を Azure Marketplace に載せることを発表**：脆弱性を修復（remediate）・検証（validate）し、本番で使っているバージョンへ backport するサービスです。SLA 付きの提供と署名付き成果物を伴います。破壊的なアップグレードや再認定を強いず、すでに動いているのと同じビルドの修正版が得られると説明されました。（Lightwell の綴りは聞こえたままです）
- OpenShift の既存のセキュリティ機能（security context constraints、SELinux、Cgroups など）はそのまま維持されます。
- **OpenShift sandbox containers**：hypervisor に支えられたワークロード分離で、各 pod が専用カーネルを持つ軽量 VM の中で動きます。既存の OpenShift のセキュリティ機能の上に重ねる形です。実行するアプリケーションを信頼できない場合（例：非決定的な AI ワークロード）に、封じ込めるための仕組みと説明されています。
- **Confidential containers**：信頼モデルが逆で、動かすインフラやクラスター自体を信頼しない場合に、実行中のデータを保護するための仕組みです。夏に ARO での提供開始が発表され、managed identity のサポートも含まれています。HCP 向けにもサポートと tech preview があるとのことです。

### 5.5 パフォーマンス検証（Campbell 氏）

HCP とデモのあと、Campbell 氏は、2 社の hypervisor engineering の非常に密な協力による性能検証を紹介しました。

#### 2 つのアーキテクチャ

- **従来型（boosted KVM）**：Azure の物理サーバー上の hypervisor が Azure VM を動かし、その中で親 OS（RHEL を想定）が nested hypervisor を介してゲスト VM を動かします。nested hypervisor がリソースを消費します。GA の製品は、Azure の **Boost**（hypervisor の処理をハードウェアにオフロードする、データセンター向けの専用ハードウェア）チームと協力してパフォーマンス検証を行い、この形で動いています。nested KVM にも Boost の恩恵があるため「boosted KVM」と呼んでいます。
- **direct virtualization**：Azure が取り組んでいる新しい仮想化方式です。利用者の Azure VM は nested virtualization で動いているように見え、動作もそう感じられますが、実際には Azure Hypervisor（Microsoft の hypervisor 層）と直接通信しています。VMM の stack 全体とカーネルは利用者側で制御します。ゲスト VM は最上位の Azure VM と同じ方法で動くため、Azure VM と同じ性能とハードウェア機能が得られると説明されました。Azure の技術講演（Mark Rosinovich 氏の tech talk と聞こえた。表記は音声認識のままです）でも触れられたとのことです。

#### 結果の読み方に関する注意

Campbell 氏は、これらが**合成ベンチマーク（synthetic benchmarks）**の実際のログを、見やすく可視化したものだと明確にことわりました。アプリケーションの性能を知る方向性の目安にはなりますが、実際のアプリケーションはもっと複雑で、この検証はボトルネックを切り分けるためのものです。

#### 結果

1. **SysBench**：青線が Azure VM、赤線が ARO の OpenShift virtualization 上の boosted KVM VM です。2 本は非常に近く、誤差範囲（error bar）を入れておくべきだったほどでした。「OpenShift virtualization の VM は通常の Azure VM とほぼ同等に動くのか」というステージ上の問いかけに、Campbell 氏は「同等と言える。ただし、アプリケーションがマシンの一部の要素を極端に酷使すれば限界点は違ってくる。それでもエンタープライズのほとんどのワークロードで、違いに気付くとは考えていない」と答えました。
2. **世代について**：この検証は新しい V7 SKU で行っていますが、多くの場合は V5 を推奨しています。合成ベンチマークでも意味のある差を見いだせなかったためで、これはディスクのスループットなどによると述べています。
3. **メモリスループット**：boosted KVM で同様の結果でした。
4. **I/O**：ソフトウェアデバイスでディスクやネットワークをエミュレートするため、負荷（tax）がかかります。知りたかったのは、Red Hat が他の環境で推奨するスケーリング施策が、ARO の仮想化環境でも同様に効くかどうかです。結果は効く、とのことでした。ここでは、Red Hat が推奨する multi-queue の構成変更を使っています。ディスクとして 400,000 という値に言及し（単位は発表中では明言されていません）、一つのディスクがそれだけの処理をしているなら、むしろアーキテクチャに問題があるだろうと付け加えています。
5. **CPU のオーバースケジューリング**：Azure VM の 48 仮想コアに対し、2 倍の vCPU を持つ VM をスケジュールして負荷をかけるテストです。青線が boosted KVM、赤線が direct virtualization VM で、ここでは差が出ました。direct virtualization は Azure VM がネイティブで動くのと同じ方式で動くためです。
6. **4 対 1 のオーバースケジューリング**：スケジュールした VM コア数と実際の VM コア数の比を 4 対 1 にしたテストです。boosted KVM が目に見えて苦しくなり、差は「13% か 14%」と話されていました（本人が正確な値を覚えていないと述べたうえでの数字です）。

Campbell 氏は、合成ベンチマークのように CPU を叩き続けるワークロードは普通は少ないとしたうえで、非常に CPU バウンドなワークロードや、**VM stuffing**（物理リソースに対して VM を詰め込む運用）を意図する場合に影響が見え、direct virtualization ではそれが出ない、とまとめています。

### 5.6 direct virtualization と参考リソース

direct virtualization は、Azure と Red Hat の優秀なエンジニアによる大きな取り組みで、**private preview の開始が間近**とのことです。参加したい人は、スライドの QR コードから申し込みます。direct virtualization だけでなく、他のイノベーションも含めた「unified private preview program」であり、ユースケースや、限界を押し広げたい機能について意見を聞きたいと呼びかけています。

そのほかスライドには、公開済みの blog post（より技術的な内容）、ARO の hosted control planes のリリース情報、そして Oren 氏のデモに近いが Azure の予算を消費しない interactive demo への QR コードがありました。Campbell 氏自身の blog post は「まもなく公開」で、QR コードには間に合っていないと説明されています。

### 5.7 Q&A

セッションは時間切れの状態でしたが、最後のセッションでもあり、質疑が行われました。

**Q1. direct virtualization と boosted KVM でコストに違いはあるか。**
direct virtualization は private preview にもまだ出していないため、商用面の違いは検討中です。ただし、VM の容量をより多く使えるという性能差があるため、コスト面の違いは出ると見込んでいます。現時点では **full node SKU**（物理マシン全体を使う SKU）に焦点を当てており、これは高価で大きなサイズです。この状況はずっと続くわけではないと思うが、初期には違いが出る、との回答でした。

**Q2. 差は合成ベンチマークでの小さな差では。実ユーザーの体験に影響するのか。direct virtualization の価値は何か。**
Campbell 氏は質問を繰り返したうえで答えました。

- エンタープライズのほとんどのワークロードでは、boosted KVM と Azure VM の性能差は見えないと考えている。
- 差が出るのは、マシンを限界まで使うケース。例えば、単一レプリカがディスクを酷使するデータベースのシナリオ。この場合は構成を少し変える必要があり、Azure ではオンプレミスのサーバーよりも早い段階で、Red Hat が推奨する multi-thread の構成（発話は "multi-thread configuration"）が必要になる可能性がある。ただし、hardware pass-through 付きの direct virtualization なら別。
- 最初のリリースは、CPU、メモリ、VM の作成と管理という基本機能が中心。本当の価値は **hardware pass-through** で現れる。GPU や GPU の一部を OpenShift virtualization のワークロードに割り当てられ、ストレージデバイスやネットワークにも同様の利点が及ぶ。これは boosted KVM では今は不可能で、hyperscaler のどこでも不可能だと思う、とのことです。

**Q3（Campbell 氏が自ら補足）：サポートに問い合わせるときはどうなるか。**
質問は出ませんでしたが、Campbell 氏が統合サポートについて補足しました。関係するすべての組織のサポートチームが同じチケットを共有する、一つの窓口です。ベンダー同士が責任を押し付け合う状況は起きないとのことです。Windows、RHEL、その他の承認済みディストリビューションが対象で、ARO virtualization 上または Azure 上で動いていれば、窓口の振り分けまで行うと説明しています。

## 6. デモ・手順

このセッションでは、Oren 氏が 2 つのデモ（HCP クラスターの作成、Lightwell を使った脆弱性修復）を見せました。1 つ目は「ライブデモ」だと本人が明言しています。2 つ目はライブか録画かの明言がなく、「ちょっと一時停止します」と述べながら、すでに PR が作成済みの状態から説明しているため、事前に用意した映像の可能性があります（音声だけでは断定できません）。3 つ目として予告された「仮想化のパフォーマンス」は、ライブ操作ではなく、合成ベンチマークのログを可視化したスライド上の映像を使った説明です。画面上の細部は音声からは確認できないため、以下は語られた範囲に限ります。

### 6.1 デモ 1：Azure portal で ARO の HCP クラスターを作成する

Oren 氏は、ポータルでの作成が唯一の方法ではなく、CLI、ARM、Bicep、そして「まもなく」Terraform でも作成できると前置きしています。「実際にライブで動かしている」と述べていました。

1. Azure portal で Azure Red Hat OpenShift の画面に進み、Create をクリックする。
2. 新しい resource group を作成する。
3. 基本情報を入力する。ここで standard architecture のクラスターと hosted control planes を選べる。hosted control planes を選ぶ。
4. 名前を付ける。既定で選ばれているバージョンは **OpenShift 5** だった（別のバージョンも選べる）。
5. node pools では、既定で 1 つの node pool が作られる（追加も可能だが、今回は先へ進んだ）。
6. authentication では managed identity が既定で設定されており、ほかの選択肢はない。
7. etcd（聞こえた表記は "CD"）を customer managed key で暗号化できる。今回は、この作成フローの中で key vault を作成し、キーも自動作成させた。
8. networking では、既存のネットワークがあれば対応する VNet とサブネットを選べる。今回は新規に作成させ、公開範囲（visibility）は既定のままにした。
9. tags は追加できる（追加するかは任意）。
10. 最終ページで簡単な検証が走り、通過したら内容を最終確認して Create をクリックする。
11. すると、key vault、キー、managed identities、適切な role assignments など、関連コンポーネントが自動的に作成される。

クラスター作成の完了までは待たず、事前に作成してあるクラスターに切り替えて、次の点を説明しました。

- クラスターは 422 で、node pool の一つが 422、もう一つが 421 になっている（バージョン表記は聞こえたままです）。これが先に説明した柔軟性の例である。
- 活用例として、クラスターが 421 のときに 422 へ上げても自分のアプリケーションが影響を受けないかを確認したいなら、422 の新しい node pool を追加してテストし、問題なければ 421 の pool を削除する。問題があれば、すでに動いているものには何も手を付けていない。
- managed resource group で VM だけを見ると、2 つの node pool 全体で計 5 台（本人の計算が合っていれば、という前置き付き）。**control plane のノード 3 台は表示されない。**

### 6.2 デモ 2：Lightwell と Ansible による脆弱性の自動修復

Oren 氏は、サンプルアプリケーションが持つ CVE を見つけ、Ansible Automation Platform による自動化の仕組みで Lightwell から修正済みパッケージを取得し、PR を作成してテストを走らせ、すべて成功すれば反映する流れを見せる、と説明しました。

1. サンプルは社内向けのチケット管理アプリケーション。画面下部のパッケージ一覧で、SQL parse が 0.5.5 で動いている。この版はコンテナーで動いており、同じものが VM 上でも動いている（コンテナーでも VM でもよいことを示すため）。両方で 0.5.5 であることを確認した。
2. コードは GitHub 上にあり、バージョンを 0.5.5 に固定（pin）している。更新したくないためである。
3. Dependabot でいくつかの脆弱性が見える。Microsoft Defender でも同じ脆弱性が見える。いずれも「0.5.6 まで上げる必要がある」と示している（聞こえた表記は「.6」）が、バージョンを維持したい立場では難しい。
4. 別の見方として Lightwell Lens を表示し、同じパッケージの同じ脆弱性が見えることを確認した。
5. ここで Oren 氏は、事前の設定として、Dependabot が脆弱性を見つけると webhook 経由で Ansible の runbook が起動する仕組みを説明した。runbook は、Lightwell からパッケージを取得し、検証（verify）し、修復（remediate）し、最後に PR を作成する。
6. Ansible 側で、verify と remediate の 2 つのステップ（playbook）が実行されたことを確認した。そのあと PR が作成されている。
7. PR のテストを確認した。テストには、最近発表された REL runners（音声認識の都合で表記が確定できません）が使われている。テストは成功し、追加された新しいタグ（0.5.5 に付いたもの）が、Lightwell から取得した修正済みパッケージであることを示している。最下部では、テストが実行され検証済みで、マージして安全だと表示されている。
8. PR をマージすると、別の runbook によって更新が反映される。
9. アプリケーションに戻り、パッケージを確認すると、コンテナー側も VM 側も Lightwell から来た新しいパッチを使っていた。

Oren 氏は、これを多様な対象にまたがる「unified remediation（統合された修復）」を、素早く自動化して行う一例だと位置づけています。ライブラリのバージョンを上げて再ビルドする必要がない点も、事前の説明で述べられていました。

### 6.3 パフォーマンス検証の映像

デモというより、合成ベンチマークの実ログを可視化した映像を見ながらの説明でした。内容は 5.5 に記載したとおりです。

## 7. 注意点・発表者の気付き

- **ベンチマークは目安にすぎない。** Campbell 氏は、合成ベンチマークは方向性を示す指標であり、実際のアプリケーションはより複雑だと繰り返し強調しています。性能について約束することには慎重で、アプリケーションが何を酷使するかで結果は大きく変わるとも述べました。
- **オンプレミスと同じ運用モデルを ARO にそのまま持ち込むのは避ける。** 静的 IP を identity の基盤にするような使い方は、クラウドの強みを活かせません。
- **移行後もプラットフォームは変わり続ける。** Goethe-Institut は、public cluster で始めたあとに BSI から private cluster が推奨されました。現在も OpenShift と Azure のツールを並行して管理する負担が残っています。
- **direct virtualization は初期には full node SKU が中心で、高価な大きなサイズ。** コスト差は検討中で、まだ確定していません。最初のリリースは CPU・メモリ・VM の作成と管理が中心で、hardware pass-through は先になります。
- **CPU 負荷の高いワークロードや VM stuffing では、boosted KVM の差が出る。** 4 対 1 のオーバースケジューリングで、13〜14% 程度の差が示されました。
- **Azure では、高負荷のディスク I/O を扱う場合、Red Hat が推奨するスケーリング設定をオンプレミスより早い段階で適用する必要がある場合がある。** direct virtualization（hardware pass-through を含む）に至るまでの話です。
- **HCP は managed identity のみ。** 長期間有効な service principal は使えません。
- **Goethe-Institut の教訓は、技術よりも人と組織の調整。** チームの説得、依存関係の調整、ファイアウォール・ルーティング・DNS の事前整備、責任の共有が難所でした。

## 8. 利用者にとっての利点

発表で語られた利点を整理します。

| 領域 | 利点 |
| --- | --- |
| HCP | control plane の compute コストが不要になり、一定額の fee になる。control plane と data plane の分離が強まる。control plane の更新が自動。バージョンの混在が可能。デプロイが 45 分程度から 15〜20 分程度に短縮。独自 CNI を使える。OpenShift 5 を利用できる |
| Lightwell | 破壊的なアップグレードや再認定をせず、動作中のビルドの修正版を得られる。SLA 付き・署名付き成果物。コンテナーと VM の両方で自動修復できる |
| 仮想化 | 大半のエンタープライズのワークロードで通常の Azure VM と同等の性能。BGP Cloud Operator による VM へのアクセス。OS を問わない単一の窓口でのサポート |
| direct virtualization | Azure VM と同じ性能とハードウェア機能。将来の hardware pass-through により、GPU（その一部も）、ストレージ、ネットワークのデバイスを使える |
| Goethe-Institut の成果 | 1 つのパイロットから 50 のプロジェクトへ拡大。Moodle の表示が最大 30 秒からほぼ瞬時に。同時利用 1,000 人超、過去 12 か月で 1 億ページビューの規模を支えている |

## 9. まとめ

- Microsoft と Red Hat は、hybrid を一時的な状態ではなく運用モデルと捉え、「unified」「specialized」「AI is hybrid」の 3 本柱で痛みの軽減に取り組んでいます。
- Goethe-Institut は 2020 年の 1 パイロットから 50 プロジェクトまで ARO 上で拡大しました。難所は技術ではなく、人・組織・運用の調整でした。
- ARO の HCP は、コスト、セキュリティ、バージョンの柔軟性、デプロイ時間の面で改善をもたらし、デモでは control plane ノードが利用者側に見えない様子が示されました。
- Lightwell と Ansible による自動修復で、コンテナーと VM の両方の脆弱性を、バージョンを上げることなく修復できることが示されました。
- 仮想化では、boosted KVM が多くの合成ベンチマークで Azure VM と近い結果を出し、高負荷時の差を埋めるのが direct virtualization です。private preview の参加募集が間もなく始まります。
