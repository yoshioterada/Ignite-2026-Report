# THR1015 Foundry Discord Live: Product Updates & Community AMA

## 1. はじめに

このセッションは、Microsoft Foundry の開発者コミュニティである **Foundry Discord** を紹介する、25 分のシアターセッションです。Discord で普段行っているやり取りを会場で再現する形で、Discord の使い方、コミュニティメンバーの紹介、毎週のライブ配信（"handoff" と聞こえます）での最新機能デモ、そして会場からの質問への回答が行われました。

通常のブレークアウトとは違う「コミュニティセッション」で、Nick 氏のパートはスライドを使わず、デモと対話が中心です。Foundry の新機能としては、Foundry Canvas、Copilot harness で作るエージェント、model router、routine（定期実行）、MAI Transcribe 2 streaming、継続的改善（insights と agent optimizer）が画面を使って紹介されます。

セッション URL: [https://ignite.microsoft.com/en-US/sessions/THR1015](https://ignite.microsoft.com/en-US/sessions/THR1015)\
YouTube URL: [https://www.youtube.com/watch?v=nAMd4QI3nfA](https://www.youtube.com/watch?v=nAMd4QI3nfA)

## 2. 発表者

| 氏名 | 所属 | 役職 |
| --- | --- | --- |
| Amy Boyd | Microsoft | Foundry Developer Relations Lead |
| Nick Brady | Microsoft | Principal Product Manager, Foundry Developer Experience |
| Lee Stott | Microsoft | Principal Cloud Advocate |

> 補足：冒頭の自己紹介で、Amy 氏と Lee 氏は Foundry の developer advocate、Nick 氏は開発者向けプラットフォームの developer experience とドキュメントを率いる principal product manager と名乗っています。デモの大半は Nick 氏が行いました。このほかに、コミュニティメンバーとして SHI の Agentic Solutions Manager である **Rafsan 氏**（綴りは音声認識により「Rafsam」「Rafson」「Rasan」などと揺れ、確定できません）がゲストとして紹介され、短く話します。

## 3. 何に関する発表か

Foundry は、発表では「開発者向けの AI アプリとエージェントのファクトリー」と紹介されています。モデルプロバイダーの幅広い選択肢、コードファーストでエージェントをホスト・最適化する agent service、IQ や tools を使った知識の grounding、guardrails や content safety による安全対策などが、Foundry 内のサービスとして挙げられました。

今年の Ignite のキャッチコピーの 1 つとして、**continuous improvement（継続的改善）**が出てきます。初日はエージェントを作る日で、その後の毎日はエージェントを改善する日だという考えです。ベースラインを評価し、モデルやプロンプトを最適化し、必要なら fine-tuning で学習させて、本番のエージェントを継続的に良くしていきます。

Foundry は範囲が広く変化も速いため、変更に追従し、互いに学び、問題を解決し合い、製品へのフィードバックを出せる開発者コミュニティとして Discord を作った、という流れです。

発表の流れは次のとおりです。

1. Foundry の簡単な紹介と、Discord の説明（Amy 氏）
2. Discord の画面紹介
3. コミュニティメンバー Rafsan 氏へのインタビュー
4. 毎週のライブ配信とデモ（Nick 氏）
5. 会場からの質問（Q&A）

## 4. 発表者が伝えたいメッセージ

メッセージは、**Foundry を使うなら Discord に参加してほしい**というものです。サポート窓口ではなく開発者コミュニティであり、リアルタイムのチャット、Ask Me Anything セッション、オフィスアワー、製品グループとの対話ができる場だと説明されています。作っている本人に話を聞きたい機能があれば、Discord で見つかる可能性が高いとのことです。

運営側が重視しているのは、会員数よりも**エンゲージメント**です。健全なコミュニティは、人が常にやり取りしているコミュニティだという考えで、メンバー同士の助け合い（ピアサポート）を大切にしています。また、製品グループの側にいても、プラットフォームについて率直な意見をコミュニティが出してくれることが非常に重要だとされています。

## 5. 発表内容の詳細

### 5.1 Microsoft Foundry Discord とは

Discord はサードパーティの会社で、ゲーム業界から広がったコミュニティの場です。多くのテック企業が採用しているのは、開発者がすばやく人と交流したがっているからだと説明されています。

- **規模**: 約 60,000 メンバーで、増加中。
- **性格**: サポート窓口ではなく、開発者コミュニティ。
- **できること**: Foundry を使っている人とのリアルタイムのチャット。AMA やオフィスアワー。製品グループのメンバーが、自分の担当領域について開発者と話す機会（参加する製品グループのメンバーが増えている）。
- **ピアサポート**: 経験に基づいて質問・回答する、頻繁に参加しているメンバーがいる。

### 5.2 Discord の画面紹介（Amy 氏）

Amy 氏が自分の Discord 画面（自分好みにカスタマイズしたもの）を見せています。

1. Microsoft Foundry のページを表示する。最初にやるべきことは **server guide** を読むことで、各チャンネルと機会について説明するメッセージが書かれている。
2. **Events** をクリックすると、このセッション自体が案内されている。
3. 扱うトピックの例として、Foundry models、agent framework、AI とデータサイエンスの話題、Python と AI が挙げられた。Microsoft IQ について活発に発信する人（Pamela 氏）や、Java 関連のコンテンツもある。
4. アナウンスは、できれば週に 1 件程度に抑えている。全員に届くため、頻繁には出さない方針。Lee 氏が Ignite 基調講演の写真を投稿した例が見える。オフラインをオンラインにつなぐ狙い。
5. 左側にチャンネルが大量にあるが、慌てる必要はない。始めやすいのは製品関連のチャンネル。**Foundry の General discussion** を見ると、質問と回答が行き交い、エンジニアリングチームが取り組み中だと Nick 氏が書き込んでいる例もある。

### 5.3 コミュニティメンバーのインタビュー

Lee 氏が、SHI の Agentic Solutions Manager である Rafsan 氏を紹介しました。SHI は、主要な Microsoft パートナーの 1 つで、北米で最大の Microsoft パートナーだと紹介されました。

Rafsan 氏が話した内容は次のとおりです。

- Foundry には新機能がたくさん入ってくるため、学び、実践し、他の人がどう使っているかを理解することが重要。顧客向けにソリューションを作っているので、新機能を学ぶとき、疑問を投稿して答えをもらうときの**第 1 のチャンネル**が Discord で、とても価値がある。
- Discord で自身もセッションを何度か行った。学んだことを他の人と共有するときが最もよく学べるし、質問を受けることで、Foundry の他の領域を学ぶことにも集中できる。
- Copilot Studio と Microsoft Foundry、Foundry のモデルを Copilot Studio に統合する分野の専門家として、関心を集めている（登壇者による紹介。Amy 氏と Lee 氏のどちらの発言かは文面から確定できません）。Discord のタグは名前そのままだと本人が答えています。

Rafsan 氏は Microsoft の **MVP（Most Valuable Professional）**でもあると紹介されました。このあと進行役の登壇者（Amy 氏か Lee 氏かは確定できません）が、コミュニティの狙いを次の 3 点にまとめています。経験を共有すること、互いの課題を助け合うこと、登壇者と Nick 氏が足りない部分を補い、製品の方向性や詳しいガイダンスを伝えること。あわせて、Foundry がエージェント型の各業界をどう支えているかを示すことも目的です。

### 5.4 毎週のライブ配信 "handoff"（Nick 氏）

Nick 氏は、毎週のライブ配信を始めた背景を説明しています。新しい情報を追いかけるのは難しく、毎週のように顧客が Redmond を訪れて製品の話をするものの、残りの人たち、そして Discord の 60,000 人に伝える方法が必要だった、というものです。

- 配信は YouTube、X、LinkedIn でライブ配信され、録画も残る。Discord には少し年齢が上すぎると感じる人は、そちらで視聴できる。
- スライドは使わず、デモ中心。ここ 1 か月は AV の不調が続いたが、ようやく解消したため、定期シリーズとして始める。
- 別に、開発者ブログで **What's New in Microsoft Foundry** の月次ブログも書いている。画面上の日付は 10 月のもの。来週月曜日に、Ignite の総まとめ版を公開する予定。その翌週は、休暇明けに配信を再開する見込み。
- "handoff" は、エージェントに仕事を引き渡す意味だけでなく、**ゲストに引き渡す**意味もある。モデルプロバイダーなどを招く。例として、動画・マルチモーダルモデルを持ち込んだ fal.ai と聞こえる企業が挙げられた（音声認識の都合で表記が確定できません）。
- ライブでは、Foundry でのエージェント構築のデモも行う。

### 5.5 デモで見せた新機能（Nick 氏）

次の 6.1 から 6.3 で順に説明します。要点は次のとおりです。

- **Foundry Canvas**: GitHub Copilot アプリにインストールするキャンバスで、チャットウィンドウの中にアプリが入る。Copilot との会話とキャンバス内の操作が相互運用される。画面上は preview 表示だが、発表では「ちょうど GA になった」と述べられている。
- **Copilot harness**: Microsoft Foundry の public preview で提供され、prompt agents の能力を拡張する。
- **Model router**: 約 40〜50 のモデルをサポート（Nick 氏は「44 くらいだと思う。明日変わるかもしれない」と述べた）。
- **Routine**: エージェントをスケジュールなどで定期実行できる。

### 5.6 Q&A

**質問 1: トークンキャッシュ（prompt caching）に、ネイティブな対応はあるか。**

Nick 氏の回答の要点は次のとおりです。

- 推論を担当するチームが、モデルの効率化に多くの時間をかけている。
- model router については、prompt caching が顧客にとって重要なため、先月 **Session Affinity** という新機能を導入した。モデル間を動的にルーティングしても、キャッシュの恩恵を失わないようにするため。
- prompt caching の説明: エージェントのターンでは、応答を返したあと、次の質問と合わせて会話全体を再び送る必要がある（金魚の記憶のようなもの）。prompt caching は、その会話全体を毎回処理し直さなくて済むよう、GPU メモリに保持する仕組み。揮発性で、すぐに消える。
- Azure OpenAI の GPT 系モデルや、Azure 直販（direct from Azure）のモデルでは、prompt caching の機能が強い。model router と session affinity を使う場合も、モデルに直接依存する場合も、そのコスト削減を受けられるように取り組んでいる。
- 利点は 2 つで、計算を省けるためお金が節約でき、次のトークンまでの時間も短くなる。
- 特定のモデルで prompt caching の効果が見られない場合は、フィードバックがほしい。コミュニティで見つけた場合は、推論チームに伝えて直してもらっている、と述べた。

**質問 2: 非常に重要だが、月に 2 回程度しか使われない長時間エージェントに、継続的改善の機能は効くか。チューニングできるメトリクスはどれくらい細かいか。**

Nick 氏は、直接の数値ではなく、自分の週次の dependency agent を例にして insights と agent optimizer を説明しました（6.4 を参照）。質問にあった「メトリクスの粒度」について、具体的に答える発言は確認できませんでした。この例は、週 1 回しか動かないためボリュームが少ないエージェントを想定しており、数週間分のトレースがたまれば optimizer が効く、という趣旨です。

## 6. デモ・手順

このセッションにはデモがあります。主に Nick 氏が画面を共有して行いました。録音は音声のみのため、画面の細部は確認できません。以下は語られた範囲の内容です。

### 6.1 Foundry Canvas と Foundry portal

1. VS Code の拡張機能 Foundry Toolkit を使う開発者も多いが、portal を使う人も多いと説明した上で、**Foundry Canvas** を見せた。GitHub Copilot アプリにインストールするもので、チャットウィンドウの中のアプリとして動く。
2. Copilot が、このアプリの中のすべてと、そこで行う操作を理解する。プロジェクトを選ぶことも Copilot に伝わる。
3. 先週のライブ配信で作った、Copilot harness を使うエージェントのプロジェクトを開いた。
4. エンドポイントをコピーして VS Code に移る、またはここから直接 VS Code で開く、といった使い方ができると説明した。
5. このプロジェクトのエージェントは、すべて **hosted agents**。クラウドにコードを出荷する方法で、管理でき、routine で動かしたり、GitHub Actions でトリガーしたり、Teams のチャンネルやグループチャットでメンションされたときに動かしたりできると説明した。

### 6.2 Foundry portal でのエージェントと model router

1. 同じプロジェクトを Foundry portal で開く。GitHub Copilot harness で作られたエージェントで、portal から直接作ることもできる。
2. モデルを選べる。ここで **model router** を選択した。model router は、タスクやプロンプトの複雑度に応じて使うモデルを切り替える。ルーティング方法を変えることで、最高品質のモデルを優先するか、低コストで汎用性の高い、フロンティアに近いモデルに寄せてコストを抑えるかを選べる。組織のコスト管理手段になる、と説明した。
3. 入力例として「hi, what can you do?」を挙げた（大文字にしたことに冗談を添えている）。これに対する実行結果は語られていません。
4. Copilot harness に標準で付く**ツール**を紹介した。ウェブから情報を取得するサンドボックス、GitHub リポジトリの検索、Foundry の最新ブログの検索、サンドボックス内のターミナルセッションでのシェルコマンド実行、**サブエージェントの生成**。
5. サブエージェントの例: リサーチを頼むと、検索呼び出しを 1 つずつ待たずに、3 つのサブエージェントが小さな Web 検索クエリのエージェントとして並行し、一度に結果を返す。オーケストレーションを自分で作る手間が減る。

### 6.3 Routine と Teams の update agent

1. 別のプロジェクトを見せた（GitHub、dev blogs、cookbook の recipes などのエージェントを作っている）。ここには **dependency agent** がある。
2. portal の routine「Depend Agent Weekly」を見せた。スケジュールで実行され、毎週金曜日の 1:15 PM に動く（この時刻にした理由の説明は聞き取りにくい）。実行履歴には 6 日前と 1 日前が見える。
3. dependency agent は、REST API 仕様、Foundry SDK、Foundry samples リポジトリから情報を集め、「前週の Foundry の新情報から、月曜の handoff で何をデモできるか」を探す。
4. 結果は、Microsoft Teams にある **update agent** が受け取って届ける。update agent は Microsoft agent framework で作られている。Nick 氏は録画で Teams を見せるべきではないかもしれない、と断った上で表示した。
5. 画面には「developer updates」として、Redis context indices の再構築、アップグレード前の simulation seed data generation、新しい Azure AI projects ライブラリ 3（beta）、依存関係の Rust SDK へのアップグレードなどの項目が読み上げられた（音声が聞き取りにくく、正確な文言は確定できません）。これらを毎週ライブで紹介する、と説明した。

### 6.4 継続的改善: insights と agent optimizer

Q&A（質問 2）の流れで見せた内容です。並行して、画面では **MAI Transcribe 2 streaming** が動いていました。

1. 先に MAI Transcribe 2 streaming を紹介した。基調講演で Mustafa Suleiman 氏が発表した MAI モデル群の 1 つで、リアルタイムの speech-to-text モデル。非常に高速・高効率・高精度で、複数話者と多言語に対応する。VP of developer relations の Patrick 氏がフランス語で話しかけ、Nick 氏には分からなかったが、変換はできた、と話した。
2. dependency agent は、Nick 氏に直接応答するのではなく、結果を update agent に渡す（Microsoft Teams の activity protocol を使う）。この性能を見るため **insights** を使い始めた。
3. continuous improvement の発表は、prompt agents 向けの**プレビュー**で、insights を **agent optimizer** に渡して修正できる。
4. 例では、中程度のアラートとして rate limit recovery が出た。モデルデプロイメントの 1 分あたりのトークン数（クォータ）が足りなかったため、トレースの冒頭で、data zone の model router の呼び出し 2 回が rate limit 超過で失敗している。証拠となるトレースがハイライトされて表示される。
5. 下には**修正案**も出る。rate limit recovery policy を更新し、サイズが大きすぎるリクエストを同じプロンプトで即座に再送しないようにすること。あわせて、フォールバックとして会話を圧縮（compaction）する仕組みを追加することが挙げられた。Nick 氏は、Microsoft Agent Framework なので、会話サイズを見て圧縮するサブエージェントを置いてもよい、とも話した。
6. agent optimizer の使い方として、本番のトレースがたまれば、評価（evaluators）を実行できる。例として、ツール呼び出しの正確さ（Web 検索ではなく GitHub MCP server でリポジトリを探せているか）を挙げた。
7. 満たせなければ、optimizer が新しい構成を試す、と説明した。指示の改善、skill があれば手順やスクリプトの更新、ツールの説明に例を加えてツールが呼ばれる頻度を上げる、モデルの選択の変更などである。
8. 多数の候補を試した後に、最適化対象に合わせた構成が返ってくる。品質を目標にして「品質を上げ続ける」ループを回し、基準に達したらそれを固定して、**コストだけを対象に最適化**する、といった目標志向の使い方ができる。同等の品質を、桁違いに安く出せる、能力の低いモデルを使えないか探る、という例が示された。

> 補足：6.4 の「optimizer が新しい構成を試す」以降は、Nick 氏が「こうできる」と説明した内容で、画面上で optimizer を実行して結果を見せた様子は語られていません。

## 7. 注意点・発表者の気付き

- **Discord はサポート窓口ではない。** 開発者コミュニティとして位置づけられています。
- **Foundry Canvas は、画面上は preview と表示されていたが、Nick 氏は「ちょうど GA になった」と説明した。**
- **continuous improvement（insights から agent optimizer）は、prompt agents 向けのプレビュー。**
- **Copilot harness は Microsoft Foundry で public preview。**
- **prompt caching は揮発性。** GPU メモリに保持され、すぐに消えます。
- **model router のモデル数は変わりうる。** Nick 氏自身が「明日変わるかもしれない」と話しています。
- 会場からの質問は 2 件でした（残り約 5 分の案内のあとに受け付けています）。

## 8. 利用者にとっての利点

発表で語られた利点は次のとおりです。個別の顧客事例の数値は出ていません。

- **学習と相談の場**: 新機能の学習、疑問の投稿、回答の取得が Discord でできる。SHI の Rafsan 氏は、顧客向けソリューションを作る際の第 1 のチャンネルと述べた。
- **製品チームに近い**: 作った本人や、製品グループのメンバーと直接話せる。製品の方向性やガイダンスも得られる。
- **コスト管理**: model router で、タスクの難易度に応じてモデルを切り替え、低コストのモデルに寄せることで、組織のコスト管理手段になる。
- **prompt caching による節約**: 計算の省略によるコスト削減と、次のトークンまでの時間の短縮。
- **エージェントの改善**: insights で原因と修正案を確認し、optimizer で品質やコストを目標に構成を最適化できる。
- **オーケストレーションの手間削減**: Copilot harness のサブエージェント生成により、並行した検索を自前で組む必要がない。

## 9. まとめ

このセッションは、Foundry の開発者コミュニティである Discord（約 60,000 メンバー）への参加を呼びかけるものでした。コミュニティはサポート窓口ではなく、メンバー同士の助け合いと、製品チームとの対話の場で、運営側は会員数よりもエンゲージメントを重視しています。

Nick 氏のデモでは、Foundry Canvas、Copilot harness で作るエージェント、model router、routine による定期実行、Teams の update agent、MAI Transcribe 2 streaming が紹介されました。Q&A では、model router の Session Affinity を含む prompt caching の考え方と、insights と agent optimizer による継続的改善（プレビュー）が説明されました。毎週のライブ配信は YouTube、X、LinkedIn で視聴できます。
