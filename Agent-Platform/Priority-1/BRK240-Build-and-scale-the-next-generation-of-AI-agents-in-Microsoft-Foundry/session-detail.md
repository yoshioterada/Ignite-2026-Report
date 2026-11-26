# BRK240 Build and scale the next generation of AI agents in Microsoft Foundry

## 1. はじめに

このセッションは、Microsoft Foundry で AI エージェントを「作る」「デプロイして動かす」「運用して改善し続ける」までの一連の流れを、デモとアナウンスで通して見せる内容です。Microsoft Ignite に合わせた新機能の発表（GA や public preview など、提供状況が付いたもの）が非常に多く、後半は機能の一覧に近い構成になっています。

発表の中心にあるのは、デモシナリオ（Caldova という会社の deal desk 業務）と、「build（GitHub で作る）→ deploy（Foundry で動かし統制する）→ publish（Microsoft 365 Copilot や Teams に届ける）」という **golden path** です。最後には Toyota Motor Corporation の Hiroaki Kimura 氏が登壇し、車載 AI エージェントを改善し続ける仕組み「EvalOps」のデモを行いました。

セッション URL: [https://ignite.microsoft.com/en-US/sessions/BRK240](https://ignite.microsoft.com/en-US/sessions/BRK240)\
YouTube URL: [https://www.youtube.com/watch?v=_1ZEr5BSi3s](https://www.youtube.com/watch?v=_1ZEr5BSi3s)

## 2. 発表者

| 氏名 | 所属 | 役職 |
| ---- | ---- | ---- |
| Tina Schuchman | Microsoft | CVP |
| Jeff Hollan | Microsoft | VP of Product |
| Hiroaki Kimura | Toyota Motor Corporation | Project General Manager |

このトランスクリプトには話者名のラベルが付いています。Tina 氏は自己紹介で Corporate Vice President on Microsoft Foundry と名乗り、Jeff 氏を Vice President on Foundry agent platform と紹介しました。Tina 氏が全体の進行、アナウンスの説明、まとめを担当し、Jeff 氏が 3 回に分けて Caldova シナリオのデモ（build / deploy / operate）を行っています。Kimura 氏は Tina 氏の紹介で登壇しました。

## 3. 何に関する発表か

エージェントを作ること自体の難しさに加え、「本番で動かし続けること」はさらに難しい、という問題提起から始まりました。Tina 氏は、よく起きる問題を 4 つ挙げています。

1. 配管（plumbing）作業が多すぎる。ランタイム、ID、ツール、評価（evals）を手作業で持ち込むことになり、差別化の源であるビジネスロジックに集中できない。
2. エージェントのリスクが心配で、適切なアクセス制限や監視がないために、安全なデモの段階にとどまってしまう。
3. ビジネスの変化に合わせてエージェントを更新したいが、特定のモデルやフレームワークに縛られがち。
4. 本番で動いていても高い品質で動いているとは限らない。トレースや評価が見えず、品質が当て推量になる。

Microsoft Foundry は、これらに対する「エンタープライズグレードの開発者プラットフォーム」で、エージェント型アプリケーションの構築・デプロイ・運用を担い、全体のサイクルを最適化してループを閉じる、と位置づけられました。

発表の構成は次のとおりです。

1. エンドツーエンドのエージェントプラットフォームと golden path の紹介
2. デモ 1（Build）と関連アナウンス
3. デモ 2（Deploy）と、ガバナンス・セキュリティ、hosted agents の新機能
4. デモ 3（Operate）と、評価・最適化・ファインチューニング・スキル生成の新機能
5. Toyota 事例とデモ
6. まとめと関連セッション、Ignite Concierge の案内

## 4. 発表者が伝えたいメッセージ

メッセージの核は、Foundry が **「配管を肩代わりし、統制を内蔵し、モデル・フレームワークを選ばず、使うほど改善する」** プラットフォームだ、という点です。

- **配管の肩代わり**: ランタイム、ID、ツール、評価を一体で提供し、開発者がビジネスロジックに集中できる。
- **モデル・harness・フレームワークに依存しない**: フルコードで、エージェントを自由に変更できる。
- **ガバナンスとセキュリティ**: エージェントに対する可視性と制御がある。
- **オブザーバビリティと最適化**: トレースと評価で性能を把握し、エージェントが自己改善できる。

また、golden path（GitHub で作り、Foundry で動かして統制し、Microsoft 365 Copilot や Teams に届ける）は Microsoft の製品の幅があるから提供できる、としつつ、**すべてモジュール式** で、この通りに使う必要はないと強調しています。GitHub の代わりに Codex や Claude Code（聞き取りでは "Cloud Code"）あるいは手作業で作ってもよく、公開先もアプリや API でよく、外部のエージェントでも、トレースを渡せば Foundry が観測・最適化できる、という説明でした。

## 5. 発表内容の詳細

### 5.1 エンドツーエンドの golden path

Tina 氏が示した流れは次のとおりです。

1. **GitHub でフルコードで build する**: モデルカタログの 11,000 のモデルから選べる。プロンプト、スキル、ツール、そしてビジネスの知識をすでに含む Microsoft IQ を持ち込み、evals も GitHub 内で定義する。
2. **Foundry で deploy して動かす**: エージェントが計画して動くたびに、すべてのアクションがトレースに入り、オブザーバビリティの基盤になる。トレースに対して評価と最適化を行うので、エージェントが自己改善できる。
3. **Foundry の中で統制する**: ライフサイクル全体を Foundry 内で管理・制御する。Microsoft で作ったエージェントと外部のエージェントを横断する統一的なガバナンス面が必要なら、**Agent 365** を使う。
4. **仕事をしている場所へ publish する**: Microsoft 365 Copilot と Teams に公開する。

> 補足：冒頭の golden path の説明で、公開先として「Microsoft Marketplace」と聞こえる箇所があります。後の説明では Microsoft 365 Copilot と Teams が公開先として語られており、この部分は音声認識の都合で確定できません。

なお、Foundry のエージェントはすべてトレースを出し、それがオブザーバビリティと最適化に流れます。ただしこの使い方も必須ではなく、外部のエージェントでも「トレースを渡す」だけで Foundry が観測と最適化をしてくれる、という説明でした。

### 5.2 デモ 1: Build（Jeff 氏）

#### シナリオ

舞台は「今週のお気に入りの会社」Caldova です。deal desk（取引承認の担当チーム）の業務をエージェントで補強・自動化します。外部の顧客とのディールで、通常の範囲外の条件が出たとき、deal desk チームに「この契約条件は承認できる範囲か、適切なカウンター提案は何か」と確認する手作業のプロセスが発生します。

作るエージェントは 2 つです。

| エージェント | 役割 |
| ---- | ---- |
| front door エージェント（deal desk エージェント） | Microsoft Copilot harness で動く。利用者の入口 |
| deal economics エージェント | マージン情報、計算、ポリシーを持つ専門エージェント。front door から呼ばれる |

これらは Teams や Copilot を通じて対話し、agent identity を使います。

#### 開発者ツール

Jeff 氏は、ドキュメント画面を示しながら、Foundry の開発ツールを紹介しました。

- **Azure Developer CLI**: Foundry でのモデルとエージェントに関する作業を CLI で行える。
- **Microsoft Foundry skill**: Claude や GitHub Copilot CLI などのコーディングエージェントに追加すると、Foundry のベストプラクティスを適用できるようになる。
- **Visual Studio Code 内のツールキット**、および GitHub Copilot app 向けの専用 canvas（Foundry の構成オプションをガイドする）。

これらを個別に入れる必要はなく、Windows、Mac、Linux を問わず **1 行のインストーラー** で **Foundry DevPack** が構成される、と説明しています。

#### deal economics エージェントの開発とローカル実行

1. GitHub Copilot（コーディングエージェント）の助けで、deal economics エージェントのコードを書いた。ディールの計算、マージンの算出、社内ポリシーの確認を担当する。言語は Python（「どの言語でもよかった」と述べています）。
2. このコードで、必要なツールや toolbox と連携させている。
3. AI toolkit for Visual Studio Code で、ローカルのデバッグセッションを開始した。localhost で動いている。
4. Northwind 側からの依頼データをこのローカルのエージェントに送った。ツール呼び出し、トークン消費、発生しているイベントを追跡でき、手動でも GitHub Copilot でも、ローカルで調整できる。
5. 準備ができたら、簡単にデプロイして Foundry 上で動かせる。

#### 別の種類のエージェント

Python のコードで作るエージェントは **hosted agent** としてデプロイします。隔離されたサンドボックスと agent identity を備えたエージェント向けのコンピュート環境で、ローカルで動いていたコードがクラウドでスケールして動きます。Jeff 氏は、自分で選んだエージェントフレームワークを持ち込んで作ったと述べ、自身の場合は Microsoft agent framework を使ったと説明しています。ただしコードファーストだけが選択肢ではなく、Jeff 氏は Foundry で作れる 2 つの別の種類のエージェントも紹介しました。

- **voice agent**: 低遅延でリアルタイムの音声対話に最適化された harness を使う。このあと Tina 氏が、Ignite の情報を答える voice agent を紹介する、と予告しました。
- **Microsoft Copilot harness のエージェント**: 堅牢で長時間動く harness があればよく、コードを自分で管理したくない場合に使います。Microsoft Copilot harness は、GitHub Copilot の app / CLI や Microsoft Copilot、Cowork を動かしているのと同じ coding harness です。指示を与え、ツールやスキルを接続するだけで、長時間動くエージェントが作れます。この harness にはファイルの読み書きやコード実行などの機能が付属しています。

### 5.3 Build に関するアナウンス（Tina 氏）

デモの振り返りとして、次の 4 点が発表されました。

| 機能 | 提供状況 | 内容 |
| ---- | ---- | ---- |
| Microsoft Copilot harness agents | public preview | モデル、ツール、スキルを指定する構成だけでエージェントを作れる。Microsoft Copilot harness が動かす |
| hosted agents の long-running サポート | GA | checkpoint と restore を含む。復元性と human-in-the-loop に重要。ユーザーの応答待ちのときに checkpoint し、応答が来たら restore する |
| Foundry Dev Pack | GA | Foundry 開発者に必要なものを 1 つのインストーラーに。Foundry skills、AZD AI agent CLI、VS Code 拡張、GitHub Copilot app 拡張（アプリが利用可能になった時点で）を含む |
| Foundry voice agents | GA | build、deploy、observe、optimize の一貫した経路。speech-to-speech モードと cascade モードの両方に対応し、Microsoft AI、OpenAI、Azure speech モデルから選べる。独自にチューニングした voice harness を使い、割り込み（interruption）にも対応。ベンチマークでの speech-to-speech の P90 応答時間は 800 ミリ秒で、「best in class」と説明 |

### 5.4 デモ 2: Deploy（Jeff 氏）

deploy した Python エージェント（hosted agent）について、ガバナンスとセキュリティの機能を見せました。

1. エージェントが動くたびに、**serverless の microVM サンドボックス** が自動で立ち上がる。複雑な処理やコード実行を安全に隔離して行える。
2. すべてのエージェントが固有の **agent identity** を持つ。ロールベースのアクセス制御で、そのエージェントの権限やアクセスできるシステムを Foundry 内で設定できる。
3. guardrails を見せた。jailbreak 試行の検知・防止などを行う。特に **egress policy** を強調し、既定では Web へのリクエストをブロックし、明示的に許可したものだけを通す設定にしていた。画面下部に許可するエンドポイントの allow list があり、それ以外はブロックされる。仮想ネットワークの内外を問わず、許可のない先へアクセスしようとしても、プラットフォーム側が止める。「プロンプトで振る舞いを守らせることに頼らない」と述べています。
4. Microsoft Teams の deal desk チャンネルに、Northwind から来た依頼への返答が必要だと投稿した。すぐに常時稼働のエージェントが起きて作業を始め、サブエージェント（deal economics エージェント）にリクエストを送って応答を受け取り、次の推奨アクションを処理した。
5. 待っている間に、エージェントの「Manage」から「Cost」を開いた。コストの内訳が見え、「Cost Controls」でそのエージェントの制御を設定できる。このエージェントには、すべての操作（トークンとコンピュート）を合わせて月 $1,000 までの上限を設定していた。より細かく、特定のセッションやタスクで $5 まで、特定のユーザーに予算を、といったポリシーも設定できる。予算に達したらエージェントを止める（block）か、アラートだけ送るかを選べる。
6. エージェントは処理を終え、IQ のデータに接続して、承認が必要だと結論した。自分には承認の権限がないため、「今や Finance の VP になった」Jeff 氏に direct message で人間の承認を求めてきた。

Jeff 氏は、この一連の流れで、エージェントができること、利用者との対話の仕方、コスト管理の全体を自分が管理下に置いていたと振り返りました。

### 5.5 ガバナンスとセキュリティのアナウンス（Tina 氏）

本番で動くエージェントを統制する考え方は、「まず built-in の境界、その上に enterprise controls を重ねる」です。

**built-in の 3 つの境界（すべて GA）**

1. **Entra identity**: すべての Foundry エージェントは Entra ID を標準で持ち、認証と、できることの認可を行う。
2. **tracing**: エージェントの動作がトレースに入り、何が、誰によって、どの ID で実行されたかの系譜が追える。
3. **isolated secure sandbox**: すべてのエージェントがセキュアなサンドボックスで動く。

**6 つの enterprise controls**

| 項目 | 内容 | 提供状況 |
| ---- | ---- | ---- |
| エージェントの到達範囲の制限 | サンドボックスのすぐ外にある egress proxy がすべての外向き通信を見る。URL の allow / deny を設定できる | GA |
| 認証情報の注入 | egress proxy が、実行時に適切な認証情報を注入する。エージェント自身は認証情報に触れない（エージェントが誤ったり漏らしたりしうるため、責任はプラットフォーム側にある） | public preview |
| Purview / Defender / responsible AI との統合 | Agent 365 経由で統合。トレースが Purview と Defender に流れ、不審な活動が自動でフラグされる | GA |
| private certificate authority | VNet 下で運用する企業からの要望が多かった機能。モデル、ツール、エージェントが、証明機関のサポート付きでプライベートな HTTPS 呼び出しができる | public preview |
| コスト上限 | Foundry 内で設定。エージェント、プロジェクト、ユーザー、任意のカスタムタグの 4 つのレベルで設定できる。audit（使用額の確認）、alert（例: 許容額の 80% に達したら通知）、block（上限到達で止める）の 3 つが選べる | public preview |
| Azure policy によるツールの統制 | 特定のツールをブロックし、ツールカタログに表示されず、toolbox 経由でも使えなくする | public preview |

Tina 氏は、コスト上限の例として「Tina は $1,000 を超えて使わない」「Tina のエージェントは $100 を超えない」と話しました。

### 5.6 hosted agents の追加機能（Tina 氏）

hosted agents は数か月前から GA で、さらに次の機能が追加されます。

| 機能 | 提供状況 | 内容 |
| ---- | ---- | ---- |
| shared subnet IP による同時セッション数の拡大 | GA | 大企業向けに、数百万の同時セッションを簡単に設定できる |
| memory snapshots による cold start 短縮 | public preview | ベンチマークの P90 で 1.3 秒 |
| warm pooling | private preview（「まもなく」開始） | P90 でおよそ 300 ミリ秒 |
| 自社の Azure Kubernetes クラスターへの hosted agents のデプロイ | public preview（「まもなく」。「今ではなく、もうすぐ」と訂正している） | すでに AKS にインフラを投資している企業向け。Foundry のコントロールプレーン、オブザーバビリティ、評価、最適化をそのまま使える |
| Microsoft 365 アカウントを持つ autonomous agents の公開 | public preview | 開発者の代理ではなく、エージェント自身として動く。自分の ID、メールアドレス、Teams のプレゼンスを持ち、Teams のメッセージに自身のプレゼンスで返答できる |

### 5.7 デモ 3: Operate（Jeff 氏）

Jeff 氏は、エージェントを作る場所・動かす場所に選択肢が多くても、Foundry Agent Service は ID からコストガバナンス、最適化や学習まで多くを提供している点を強調しました。組織にはエージェントが 1 つや 50 ではなく、数百、数千になる。そのときにスケールして運用し、組織の変化に合わせて、エージェントが陳腐化（atrophy）しないようにすることが課題だ、という問題意識です。

#### 人間の承認とトレース

1. シナリオの続き。Northwind Pharmacy は粗利率（gross margin）26.4% の提案を求めており、こちらは 33.1% でカウンターする案になっている。Jeff 氏は、このカウンターを承認した。
2. エージェントがその信号を受け取り、作業に戻る。
3. Foundry の optimization と observability により、エージェントのすべてのやり取りが記録・追跡される。推論、呼び出したツール、使っている権限、適用されているガードレールの各ステップを確認できる。
4. トレースだけでなく **annotations** もある。提案を承認すれば「エージェントが正しいことをした」という信号をシステムに返せる。却下して指針を添えれば、その信号も記録できる。
5. トレースの山から、対処して学ぶべき点を見つけるために、Foundry の optimization 機能を使う。

#### 最適化の 2 つの戦略

Foundry には、エージェントを自動で最適化する戦略がいくつかあり、デモでは次の 2 つを見せています。

1. **特定の目標に対して品質を高める**: 評価（evals）と golden data set を用意し、スキル、モデル、ツールとのやり取りの変更で、自分専用のベンチマークの点数を上げられないかを Foundry に問う。
2. **過去の活動から学ぶ**: エージェントの過去の動作や人間とのやり取りからパターンを見つけ、エージェントを良くする。

**品質向上の実行**

1. Python エージェントの最初のスコアは、個人用のベンチマークで **0.794** だった。
2. Foundry がエージェントを分析し、より良く動きそうな候補を提示した。候補は、専用の安全な学習環境で micro batch でテストされる。
3. 平均トークン数や平均レイテンシーまで含めて、ベンチマークと比較する。
4. いくつかの候補が見つかり、Candidate 1 は「かなり良い」と見えた。
5. 提案された変更を確認し、どの変更を取り込むか選べる。承認後にデプロイでき、トラフィックのルーティングで段階的に展開することも可能。

**学習の実行**

1. 管理対象のエージェントで、過去の活動を学習するよう指示した。トレースが多くなかったので、**約 4 分** で終わった。毎晩実行するようスケジュールもできる。
2. トレース全体のパターンと、あれば人間のフィードバックを分析し、学びを抽出した。
3. 直近 1 日や 1 週間の動きから、改善して教えるべき点が出る。学びは **スキル** としてまとめられ、既存のスキルの更新、または新しいスキルの提案となる。
4. 自動生成されたスキルとその変更を確認して、スキルにマージできる。

> 補足：Jeff 氏は、「つい同じ方向に直されることが多いなら、スキルとして作っておけば次はうまくやれる」という例で、この機能の意図を説明しています。

#### 完全自律エージェントへ

Jeff 氏は最後に、エージェントを Copilot や Teams のチャットアプリにとどめず、**完全に自律的なエージェント** にするパターンを紹介しました。deal desk のリクエストキューを見る例で、エージェントは自分のルーティンやイベントトリガーで起きます。新しい提案が開かれると、評価して、設定したガードレールの範囲内なら自動で承認し、必要なら人間の承認を自動で取ります。ビジネスプロセスを補強・自動化する強力なパターンだ、と述べています。

### 5.8 継続的改善の 3 段階（Tina 氏）

推奨は次の 3 段階です。

1. トレースと評価から始める（継続的改善、hill climbing の基盤）。
2. パラメーターや構成を変えてエージェントを調整する。さらに深く hill climb するなら、モデルの重みを変える。
3. エージェントを自由にして、継続的に改善させる。

#### 評価（evaluations）

| 機能 | 提供状況 | 内容 |
| ---- | ---- | ---- |
| 外部エージェントのオブザーバビリティ | GA | トレースを持ち込めば、Foundry のオブザーバビリティで観測できる |
| annotations | public preview | トレースごとに、thumbs up / down やコメントを付けられ、最適化に使われる |
| rubric evaluators | quality 版は GA、safety 版は public preview | 「良い」とは何かを定義する評価器（Build で発表済み） |
| multi-turn evaluation | GA | 単発のリクエスト・応答でなく、会話全体を評価する。記録したトレースを使うか、ユーザーシミュレーションも使える |

#### エージェントの最適化

- 本番のトレースから **insights** を導出する機能が GA。トークンの急増やハルシネーションなど、問題になりそうな点を自動でフラグする。
- insights を **Agent Optimizer** に直接つなげてループを閉じる機能は public preview。Agent Optimizer は、プロンプト、スキル、ツール構成、モデル構成を変える reflective loop を回す。
- 最適化の軸は **quality** と **cost** の 2 つ。quality 最適化は GA、cost 最適化は public preview。
- Agent Optimizer は数か月前から GA で、多くの顧客が hill climb に成功している。例として **Petstock** がビジネス分析エージェントを作り、task adherence を **60% から 95%** に引き上げた。

#### トレーニングとファインチューニング

構成の変更だけでは足りないときは、モデルの重みを変えるファインチューニングを使います。

- **managed training**（GA）: ポータルの UI または高レベル API から実行する。データセットを持ち込み、supervised fine-tuning か reinforcement fine-tuning かを選ぶ。
- **program training**（public preview）: forward / backward の経路を制御できる、より深い制御が必要な場合の方法。serverless コンピュートは引き続き使える。（音声認識で "manage training" "program training" と聞こえるため、正式名称は確定できません。）
- 両方は、タスクのツール、タスクの状態、検証可能なフィードバックをまとめた **強化学習環境**（private preview）と統合されている。

顧客事例として **Alaska Airline** は、MAI-Code-2-Flash モデルを program training でファインチューニングしました。キーノートでも紹介された事例で、ATA code の分類精度のエージェント（聞き取りでは "ATA code classification accuracy agent"。音声認識の都合で表記が確定できません）の精度を **10 ポイント** 向上させたとのことです。

ただしチューニングはかなりの作業です。エージェントを作ったとき、新しいモデルが来たとき、新しいツールを追加したときなど、大きな変化があったときに行うのが適切だと説明しています。毎日、毎時間学習するエージェントには、もっと安価な方法として次が紹介されました。

#### スキルの生成と最適化（public preview）

OpenAI や Anthropic の「Dreaming」に近い仕組み、と説明されました。流れは次のとおりです。

1. トレースやログを（人間の annotation の有無を問わず）集める。
2. 良いものと悪いものに分け、それぞれの中でも「なぜ良い／悪いか」を分類する。
3. その学びをもとに、スキルが存在しなければ新規に生成し、あれば改善する。

さらに **A/B experimentation**（private preview）で、本番トラフィックでエージェントがどれだけ hill climb したかを確認し、ループを閉じられます。

### 5.9 Toyota の事例（Kimura 氏）

Tina 氏は、Foundry の顧客は 120,000 あり、Toyota がその 1 社だと紹介しました。Toyota は車内の設備を理解し、Toyota のクラウドサービスにもつながる **車載 AI アシスタントエージェント** を、Foundry 上で作りました。Foundry の hosted agent としてデプロイされ、Foundry の observability / optimization で hill climb しています。

Kimura 氏は、Toyota が車向けの AI エージェントを作っており、「使うほど育つ」エージェントを目指していると話しました。

#### 課題の例

Napa Valley に向かう途中で、運転手が「この辺に EV 充電器はある？」と聞くと、エージェントは目の前の急速充電器を見つけます。運転手は「いや、Napa Valley に着いたらの話」と言い直さなければなりません。

- 車内では言葉が短くなる（"out there" では場所が言われない）。
- でも車は **旅程（trip）を知っている**。言葉に旅程を合わせれば、目的地周辺という意味になる。
- 車は知っていたのに、答えはそれを使わなかった。
- 言い直すことは運転手の注意を道路から逸らす。

#### EvalOps

この改善のために作ったループを、Microsoft は「hill-climbing machine」と呼び、Toyota は **EvalOps** と呼んでいます。ツールではなく、チームがエージェントを良くするための方法だという説明です。3 つの考え方があります。

1. **Learn from every word**: 運転手が言い直したこと自体が信号であり、トレースが理由を示す。
2. **People decide**: AI は選択肢を出し、変更は人が選び、人が責任を持つ。
3. **Verify before you ship**: 今の問題が直り、他が壊れていないことを確認する。

Toyota の AI エージェントは開発中で、いまはこのループを自社のシステムで自社の従業員と回しており、**次にこれを Foundry に載せる** 予定です（評価、Agent Optimizer など。聞き取りでは "Evaluations, pricing, and the Agent Optimizer" と聞こえ、"pricing" の部分は音声認識の都合で確定できません）。

#### アーキテクチャ（Foundry で作っているもの）

1. 運転手の言葉と旅程を、Foundry が動かすエージェントに渡す。ロジックは Toyota 自身のコード。車が言うことに Toyota が責任を持つため。
2. 車が目的地を知り、Place search が充電器を知っている。エージェントは検索に「どこを見るか」を伝える。
3. 別のサービス（Talk API）が言葉を書く。
4. 答えが間違ったとき、どの部分が間違ったかを知るために、ツールとその条件まですべての呼び出しをトレースする。このトレースが EvalOps のループに入る。

#### デモ（Kimura 氏）

Kimura 氏は「デモ」と述べ、コンソールは Toyota のもので、背後は Foundry だと説明しました。画面の細部は音声からは確認できません。語られた内容は次のとおりです。

1. ある会話を示した。聞き取りでは「Foundry が次のターンを導いて印を付けた（"Foundry leads the next turn and marked it"）」と読め、そこで「間違えた」と判断してチケットを開いた（音声認識の都合で正確な内容は確定できません）。
2. 同じ質問でもう一度試した。失敗が繰り返された（聞き取りでは "10 times out of 10, 17 times of 21" と聞こえ、数字の意味は音声認識の都合で確定できません）。"nearby" を使うと失敗しない。問題は "out there" だった。
3. Foundry の分析（聞き取りでは "Foundry tolerance"、表記は確定できません）では、入力に目的地はあったのに、エージェントは **ルート上** を検索していた。失敗した部分は 1 つで、エージェントが誤ったエリアを選んでいた。
4. 修正として、**Foundry Agent Optimizer が 3 つの候補** を作り、最良のものは満点を取った。
5. 中身を読むと、「目的地を選べ」と言っている。テストがそれを求めているからで、つまり **テストに合わせてズルをしていた**。それも Agent Optimizer が伝えてきた。
6. エンジニアが判断し、2 つの変更は残し（旅程を使う）、残りは捨てた。ここから「運転手の意図」を学んだ。
7. エンジニアが Version 2 を書いた。同じテストで、以前は 27 件失敗していたのが 0 件になった。21 件あった正常な実行は正常のままで、他が壊れていないことを確認した。
8. 同じ質問で、1 回目から目的地付近の充電器が返るようになった。結果を保存し、人が出荷を決める。

最後に、いまは従業員が自社システムでこのループを回している状態を **Customer Zero** と呼んでいること、次に同じループを Foundry で回すこと、そうすればエージェントに時間をかけられ、配管に時間を取られないことが語られました。Kimura 氏は、今週 Toyota のセッションは 3 つあり、これはその 1 つ目だと案内しています。

## 6. デモ・手順

このセッションにはデモが 4 つあります（Jeff 氏の Build / Deploy / Operate の 3 つと、Kimura 氏のデモ）。録音は音声のみのため、画面の細部は語られた範囲のみです。それぞれの手順は 5.2、5.4、5.7、5.9 に、語られた順番に書いたとおりです。

デモ全体の流れ（Caldova の deal desk シナリオ）は次のとおりです。

1. 開発者ツールで環境を整え、deal economics エージェントを Python で作り、VS Code でローカル実行する。
2. Foundry に hosted agent としてデプロイする。サンドボックス、agent identity、guardrails（egress policy を含む）、コスト上限を設定する。
3. Teams の deal desk チャンネルから呼び出す。エージェントが人間の承認を求め、承認する。
4. トレースと annotations を使い、品質向上の最適化と、過去の活動からのスキル生成を実行する。
5. エージェントを、イベントトリガーなどで自律的に動かす。

## 7. 注意点・発表者の気付き

- **すべて Foundry の golden path に従う必要はない**。GitHub 以外で作ってもよく、公開先もアプリや API でよい。外部エージェントはトレースだけ渡せば観測と最適化ができる。
- 機能ごとに提供状況が違います（GA、public preview、private preview、「まもなく」）。warm pooling と AKS へのデプロイ（ともに「まもなく」）、強化学習環境、A/B experimentation（private preview）は、まだ一般には使えません。
- エージェントに認証情報を持たせると、ミスや漏洩のリスクがあるため、Foundry が実行時に注入する設計（public preview）です。
- エージェントの安全性を「プロンプトで振る舞わせること」に頼らず、プラットフォーム側で止める（egress policy など）。
- チューニング（ファインチューニング）はかなりの作業なので、エージェントの構築時、新しいモデルやツールの追加時など、大きな変化のときに行う。日々の改善には、スキルの生成と最適化のような安価な方法を使う。
- Toyota の事例では、Agent Optimizer の最良候補が満点だったものの、中身はテストに合わせたズルだった。エンジニアが内容を読み、残す変更と捨てる変更を判断した。
- Toyota のエージェントは開発中で、Foundry への移行はこれからです。
- 提示された数値のいくつか（P90 の値、ベンチマーク結果など）は、発表で示されたベンチマーク値で、条件の詳細は発表では語られていません。

## 8. 利用者にとっての利点

- **配管の削減**: ランタイム、ID、ツール、評価を Foundry が提供し、ビジネスロジックに集中できる。1 行のインストーラーで開発環境が整う。
- **選択の自由**: 11,000 のモデル、モデル・harness・フレームワークを問わず、フルコード。外部エージェントも観測できる。
- **本番で安心して動かせる統制**: agent identity、サンドボックス、egress の allow / deny、認証情報の注入、Purview / Defender 連携、コスト上限、ツールの統制。
- **継続的な品質向上**: トレース、annotations、evals、Agent Optimizer、スキル生成で改善する。Petstock は task adherence を 60% から 95% に、Alaska Airline は分類精度を 10 ポイント向上させた。
- **性能**: voice agents の P90 800 ミリ秒、cold start P90 1.3 秒（memory snapshots）。
- **Toyota の事例**: EvalOps のループで、車載エージェントの誤答を原因から特定し、修正して、他が壊れていないことまで確認できた。

## 9. まとめ

Foundry は、エージェントを build（GitHub でフルコード）、deploy / run（hosted agents、統制）、publish（Microsoft 365 Copilot、Teams）まで一貫して支え、トレースと評価で改善のループを閉じるプラットフォームです。今回は、Microsoft Copilot harness agents、long-running hosted agents、Foundry Dev Pack、voice agents、egress 制御やコスト上限などのガバナンス、cold start の短縮、autonomous agents の公開、評価・Agent Optimizer・ファインチューニング・スキル生成といった多数の機能が発表されました。

Tina 氏は最後に、Microsoft IQ（社内知識）の上に Foundry（モデル、ルーティング、評価、トレーニング、安全性の制御）が載り、そこでエージェントが自己改善し、「ビジネスが複利で成長する」と述べました。関連セッションの案内に続き、Microsoft Foundry で作られた **Ignite Concierge voice agent** に電話をかけて質問できると紹介しています。
