# BRK268 Build as a team with GitHub Copilot tailored to your workflow

## 1. はじめに

このセッションは、GitHub Copilot をチームで使うときに「個人ごとの設定」を「チーム全体で共有・管理できる仕組み」に育てる方法を扱います。登壇者は VS Code チームの 2 人で、主題は **skills（スキル）を作り、評価し、組織全体に配布して、利用状況を測る**ところまでの一連の流れです。

話は大きく次の順に進みます。

1. skills とは何か、agents.md との違い
2. agent に skill を作らせる方法（デモあり）
3. evals による skill の品質確認
4. plugin と plugin marketplace による配布
5. managed settings、組織・チーム・リポジトリ単位でのロールアウトと強制
6. クラウドセッションと automations、connectors、canvases
7. OpenTelemetry・usage metrics・dreaming による計測と改善

デモが多いセッションです。ただし録音は音声のみのため、画面の細部は、登壇者が口頭で説明した範囲に限って書いています。

セッション URL: [https://ignite.microsoft.com/en-US/sessions/BRK268](https://ignite.microsoft.com/en-US/sessions/BRK268)\
YouTube URL: [https://www.youtube.com/watch?v=9KvG88rZTMg](https://www.youtube.com/watch?v=9KvG88rZTMg)

## 2. 発表者

| 氏名 | 所属 | 役職 |
| --- | --- | --- |
| Harald Kirschner | Microsoft | Principal PM |
| Ross Wollman | Microsoft | Software Engineer (VS Code) |

> 補足：冒頭の自己紹介では、「Harold」（メタデータの表記は Harald）と「Ross」が、VS Code チームで GitHub Copilot の governance（統制）と customizations（カスタマイズ）に取り組んでいると名乗っています。トランスクリプトに話者ラベルはありません。「Ross が自分のデモを見せた」「自分の skill が 31 個ある」といった自己言及で明確な箇所だけ、Ross 氏の発言（デモ）として書いています。それ以外は「登壇者」としています。

## 3. 何に関する発表か

冒頭、登壇者は会場に挙手を求めます。「agent で skills を使っている人」「自分で skill を作ったことがある人」「組織の他のメンバーにも skills を広げたい人」の順で手を下ろしてもらい、ほとんどの人が使っていることを確認したうえで、「skills とは何か、どう作るか、どう大規模に展開するか」が今日のテーマだと説明しています。

背景にあるのは、**個人が作った skill がチームの成果物（エンジニアリングの対象）に変わる**という問題意識です。共有すると、その品質をどう保証するか、次のモデルや SDK の更新後も良いままかを問う立場になる、という説明です。そのための道具として、発表では次のものが紹介されます。

- **skills / agents.md / hooks / MCP / canvases / settings**：agent の振る舞いを変える customization の部品
- **evals**：skill やプラグインをテストする仕組み
- **plugin と plugin marketplace**：skill などをまとめて配布する単位と、その置き場
- **managed settings**：管理者が配布・強制を設定する仕組み
- **OpenTelemetry、usage metrics API、dreaming**：利用状況の計測と、改善の自動化

## 4. 発表者が伝えたいメッセージ

発表全体を通した主張は次のとおりです。

- **skill は手書きするものではなく、agent と一緒に作る。** 繰り返し行った作業の履歴や、良い例・悪い例を agent に渡して skill に落とし込む、という考えです。ただし最初から正しい出力を得られるとは限らないため、複数案を出させて比較するよう勧めています。
- **共有する skill はエンジニアリング成果物として扱う。** そのため evals で品質を測り、marketplace でバージョン管理し、CI/CD に載せます。
- **配布と統制は 3 段階で考える。** 「利用可能にする（marketplace）」「既定で有効にする（リポジトリ・チーム）」「強制する（管理者レベル）」を使い分けます。
- **中央集権と、現場からの実験の両方を残す。** 中央の marketplace を持ちつつ、チームが独自に作れる余地を残すべきだ、という立場です。アイデアの多くは、開発者が日々の問題を解くなかから出てくるという理由です。
- **小さく始める。** 1〜2 個の、チームが最も困っている問題から skill を作ります。

## 5. 発表内容の詳細

### 5.1 customization の全体像

登壇者は、agent の文脈を構成する customization を次のように整理しています。

| 要素 | 役割（発表での説明） |
| --- | --- |
| agents.md | リポジトリの説明。ベストプラクティスなどを書き、agent を学習データの癖から「自分たちのやり方」へ寄せる |
| skills | 出力の見た目や、agent の質問の仕方、レビューの仕方など、「どう仕事をするか」を定義する（例: PR description、review） |
| hooks | より明示的な制御。強力だが説明が長いため、このセッションでは詳しく扱わない |
| MCP、リポジトリ、ユーザーフォルダー | 展開したい対象としても挙げられている |
| Canvases | Copilot CLI の拡張の仕組み。リッチな UI を customization に持ち込める（dogfooding 用の例がある） |
| settings（JSON など） | 上のどれにも収まらない、体験をさらに調整する設定 |

また、これらのプリミティブは VS Code だけでなく Copilot CLI など、Copilot のあらゆる画面（surface）で揃えつつあり、エコシステム全体で標準化も進んでいると説明しています。以前は VS Code 向けに作ったものが Copilot CLI や Claude で同じように動かないことがありました。Microsoft の plugin registry も、この発表で話す plugin の仕組みを使っているとのことです。

### 5.2 agents.md と skills の違い

「リポジトリの markdown（agents.md）と skill フォルダーの markdown は何が違うのか」という混乱が多いと言い、次のように切り分けています。

- **agents.md**：このリポジトリの開発者として「どう作業するか」を説明するもの。ビルドとコンパイル、ベストプラクティス、ファイルの置き場所など、agent が迷わないための地図の役割をする。毎回の実行で使われるため、**できるだけ最小限にする**。うまく書けばトークンを節約できる。
- **skills**：agent に最初に見えるのは description だけで、必要になったときにロードされる（on demand）。コンパイルやテストの方法のような内容を skill に移し、template.md などの追加ファイルに詳細を分けて、最初の読み込みを小さく保てる。agent が誤って skill を読み込んでも、必要な情報が全部流れ込まないようにするためです。

skill の呼び出し方は 2 通りです。

1. description が適切で、そのワークフローが必要な場面で自動的に起動される。
2. スラッシュコマンドとして明示的に呼ぶ（例: `/` の後に PR description の skill を指定する）。チームには「このコマンドを実行してください」と教える必要があるが、自動起動と明示呼び出しの両方を組み合わせられる。

### 5.3 skill の遅延ロード（Ross のデモ）

Ross 氏は、事前に実行したセッションを画面に出して、skill が必要なときだけ読み込まれる様子を示します。

1. 1 つ目のセッションでは、スラッシュコマンドで skill を呼び出している。コマンド名は kitchen sink、sync、agents QA を組み合わせたような名前で、音声認識の都合で正確な表記が確定できません。「ここから始めて」とだけ指示した。
2. 2 つ目の exploration セッションでは、やりたいことを自然言語で説明しただけ。画面上の可視化で、agent が使える skill の一覧と、実際にロードされた 3 つが確認できる、と説明している。
3. 自分は skill を 31 個持っていて、同僚にはもっと多い人もいる。全部をロードするとコンテキストウィンドウが膨らむため、遅延ロードは重要である。

### 5.4 冒頭のデモ: dogfooding skill と Blobby Sandbox

セッション冒頭で、Harald 氏は自分が普段デモする skill が dogfooding skill だと話します。VS Code には組み込みブラウザーがあり、アプリを「ユーザーのように使う」よう agent に指示します。製品のピッチ（対象ユーザーなどの概要）を渡すと、agent が画面を操作します。その後、Ross 氏が自分の例を見せたことで、自分のデモが霞んだ、という流れです。

Ross 氏のデモは次のとおりです。

1. VS Code から agents window に切り替える。会場に人が入ってきた頃から始めておいた複数のセッションが見える。
2. exploration セッションでは、agent が VS Code の中を動き回り、スクリーンショットに注釈を付けている。これは実際の製品内の体験である。
3. これを支えるのが、本人が **Blobby Sandbox** と呼ぶ skill とローカルのツール群。micro VM を起動し、製品の最新ビルドをダウンロードして、そのサンドボックス内で computer use（画面操作）を行う。
4. 問題が見つかった場合は、QA skill と canvas を使って、agent が結果を提示する。UI のバグなどを自分が確認し、読んで、issue を起票する。
5. これは自分が毎朝手作業でやっていた仕事であり、skill を作って agent に任せる形にした、と説明している。

### 5.5 PR description skill の作り方（デモ）

Ross 氏は、PR description の例で skill の作り方を示します。

**課題**：agent に PR description を書かせると、検証コマンドの長い文章が入り、視覚的なノイズになる。PR の内容、変更前後を素早く知りたい本人やチームにとって、検証コマンドは役に立たない。そこで自分の好みに合わせた PR description skill を作った。

**効果**：skill 作成後の PR には、注釈付きのビデオデモが自動で付く。これは先ほどの micro VM でアプリを動かし、操作を録画し、agent が注釈を付ける別の skill を使っている。変更前後の簡単な比較と、試すための短い再現手順が添えられ、検証コマンドの長文はない。

**作成デモ**（ブラウザーで PR を見せた後に VS Code で実演）：

1. VS Code で新しいチャットセッションを開く。
2. 「PR description の skill を作って。この issue の description のような形式が好き」という趣旨の指示を入力し、対象の issue の説明を貼る（入力の細かい経緯は音声からは分かりません）。入力にはタイプミスがあったが、直さず送信した（agent の方が文法や綴りが正確だから、と冗談を添えている）。
3. agent は自律的に動き始める。すでに作成済みの skill を見つけて調整を始める可能性もあるが、もし無ければ新しく作る、と説明している。
4. 実行はバックグラウンドで続き、このセッション中に完了結果は示されていません。

作り方の要点は次のとおりです。

- 同じ作業を何度か行い、その往復の履歴を agent に見せて、何がうまくいき、何がうまくいかなかったかを skill に記録させる。または、良い例と悪い例を例として渡す。
- agent は自分のセッションを参照できる。Ross 氏は Copilot を数年使っており、全セッションが残っている。skill の作成時に「自分が agent を好みの方向へ修正した箇所を見て、学びを skill に取り入れて」と頼める。
- skill は手書きせず agent で作る。変種（バリエーション）を作らせて試す、作る前に「出力を 3 種類出して」と頼む、といった使い方もできる。ただし、**最初から正しく出力されると信頼しない**。agent の力で探索し、発散させてから絞り込む（発話では、ここで「PM 用語で言うと」と続けています）という進め方です。

また、PR description の問題として、AI が書いたように見える巨大な markdown に悩んでいる人は多い、と話しています。人間が考えたことを AI に書かせても、agent は自分がやったことやレビューコメントをすべて PR に流し込み、description が際限なく伸びていく。複数の agent が動くリポジトリで PR description の skill がないと、特に起こりやすい、という指摘です。

### 5.6 evals で skill の品質を測る

skill を共有すると、その利用者に対して「プロダクトマネージャー」のような立場になる、と登壇者は説明します。そこで次のような疑問が出ます。

- それが良いとどうやって分かるか
- 次のモデルが出たときも良いか
- SDK を更新したとき（例として Copilot SDK 2.0 という言い方があります）に、システムプロンプトが少し変わっても大丈夫か

答えとして、skill、配布するプラグイン、MCP、AI アプリを **実際のエンジニアリングシステムとして、テストで評価する**ことを挙げ、そのために evals を使います。skill の evals で考えることは次のとおりです。

1. skill が何をすべきか。
2. skill が呼ばれるべきでない場面（「アンチケース」）。
3. skill が呼ばれたときに期待する出力。
4. 同じモデル、同じタスクで、skill あり・なしを比較する。

VS Code の中にも、plugin や canvas を通じて、この evals の体験が用意されています。GitHub Copilot app、CLI でも使え（発話では「GitHub emails」と書き起こされており、文脈上は evals の機能を指しますが、表記は確定できません）、ローカルのインナーループで最適化を繰り返し、marketplace がある場合は後で CI/CD にも載せられます。同僚の John 氏が会場の後ろにおり、ブースで詳しいデモを見せられる、と案内しています。

**eval の PR デモ**（Ross 氏）：John 氏が当日取り組んだ PR を画面に出す。変更ファイルの一つが skill.md。PR の会話タブでは、eval のパイプラインが PR にコメントを自動投稿し、変更の内容を要約している。拡大して見せたコメントでは、**この skill の変更でトークン使用量が 6% 減り、eval の評価上は品質に影響がなかった**。この変更を採用すべきか、サイクルを無駄にしているだけかを判断できる、と説明しています。エディター内にも、これを対話的に回す体験がある、とのことです。

注意点として、次のことが挙げられています。

- agent に skill を改善させると、会話の中の細かい指摘を拾って「こういう場合はこうしない」というルールを足し続けがちである。単発のルール追加ではなく、プロンプトの作り方（prompt engineering）と evals を使い、skill 全体を改善する必要がある。
- 新しいモデルが出るたびに、以前は多くの誘導が必要だったが、今は誘導を最小限にしてもモデルの「センス」が良い。自分は、モデルと自分の好みが違うとき、またはチーム固有のやり方で一般的なモデルでは分からないときに skill を作る。

### 5.7 plugin と plugin marketplace

「plugin marketplace や skill registry を展開したことがある人」を会場に尋ね、数人が手を挙げています。登壇者の考えは次のとおりです。

- **skill registry は独立したものにならないはず。** skill の情報（名前、description）は agent 向けのもので、必要なのはそれを「パッケージ化する」ことだ。
- フォルダーが一つの形だが、さらに plugin にする。skill 標準は最小限で、フォルダーの中の markdown や他のファイルと、一部の front matter で構成される。
- **agent plugin spec は 1.0 で、1.1 がまもなく出る。** 登壇者の一人は、自分はこの spec の core contributor だと述べています。名前、description、バージョンといった、marketplace で見つけてインストールするために必要な情報が加わる。アイコンや display name も予定されている。
- plugin には skill に加えて MCP も入る。初期の spec は MCP と skills だけで、skill は skills フォルダー、MCP はルートの MCP.json で定義する。MCP registry や skills registry を持っているなら、plugin に包んで marketplace に載せれば、同じ仕組みで配布できる。
- marketplace は発見・インストール・更新の体験を提供する。各クライアントの customization 画面は、plugin marketplace を土台にしている。
- plugin は複数の harness で動く。Microsoft の plugin registry、Codec（発話の表記）、Cursor、Cairo（同）などが対応している。「1 つの harness を除いて全員が spec に関わっている」とのことです（どれかは明言されていません）。
- 名前空間 `com.github.copilot` に、まだ標準になっていないものが置かれる。agent plugins 1.1 で hooks が入り、標準入り後は上の階層へ移せる。extensions などもこの名前空間にある。harness を作る人が自分の名前空間を持つ余地もある。

**オーディエンスへの課題**として、`github.com/github/plugin-marketplace-starter`（発話は "github slash github slash plugin marketplace starter"）を fork し、PR description の plugin を自分のチーム向けに改善・カスタマイズしてみることが挙げられました。これは plugin marketplace を作るためのテンプレートです。

**marketplace テンプレートの説明**（画面を見せながら）：

1. PR description の plugin は、classic な skill 仕様の上に、名前、description、バージョン、changelog、README などを持つ。
2. テンプレートには `marketplace.json` があり、システム内のすべての plugin を記述する。marketplace の実体はリポジトリで、現状は人々をそのリポジトリへ誘導する。
3. リポジトリ内でバージョン管理できる。例えば eval plugin は特定の ref（発話は "graph"、表記が確定できません）を指しており、ロールバックや段階的なバージョンアップ、「eval を実行せずに新版を出さない」といった運用ができる。レビューの仕組みも持てる。

### 5.8 marketplace の運用と、チーム内での広げ方

marketplace を展開する方法として、次の 2 つが紹介されています（2 つ目は Microsoft 社内の例です）。

1. **GitHub Copilot の centralized managed settings**：全員が使う marketplace を設定できる。そこ以外からのインストールを禁止することもでき、セキュリティやチーム共有の観点で有用。任意の MCP や skill の追加を防ぎつつ、企業レベルで全員に PR description などを入れて統一できる。
2. **Microsoft 社内の例**。Microsoft には「Agency」という中央の plugin marketplace があり、agency チームが Microsoft 全体に意味のある plugin を集めている。VS Code チームにも、オープンソースの **VS Code TeamKit** リポジトリがあり、多数の canvases が入っていて、発表時点の最新 PR は 20 時間前だった。

Ross 氏は、VS Code チームが金曜午後に 1 時間の self-hosting dogfooding セッションを持っていることを話します。自分のワークフロー（冒頭の micro VM の例など）を共有するうちに、Harald 氏から「それをどう共有するのか」と指摘され、個人用の marketplace（オープンソース）を作ったとのことです。skill、plugin、canvas を入れており、VS Code や Copilot CLI でワンクリックでインストールできる。ただし構造は「kitchen sink」（何でも入れた形）で、全部入るか全部入らないかになる。**自分のリポジトリの構成は真似せず、公式のスターターキットを使ってほしい**、と注意しています。

運用の考え方として、次のように説明しています。

- 実験を可能にすることが重要である。skill やアイデアの多くは、開発者が日々の問題を解くなかから出てくる。中央のチームだけで AISDLC（発話の表記）の全体像を考えるのは難しい。
- 「中央の marketplace だけで他は作れない」形にするか、「中央に加えてチームが自分たちの marketplace を作れる」形にするかを選べる。
- CI/CD では markdown の lint だけでなく eval も実行する。将来的には eval のスコアを plugin の description に載せ、人々が最適化に取り組んだことを見える化できる、と説明しています。

### 5.9 ロールアウトの 3 段階と managed settings

**① managed settings（enterprise レベル）**：GitHub Copilot の管理設定で、AI controls が特定の managed settings リポジトリを指す（デモでは「super secret world termination test enterprise」という冗談めいた名前の enterprise）。そのリポジトリは組織内の `.github` プライベートリポジトリで、中の `managed settings.json`（発話のとおり）で既定の体験を定義する。

- ユーザーを閉じ込めるためのものではない、と断っている。例として、全員に Fusion（発話の表記で、音声認識の都合で確定できません）というモデルを使わせたい（切り替えを忘れる人が多く、30% 割引のお得な条件もあるため）場合に、全セッションで最初に選ばれる既定のモデルを変更できる、と説明している。
- ほかにできること：ローカルのサンドボックスで高いセキュリティに入れる、製品内の「管理者に連絡」の support URL を自社のページに変える、marketplace を全ユーザーに注入する。
- 管理者設定から入れた marketplace は、ユーザーが削除できない。そこから plugin を既定で有効化、または強制的に有効化できる。
- 強制する plugin は、**高い確信を持てるもの、またはセキュリティ・サポート関連**にすべきで、そのために evals が重要になる。配布したものがチームの体験を悪くしないよう、改善（より良く、安く、速く）を確認する必要がある。

**② 組織内のチーム単位（新機能）**：GitHub の changelog が前日に出たばかり、と紹介されています。組織内の team の画面で marketplace を追加できる。「新しい marketplace を作る」ボタンがあり、先に見せたテンプレートが使われ、ベストプラクティスが最初から入る。

デモ（画面を見せながらの説明）：

1. チームの設定で marketplace を追加する。テンプレートから作る例と、すでにある自分の marketplace を追加する例が挙がり、後者は未検証で eval が入っていない、と断っている。
2. enterprise レベルで追加した TeamKit が、すでにこのチームにも見えている。チームのメンテナーは、enterprise の管理された plugin から一部を選べる。
3. 「Browse plugins」で、追加した marketplace の plugin が並ぶ。レビュー関連の skill が多数ある中で、Awesome Copilot、VS Code TeamKit、そして支持の多い「review areas」を取り上げている。
4. review areas は、セキュリティやコード品質などの persona を持つ複数の sub-agent に分けてレビューし、結果を集約する多頭型のアプローチで、コード品質の向上に役立つと説明している。
5. これをチームで有効にすると、全員に有効になる。ただし個々のユーザーは無効化できる（例として、自分のレビュー skill の方が好みで、チームのものが先に呼ばれて邪魔なので無効にする、という使い方を挙げている）。

**③ リポジトリレベル**：この部分はスライドで説明しています（以下の表は、リポジトリ、チーム、管理者レベルをまとめた整理として語られた内容です）。

| 段階 | 内容 |
| --- | --- |
| 利用可能（available） | marketplace に入っているものは、全員が追加で使える。例えば「経費精算はどうするの」と聞かれたら、plugin から経費精算のものを入れてもらう |
| 既定で有効（default enabled） | リポジトリやチームのレベルで設定する。ユーザーは「自分のものがあるから不要」と外せる |
| 強制（enforced） | 管理者レベルで既定有効にしたもの。使用が必須になる。多くは observability（hooks でメトリクスを自社システムへ送る）、または security（コーディングや agent の使い方のガイダンス）に関するもの |

さらに、managed settings は enterprise teams を作って組織の一部ごとに展開でき、plugin を開発しているチームには marketplace をロックダウンしない、といった柔軟な設定（パイロットを含む）ができます。

**monorepo**：大規模な monorepo で作業している人は会場に少数でした。monorepo の場合は、marketplace を同じリポジトリに置いて skill や plugin を管理できる。リポジトリの Copilot 設定で marketplace を指すと、そのリポジトリを開いたときに marketplace が表示され、自動で有効化もできる。今後、まだ確信のないものを強調する「推奨」も検討している、とのことです。どの機能でも、利用者には提供元が分かり、必要なら無効にできる状態にしたい、という考えです。

チームが複数のリポジトリにまたがる場合は、組織内のチームへ展開し、各チームが自分たちで選べるようにします。skill 作りの得意な「AI champions」の存在が展開の助けになる、とも話しています。enterprise teams の単位では、ベストプラクティスを集めて強制することもできます。

**strict plugin only customizations（今週の changelog）**：plugin 以外から入る primitive をロックダウンできる。管理者が定義した marketplace の plugin 経由で MCP を配布し、それ以外の MCP を利用者のマシンで動かさない、という使い方ができる。ロックされたときは UI で「管理者に連絡」と表示し、support URL を設定しておけば問い合わせが直接来ない。

**device policy**：managed settings にはデバイスポリシーでの配布もある。ただし、話を聞いた多くの顧客は、デバイスポリシーの展開に時間がかかるため、GitHub のプライベートリポジトリ方式を好む、と話しています。cloud environments などのリモート環境では、managed files で環境ごとに skill、モデル、承認方式を変えるといった、統制の「レイヤー化」ができる。なお、managed settings については前日に別のセッションがあった、とも案内しています。

### 5.10 クラウドセッションと automations

- すべては GitHub Copilot のあらゆる surface で動く。クラウドセッション（cloud sandboxes）を始めると、自分の customization がそのまま付いてくる。connectors なども環境をまたいで使えるようにしていく予定。
- VS Code チームの最近の好みは、Microsoft dev boxes を複数立ち上げて agent に計算資源を使い切らせ、VS Code を中央のオーケストレーターにする方法、また GitHub に結果が戻るので携帯からも続ける方法。これらは自分として動く個人のクラウドで、ローカルでは「agent が動いていてノートパソコンを閉じられない」という悩みがあると話している。
- リポジトリレベルの **automations** も新しく使える。

**automations のデモ**（Ross 氏）：

1. オープンソースのリポジトリの pull requests を開き、「Label PR demo」という automation を見せる。同僚がビデオを PR に載せ始めたが、実行に時間がかかるため、チーム全体向けに自動化した。
2. PR が開かれるたびに、agent が PR をダウンロードしてビルドし、操作して録画する。レビュー時（例として、同僚の Megan の PR）には、コメントを見るだけで内容が分かる。コミットが追加されるたびに録画が再実行され、agent が dogfood する。PR の主張どおりに動かない境界ケースが見つかれば、bot が報告する。
3. もう一つは、毎晩動く self-host UA（発話の表記）の automation。冒頭にラップトップで動かしていたものが、いまはクラウドの automations で動いている。毎晩新しい insiders ビルドをダウンロードし、操作して、問題があれば issue を起票する。
4. 朝のトリアージで文章の山に埋もれないよう、issue の形式を skill で調整した。画面写真、再現手順、期待される結果と実際の結果、必要な追加画像が並ぶ。
5. 有効にした直後、チームのチャットで「これは良い」「これはだめ」「ここが間違っている」といった反応が来て、改善を重ねた。そこから eval を作り、回帰を防げる。issue のテンプレートやデモ録画のやり方は、リポジトリで共有する skill にできる。

### 5.11 connectors と canvases

**connectors**：VS Code の customizations タブ（登壇者の一人が担当）には、marketplace から入れたものと並んで Copilot connectors が表示される。connectors は基本的に MCP で、MCP という 3 文字の言葉から「connectors」に移行していく方針です。コンテキストを持ち込み、ツールを持つ。「skills over MCP」という MCP spec の最新の進化も入ったばかりで、MCP がツールとリソースだけでなく skill も持てるようになった、と話しています（発表資料には含めていないとのこと）。

- 例えば Figma は一度接続すれば、二度とサインインしなくてよい。認証は GitHub 側で管理され、今いる環境でそのまま使える。Slack、WorkerQ（発話の表記）なども接続済みで、今週だけで 8 つの connector を追加した。
- クラウドサンドボックスも同じ connectors にアクセスできる。ローカルで MCP のログインを使って行っていた作業を、自分のマシンを離れて定期的に自動化できる。

**canvases**：VS Code と GitHub Copilot app で動く、最も強力な追加機能だと説明されています。Ross 氏のデモは次のとおりです。

1. canvas は、Web ページのようなものだが、agent と双方向に通信する。agent が canvas 内のツールを呼んで UI を更新でき、ユーザーが好きな UI を作る（または agent に作らせる）こともできる。
2. 先ほどの QA agent が開いた canvas で、操作して「file issue」を選び、agent に送る。実際に issue を起票すると人に迷惑なので、キャンセルした。送信されたメッセージ（"agents QA, one decision"）に対して、agent が応答を始める。
3. この設定では、agent は issue がまだ問題かを再確認し、VS Code リポジトリの他の open issue を調べて重複起票を避け、関連付ける。
4. チームの全員が複数の canvases を持っている。デモで見せたもの：agents QA、Blobby sandbox（QA を支える技術の一つで、canvas の中に micro VM が共有されて表示される）、skills funnel（セッション中に skill が呼ばれたか、ロードされたかを知るための、自作のデバッグビュー）。
5. 作り方：VS Code で「X や Y が欲しい」と思ったら、「create canvas」と言って内容を説明すれば、agent が作ってインストールする。チームと共有するときは、marketplace 経由で引き上げる。Ross 氏は、canvas はプロンプトで作ったもので、自分でコードを書いた量は少ない、と答えている。
6. VS Code で作った canvas は、GitHub Copilot app を起動しても同じように見える。一度作れば、どこから作業しても使える。既定ではユーザーフォルダーに作られるが、リポジトリの `.github` フォルダーにも置け、evals をかけて最適化し、後で plugin にもできる。

canvas の裏側では **Copilot SDK 全体が動いており、SDK でできることは canvas でもできる**、と説明しています。つまり canvas は、エディター内で動いてチームと共有できる、本格的な agentic app です。LLM に任せる部分と、issue を読むような決定的な処理の分担、agent が行動し人が行動するフィードバックループ、あるいは「automate」ボタンで agent が全部行う、といった設計ができます。チームにはトリアージ用の inbox が約 10 種類あり、agent をどうオーケストレーションし inbox として管理するかの違いを試して、何がうまくいくかを学んでいます。

製品の機能自体も canvas で出荷されつつあります。GitHub Security が GitHub Copilot app 向けに出した plugin は、主要なインターフェースが canvas です。Azure DevOps が出した GitHub Copilot app との統合（VS Code でも動く）も canvas です。分野によっては、将来 chat に代わる主要な方法になるかもしれない、という見方も示されています。

### 5.12 計測と改善: OpenTelemetry、usage metrics、dreaming

最後の 2 分ほどで、学習・改善のための計測が駆け足で紹介されます。

1. **OpenTelemetry**：agent に OpenTelemetry を展開している人を会場に尋ねている。managed settings で、テレメトリーの送り先、収集量、tool call の詳細の粒度を強制できる。大規模に agent のデータを取り込め、プロンプトや出力も入りうるので、人々が何をしているか、結果はどうかを分析し、独自の evals を載せることもできる。
2. **usage metrics API（GitHub）**：ユーザーごと・日ごとの集計で、どの skills、custom agents、MCP、スラッシュコマンド、plugin が使われたかが分かる。自社のシステムに取り込み、ユーザーと突き合わせてダッシュボードにできる。skill を展開して使われていれば良く、使われていなければ理由を探る。中央の marketplace にはないが人々が使っている skill は、marketplace に取り込む。
3. **dreaming**：リポジトリ全体・全メンバーの過去のセッションを見て、agent が失敗したパターンを探し、agents.md や skills の改善案を出す仕組みで、GitHub Copilot で現在利用できる。memory の整理、リポジトリ内の skills や instructions の改善、PR のレビューガイダンスの改善につながる、と説明している。

これらの改善は、marketplace で「提案、eval の実行、レビュー、リリース」という流れで進める、というのが全体の考え方です。人が時間を使っている場所や失っている場所、agent に任せたい作業、コードレビューのように agent が苦手な品質を上げたい場所から小さく始める、とも話しています。

### 5.13 案内

- evals については `aka.ms/evals`（発話は "AKAMS slash evals"）。John 氏が会場後方で話せる。
- VS Code チームの別のセッションが後ほどあり、agents window や、より多くの作業を agent に任せる話題を扱う。
- 登壇者はその日の午後 7 時 30 分ごろまで階下（ブース）にいる。質問やコメントは VS Code の issues にタグ付けしてほしい、とのことです。

質疑応答の時間は、トランスクリプトには見当たりません。

## 6. デモ・手順

このセッションには**画面を使ったデモが複数あります**。いずれも録音からは画面の細部が確認できないため、登壇者が語った範囲のみを、発表された順に整理します。

### 6.1 冒頭: agents window での探索セッション（Ross 氏）

1. VS Code から agents window に切り替える。
2. 会場に人が入ってきた頃から動かしていた複数のセッションを見せる。
3. exploration セッションで、agent が VS Code の中を動き回り、スクリーンショットに注釈を付けている。
4. 裏では Blobby Sandbox の skill とローカルツールが、micro VM の起動、最新ビルドのダウンロード、サンドボックス内での computer use を行う。
5. 問題が見つかれば、QA skill と canvas で内容を提示し、自分が確認して issue を起票する。

### 6.2 skill の遅延ロードを見せるセッション（Ross 氏）

1. 上部のセッションで、スラッシュコマンドによる skill 呼び出しを見せる。
2. exploration のセッションで、自然言語だけの指示を見せる。
3. 可視化で、使える skill と、ロードされた 3 つを示す。

### 6.3 PR description の before / after と、skill 作成（Ross 氏）

1. ブラウザーで、skill 作成前後の PR description を見せる。作成後は、注釈付きビデオ、変更前後の比較、短い再現手順になっている。
2. VS Code で新しいチャットを開く。
3. 「PR description の skill を作って。この形式が好き」という趣旨の指示を書き、issue の説明を貼って送信する（タイプミスはそのまま）。
4. agent が動き始めるが、バックグラウンドで実行中のまま、完了結果は見せていない。

### 6.4 eval の結果が付いた PR（Ross 氏、John 氏の PR）

1. 変更ファイルに skill.md が含まれる PR を開く。
2. PR の会話タブで、eval パイプラインが自動投稿したコメントを拡大する。
3. トークン使用量が 6% 減り、品質には影響がなかったと確認できる。

### 6.5 plugin marketplace のテンプレート

1. plugin marketplace のスターターのテンプレートを示す。
2. PR description の plugin の構成（名前、description、バージョン、changelog、README、skills フォルダー、ルートの MCP.json）を示す。
3. `marketplace.json` と、eval plugin が特定の参照を指していることを示す。

### 6.6 Ross 氏の個人 marketplace

オープンソースで公開している marketplace を示し、構成は「kitchen sink」で、真似しないよう注意している。

### 6.7 managed settings とチームの marketplace

1. GitHub Copilot の管理設定で、AI controls が managed settings のリポジトリを指していること、`.github` のプライベートリポジトリに `managed settings.json` があることを説明する。
2. 組織内のチームの画面で、marketplace を追加する（「新しい marketplace を作る」ボタンのあるテンプレート版と、既存の marketplace を追加する例）。
3. enterprise レベルで追加済みの TeamKit が見えることを確認する。
4. Browse plugins から、review areas の plugin を選んで有効にする。

### 6.8 automations（Ross 氏）

1. オープンソースのリポジトリの PR 画面で「Label PR demo」の automation を見せる。
2. 同僚の PR に付いた、動作の録画付きコメントを示す。
3. 夜間の self-host UA の automation が起票した issue を見せる（スクリーンショット、再現手順、期待値と実際の結果）。

### 6.9 connectors と canvases

1. customizations タブと connectors（この部分の説明者は音声からは特定できません。以降の canvases の実演は Ross 氏）（Figma は一度接続すれば再サインイン不要）を説明する。
2. agents QA の canvas で issue 起票を操作し、キャンセルする。agent が重複を調べる動きを説明する。
3. Blobby sandbox、skills funnel の canvas を見せる。
4. 「create canvas」で canvas を作る方法と、GitHub Copilot app でも見えることを説明する。

## 7. 注意点・発表者の気付き

- **skill は手書きせず、最初の出力を信じない。** 複数の案を出させて探索し、そこから絞る。
- **単発のルール追加で skill を膨らませない。** agent は会話の細かい指摘を拾ってルールを足しがちなので、evals と prompt engineering で全体を見直す。
- **agents.md は最小限に。** 毎回読み込まれるため、詳細は skill に分ける。
- **skill の数が多いとコンテキストを圧迫する。** 遅延ロードが重要で、自分は 31 個持っている、という例が出た。
- **強制する plugin は、確信が持てるものか、セキュリティ・サポート関連に限る。** そのために evals が必要になる。
- **小さく始める。** 100 個の skill を一度に作らず、1〜2 個の最も困っている問題（PR description、アクセシビリティを忘れる問題など）に集中し、エンドツーエンドの出荷方法を学びながら広げる。
- **Ross 氏の個人 marketplace の構造は参考にしない。** 公式のスターターを使う。
- **ロックダウンした場合は support URL を設定する。** UI は「管理者に連絡」と案内するため、問い合わせが製品側に来ないようにする。
- **管理者設定の配布方法は、デバイスポリシーより GitHub のプライベートリポジトリを好む顧客が多い。** デバイスポリシーは展開に時間がかかることがある。
- 機能の中には「とても新しい」ものがあり（org team の marketplace は changelog が前日、strict plugin only は同週）、plugin spec も 1.1 が間もなくという段階で、将来の変更が前提になっている。
- 発表では、hooks の詳細は扱われていません。

## 8. 利用者にとっての利点

発表で語られた利点・成果は次のとおりです。

- **PR の見やすさ**：PR description skill により、検証コマンドの長文が消え、注釈付きビデオ、変更前後、短い再現手順が付く（Ross 氏の例）。
- **トークンの削減**：eval を付けた PR で、skill の変更によりトークン使用量が 6% 減り、品質への影響がないことを確認できた。
- **一貫した体験と統制**：managed settings、チーム単位の marketplace、strict plugin only により、管理者は許可する MCP や skill を絞り、全員に同じ plugin を配れる。強制と、ユーザーが外せる既定の有効化を使い分けられる。
- **作業の自動化**：automations により、PR のたびの録画付きコメントや、夜間の UI 探索と issue 起票がクラウドで動き、ローカルで行っていた朝の作業が置き換わった（Ross 氏）。
- **環境をまたぐ連続性**：customization や connectors が、ローカル、クラウドセッション、automations に引き継がれる。
- **可視化と改善**：usage metrics API で skill の利用が分かり、dreaming でセッションから agents.md や skills の改善案が出る。

なお、顧客企業の事例は、このセッションでは紹介されていません。

## 9. まとめ

このセッションの要点は、**skill を「個人のメモ」から「テストされ、バージョン管理され、配布される成果物」に変える**ことです。

1. skill は agent と作り、evals で品質（例: トークン 6% 減）を確認する。
2. plugin にまとめ、marketplace（テンプレートが公開されている）でバージョン管理する。
3. 配布は「利用可能、既定で有効、強制」の 3 段階で、managed settings、組織内のチーム、リポジトリで使い分ける。
4. automations、connectors、canvases で、skill の外側の作業も自動化し、チーム共通の体験にする。
5. OpenTelemetry、usage metrics API、dreaming で使われ方を測り、改善に戻す。

最初は 1〜2 個の本当に困っている問題から始めてほしい、というのが登壇者からの具体的な助言でした。
