# THR891 Behind the scenes of patch management: Windows, drivers, models & apps

## 1. はじめに

このセッションは、Windows 端末のパッチ管理を Intune の画面で順にたどっていく約 25 分のシアターセッションです。登壇者は冒頭で「スライドは使わず、全編をライブデモにする」と宣言しており、実際に Intune の管理画面を操作しながら、update rings、quality update policy、レポート、maintenance windows、アプリの自動更新などを紹介します。後半は参加者からの質問に答える時間に充てられ、ドライバー、アプリ、サーバー、GCC High、feature update の safeguard など、現場で出がちな疑問が多く取り上げられます。

この記事では、デモで語られた順に設定項目と推奨値を整理し、最後に Q&A の要点をまとめます。

セッション URL: [https://ignite.microsoft.com/en-US/sessions/THR891](https://ignite.microsoft.com/en-US/sessions/THR891)

## 2. 発表者

| 氏名 | 所属 | 役職 |
| ---- | ---- | ---- |
| Ahmed Ghaddah | Microsoft | Product Manager |
| Aria Hanson | Microsoft | Principal Group Product Manager |

> 補足：冒頭で「I'm Aria, and with me is Ahmed」と自己紹介があります。Aria 氏が「Ahmed、品質更新をもっと細かく管理するには？」と振った直後の quality update policy の説明は、Ahmed 氏が話していると読めます。ただし、そのほかの部分や Q&A の回答がどちらの発言かは、トランスクリプトからは区別できないため、以降は「登壇者」と書きます。

## 3. 何に関する発表か

テーマは、セキュリティ担当や IT 管理者が、事業の継続性を保ちながら端末を素早くパッチするための管理方法と、その仕組みです。対象は Windows の更新だけでなく、ドライバー、アプリ、（ローカルの）モデルにまで及びます。登壇者は、Windows の管理機能はほとんどが Intune か、サードパーティの管理ソフトに現れるが、今回のデモではすべて Intune で見せる、と述べています。

発表の流れは次のとおりです。

1. 名称の整理：Windows Update for Business、Windows Update Client Policies、Autopatch
2. update rings（deadline と grace period を中心に）
3. quality update policy（hotpatch、quick machine recovery、手動承認）とドライバー、新しい体験の管理
4. レポート（Windows autopatch の概要、OS reliability レポート）
5. maintenance windows
6. サーバーへの展開（プレビュー）
7. アプリの管理と自動更新（Intune Enterprise App Management）
8. モデル・エージェントに関する補足
9. Q&A

## 4. 発表者が伝えたいメッセージ

メッセージは「**普通の 1 人 1 台の PC は update rings の推奨設定で任せ、さらに細かい制御が必要な部分だけ、granular な機能や maintenance windows を使う**」という使い分けです。ハンズオフにしたければ、すべて update rings で管理できる、とも述べられています。

もう一つは、パッチの攻撃的な運用、つまり短い deadline と grace period を勧めている点です。登壇者は「いまは、これまで以上に deadline を攻めた設定にすることが重要」と話しています。

また、製品の変更は IT 管理者のフィードバックをもとに作られており、参加者に preview への参加とフィードバックを呼びかけています。

## 5. 発表内容の詳細

### 5.1 名称の整理：Autopatch

Windows Update for Business、Windows Update Client Policies、Autopatch は、**すべて同じもの**だと説明されています。ブランド名を変えたが理解されていない、と登壇者は冗談交じりに述べています。update rings を使っているなら、すでに Autopatch を使っていることになります。

### 5.2 update rings の設定

update rings は、更新体験を管理する出発点で、手間をかけたくなければ、すべてをここで管理できます。デモでは、Windows Update の画面から次のポイントが説明されました。

| 設定 | 登壇者の説明 |
| ---- | ---- |
| Allow other Microsoft product updates | 実際に対象になるものは「ほとんどない」。Visual Studio が含まれ、他にも少数がある。社内でも廃止について議論したが、許可するかどうかは任意 |
| Windows drivers | 許可のままにしておき、あとで granular に管理することを推奨 |
| Quality updates | deferral は 0 にするか、ring を設定するか、後述の granular な承認とスケジュールを使う |
| Deadline | このページで**最も重要な設定**。1 日の deadline と 2 日の grace period のように、かなり攻めた値を推奨 |
| Feature updates | quality updates と同様に設定する |
| Reset to default | 既定のダウンロード・インストール期間を使う設定。通常の 1 人 1 台の PC には、reset to default を推奨 |

deadline と grace period の違いは次のように説明されています。

- **Deadline**：更新が提供され、ダウンロードされてから、インストールされるまでの期間
- **Grace period**：ユーザーにとって実感のある期間。更新のために再起動を強制された経験があるなら、原因はおそらく deadline で、grace period は「再起動待ちの状態から、強制再起動までにどれだけ猶予があるか」を表す

あわせて、デバイスが電源につながっていれば夜間に自動で再起動できるようにするため、**Allow auto reboot before deadline** を許可することが勧められています。feature update も同様に設定し、Windows 10 を使っているなら Windows 11 に移行するか、ESU を契約してセキュリティを保つよう呼びかけがありました。

### 5.3 quality update policy（granular な制御）

もっと細かい制御が必要な場合は、quality update policy を作成します。デモでは次の推奨が示されました。

1. **Security updates は自動承認**にする。非セキュリティ更新は自動承認せず、手動で承認することもできる
2. **Hotpatch** を有効にすることを推奨する。有効にすると、再起動なしでセキュリティ更新を受け取れる。ただし baseline の月には再起動が必要
3. **Quick machine recovery** は手動承認を推奨する。障害が起きた場合（登壇者は CrowdStrike の件を例に挙げています）に、更新をスキャンして確認でき、デバイスが quick machine recovery のためにそれを取得できる

手動承認を選ぶと、ポリシー一覧に「review すべき release がある」と表示されます。クリックして承認する流れで、デモでは、セキュリティ更新は自動承認されているが、out-of-band の更新は確認が必要、という状態が見えました。これにより、**preview や out-of-band の更新を granular に制御**できるようになります。例えばプリンターや VPN の修正が必要な場合に、その更新だけを選んで端末へ展開できます。.NET も手動で承認でき、単独で表示されます。

### 5.4 ドライバー、New experience controls

ドライバーの更新も、quality update と同様に手動で管理できます。ドライバーの改善要望は多いものの、**今日発表できる改善はない**（「本当に残念」）と登壇者は述べ、近日中に多数が来る予定だとしています。Graph API の preview を試したい場合の参加方法は、セッションの最後に案内されると説明されました。

**New experience controls** は、更新によって Windows の UI に追加される新しい体験を管理するための機能です。UI に出てくる新機能を望まないとき、それをテンプレートにまとめて 1 か所で管理でき、新しい更新が入るたびにテンプレートへ徐々に加わるため、何度も設定し直す必要がありません。作成すると **discovery ページ**も使え、テンプレートに何が追加されているか、ほかにどのポリシーに設定されているかを見て、管理できているかどうか、あるいは新しい体験を大規模に管理できていないかを把握できます。

### 5.5 レポート

quality update を展開し、何を含めるかを制御できても、その更新が環境にとって健全かどうかが気になります。そこで使うのが、reports の「Windows autopatch」にある概要のレポートです。update rings を使っていても、ここで確認できます。

**概要**

- 環境の状態の概要が見える
- quality updates については、直近 3 日以内に更新されていれば current、3 日更新がなければ exposed、7 日はさらに exposed として扱われる（発話は聞き取りにくく、定義の細部は音声認識の都合で確定できません）
- feature updates は、サポート終了（end of service）になっていない安全なバージョンで動いているかどうかを見る
- 悪い状態に陥らせているポリシーを強調表示し、環境で動いているバージョンも示す
- 登壇者の環境（VM が多い）は少し雑然としているが、皆さんのテナントはもう少しきれいなはず、との冗談がありました

**OS reliability レポート（preview）**

新しく preview として加わったのが、OS reliability レポートです。更新に関連する **stop error code** とクラッシュを確認できます。デモでは次の順で説明されました。

1. 最近の更新に関連するスパイクが見え、そこから調査できる
2. 下にスクロールすると、関連する stop error code と、推定される failure module name が表示される。その stop error code のドキュメントも案内される
3. サイドパネルで stop error code をクリックし、影響を受けたデバイスの一覧を開くと、個々のデバイスと、そのデバイスが最近インストールした更新が分かる。調査を 1 か所で完結できる
4. 「reliability by updates」の表示では、更新を適用した後に、何台のデバイスがこの stop error code に遭遇し始めたかを確認できる

### 5.6 maintenance windows

病院やラボのデバイスのように、「11 時から 12 時の間にしか更新を許可しない」ような環境向けの機能です。Intune 全体に展開中で、Managed devices の下にあります。

デモでは、maintenance windows policy を作成し、次の設定が説明されました。

| 設定 | 説明 |
| ---- | ---- |
| 対象の範囲 | download・install・restart、install と restart、restart のみから選ぶ。状況による。非常に重要なラボ機器なら一般に **install and restart** を推奨。restart only は避ける傾向にある |
| 開始日・開始時刻 | 有効になる日時を指定する |
| 期間（duration） | 1 時間や 2 時間などから選ぶ。feature、quality、.NET、アプリの更新を適用するなら、可能なら **2 時間**を推奨。install と restart の両方を行うため、成功の可能性が最も高くなる |
| 繰り返し | 毎日、毎週、毎月などから選び、曜日も指定できる。登壇者は例として、毎週月・水・金に再起動し、火・木には再起動しない設定を挙げた |

restart only を避ける理由として、登壇者は次の予定を説明しています。**2027 年の初め**から、商用のお客様に「1 か月に 1 回の再起動」を導入します。これにより、再起動を要するドライバーは、既定で OS のセキュリティ更新と同時にインストール・再起動されるため、来年の初めには再起動の回数が目に見えて減ります。ドライバー画面から手動で管理して、早く再起動したい場合はオプトアウトできます。

デモ中、開始時刻の入力に失敗する場面があり、登壇者は「入力方法が正しくなかった」と謝っています。この機能は、今後 1 か月ほどかけてコンソールに順次表示されると説明されました。

### 5.7 サーバーへの展開

同じ autopatch のポリシーで**サーバーも管理できる**ようにする取り組みが紹介されました。server insider program を通じて、プレビューにサインアップできます。

### 5.8 アプリの管理と自動更新（Intune Enterprise App Management）

ここからはアプリです。Intune Enterprise App Management（EAM）をすでに使っている参加者に挙手を求め、まだなら確認することを推奨しています。

preview に入っている場合、新たに **app updates** が使えます。デプロイしたアプリだけでなく、**ユーザーが自分でインストールしたアプリ**にも自動更新を有効にできる、と説明されました（Intune 管理者がデプロイしていないアプリを、Ahmed 氏がたくさん入れている、という冗談付き）。

app catalog での設定は次のとおりです。

1. 「update settings」で、複数の ring を作れる（deployment plans にも同様の仕組みを入れる予定と述べられている）
2. 例として、対象のユーザー、自動更新にするか、**2 日後に展開する**か、といった設定を ring に定義できる
3. deadline は、更新にどれだけ攻めるかによって、設定してもしなくてもよい
4. 通常の assignments を設定する
5. ポリシーは複数作れる。app catalog で、**リリースのたびに自動更新したいアプリ**と、より細かく制御したいアプリを選ぶ

デモの例では、自動更新を有効にしたいものはそのまま有効にし、特定のアプリ（Dropbox が選ばれ、「AWS managed」も選ぶかもしれないと述べられています。後者の名称は音声認識の都合で確定できません）については自動更新を無効にしていました。無効にするのは、EAM の別の構成プロファイルで手動管理するためで、特定のバージョンに固定（pin）できます。この方法なら、依存しているクリティカルなアプリだけ手動でレビュー・承認し、それ以外は自動で最新に保てる、という使い分けです。

### 5.9 モデルとエージェント

ローカルのモデルの更新は、Windows Update 経由で配信される場合、**quality update の一部として、または単独で自動的に届き、autopatch のポリシーに従います**。また、MXC（音声認識の都合で表記が確定できません）を使う場合、デバイス上で動作するエージェントが**何にアクセスできるかを管理**できるようになった、と登壇者が「特に面白い」と紹介しています。詳細はこのセッションでは示されていません。

### 5.10 Q&A

参加者からの質問と回答の要点です。

| 質問 | 回答 |
| ---- | ---- |
| Windows drivers の画面で、適用可能なデバイス数は出るが、そのデバイスを一覧できない。追加される？ | 今回のリリースには含まれないが、取り組んでいる。現在は Graph API で取得でき、UX にはまだ出していない。近日中に来る予定 |
| 手動でインストールされたアプリを発見して、このツールで削除できる？ | 検討している。今見せた機能では削除できない。app inventory を進めており、Windows に正しく登録されたものに限らず、デバイスで見つかったアプリの一覧を見られるようにするのが次のステップ。長期的には削除につなげたい |
| compliance policy で、アプリのバージョン（例：7-zip が 25.0 未満なら非準拠）を扱えるか？ | 管理対象のアプリ（managed apps）なら、より多くの制御が使える。特定のバージョンや最小バージョンへの pin などが可能。自動更新は、細かく管理したくないアプリのためのもので、そうした制御はしない |
| EAM カタログにないアプリはどう管理する？（質問者の環境では約 20% がカタログにあり、80% は無い） | Windows 側で Microsoft Store と連携し、**trusted catalog** を拡充している。現在の preview は一部のアプリだけで、20% を超えて、より広い範囲を含めることを目指している |
| extension drivers は現在管理できない。変更の予定は？ | 予定があるとの回答 |
| サーバー OS はどこまで対応する？ | プレビューとして「Server 2028」が発表されている（発話どおり。音声認識の都合で表記が確定できません）。どこまで遡るかは未発表。サーバーの更新には時間がかかるため、down-level への対応の必要性は認識している |
| 1 台のデバイスだけを、ring 全体を戻さず pause / uninstall したい。グループを 1 台だけで作らずにできる？ | 今できる方法があるか考えて、後で連絡すると回答 |
| GCC High ではいつ使える？ | 「Soon」。認証の一部を完了し、あと 1 つを待っている。長くかかっていることを謝罪 |
| web apps も可視化する予定は？ | 回答は分からない。セッション後に声をかければ調べて答える |
| WDAC と compliance policy の違いは？（アプリの実行を防ぎたいが、デバイスをブロックしたくない） | 最大の違いは、ここでの目的が、バージョンを最新に保って compliant にすること。アプリが何をするか、何にアクセスできるかを管理するものではない。WDAC のポリシーは別になるが、同じ場所に置きたいという要望は理解している |
| ring 展開中に、サードパーティ製品でパフォーマンスやリグレッションをテストする連携は？ | サードパーティが Graph API を使っているかどうかで、得られるものが変わる。デプロイの品質やリグレッションを見るレポートがある。device health への投資を増やしており、クラッシュ、パフォーマンス、CPU、バッテリー寿命などを見られるようにしていく。現時点は、更新のインストール、脆弱性、アプリに関するものと、新しい OS reliability レポート。OS reliability は Graph API にも出るので、サードパーティも利用できる |
| アプリのパッチ管理の最終目標は、Patch My PC のようなサードパーティ製品の置き換えか？ | 最終目標は、お客様がどこにいても必要な手段を持てるようにすること。サードパーティのパートナーや ISV を歓迎している。ただ、本来もっと前から提供されているべき機能は、Intune でも、Graph API を使うサードパーティ経由でも使えるようにしたい。パートナーも機能を進化させ続けるだろう |
| Autopatch で feature update を配ると 90% に配信されると読んだ。約 10% に提供されない。100% にする設定が無い | feature update には **safeguards** があり、自動で適用される。更新後に問題（メジャーバージョンのクラッシュやリグレッションなど）が起きそうなデバイスを検出し、問題が修正されるまで更新をブロックする。修正に 1 年かかるなど長期間になることもあり、更新がオンラインになってから想定より長い期間をかけて届くことがある。safeguard は無効にでき、影響しているデバイスの **safeguard hold** も確認できる。方法はその場で示せると述べている |

最後にスライドの撮影を呼びかけ、preview への参加方法が案内されました。サーバーの preview には、Windows experience controls にメッセージを送ると、参加を支援してもらえるとのことです（連絡先の名称は音声認識の都合で表記が確定できません）。ドライバー、extension controls、デバイス単位の制御など、まだやるべきことが多いとも述べています。

## 6. デモ・手順

このセッションでは、**ほぼ全編が Intune 管理画面のライブデモ**です。録音は音声のみのため、画面上のボタン名や配置の細部は分かりません。以下は語られた順の流れです。

1. Intune の Windows Update の画面（auto patch の管理体験）から始める
2. update rings で、Allow other Microsoft product updates、Windows drivers、Quality updates（deferral は 0 など）、deadline（1 日）と grace period（2 日）、Allow auto reboot before deadline、feature updates、reset to default を順に確認する
3. quality update policy を作成し、security updates の自動承認、hotpatch、quick machine recovery の手動承認を設定する
4. ポリシー一覧の「review すべき release」を開き、out-of-band の更新を承認する流れを見せる。.NET の手動承認にも触れる
5. ドライバーの手動管理に触れる
6. New experience controls をテンプレートとして作成し、discovery ページを確認する
7. レポートの Windows autopatch 概要を開き、状態の概要、ポリシー、バージョンを確認する
8. OS reliability レポート（preview）で、スパイク、stop error code、failure module name、影響デバイス、reliability by updates を順に確認する
9. Managed devices の下で maintenance windows policy を作成し、適用範囲（install and restart を選択）、開始日・時刻、期間、繰り返し（曜日の指定）を設定する（開始時刻の入力ではうまくいかず、やり直しがあった）
10. app catalog の update settings で、ring を作成し、自動更新・2 日後の展開・deadline・assignments を設定する。個別のアプリ（Dropbox など）で自動更新を有効・無効にする

## 7. 注意点・発表者の気付き

- **Deadline は攻めた設定に。** 1 日の deadline と 2 日の grace period が目安として挙げられました。
- **ドライバーの改善は今回はない。** 要望が多く、近日中に多数が来るが、今日の発表はありません。
- **restart only は避ける傾向。** 2027 年の初めから導入される「月に 1 回の再起動」で、再起動が必要なドライバーは OS のセキュリティ更新と同時に処理されるため。
- **maintenance windows の duration は余裕を持って。** install と restart の両方を行うなら 2 時間が、成功の可能性が最も高い。
- **自動更新は細かい制御の代わりにならない。** バージョン固定や最小バージョンの指定は、managed apps で行います。自動更新は、細かく管理しなくてよいアプリ向けです。
- **feature update は safeguard により、すべての端末に一度には届かないことがある。** 問題が修正されるまで更新が止められ、期間は長期（1 年のような例）になりうるとされています。
- **「Other Microsoft product updates」で更新されるものは少ない。** 設定は任意です。
- **現時点で提供されていないもの。** ドライバー画面の対象デバイス一覧（Graph API のみ）、アプリのアンインストール、web apps の可視化の回答（不明）、GCC High での提供（認証の最終段階）、1 台単位の pause / uninstall（その場では回答なし）。

## 8. 利用者にとっての利点

- update rings の推奨設定で、通常の PC をハンズオフで管理できる
- quality update policy で、security updates は自動承認のまま、out-of-band や preview の更新を選んで展開できる（プリンターや VPN の修正を急ぎたい場合など）
- Hotpatch により、baseline の月を除いて、security updates を再起動なしで受け取れる
- OS reliability レポートで、更新に関連するクラッシュや stop error code を、影響デバイスまで 1 か所でたどれる
- maintenance windows で、ラボや病院のような重要な端末の更新時間帯を制御できる
- 2027 年初めからの月 1 回の再起動により、再起動の回数が目に見えて減る
- app updates で、ユーザー自身が入れたアプリも自動更新でき、重要なアプリだけをバージョン固定できる

## 9. まとめ

Windows の更新管理は、Windows Update for Business、Windows Update Client Policies、Autopatch がすべて同じもので、まず update rings で deadline（1 日）と grace period（2 日）を攻めた設定にするのが出発点です。細かい制御が必要なら、quality update policy（security updates の自動承認、hotpatch、quick machine recovery の手動承認）と、maintenance windows を使い分けます。

更新の健全性は、autopatch のレポートと、新しい OS reliability レポート（preview）で確認します。アプリは Intune Enterprise App Management の app updates で、自動更新と pin による手動管理を組み合わせます。サーバーやドライバー、アプリの inventory などは、preview または今後の予定とされています。登壇者は、フィードバックと preview への参加を呼びかけて締めくくりました。
