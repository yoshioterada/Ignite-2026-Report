# BRK445 Architecting for end-to-end resiliency on Azure

## 1. はじめに

このセッションは、Azure 上のワークロードを「障害が起きてから直す」状態から「障害が起きても動き続ける」状態へ近づけるための考え方と、新しい機能を紹介する 45 分のブレークアウトです。3 人の登壇者が順に、ゾーン障害への備え、リージョン障害への備え、そしてそれらをアプリケーション単位でまとめて管理する Resiliency Manager / Resiliency Agent を説明しています。

冒頭では、Microsoft 自身が起こした過去の障害を取り上げ、「共同責任」としてお客様側に何を期待するのかが率直に語られます。途中でビデオ形式のデモが 3 本流れ、次の新機能が紹介されます。

* zone resilience for Azure VMs（public preview）
* Azure Multicloud Interconnect（AWS とは GA、GCP とは public preview）
* Azure Copilot の resiliency agent と Infrastructure Resiliency Manager

セッション URL: [https://ignite.microsoft.com/en-US/sessions/BRK445](https://ignite.microsoft.com/en-US/sessions/BRK445)

## 2. 発表者

| 氏名 | 所属 | 役職 |
| --- | --- | --- |
| Yuemin Lu | Microsoft | Partner PdM Manager |
| Sudha Mahajan | Microsoft | Partner Product Management |
| Rochak Mittal | Microsoft | Partner Product Manager |

> 補足：トランスクリプトでは、最初に話す人が「I'm Sudha Mahajan」と名乗り、あと 2 人（Yumin Lee と Rochit Mittal と聞こえます）を紹介しています。2 人目は Sudha 氏から「Yumi Li」として紹介され、3 人目は 2 人目から「Rotek」として紹介されています。いずれも音声認識の表記ゆれで、上の表ではメタデータの綴りを使っています。発表は、Sudha 氏（共同責任・ゾーン耐障害性・マルチクラウド接続）、Yuemin 氏（リージョン障害への備え）、Rochak 氏（Resiliency Manager / Agent）の順で進みます。

## 3. 何に関する発表か

テーマは、コンピュート、ストレージ、ネットワーク、アプリケーションにまたがる resiliency（耐障害性）を、設計の最初から組み込むことです。発表は次の流れで進みます。

1. なぜ resiliency が後回しになるのか（よくある 5 つの課題）
2. Microsoft とお客様の共同責任と、過去の障害から得た教訓
3. ゾーン単位の備え（Availability Zones、VMSS、zone resilience for Azure VMs）
4. Azure Multicloud Interconnect
5. リージョン障害への備え（Azure Site Recovery、セルフマネージドな復旧、ストレージ、バックアップ、ネットワーク）
6. Infrastructure Resiliency Manager と resiliency agent、Chaos Studio による検証

## 4. 発表者が伝えたいメッセージ

最初の登壇者は、resiliency は「うまくいっているときは誰も気にしない」ものであり、問題が起きたときに初めてネットワークやコンピュート、ストレージが耐障害性を備えていたかが問われる、と話します。そのうえで、Microsoft には障害の分離・安全な更新・分かりやすい情報提供という責任があり、お客様にも適切な構成の選択・テスト・目標設定という責任がある、という **shared responsibility（共同責任）** を繰り返し強調しています。

最後の登壇者は、聴衆への行動の呼びかけとして「戻ったら重要なアプリケーションを 1 つ選び、Azure Infrastructure Resiliency Manager か Resiliency Agent で構成し、測定・観測し、耐障害性の証跡を作ってください」と述べています。

## 5. 発表内容の詳細

### 5.1 resiliency が後回しになる理由

登壇者は、オンプレミスからクラウドへの lift and shift を例に、CIO に与えられる目標が速度、コスト、モダナイズであり、resiliency は目標に入っていないことが多いと指摘します。そこから次の課題を挙げています。

| 課題 | 説明 |
| --- | --- |
| 優先順位付けが初期段階で行われない | 「resiliency はフェーズ 2 で」と後回しになり、フェーズ 2 が実現しないか、実現しても作り直しが必要で高コストになる。P0 にならず P1〜P3 のまま時間が過ぎる |
| 責任の所在が曖昧 | 障害時にアプリ開発者はインフラの問題、インフラ担当は Microsoft の問題と言う。実際には Microsoft とお客様の共同責任 |
| リスクが早期に定量化されない | 例として、給料日に決済システムが電源障害で止まり、1 拠点・1 ゾーンだけで構成していたために ATM も止まり、1 時間で数百万件のトランザクションが止まる場面が挙げられた |
| 測定できる指標が無い | RTO（recovery time objective）が設定されていない。SLA とは別に、自社のビジネスに意味のある RTO を決めるべきだと説明された |
| ベストプラクティスの採用がばらつく | 社内の 5 人に聞くと 5 通りの答えが返り、「鎖は一番弱い輪の強さしかない」ため、1 部門が未対応でもエンドツーエンドで障害になりうる |

### 5.2 共同責任と、2 つの障害の振り返り

登壇者は「2024 年 1 月と 2025 年 10 月」という日付を示し、これが Azure で起きた大きな障害の日であることを明かします。そのうえで、Microsoft 側の責任を次のように整理しています。

* 障害が起きたとき、すぐに分離し、ワークロードへの影響を最小にできているか
* ソフトウェアの更新やパッチを、影響なく安全に行えているか
* 問題が起きたとき、原因を明確に伝え（RCA）、いつ直るのか、是正措置を取ったかを伝えているか

お客様側の責任としては、ワークロードの重要度に応じて適切なサービスと SKU を選ぶこと（dev/test なら resiliency に追加費用を払わないのも意識的な選択）、独立した failure domain をできる限り用意すること、必要なキャパシティに備えること、データが耐障害性のある構成にあるかを確認することが挙げられました。さらに、エンドツーエンドのヘルスシグナルを整えてアラートや SLA 違反に先に気付けるようにすること、フェイルオーバーを実際にテストすること（構成だけして何年もテストしていないと、本番で 1 か所が壊れていたと気付く）、目標を設定することが、お客様と Microsoft が一緒に取り組む点として語られました。

続いて登壇者は、ExpressRoute のプライベート接続に影響した 2 つのインシデント（9 月 30 日と 10 月 30 日と説明されています。年は発話では明示されていません）を説明します。

1. **9 月 30 日**：30 分の障害。特定のシナリオで顕在化した bug があり、問題を把握しようとしている間に last known good state へのフォールバックが行われ、それが有効になるのは再試行の 30 分後だった。数千のお客様がプライベート接続を使っており、30 分のダウンタイムは「到底受け入れられない」。これは Microsoft の責任であり、お客様側でできることは無く、resiliency の構成を済ませていても失敗した、と説明されています。
2. **10 月 30 日**：緊急パッチを 3 日間の時間枠でフリート全体に適用する必要があった。しかしお客様が設定していた customer-controlled maintenance window（営業時間外に設定されたメンテナンス時間帯）を迂回してしまい、しかもその旨をお客様に知らせ忘れた。株式取引所や金融系のワークロードで営業時間中に影響が出たとしたら、この対応が原因である、と登壇者は認めています。

その後、冒頭の 2 つの日付に対応する障害として、次の 2 件の教訓が語られます。

**Chicago（2024 年 1 月）**：サイト全体が停止しました。気温が下がり、chiller（冷却装置）が止まったことが原因と説明されています。プライベート接続について、2 つのサイトに接続していたお客様は別のサイトにフェイルオーバーできましたが、2 本のリンクがどちらも Chicago サイトにだけつながっていたお客様は接続できなくなりました。登壇者は、病院や金融機関では接続断が致命的であり、Black Friday のようなときなら事業への損害が大きいと述べ、**プライベート接続を使うなら複数のサイトに接続する**ことを強く勧めています。

**AFD（2025 年 10 月）**：登壇者は、前年のこのイベントの直後に AFD（Azure Front Door）の障害があったと話し、原因は configuration bug だったと説明しています（音声認識の表記は「AFT」で、Azure Front Door のことと説明されています）。AFD の前段に Traffic Manager プロファイルを置き、CDN が落ちたときに Application Gateway や別の CDN へフォールバックする仕組みを持っていたお客様は、その日に影響を受けませんでした。単一の構成でフォールバックが無いお客様は影響を受けた、とのことです。

> 注意：9 月 30 日・10 月 30 日の 2 件と、Chicago・AFD の 2 件の対応関係や年については、発話から正確に切り分けられません。上の記述は、聞き取れた範囲に基づいています。

### 5.3 ゾーン耐障害性の基本

登壇者は、Availability Zones を「互いに十分近く、レイテンシが低い一方で、1 つが落ちても 2 つ目や 3 つ目が使えるように分離されたデータセンター」と説明し、まず次の 3 点を勧めています。

1. すべてのリソースを Availability Zones に分散する。
2. 依存リソースも同じゾーンに置く。VM だけを分散していても、VM に依存する IP アドレスや NIC が特定のゾーンに固定（pin）されていると、そのゾーンが落ちたときに依存リソースも落ち、他のゾーンが役に立たない。
3. こうして構成すると SLA 上の uptime が最大になる、という説明。

ゾーンをまたいで VM をデプロイする方法は 2 つあります。

| 方法 | 説明 | 向いているワークロード |
| --- | --- | --- |
| zone spanning virtual machine scale sets（VMSS） | 必要に応じて自動でスケールアップ・ダウンする、柔軟な方法 | stateless |
| 自分でゾーンを選んでデプロイ | 使う 3 ゾーンを自分で決めて構成する | stateful |

また、resiliency は次の 3 つの場面に分けて考える、とも説明されています。

1. **Contain（封じ込める）**：ソフトウェア bug など制御できない原因による問題の blast radius（影響範囲）を限定する。登壇者は per disk resiliency（VM ではなくディスクを detach する。複数ディスクがあるとき、1 つが落ちても同じ Availability Zone 内の別のディスクは動き続ける）を例に挙げています。個々の機能は他のセッションで扱った、として詳細には入っていません。
2. **Recover（復旧する）**：封じ込められない場合に、最短で復旧する。特定のゾーンで VM が落ちたとき、別のゾーンへフォールバックできるか。
3. **Reconnect（再接続する）**：復旧後に接続が戻るか。ここで ExpressRoute Resiliency Guard（フェイルオーバーテスト用）が名前だけ挙げられています。

### 5.4 zone resilience for Azure VMs

登壇者は、これを話すより見せたほうが早いとして、ビデオを再生します。詳細は「6.1 デモ」にまとめます。要点は、ゾーン障害時に、同じ VM を構成そのままで正常なゾーンへ移せる、**Azure VM のネイティブ機能** であり、追加の Azure リソースが不要で、現在 **public preview** であるという点です。

### 5.5 Azure Multicloud Interconnect

登壇者は、前日に「resilient by default」として Azure Multicloud Interconnect を発表したと述べています。

* AWS とは **GA**、GCP とは **public preview**、Lumen とは last mile interconnect を発表した。
* クラウド間の接続が、この方式を選べば自動的に確立される。
* **quad model** を採用しており、2 つの異なるサイトに、サイトごとに 2 本のリンクで接続される。そのため前述の Chicago のような状況は起きない、と説明されている。
* クラウド間接続の運用負荷を減らす。マルチクラウドの企業は検討してほしい、と勧められている。

接続の作成手順は「6.2 デモ」に記載します。

### 5.6 リージョン障害への備え：Azure Site Recovery

2 人目の登壇者（Yuemin 氏）は、リージョン障害は稀で誰も望まないが、ミッションクリティカルなワークロードを稼働させる前に計画が必要だと話します。一般的な道は 2 つあります。

* Azure Site Recovery（ASR）による managed recovery orchestration
* 自分で設計する self-managed recovery flow

どちらが正解ということは無く、ワークロードや既存の環境・ツールの再利用の都合で選ぶ、という説明です。

**ASR の現状**：ASR は長く市場で実績のある VM recovery engine で、VM とディスクを継続的にセカンダリサイトへレプリケーションし、recovery plan に基づいて VM の依存関係を考慮した起動や、影響の無いテスト（non-impact testing）ができます。ここ数か月で、対応ディスクの種類の拡大、サポートする churn rate の引き上げ（データ変更の多いワークロードも ASR でレプリケートできるように）、ハイブリッドのパスの拡大といった強化が行われました。狙いは「DR 戦略が本番のデプロイに遅れないようにすること」とされています。

**ASR の次の段階**：VM recovery engine から **application recovery engine** へ広げる、regional resiliency experience を準備しています。gated preview はまもなく全員に公開される、とのことです。

1. アプリケーションの重要度に応じて **RTO と RPO の目標を設定**できる。
2. アプリケーション固有の DR ギャップについて推奨事項と、解消するための具体的な手順を提示する。
3. VM だけでなく、アプリが依存する他の Azure リソースにまたがって、アプリケーション単位の順序で復旧を管理する。
4. ドリルを実行でき、そのレポートをダウンロードして、セキュリティやコンプライアンスのレビューに使える。

登壇者は「保護の単位を VM からアプリケーションに引き上げる」ことが要点で、個々の VM を連結して復旧順序を考える作業が不要になると説明しています。

### 5.7 self-managed な復旧：3 ステップ

self-managed を選ぶ理由として、環境が複雑で何度か試して戦略を固めたい場合や、すでに BCDR パートナーがいる場合が挙げられました。手順は次の 3 ステップです。

1. protect your data（データを守る）
2. activate your compute（コンピュートを起動する）
3. reconnect your traffic（トラフィックを再接続する）

登壇者は、多くのお客様がステップ 1 で止まってしまい、ここを越えられないと他も管理が難しくなるため、時間の多くをデータ保護に割くと話しています。

**ストレージのネイティブ機能**：Blob や Files を使う場合、**GRS**（geo-redundant storage）と **GZRS**（geo-zonal redundant storage）があり、プライマリからペアのリージョンへデータを複製します。発話では "synchronously replicate" と説明されています。

> 補足：この点は発話のとおり書いています。レプリケーション方式の詳細は、公式ドキュメントでご確認ください。

「Azure のペアリージョンでは都合が悪い」「自分でリージョンを選びたい」という声は以前から聞いており、現在取り組み中で、近いうちにタイムラインを共有できる見込みと述べられました。

**レプリケーションのツール群**：Storage Mover、AzCopy、blob object replication があります。新機能としては、**Azure Files の cross-region replication に対する Storage Mover のサポート**が紹介されました。

* Storage Mover は「managed な AzCopy」のようなもの。スクリプトを自分で管理する代わりに、job とスケジュールを作ると、完了まで Storage Mover が管理する。
* Azure Files の SMB ファイル共有を、リージョンをまたいでコピーする job を作れる。
* 例：Azure Virtual Desktop（AVD）のユーザープロファイルを Azure Files に置いている場合、コピー元とコピー先のシェアを指定して job を登録する。毎日スケジュール実行するお客様が多い。プライマリリージョンが落ちたら、セカンダリリージョンに AVD を用意し、ユーザーはレプリケート済みのプロファイルでログインして、いつもの作業環境とデスクトップ設定を見られる。

**新しいストレージ製品のバックアップ/DR**：最近リリースした製品には、preview の時点から backup/DR の基本サポートが付く、という方針が語られました。例として、このイベントで発表した **次世代の Elastic SAN** は、preview 時点で instant snapshot と restore を備え、スナップショットを別リージョンへエクスポートする機能は対応を進めている段階とのことです。

**Azure Backup**：backup vault に GRS を設定し、バックアップの復元ポイントからセカンダリリージョンへ restore する方法です。対応ワークロードは広がっていますが、登壇者は個別の名前を挙げていません。また、**Azure Resiliency Manager** の一部として、Copilot を活用した AI アシスタント体験を構築中であると述べています（詳細は次の登壇者へ）。

**BCDR パートナー**：Azure Backup や ASR 以外の BCDR ソリューションを使っているお客様向けに、戦略的 BCDR パートナーと協力し、vault の使い勝手や hyper resiliency の体験を Azure ネイティブサービスに持ち込んでいると説明されました。スライドには最近のリリースと各パートナーの特徴をまとめた表が示されましたが、個々のパートナー名は音声からは確認できません。

**ネットワークの再接続**：インターネット向けと、オンプレミスのプライベートネットワーク向けの両方を、プライマリとセカンダリの両リージョンに接続できるようにしておく必要がある、という説明です。最も一般的な方法として **Azure Front Door**（複数エンドポイントへ負荷分散するグローバルサービス）が挙げられ、「ネットワークの DR をその場で作らない」ために、プライマリとフェイルオーバー先のセカンダリの両方を Front Door にあらかじめ登録し、フェイルオーバー時に素早くセカンダリへルーティングできるようにしておく、という推奨です。

### 5.8 Infrastructure Resiliency Manager と resiliency agent

3 人目の登壇者（Rochak 氏）は、今回のイベントで多くのお客様と話し、共通のフィードバックを聞いたと述べます。

* どの機能を使えばよいか分からない。
* resiliency の構成方法、どの機能をいつ使うかが分からない。
* 目標を達成できているか測定できるのか、監査人に動作することを証明できるのか。

これに応えて、アプリケーションから、そしてコンプライアンス要件から始まる体験として開発しているのが **Infrastructure Resiliency Manager** です。アプリケーションを把握し、アプリケーションレベルで構成を支援し、RPO/RTO に基づいてガイダンスを出し、体験の中で検証でき、コンプライアンスや監査向けの詳細も取得できます。さらに、アプリケーションに drift が起きたり構成を変更したりすると、自動で通知し、必要な変更を知らせる、という説明です。

シナリオは 3 つに整理されています。

1. **Day zero**：構築時に、適切な resiliency の姿勢（posture）を構成して始める（start resilient）。
2. **既存アプリケーション**：すでに耐障害性が十分でない構成を、どう resilient にするか（get resilient）。
3. **Stay resilient**：resiliency の posture を観測し、ガイダンスを受け続ける。

例として、Front Door、App Service、インターネットロードバランサー、ロードバランサー配下の VM、データベース（「SPG SQL」と聞こえます。音声認識の都合で表記が確定できません）、Service Bus、Key Vault で構成された「pay service」が示され、ビデオが再生されました。内容は「6.3 デモ」にまとめます。

**検証の仕組み**：検証部分は **Chaos Studio** が支えており、ゾーン障害・リージョン障害に加え、CPU やメモリの負荷、イベント駆動のメッセージ中断、ネットワーク障害など、あらゆる障害をシミュレートできます。これは Resiliency Manager に統合されており、コンプライアンスの証明のために Resiliency Manager の外へ出る必要がありません（発話は "Kiosk" と聞こえますが、文脈上 Chaos Studio を指すと考えられます。音声認識の都合で表記が確定できません）。

**non-impacting test failover**：この end-to-end 統合の計画として、本番に影響を与えずにテストフェイルオーバーを行えるようにすると説明されています。分離した環境を作り、テストフェイルオーバーを実施し、うまくいっていることを確認したあとクリーンアップする、という流れです。構成・測定・証明の一連をカバーできるとのことです。

## 6. デモ・手順

このセッションのデモは 3 本ともビデオ形式で、画面の細部は音声からは確認できません。以下は、ナレーションで語られた内容だけを順に記載します。

### 6.1 zone resilience for Azure VMs

前提：Caldova（音声認識の都合で表記が確定できません）という会社を想定した設定です。この会社は、3 層構成の e-commerce アプリケーションのうち、注文処理を担う重要アプリケーションをゾーン障害から守りたいと考えています。データベース VM を、構成を失わずに正常なゾーンへフェイルオーバーする必要があります。

1. 注文を処理する e-commerce アプリケーションを示す。単一インスタンスのデータベース VM は、West US リージョンの availability zone 1 にあり、VM もゾーンも正常である。
2. 復旧後の検証用に、IP アドレス、VM ID、最後にコミットされた注文エントリ、データベースを控える。
3. 障害をシミュレートする。データベース VM が落ちると、フロントエンドのアプリケーションの状態が unavailable になり、データベース接続を確立できなくなる。
4. 復旧のために zone update command を実行して、VM を正常なゾーンへ移す。このコマンドは VM を強制的に deallocate し、VM ID・IP アドレス・ARM リソース ID を含む構成を保持したまま、正常なゾーンへ移す。更新されるのは VM のアベイラビリティゾーンだけである。
5. VM は、正常なゾーンにある同じ zone redundant storage のディスクを使って起動する。アプリケーションが再び応答する。
6. このデモでのデータベース VM の復旧時間は 2 分。データベースも応答し、障害前にディスクに書かれていたコミット済みの注文 ID も無事だった。

まとめとして、同じ VM が、同じ構成・同じ IP アドレス・同じ ARM リソース ID のまま正常なゾーンに復元され、フェイルオーバー後に手動の復旧手順は不要で、追加の Azure リソースも不要である、と説明されました。

### 6.2 Azure Multicloud Interconnect（Azure と AWS）

1. Azure 側で、接続リソースを作成する。重要な選択は port type で、従来のプロバイダー経由の ExpressRoute ではなく **Azure multi-cloud interconnect** を選ぶ。これで、接続の相手側が別のクラウドプロバイダーであることを Azure に伝える。このデモでは相手は AWS。
2. プロバイダー、接続ロケーション、帯域幅を選び、相手側のプロバイダーで要求を redeem（引き換え）するアカウント ID を入力する。
3. 内容を確認して、Azure でリソースを作成する。すると activation key が生成される。
4. activation key をコピーして、AWS に切り替える。AWS の Direct Connect で、この key を貼り付けて interconnect の要求を承認する。相手側のプロバイダーが要求の詳細を表示するので、先へ進む前に確認する。これで Azure の要求が、意図したプロバイダーのアカウントに紐付けられる。
5. ゲートウェイの設定を終え、次のステップで確認して interconnect を accept する。引き渡し（handoff）が完了する。
6. Azure のリソースに戻って、接続状態を確認する。プロバイダーと provisioning のステータスが表示され、相手側が受け入れた要求が反映されている。interconnect リソースが provisioned になり、BGP セッションが構成され、正常な状態になっている。

ナレーションで補足されたのは次の点です。

* 物理設計としてサイトの冗長化が組み込まれており、トラフィックは equal-cost multipath routing により 4 本の異なるパスに分散される。単一のパスやサイトへの依存が減る。
* MACsec が、プロバイダー間のポイントツーポイントのリンクを保護して、セキュリティを高める。
* お客様は、物理的な cross-connect の手配や、オンプレミスネットワーク経由の接続管理、そこを経由して他のクラウドへ向かう hairpin をしなくてよい。Azure で接続を定義し、相手プロバイダーで要求を redeem すれば、あとはプロバイダー同士が下回りの interconnect を調整する。
* point-to-point アドレスや LAN ID などのリンク設定は、Azure と相手プロバイダーが調整し、お客様が指定・設定する必要は無い。「お客様が 2 本の接続をつなぎ合わせるのではなく、1 つの調整された interconnect ワークフロー」である。
* この後、お客様は ExpressRoute gateway を attach して、ワークロードのネットワークを接続し、両クラウド間のプライベート接続を使える。

### 6.3 resiliency agent in Azure Copilot

ビデオのナレーションは、Azure Copilot の resiliency agent が、点在するシグナル、プロジェクト化するギャップ、一度もテストしていない復旧計画といった課題をまとめる、という紹介から始まります。語られた内容を順に記します。

1. **全体像**：service group またはリソースの resiliency を 1 つのビューで見て、ギャップに対して agent で 1 クリックで対応できる。
2. **新規構築**：アーキテクチャ図をアップロードすると、agent が各サービスをゾーン耐障害にする方法を、選択ごとのコストとダウンタイムとともに推奨し、検証済みの Bicep または ARM テンプレートを生成する。pull request の準備ができる。resiliency が後付けではなく初日から組み込まれる。
3. **本番稼働中**：agent がリソースを検出してアプリケーションにグルーピングし、resiliency の目標をすぐに設定できる。assessment を依頼すると、何を直すか、コスト、ダウンタイムをまとめたレポートが出て、承認用に共有できる。
4. **Stay resilient**：SLI とエラーバジェットを追跡し、バジェットが少なくなったら agent に何が危険かを尋ねる。agent は assessment を実行し、まず posture を示し、さらにポータルだけでは分からない observability、ドリル、復旧計画に関する所見を加える。
5. **画面で示された例**：SLI がエラーバジェットを使い切りそうなのに、recovery plan が未構成で、ゾーンが落ちると復旧の道が無い。ドリルが未準備で、resiliency が一度もテストされていない。各所見にはリスクと修正案が付き、レポートをダウンロードできる。
6. **recovery plan の作成**：agent に作成を依頼すると手順を案内してくれる。ポータルで実行できる。
7. **ドリルの作成**：agent が、対象リソースの選定、Chaos Studio の native な zone-down fault（またはカスタムの fault）の適用、SLI や Azure Monitor の health model による監視の用意を案内する。ドリルはポータルから実行して、ゾーン耐障害性を検証する。
8. **再評価**：実行中はアプリケーションを監視でき、agent に assessment を再実行させると、アプリケーションが正しく復旧したか、SLI がベースラインに収まっていたかが分かる。収まっていれば resiliency の posture が検証されたことになる。

ナレーションは、agent が操作の前に確認を求めること、ドリルと recovery plan の実行はポータルからでお客様の制御下にあることを述べています。

## 7. 注意点・発表者の気付き

* **構成しただけでは足りない。** zone の分散を済ませても、IP アドレスや NIC などの依存リソースが特定ゾーンに固定されていると、他のゾーンは役に立ちません。
* **フェイルオーバーは実際にテストする。** 構成済みでも何年もテストしていないと、障害時に 1 か所が壊れていたと気付くことになります。
* **接続の冗長化が効いた事例。** Chicago では複数サイトに接続していたお客様、AFD の障害では Traffic Manager とフォールバック先を用意していたお客様が影響を受けませんでした。単一サイト・単一構成のお客様は影響を受けました。
* **Microsoft 側の誤りの認識。** ExpressRoute の 30 分の障害（last known good state へのフォールバックが 30 分後だった点）と、緊急パッチ時に customer-controlled maintenance window を迂回し、通知もしなかった点を、登壇者は Microsoft の責任として認めています。
* **resiliency は後回しにしない。** 後から作り直すとコストがかかり、優先度が P0 にならないまま時間が過ぎる、と指摘されています。
* 新機能の提供状況は次のとおりで、いずれも発表時点の情報です。

| 機能 | 状況 |
| --- | --- |
| zone resilience for Azure VMs | public preview |
| Azure Multicloud Interconnect | AWS：GA、GCP：public preview（Lumen との last mile interconnect も発表） |
| ASR の regional resiliency experience | gated preview（まもなく全員に公開予定） |
| 次世代 Elastic SAN | preview（instant snapshot と restore。スナップショットの別リージョンへのエクスポートは対応中） |
| ペアリージョン以外の「region of choice」 | 取り組み中。タイムラインは近く共有する見込み |
| Resiliency Manager の non-impacting test failover | 提供状況は発話から確定できない（"we plan" と "now you can" の両方の表現がある） |

発表では、resiliency agent や Infrastructure Resiliency Manager の提供時期・提供状況については触れられていません。

## 8. 利用者にとっての利点

発表で語られた利点を整理します。

* **復旧の手間と時間の削減**：zone resilience for Azure VMs のデモでは、データベース VM の復旧に 2 分、IP アドレス・VM ID・ARM リソース ID を保持したまま、手動の復旧手順なしで、追加リソースも不要で行えました。
* **マルチクラウド接続の簡素化と冗長性**：Azure Multicloud Interconnect では、物理 cross-connect の手配や hairpin が不要で、quad model により 2 サイト・4 本のパスが標準で用意され、MACsec で保護されます。
* **DR の単位がアプリケーションに**：ASR の regional resiliency experience で、RTO/RPO の設定、DR ギャップの推奨、アプリ単位の復旧順序の管理、ドリルのレポートが得られます。
* **Azure Files の cross-region レプリケーションの運用負荷軽減**：Storage Mover の job とスケジュールで管理でき、AVD のユーザープロファイルを毎日セカンダリリージョンへ複製する使い方が示されました。
* **設計から検証、証跡まで一続き**：Resiliency Manager / agent で、新規構築時の推奨（コストとダウンタイム付き、Bicep/ARM 生成）、既存アプリの assessment、recovery plan とドリルの作成、監査やコンプライアンス向けのレポートまで、1 つの体験で進められます。

## 9. まとめ

このセッションは、resiliency を「設計の初日から」「共同責任として」「測定と検証を伴って」扱うことを訴えています。ゾーン単位では、Availability Zones への分散と依存リソースの扱い、そして zone resilience for Azure VMs（public preview）が示されました。クラウド間接続では Azure Multicloud Interconnect が quad model で resilient by default を実現すると説明されました。リージョン単位では、ASR のアプリケーション単位への拡張、Storage Mover・Azure Backup・パートナー・Front Door を使った 3 ステップの self-managed 復旧が紹介されました。最後に、Infrastructure Resiliency Manager と resiliency agent、Chaos Studio による検証が、構成・測定・証明の流れとして示されました。

最後に呼びかけられたのは、重要なアプリケーションを 1 つ選んで構成し、resiliency の posture を測定・観測し、耐障害性を証明する証跡を作ることです。
