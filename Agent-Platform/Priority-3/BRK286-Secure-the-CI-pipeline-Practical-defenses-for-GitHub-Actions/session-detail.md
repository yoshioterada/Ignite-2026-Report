# BRK286 Secure the CI pipeline: Practical defenses for GitHub Actions

## 1. はじめに

このセッションは、GitHub Actions チームの 2 人が、ここ 1 年半〜2 年で出荷してきたセキュリティ強化の機能とデフォルトを、「サプライチェーン攻撃の 1 つのシナリオ」に沿って紹介する内容です。対象は、ビジネス側の意思決定者、AI エンジニア、開発者です。

攻撃を 5 つの段階に分け、段階ごとに「どの新機能（または新しいデフォルト）が、どの鎖を断ち切るか」をデモ付きで見せていきます。取り上げられるのは、`actions checkout` の安全側デフォルト、キャッシュの読み取り専用トークンと cache mode、actions policies、actions lock files、actions firewall、Keyless GitHub app tokens です。後半では、CI の中で動くエージェント（agentic workflows）に同じ統制が効くこと、Azure の仮想ネットワーク内で runner や Copilot Cloud Agent を動かす方法も紹介されます。

最後に、無料で使えるもの、まだプレビューのもの、ロードマップ、導入計画の 4 ステップがまとめられます。このセッションには、画面操作を伴うデモが複数あります（6 章）。終盤に質疑応答の呼びかけもありますが、トランスクリプトに質問と回答は含まれていません。

セッション URL: [https://ignite.microsoft.com/en-US/sessions/BRK286](https://ignite.microsoft.com/en-US/sessions/BRK286)

## 2. 発表者

| 氏名 | 所属 | 役職 |
| --- | --- | --- |
| Steve Glass | GitHub（GitHub Actions チーム） | 発表では役職への言及なし |
| Jeff Martin | GitHub（GitHub Actions チーム） | 発表では役職への言及なし |

> 補足：メタデータに登壇者一覧はありません。冒頭で Steve 氏が、自身が Steve Glass であること、同僚の Jeff Martin とともに 2 人とも GitHub Actions チームの所属であることを自己紹介しています。Steve 氏が導入、攻撃の各段階の前半（段階 1〜3）、エージェントのパート、Azure private networking、提供状況・ロードマップ・導入計画を話し、Jeff 氏は段階 4（依存関係）と段階 5（実行コードの到達範囲）のデモを担当しています。Steve 氏は Actions の仕事を約 5 年前に始めたと述べています。

## 3. 何に関する発表か

テーマは、GitHub Actions を狙ったサプライチェーン攻撃の流れを分解し、それぞれの段階で使える防御策を整理することです。Steve 氏は、GitHub Actions を「任意のコードを実行するもの」と言われた経験に触れたうえで、それは一面では正しいが、同時に CI/CD を含む汎用の自動化基盤でもある、と位置づけています。

発表の流れは次のとおりです。

1. GitHub Actions の仕組みを 30 秒で振り返る（イベント駆動、workflow、job、runner、step）
2. サンプル攻撃のシナリオ（producer と consumer のリポジトリ、3 つの workflow ファイル）
3. 段階 1：PR から workflow 実行へ（`actions checkout` の新しいデフォルト）
4. 段階 2：共有状態から権限昇格へ（キャッシュの読み取り専用トークン）
5. 段階 3：特権ワークフローを守る（cache mode、actions policies）
6. 段階 4：consumer 側の依存関係（actions lock files）
7. 段階 5：実行中のコードが到達できる範囲（Keyless GitHub app tokens、actions firewall）
8. CI の中のエージェント（agentic workflows）
9. Azure private networking（VNet injection）と Copilot Cloud Agent
10. 無料・有料、GA・プレビュー、ロードマップ、導入計画

## 4. 発表者が伝えたいメッセージ

中心になる主張は、**Actions の中では `uses` で呼んだアクションが、自分のトークンとシークレットを持った状態で他人のコードを動かしている**、という点です。Steve 氏は、攻撃は producer（アクションを作って公開する側）と consumer（それを workflow で使う側）の間の信頼を突き、producer のリリースが汚染されると、信頼された成果物にマルウェアが入ること、さらに consumer が別のアクションの producer でもある場合に被害が連鎖して広がることを説明しています。

そのため、防御は 1 か所では足りず、攻撃の鎖の各リンクで断ち切る必要がある、という構成で発表が進みます。Jeff 氏は、producer 側の対策（immutable releases、タグのルールセット）は consumer 自身では実施できないため、consumer 向けの対策として、依存関係の固定（lock file）と、実行中のコードが到達できる範囲を狭めること（firewall、短命トークン）を紹介しています。

また、エージェントを CI に載せる場合も、同じ統制（キャッシュ、egress firewall、短命トークン）が適用できると強調されています。

## 5. 発表内容の詳細

### 5.1 GitHub Actions を 30 秒で（前提）

GitHub Actions はイベント駆動です。push、PR、コメントなど、workflow をトリガーできるあらゆるイベントで workflow が実行されます。workflow はリポジトリ内の YAML ファイルで、複数の job を持ち、job は runner に割り当てられ、runner が step を実行します。step には `uses` で呼ぶアクションも含まれます。

サプライチェーンの観点で重要なのは、workflow の中の `uses` が、すべて「自分のトークンとシークレットを持った状態で他人のコードを実行している」点だと説明されました。

### 5.2 サンプル攻撃のシナリオ

説明用のアクションは **PR note** です。PR にコメントで「thank you for the pull request」と返すだけの単純なものです。ここで次の構成を使います。

| リポジトリ | ファイル | 役割 |
| --- | --- | --- |
| producer（PR note を公開する側） | lint.yaml | pull request で実行される |
| producer | release.yaml | 公開用 workflow。contents write 権限を持ち、アクションを公開する権限を与える |
| consumer | comment.yaml | consumer が PR note を workflow に組み込んで何が起こるかを模擬する |

攻撃は 5 段階で、複数の境界をまたぎます。境界 1 は、外部の貢献者による PR から workflow の実行へ。境界 2 は、その外部の貢献が、信頼された workflow の共有状態を汚染することです。汚染されたアクションを consumer が使うと被害が広がり、最終的にシークレットが「smash and grab（奪って逃げる）」で外部に持ち出されます。

### 5.3 段階 1：PR から workflow 実行へ（pwn request）

lint.yaml は **pull request target** で動いています。Steve 氏は、これは危険というより強力なトリガーだと表現します。デフォルトブランチのコンテキストで動き、シークレットにアクセスでき、デフォルトブランチと同じキャッシュスコープを共有するためです。

この workflow にはもう 1 つ危うい点があり、チェックアウトの参照が PR の head sha（発話では "GitHub event pull request head sha"）になっています。攻撃者がリポジトリの侵害を狙う場合、lint.yaml の中で動くのはデフォルトブランチのコードではなく、攻撃者のコードです。`npm ci` や `npm test` のような処理を通じて、共有状態を汚染する機会にもなります。このように、**信頼された workflow が信頼できないコードを実行してしまう**状況を **pwn request** と呼んでいます。

対策は、`actions checkout` の安全側デフォルトです。pull request target が使われ、かつ参照が PR の head sha のとき、GitHub Actions は checkout を拒否し、その workflow の実行は失敗します。pull request target は GitHub Actions で最も誤用されるトリガーの 1 つです。head sha をどうしてもチェックアウトしたい場合の構文は用意されていますが、デフォルトでは止まります。目的は、信頼できないコードが runner で実行される**前に** job を止めることです。同じく、安全でない workflow run の入力に対しても checkout を拒否します。

> 注意：Steve 氏は、これは actions checkout に対するガードレールであり、信頼できないコードをダウンロード・実行する方法をすべて塞ぐ保証ではない、と明言しています。

### 5.4 段階 2：共有状態から権限昇格へ

pull request target はデフォルトブランチのコンテキストで動き、デフォルトブランチの workflow はキャッシュのスコープを共有します。そのため、攻撃者が lint.yaml の workflow 実行を通じてキャッシュを汚染できると、その影響が release.yaml に届いてしまいます。

対策として、GitHub Actions は、リポジトリへの書き込み権限なしでトリガーされた workflow 実行に対して、**読み取り専用のキャッシュトークン**を発行するようになりました。これまでは外部の貢献者の workflow がキャッシュに書き込めたのに対し、いまは書き込めません。権限昇格につながるキャッシュ汚染を防ぎ、信頼できない workflow がキャッシュの内容を操作する機会を減らします。

### 5.5 段階 3：特権ワークフローを守る

release workflow は contents write 権限を持っています。攻撃者がキャッシュを汚染して、特権 workflow からアクセスできるようになると、アクションの新しいバージョンではなく、**元のバージョン（タグ）が指す commit を差し替える**ことができます。たとえば PR note の v3 を使っている人は、タグが差し替えられたことに気づかないまま、以前は信頼していたアクションとして悪意あるものを実行してしまいます。

ここでは 2 つの対策が紹介されました。

#### cache mode

workflow の YAML に新しい構文が追加され、job または workflow のレベルでキャッシュのアクセスを **read、write、none** で設定できます。Steve 氏は workflow の抜粋を示して説明しました。

- 読み取り専用がデフォルトの場合、書き込みはブロックされますが、restore（キャッシュの復元）は許可されます。
- cache mode を none にすると、特権 workflow での restore も止まり、汚染されている可能性のあるキャッシュの内容を release workflow が使うことを防げます。
- cache mode を変えても、その workflow の公開権限は変わりません。キャッシュに関する権限を調整して、露出を限定する仕組みです。

#### actions policies

「そもそも誰が workflow を起動できるか」を制御する仕組みです。**rule sets フレームワーク**の上に作られており、active、evaluate、disabled の 3 つのモードで適用できます。リポジトリ全体ではなく、特定の workflow ファイルを対象にできます。

- 例：release.yaml を実行できるのは Steve 氏とメンテナーだけ、という設定。
- actor rule は「誰に許可するか」、ファイルの指定は「どの workflow を統制するか」を決めます。2 つを組み合わせて、特権リリースを固定できます。
- Enterprise、organization、リポジトリの全体で、イベントトリガーの制限や、lock file の使用の義務づけもできます。
- actor には、ユーザー本人だけでなく、GitHub Apps、automation、Copilot なども含められ、これらの利用を制限できます。

### 5.6 段階 4：consumer が持ち込む依存関係と actions lock files

ここから Jeff 氏が担当します。consumer として、ここまでの緩和策だけが救ってくれるとは限りません（実施すべきではあるが）。プライベートリポジトリで動かしている場合、最大の脅威は、信頼されたコンテキストに持ち込む**依存関係**だと述べます。

アクションは多くの場合、v3 のようなリリースタグをバージョンとして使います。このタグは commit への**可変のポインタ**で、書き込み権限があれば別の commit を指すように書き換えられます。immutable releases を使えばタグの書き換えを防げますが、これは producer が行うことです。タグを保護するルールセットも同様で、consumer の側では取れない対策です。タグが乗っ取られて悪意ある commit に向けられると、次に workflow が走った時点で v3 が望まない内容に変わっていて、気づけません。

consumer 側の定番の回避策は、タグではなく commit SHA を指定する方法です（コメントにタグを書いておき、Dependabot で更新）。しかし、これには問題があります。デモの PR size labeler アクションは SHA で固定されていますが、実装を見ると **composite action** で、中の diff stats アクションをミュータブルな v1 タグで参照しています。この v1 は実行時に、その時点の指し先に解決されます。つまり、固定したつもりでも、推移的な依存が固定されていません。

そこで出てきたのが **actions lock files** です。**依存関係を推移的閉包（transitive closure）全体で堅牢に固定する**ものです。入口は GitHub CLI の拡張機能（CLI リポジトリ）で、エージェントにリポジトリの管理を手伝わせるための skill も付いています。

lock 済みの workflow では、v1 のような人間が読めるタグの表記に戻れます（「実際のバージョンに少し近い形。まだそこまでは至っていない」とも述べています）。lock file には、PR size labeler の commit SHA、その他のメタデータ、diff stats への依存とその固定が記録されています。

lock file を使うと、job が始まる前に SHA を解決できるものだけを使い、解決できなければ失敗します（lock file を使うか使わないかの 2 択で、中間はない）。デモでは、lock file に記録した SHA の diff stats が取得されました。

#### 今日できることと、次の予定

今日の lock file で得られるのは、依存グラフ内のすべてのアクションが、実行時に変わらないものに固定されることです。「固定したものが手に入る」という約束です。固定したものが実行してよいものかどうかは別の問題だ、と Jeff 氏は断っています。

- 直接の依存、推移的な依存、composite action を含む。
- policy で強制できる（Steve 氏の説明どおり）。
- Dependabot が workflow を更新するときに、lock file も同時に更新する。

今後の予定は次のとおりです。

- Enterprise や organization 向けの導入ツール（管理パネルで、lock file を採用したリポジトリと未採用のリポジトリを見て、大規模に導入を管理する）
- reusable workflows と Docker actions の lock file への対応
- producer 側で、アクションの定義ファイル（発話では "action not YAML"）自身が lock file を定義できるようにし、consumer はそれを取り込むだけにする

### 5.7 段階 5：実行中のコードが到達できる範囲

この段階では、攻撃者がすでに job に入り込んでいると仮定します。問題は、そこで何にアクセスできるか、被害の範囲（blast radius）をどう小さくするか、そして盗んだものを VM の外へ持ち出す経路をどう減らすかです。

job には、シークレット、トークン、知的財産、そして Actions の利用分数があります。攻撃者はそれらを奪って持ち出し、盗まれたシークレットが次の足がかりになります。プライベートリポジトリの consumer の場合は、社内ドメインなど、アクセスさせたくないものも含まれます。Jeff 氏は、トークンは取られるものと仮定したうえで、job の中に入れるシークレットをどう減らせるかを、この発表の後に考えてほしいと述べています。

#### Keyless GitHub app tokens（近日提供）

GitHub App を使う automation では、現状、その App の**秘密鍵**を job に入れる必要があります。これは永続的なシークレットで、取り戻すには失効させるしかなく、時間でスコープされたトークンでもありません。

近日提供予定の **Keyless GitHub app tokens** は、Actions の OIDC を使って、自分で定義した trust policy に基づく**短命のトークン**を発行し、秘密鍵全体を渡さなくて済むようにします。YAML の例では、次の点が示されました。

- OIDC 認証に必要な permissions の id-token write
- ファーストパーティの create GitHub app token アクション
- アクションに渡す入力は **client ID** だけで、秘密鍵は不要
- workflow 自身の ID で認証する。どの workflow、どのコンテキスト、どのブランチ、どのイベントがそのトークンを得られるかを制限できる

#### actions firewall

VM から外に出る経路を絞るのが **actions firewall** です。runner に対して、job が通信してよいドメインの**許可リスト**を与えます。デモの設定ファイルは enforcement mode で、audit mode もあります（後で触れるとしていますが、トランスクリプトでは詳しい説明は出てきません）。

firewall を使うには、firewall の接尾辞が付いた runner を指定し、firewall の設定ファイルを定義します。アーキテクチャは**入れ子の VM**で、firewall は信頼できるホスト側にあるため、workflow を実行するゲスト側の信頼できないコードは、firewall を実行時に止めたり変更したりできません。

### 5.8 CI の中のエージェント：agentic workflows

**agentic workflows** は、タスクを自然言語で記述し、agentic workflows のコンパイラで、エージェントが作成した YAML に変換するものです。Actions のイベントでトリガーされて実行されます。本質的には Actions の workflow であり、実行中の各ステップにエージェントの推論を加えたものだと説明されています。

用途としては、CI の失敗の分析やドキュメントなどがあり、Steve 氏自身は、Actions チームで複数チームからの更新を集めて集約し、上に報告できる形にまとめるために使っています。「もう手作業でやる理由はない。2026 年は時間ではなくトークンを使う時代だ」という発言がありました。

これまで見てきた多くの統制は、エージェントにも当てはまります。

- agentic workflows は**読み取り専用**で、書き込みは **safe outputs** を通してのみ行う。
- キャッシュのデフォルトが効き、信頼できないトリガーからのキャッシュを緩和する。
- 段階 5 の egress firewall も適用される。
- シークレットをエージェントに持たせない取り組みを続け、keyless app tokens によって長期の認証情報から短命のトークンへ移行を進められる。

### 5.9 Azure private networking（VNet injection）

**Azure private networking** は、GitHub ホステッド runner 向けの VNet injection です。network configuration を作り、Azure リソース内のサブネットを GitHub に**委任**すると、GitHub がそこにホステッド runner 用の仮想ネットワークインターフェースカードをデプロイします。

- 既存の network security group、Azure Firewall、その他の商用 firewall などの境界が、そのまま VNet 内の runner に適用される。
- Artifactory のようなプライベートなリソースや、CI で runner が到達する必要があるストレージに、より安全に接続できる。
- 顧客が VNet から proxy を設けて他のリソースに到達している例もあり、セキュリティ要件に合わせて調整できる。
- organization または enterprise で一度設定し、runner group をその network configuration に向ければ、すべての workflow 実行がその仮想ネットワーク内で動くことを実質的に保証できる。

**Copilot Cloud Agent は Actions のコンピュートで動く**ため、仮想ネットワーク内のコンピュートで動かすように割り当てられます。エージェントのセッションもネットワークのルールに従い、Copilot 自身の firewall と合わせて **2 層の防御**になる、と説明しています。

### 5.10 提供状況と料金

| 項目 | 内容 |
| --- | --- |
| 新しい Actions のセキュリティ機能、VNet injection、policies | 追加料金なし（パブリックリポジトリも含む） |
| 課金されるもの | runner の分数。プライベートリポジトリでは、larger runner はサイズ別 |
| agentic な実行 | VM の時間に加え、トークン使用量も課金 |
| GitHub Actions | パブリックリポジトリは無料・無制限。プライベートでは、無料枠（entitlement）の分数を超えると課金 |
| GA の状況 | 今日の内容は、2 点を除いて GA |
| 技術プレビュー | **actions firewall**。2027 年 6 月末までの GA を目指す。一般に公開されており、"actions firewall early access" で検索すると資料とフィードバックの場所がある。無料 |
| まもなくプレビュー | **keyless app tokens**。「次の四半期ごろ」にプレビューの見込み（発話の "staff shipped" は音声認識の都合で意味を確定できません）。GitHub ロードマップを確認してほしい |

### 5.11 ロードマップ

- **publisher 向けの保証の強化**：lock files は consumer を対象にしている。immutable releases の上に、公式の **actions releases** を構築する。
- lock files は現在、リポジトリ単位の CLI 拡張機能。数千のリポジトリを持つ組織向けに、一括導入できる体験を作る。
- workflow 実行の保護：条件を満たさない実行は今は失敗するが、管理者が実行前に承認できるようにする。どのブランチで動かせるかなどを指定する **reference rule** も追加予定。
- firewall は現在リポジトリ単位でファイルベースのルール適用だが、actions policies と同じ policy エンジンを使い、enterprise、organization、リポジトリの各レベルで適用できるようにする。リポジトリの custom properties で、ルールを適用するリポジトリをタグ付けできる。
- マーケットプレイスの改善：どのアクションを使うべきかの判断に役立つ指標を示し、開発者がより良い選択をできるようにする。

### 5.12 導入計画（推奨ステップ）

1. **デフォルトを確認する。** GitHub が最初からブロックするものを理解し、安全でない checkout が必要な場合の構文も把握する。
2. **policies を決める。** 例えば actors rule は、パイプラインを実行できる人を簡単に監査・一覧できる必要があるという、組織によっては SOC 2 の要件に当たる。
3. **lock files を試す。** 依存関係とリポジトリを固定してみる。数千のリポジトリがある場合は、change log の更新を待つ。
4. **firewall を見始める。** すでに Azure を使い、GitHub ホステッド runner を使っているなら、VNet injection（Azure private networking）が簡単な次の一歩。firewall が今できることを理解し、今後のルールと機能の追加を追う。

### 5.13 Q&A・終わりの案内

Steve 氏は、関連セッションの多くはすでに終わっているので、オンデマンドで視聴してほしいと案内しています。そのうえで、時間が少し残っているので質問を受けると述べ、終了後に会場の外で声をかけてもよいとも話しています。トランスクリプトには質問と回答は含まれていません。

## 6. デモ・手順

このセッションには、画面操作を伴うデモが複数あります。画面の細部は音声からは分からないため、語られた範囲で記します。

### 6.1 デモ 1：pwn request の失敗（Steve 氏）

1. PR note リポジトリの fork を使う。
2. README を更新して "hi" と書く（簡単だから、という説明）。
3. fork から PR を開くと、lint.yaml が動き始める。
4. Actions を見ると、workflow はすでに失敗している。
5. 失敗の理由は、安全でない fork の head sha のパターンと、pull request target の組み合わせ。

### 6.2 デモ 2：lock file とタグのハイジャック（Jeff 氏）

1. VS Code で、すべてのアクションを commit SHA で固定した workflow を示す。タグはコメントに書かれ、Dependabot が更新できるようにしてある。
2. PR size labeler アクションは SHA 固定だが、composite action で、中の diff stats アクションをミュータブルな v1 タグで参照していることを見せる。
3. diff stats の v1 タグを別の commit に動かして、攻撃を模擬する。
4. GitHub CLI の拡張機能（actions lock files）を紹介し、job を起動したうえで、lock 済みの版の workflow を見る。
5. lock 済みの workflow で、PR size labeler が v1、lock file に commit SHA とメタデータ、diff stats への依存と固定が記録されていることを確認する。
6. 実行結果を比較する。SHA 固定のみの job には、Jeff 氏が付けた "unreviewed diff stats commit ran" の目印が出る（本物の攻撃者は、このように親切に知らせない）。lock 済みの job には目印がない。setup job のステップで lock file の使用が確認でき、lock file に記録された SHA の diff stats が取得されている。

### 6.3 デモ 3：imposter commit と lock file の偽造（Jeff 氏）

1. SHA 固定のみの版の workflow を削除し、リポジトリの残りも lock する。
2. ある commit を示す。Steve 氏の PR note リポジトリの一部のように見えるが、実際は Jeff 氏の fork のもので、「この commit はこのリポジトリのどのブランチにも属しておらず、リポジトリ外の fork に属している可能性がある」という警告が表示される。commit の中身は、PR note を composite にして、目印を出力するようにしたもの。
3. lock file のコマンドを再実行すると、この commit を lock file に追加できないというエラーになる。善意の利用者が、ブログ記事などからコピーした SHA で間違えることを防ぐ仕組みだと説明。
4. 次に、lock file を手で書き換えて偽造を試みる。CLI を再実行すると、検証で失敗する。
5. それでも commit して workflow を実行すると、job が開始されない。lock file は入口だけでなく、実行時にも検証に使われるため。

### 6.4 デモ 4：actions firewall（Jeff 氏）

1. 設定ファイルを開く。enforcement mode で、許可するドメインは 3 つ。ほかに、不審なものが 1 つ含まれている。
2. firewall runner を使う workflow を見せる。matrix で、plain runner と firewall runner を並べている。
3. 使うアクションは、ライセンスをチェックする（README の説明）もの。デフォルトの policy を、pages サイトからスクリプトとして取得して実行する（curl を bash に渡す形）。Jeff 氏は、このアクションは使わないと述べています。
4. 通常のスクリプトは、ライセンスが policy で許可されているかを確認するだけ。そこで、ページが乗っ取られたと仮定してペイロードを切り替える。新しいペイロードは VM からデータを外に出し、宛先は "evil node selector"（もとのペイロードの宛先は通常の node selector）で、Jeff 氏は皮肉を込めて「totally legit metrics collector（まったく正当なメトリクス収集）」と呼んでいます。
5. 実行して、firewall 版と plain 版を比較する。待つ間にアーキテクチャ（入れ子の VM、firewall はホスト側）を説明する。
6. 両方の実行が緑になる。出力にも悪意あるペイロードの表示はない。
7. **firewall decision artifact** には、実行中に許可・ブロックされたものが記録されている。Jeff 氏のスクリプトで解析すると、許可されたもの（問題のアクションを含む）と、許可リストになくブロックされたドメインが表示される。
8. 結果：plain runner は侵害され、firewall runner は侵害されなかった。このアクションは lock file に入っていたものだった。固定されていても、実行時に何ができるかを考えるべき、と締めくくっている。

### 6.5 デモ 5：agentic workflow（Steve 氏）

Steve 氏は「一部は事前に済ませてある」と断っています。

1. issue のトリアージを自然言語で記述している（「この issue は質問か」「バックログに入れるか」など）。
2. コンパイラを実行した結果の出力を見せる（"ASCII art" は自分で作ったものではなく、コンパイラの出力）。
3. ユーザーが Ignite のテスト用 issue を作る。すでに question のラベルが付き、内容は「PR note アクションは安全に使えるか」。
4. Actions の実行履歴で、この実行がそのイベントトリガーで起動されたことが分かる。
5. 実行完了後に、追加の成果物（artifact）と情報が出る。例えば firewall のアクティビティ（エージェントがどこに接続しようとしたか）、違反した policy の有無、トークン使用量。

### 6.6 デモ 6：private networking と Copilot Cloud Agent の設定（Steve 氏）

1. 「Ignite」という名前の **runner group** を作成し、テスト用の org だけがアクセスできるようにする。
2. network configuration は作成済みのものを選ぶ。これで、この runner group で動く runner は、この network configuration に拘束される。
3. 「Ignite」の runner を作成する。同時実行のマシン数は 1 にしたが、実運用では 1 台に全負荷がかかるため勧められない。利用状況を見て、runner や runner group ごとに必要な数を決める。
4. Copilot の設定で、ラベル付き runner を、標準の Ubuntu latest から Ignite の group と runner に変更する。
5. 「allow repositories to customize the runner type」をオフにする。enterprise や organization などの中のすべてのエージェントセッションが、この runner group と private networking のセキュリティポリシーに従う。

## 7. 注意点・発表者の気付き

- 新しい checkout のデフォルトは**保証ではなくガードレール**です。信頼できないコードの取得・実行をすべて塞ぐわけではありません。
- consumer が自分で取れない対策があります。immutable releases とタグのルールセットは producer の対策で、consumer は lock file などを使います。
- **SHA で固定しても、composite action の中の依存がミュータブルなタグを参照していれば固定になりません。** lock file は推移的な依存まで対象にします。
- lock file が保証するのは、「固定したものが手に入る」ことです。それが実行してよいものかどうかは、別に判断が必要です。
- 攻撃者は、侵害に気づけないようにアクションを作ります（デモでは、両方の実行が緑でした）。`curl | bash` のような作りは、そのアクションを使わない目安になる、と述べられています。
- 固定された（lock 済みの）アクションでも、実行時に何ができるかを考え、firewall などで到達範囲を絞る必要があります。
- Steve 氏は、デモの runner の同時実行数 1 は実運用では勧めず、利用状況を見て決めるよう述べています。
- actions firewall は技術プレビュー、keyless app tokens はまだ提供前（近日プレビュー予定）です。

## 8. 利用者にとっての利点

- 新しい Actions のセキュリティ機能、VNet injection、policies は**追加料金なし**で、パブリックリポジトリでも使えます。課金されるのは runner の分数などです。
- 今日話した内容は、actions firewall と keyless app tokens を除いて GA です。
- Azure の境界（network security group、Azure Firewall など）をそのまま hosted runner と Copilot Cloud Agent に適用でき、プライベートなリソースに安全に到達できます。
- 秘密鍵の代わりに短命のトークンを使えるようになります（keyless app tokens）。
- 数千リポジトリ向けの一括導入ツールや、enterprise レベルの firewall policy が、今後の予定として示されています。
- actors rule について、パイプラインを実行できる人を簡単に監査・一覧できることが、組織によっては SOC 2 の要件にあたる、と説明されています。

## 9. まとめ

サプライチェーン攻撃を 5 段階に分けると、それぞれに対応する防御が示されます。段階 1 は `actions checkout` の安全側デフォルト、段階 2 は読み取り専用のキャッシュトークン、段階 3 は cache mode と actions policies、段階 4 は actions lock files、段階 5 は keyless app tokens と actions firewall です。agentic workflows にも、同じキャッシュ、firewall、短命トークンの統制が及びます。Azure private networking で、hosted runner と Copilot Cloud Agent を自社のネットワーク境界に載せることもできます。

導入は、デフォルトの確認、policies の決定、lock files の試用、firewall と VNet injection の検討、の順を勧めています。ほとんどが GA で追加料金なしですが、actions firewall は技術プレビュー（2027 年 6 月末までの GA が目標）、keyless app tokens は近日プレビューの予定です。
