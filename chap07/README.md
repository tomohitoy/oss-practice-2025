# 第7回：GitHubとコラボレーション実習

## この回で学ぶこと

この回では，Gitの「リモートリポジトリ」と「GitHub」について学びます．
これまで学んだGitは，すべて自分のPC（ローカル）で完結していましたが，実際の開発では他の人と共同作業を行います．
GitHubを使うことで，世界中の開発者と一緒にソフトウェアを開発できるようになります．

## Gitとリモートリポジトリ

### Gitは「道具」，GitHubは「場所」

これまで学んだGitは，皆さんのPC，つまり**ローカル**で動く「道具（ソフトウェア）」でした．
`git commit` や `git branch` などのコマンドは，すべて自分のPCの中で完結していましたね．

しかし，開発は一人では行いません．そのローカルの履歴を，他の人と共有し，共同作業する必要があります．

その履歴をインターネット上で預かってくれる「場所（Webサービス）」，それが**GitHub**です．

### GitHubの役割

GitHubは単にコードを保存するだけでなく，以下のような機能を提供しています：

- **コードの保存**：Gitリポジトリをインターネット上にホスティング
- **Issues**：バグ報告や機能要望などの課題管理
- **Pull Requests**：「こんな修正をどうですか？」という変更の提案
- **Discussions**：開発者同士の議論や情報交換

## OSS開発の基本フロー

### 3種類のリポジトリ

OSS（オープンソースソフトウェア）開発では，いきなり他人のリポジトリに書き込むことはできません．
そこで，以下の3つのリポジトリを区別します：

1. **Upstream（大本）**：公式の元となるリポジトリ（例：教員のリポジトリ）
2. **Origin（自分）**：Upstreamを自分のGitHubアカウントにコピーしたもの
3. **Local（PC）**：自分のPCにダウンロードしたリポジトリ

### OSSへの貢献：5ステップ

OSS開発の基本的な流れは以下の5ステップです：

1. **Fork**（Upstream → Origin）：大本を自分のGitHubにコピー
2. **Clone**（Origin → Local）：自分のGitHubからPCにダウンロード
3. **Branch & Commit**（Local）：ブランチを作成して作業
4. **Push**（Local → Origin）：PCから自分のGitHubにアップロード
5. **Pull Request**（Origin → Upstream）：大本に変更を提案

**この回では，ステップ1〜4を実習します．**（ステップ5は次回の講義で学びます）

### なぜForkが必要なのか？

なぜ教員のリポジトリを直接Cloneせずに，一度Fork（自分のGitHubにコピー）するのでしょうか？

**答え：書き込み権限がないからです．**

他人のリポジトリには書き込み権限がないため，直接CloneしてもPushできません．
そのため，まず自分のGitHubアカウントにForkして，自分専用のコピーを作る必要があります．

## 認証について（SSH鍵）

### なぜ認証が必要か？

皆さんがGitHubに `git push` する時，GitHubは「本当にあなた本人か？」を確認する必要があります．
その認証に**SSH鍵**を使います．

### SSH鍵の仕組み

SSH鍵は，「公開鍵」と「秘密鍵」のペアで構成されています：

- **公開鍵（`.pub`ファイル）**：「南京錠」のようなもの．GitHubに預けておく
- **秘密鍵**：「家の鍵」のようなもの．自分のPCに厳重に保管

この鍵のペアを持っている人だけが，GitHubに認証できる仕組みです．

**重要：秘密鍵は絶対に他人に漏らしてはいけません！**

---

## 実習を始める前に（セットアップ）

この実習は `main-07` ブランチで行います。
まず，実習用のブランチに移動しましょう。

### ステップ1：変更を保存（念のため）

もしまだコミットしていない変更がある場合は，先に保存しましょう：

```bash
git status
```

もし赤い文字でファイル名が表示されたら，以下を実行して変更を保存してください：

```bash
git add .
git commit -m "作業を保存"
```

「nothing to commit, working tree clean」と表示された場合は，何もする必要はありません。次に進んでください。

### ステップ2：最新のリモートブランチを取得

以下のコマンドをコピーして，ターミナルで実行してください：

```bash
git fetch origin
```

### ステップ3：実習ブランチに移動

以下のコマンドをコピーして，ターミナルで実行してください：

```bash
git switch main-07
```

「Switched to a new branch 'main-07'」または「Switched to branch 'main-07'」と表示されればOKです！

### ステップ4：確認

正しいブランチにいるか確認しましょう：

```bash
git branch
```

`* main-07` のように，main-07の前に `*` がついていれば成功です。

これで実習の準備が整いました！

---

## 実習1：GitHubアカウントの作成

### ステップ1-A：アカウント作成

もしまだGitHubアカウントを持っていない場合は，以下の手順で作成しましょう．

#### 1. GitHubにアクセス

ブラウザで以下のURLを開きます：

```
https://github.com
```

#### 2. サインアップ

- 右上の「Sign up」ボタンをクリック
- メールアドレスを入力
- パスワードを設定
- ユーザー名を決める

**ポイント**：ユーザー名は，今後OSS活動で使っていく可能性もあるので，公の場で見られても良い名前をお勧めします．

#### 3. メール認証

- 登録したメールアドレスに確認メールが届きます
- メール内のリンクをクリックして，アカウントを有効化します

#### 4. アカウント作成完了

これでGitHubアカウントの作成は完了です！

---

## 実習2：SSH鍵の生成と登録（重要）

この実習は**非常に重要**です．ここで設定を間違えると，後の実習ができなくなります．
落ち着いて，一つずつ確認しながら進めましょう．

### ステップ2-A：ターミナルの準備

#### Windowsの場合

「Git Bash」を開いてください．

#### Mac / Linuxの場合

「ターミナル」アプリを開いてください．

---

### ステップ2-B：SSH鍵のペアを生成する

#### 1. ホームディレクトリに移動

まず，自分のホームディレクトリに移動します：

```bash
cd ~
```

#### 2. 現在地を確認

今どこにいるか確認しましょう：

```bash
pwd
```

`/Users/あなたのユーザー名` （Mac）または `/home/あなたのユーザー名` （Linux）のように表示されればOKです．

#### 3. .ssh ディレクトリの確認と作成

`.ssh` ディレクトリが存在するか確認します：

```bash
ls -la | grep .ssh
```

もし何も表示されない場合は，`.ssh` ディレクトリを作成します：

```bash
mkdir -p ~/.ssh
```

既に `.ssh` ディレクトリが存在する場合は，上記のコマンドを実行しても問題ありません（何も起こりません）．

#### 4. .ssh ディレクトリに移動

```bash
cd ~/.ssh
```

または

```bash
cd .ssh
```

#### 5. 現在地を確認

```bash
pwd
```

`/Users/あなたのユーザー名/.ssh` のように表示されればOKです．

#### 6. 既存の鍵ファイルを確認

既に鍵が存在するか確認します：

```bash
ls -la
```

`id_ed25519` や `id_ed25519.pub` というファイルが既に存在する場合は，すでに鍵が作成されています．
その場合は，**ステップ2-C（公開鍵の登録）に進んでください．**

何も表示されない場合，または他のファイルしか表示されない場合は，次に進んでください．

#### 7. 鍵生成コマンドを実行

以下のコマンドを実行します．**メールアドレスは，GitHubに登録したものに置き換えてください**：

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

例：

```bash
ssh-keygen -t ed25519 -C "taro.yamada@example.com"
```

#### 8. ファイルの保存場所を確認

```
Enter file in which to save the key (/Users/yourname/.ssh/id_ed25519):
```

このように聞かれたら，**何も入力せず，そのままEnterキーを押してください．**

#### 9. パスフレーズの入力

```
Enter passphrase (empty for no passphrase):
```

今回は実習のため，**何も入力せず，そのままEnterキーを押してください．**

もう一度聞かれますので，**再度Enterキーを押してください．**

```
Enter same passphrase again:
```

#### 10. 完了

以下のようなメッセージが表示されれば，鍵の生成は成功です：

```
Your identification has been saved in /Users/yourname/.ssh/id_ed25519
Your public key has been saved in /Users/yourname/.ssh/id_ed25519.pub
```

#### 11. 生成された鍵ファイルを確認

```bash
ls -la
```

以下の2つのファイルが表示されればOKです：

- `id_ed25519`：秘密鍵（絶対に他人に見せない！）
- `id_ed25519.pub`：公開鍵（これをGitHubに登録します）

---

### ステップ2-C：公開鍵をGitHubに登録する

#### 1. .ssh ディレクトリにいることを確認

まず，現在地を確認します：

```bash
pwd
```

`/Users/あなたのユーザー名/.ssh` のように表示されればOKです．

もし違う場所にいる場合は，以下を実行してください：

```bash
cd ~/.ssh
```

#### 2. 公開鍵の内容を表示

以下のコマンドで，公開鍵（南京錠）の中身を表示します：

```bash
cat id_ed25519.pub
```

または，フルパスでも表示できます：

```bash
cat ~/.ssh/id_ed25519.pub
```

#### 3. 公開鍵をコピー

`ssh-ed25519` で始まる文字列が表示されます．
**この文字列をすべてコピーしてください．**（最後のメールアドレスまで含めて）

例：

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJqJzCl... taro.yamada@example.com
```

**重要**：必ず `ssh-ed25519` から始まり，メールアドレスで終わる**全体**をコピーしてください．

#### 4. GitHubの設定ページを開く

ブラウザでGitHubにログインし，以下の手順で進みます：

1. 右上の自分のアイコンをクリック
2. 「Settings」を選択
3. 左サイドバーの「SSH and GPG keys」を選択

#### 5. SSH鍵を追加

1. 「New SSH key」ボタンをクリック
2. **Title**：分かりやすい名前をつける（例：「My Laptop」「University PC」など）
3. **Key**：先ほどコピーした公開鍵を貼り付け
4. 「Add SSH key」ボタンをクリック

#### 6. パスワードの確認

GitHubのパスワードを求められる場合があります．
入力して確認してください．

---

### ステップ2-D：接続テスト

#### 1. 接続テストコマンドを実行

以下のコマンドで，SSH接続が正しく設定されたかテストします：

```bash
ssh -T git@github.com
```

#### 2. 初回接続の確認

初回接続時のみ，以下のようなメッセージが表示されます：

```
The authenticity of host 'github.com (...)' can't be established.
...
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

**`yes` とタイプして，Enterキーを押してください．**

#### 3. 成功メッセージの確認

以下のようなメッセージが表示されれば，**認証設定は成功です！**

```
Hi [your-username]! You've successfully authenticated, but GitHub does not provide shell access.
```

#### トラブルシューティング

もし `Permission denied` などのエラーが出た場合：

- ステップ2-Cで，公開鍵を正しくコピー＆ペーストできたか確認
- 公開鍵（`.pub`ファイル）の内容をGitHubに登録したか確認（秘密鍵ではない）
- ターミナルを再起動して，もう一度試してみる

---

## 実習3：OSSフロー実践（Fork, Clone, Push）

いよいよ，OSS開発の基本フローを実践します！

### ステップ3-A：教員のリポジトリを「Fork」する

#### 1. ひな形リポジトリのURLを開く

以下の「ひな形リポジトリ」のURLをブラウザで開きます：

```
https://github.com/tomohitoy/oss-template-2025
```

ブラウザのアドレスバーに上記のURLをコピー＆ペーストして，Enterキーを押してください．

#### 2. Forkボタンを押す

リポジトリページの右上にある「**Fork**」ボタンをクリックします．

#### 3. Fork先を確認

- **Owner**：自分のGitHubアカウント名が選択されていることを確認
- **Repository name**：そのままでOK
- 「**Create fork**」ボタンをクリック

#### 4. Forkの完了

自動的に自分のGitHubアカウントのページに移動します．
これで，自分専用のコピー（Origin）が作成されました！

---

### ステップ3-B：自分のリポジトリを「Clone」する

#### 1. SSH URLをコピー（重要！）

自分のGitHubリポジトリページ（Forkしたページ）で：

1. 緑色の「**Code**」ボタンをクリック
2. **「SSH」タブを選択**（重要！HTTPSではない）
3. `git@github.com:...` で始まるURLをコピー

**⚠️ 重要な注意事項**：

- **必ず「SSH」タブを選択してください**
- 「HTTPS」タブは選択しないでください
- コピーするURLは `git@github.com:` で始まる必要があります
- `https://github.com/` で始まるURLは使用できません

**正しいSSH URL**：

```
git@github.com:your-username/oss-template-2025.git
```

**間違ったHTTPS URL（使わない）**：

```
https://github.com/your-username/oss-template-2025.git
```

#### 2. ターミナルを開く（または同じターミナルを使用）

もしターミナルを閉じている場合は，再度開いてください．

#### 3. 作業ディレクトリに移動

まず，リポジトリをどこにダウンロードするか決めます．
ホームディレクトリに移動しましょう：

```bash
cd ~
```

現在地を確認：

```bash
pwd
```

**ポイント**：ここでは説明のためホームディレクトリを使いますが，自分の好きな場所（例：`~/Desktop`，`~/Documents` など）でも構いません．

もしデスクトップにダウンロードしたい場合は：

```bash
cd ~/Desktop
```

#### 4. 現在のディレクトリの内容を確認

```bash
ls
```

これで，後でどこにダウンロードされたか分かりやすくなります．

#### 5. Cloneコマンドを実行

以下のコマンドを実行します．**[コピーしたURL]の部分は，先ほどコピーしたSSH URLに置き換えてください**：

```bash
git clone [コピーしたURL]
```

例：

```bash
git clone git@github.com:your-username/oss-template-2025.git
```

**⚠️ エラーが出た場合**：

もし以下のようなエラーが出た場合は，HTTPS URLを使ってしまっている可能性があります：

```
remote: Invalid username or token.
fatal: Authentication failed for 'https://github.com/...'
```

この場合は，**ステップ1に戻って，SSH URLを正しくコピーし直してください**．

#### 6. Cloneの完了を確認

以下のようなメッセージが表示されれば成功です：

```
Cloning into 'oss-practice-2025-students'...
remote: Enumerating objects: 10, done.
remote: Counting objects: 100% (10/10), done.
...
```

#### 7. ダウンロードされたことを確認

```bash
ls
```

新しくリポジトリ名のフォルダ（ディレクトリ）が表示されるはずです．

#### 8. リポジトリに移動

Cloneが完了したら，そのディレクトリに移動します：

```bash
cd [リポジトリ名]
```

例：

```bash
cd oss-template-2025
```

#### 9. 現在地を確認

```bash
pwd
```

`/Users/あなたのユーザー名/oss-template-2025` のように表示されればOKです．

#### 10. リポジトリの中身を確認

```bash
ls
```

リポジトリ内のファイルやディレクトリが表示されます．`keywords` というディレクトリがあることを確認してください．

---

### ステップ3-C：ブランチを作成

#### 1. リポジトリ内にいることを確認

まず，現在地を確認します：

```bash
pwd
```

リポジトリ名（例：`/Users/あなたのユーザー名/oss-template-2025`）が表示されればOKです．

もし違う場所にいる場合は，リポジトリのディレクトリに移動してください：

```bash
cd ~/oss-template-2025
```

#### 2. 現在のブランチを確認

```bash
git branch
```

`* main` または `* master` のように表示されるはずです．これがデフォルトのブランチです．

#### 3. 新しいブランチを作成

第6回で学んだように，必ず新しいブランチを作成します．

```bash
git branch add-my-keyword
```

**ポイント**：`add-my-keyword` は，「自分のキーワードを追加する」という意味のブランチ名です．

#### 4. 作成したブランチに移動

```bash
git switch add-my-keyword
```

#### 5. ブランチの確認

```bash
git branch
```

以下のように表示されればOKです：

```
* add-my-keyword
  main
```

`*` マークが `add-my-keyword` についていることを確認してください．

---

### ステップ3-D：ファイルを作成してコミット

#### 0. キーワードの選択（重要）

**まず最初に，講義で共有されたエクセルファイルからキーワードを選びます．**

1. 講義資料で提供される共有エクセルファイルにアクセスする
2. キーワードリストから担当したいキーワードを選ぶ
3. 担当者列に自分の名前を記入する
4. **エクセルに記載されているファイル名を確認する**（例：`open-source.md`）

**注意**：エクセルに記載されているファイル名を必ず使用してください．

#### 1. keywords ディレクトリに移動

まず，`keywords` ディレクトリに移動します：

```bash
cd keywords
```

現在地を確認：

```bash
pwd
```

`/Users/あなたのユーザー名/oss-template-2025/keywords` のように表示されればOKです．

#### 2. keywords ディレクトリの中身を確認

```bash
ls
```

もしかすると，既に他の学生のファイルが表示されるかもしれません．

#### 3. 指定されたファイル名でファイルを作成

**エクセルで確認したファイル名でファイルを作成します．**例えば，ファイル名が `open-source.md` の場合：

```bash
touch open-source.md
```

**重要**：ファイル名は，**必ずエクセルに記載されている名前を使用してください．**

#### 4. ファイルが作成されたことを確認

```bash
ls
```

指定されたファイル名のファイルが表示されればOKです．

#### 5. ファイルを編集

作成したファイルをテキストエディタで開き，以下の内容を記入して保存してください：

**まず，以下のテンプレートをコピーしてファイルに貼り付けましょう：**

```markdown
# [選んだキーワード]

## 説明

[キーワードの解説を200文字以上で記入]

## 参考文献

- [参考にしたサイトのタイトル](URL)
- [参考にしたサイトのタイトル](URL)

## 作成者

- 氏名: [あなたの氏名]
- 学籍番号: [あなたの学籍番号]
- 作成日: YYYY-MM-DD
- 最終更新日: YYYY-MM-DD
```

**記入例（参考）：**

```markdown
# オープンソース

## 説明

オープンソースとは，ソフトウェアのソースコードが一般に公開されており，
誰でも自由に利用，修正，再配布できるライセンス形態のことです．

このオープンソースの理念により，世界中の開発者が協力して
より良いソフトウェアを作り上げることができます．

また，コードが公開されているため，セキュリティ上の問題も発見されやすく，
多くの人の目によってソフトウェアの品質が向上していきます．

代表的なオープンソースソフトウェアとしては，
Linux，Git，Python，Apache，MySQLなどがあり，
現代のインターネットやソフトウェア開発において不可欠な存在となっています．

## 参考文献

- [オープンソースの定義 - Open Source Initiative](https://opensource.org/osd)
- [オープンソースソフトウェア - Wikipedia](https://ja.wikipedia.org/wiki/オープンソースソフトウェア)

## 作成者

- 氏名: 山田太郎
- 学籍番号: B1234567
- 作成日: 2025-11-06
- 最終更新日: 2025-11-06
```

**自分の情報に書き換えてください！**

**重要**：

- タイトル（`#` の見出し）は，エクセルで選んだキーワードにしてください
- `## 説明` の部分に，キーワードの解説を**自分の言葉で200文字以上**書いてください
- `## 参考文献` の部分に，参考にしたWebサイトやドキュメントのリンクを追加してください（最低1つ以上）
- `## 作成者` の部分に，自分の氏名・学籍番号・作成日・最終更新日を箇条書きで書いてください

**課題の評価基準**：

- **解説の文字数**：200文字以上で評価が高くなります
- **参考文献**：自分で調べた内容とその引用リスト（リンク）を含めると，より高い評価になります
- **キーワードの数**：余裕がある人は，2つ目のキーワードにも挑戦してみましょう

#### 6. ファイルの内容を確認

```bash
cat open-source.md
```

（`open-source.md` は自分が作成したファイル名に置き換えてください）

正しく記入されているか確認しましょう．

**確認ポイント**：

- タイトル（`#`）はキーワード名になっているか
- 説明は200文字以上あるか
- 参考文献は最低1つ以上あるか
- 作成者情報（氏名・学籍番号）は正しいか

#### 7. リポジトリのルートディレクトリに戻る

```bash
cd ..
```

現在地を確認：

```bash
pwd
```

`/Users/あなたのユーザー名/oss-template-2025` のように表示されればOKです．
（リポジトリのルートに戻っています）

#### 8. 変更内容を確認

```bash
git status
```

`keywords/open-source.md` のようなファイルが新しく作成されたことが表示されます（赤い文字で表示されます）．

詳しく確認する場合：

```bash
git diff
```

新しいファイルの内容を表示する場合：

```bash
cat keywords/open-source.md
```

（`open-source.md` は自分が作成したファイル名に置き換えてください）

#### 9. ステージングとコミット

```bash
git add .
git commit -m "Add keyword explanation: open-source"
```

**重要**：コミットメッセージの `open-source` の部分は，**自分のキーワードファイル名に置き換えてください．**

例：

- オープンソースの場合：`git commit -m "Add keyword explanation: open-source"`
- Gitの場合：`git commit -m "Add keyword explanation: git"`
- ブランチの場合：`git commit -m "Add keyword explanation: branch"`

---

### ステップ3-E：リモート（Origin）へ「Push」する

いよいよ，ローカルPCのコミットを，GitHub（Origin）にアップロードします！

#### 1. 現在のブランチを確認

念のため，現在のブランチを確認します：

```bash
git branch
```

`* add-my-keyword` と表示されることを確認してください．

#### 2. Pushコマンドを実行

```bash
git push origin add-my-keyword
```

**コマンドの意味**：

- `git push`：アップロードするコマンド
- `origin`：Cloneしたときに自動設定されたリモート（自分のFork）の名前
- `add-my-keyword`：今いるブランチ名

#### 3. 実行結果を確認

実習2のSSH認証が成功していれば，以下のようなメッセージが表示されます：

```
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
...
To github.com:your-username/oss-template-2025.git
 * [new branch]      add-my-keyword -> add-my-keyword
```

エラーが出た場合は，実習2の認証設定を確認してください．

#### 4. トラブルシューティング

もし以下のようなエラーが出た場合：

**エラー1：「Permission denied (publickey)」**

```
ERROR: Permission denied (publickey).
```

→ 実習2のSSH鍵の設定を確認してください
→ `ssh -T git@github.com` でテストしてみてください

**エラー2：「Invalid username or token」または「Authentication failed」**

```
remote: Invalid username or token.
fatal: Authentication failed for 'https://github.com/...'
```

→ これは**HTTPS URLを使ってしまっている**ことが原因です
→ 対処法：リモートURLをSSHに変更する必要があります

リモートURLを確認：

```bash
git remote -v
```

もし `https://` で始まるURLが表示された場合は，以下のコマンドで修正してください：

```bash
git remote set-url origin git@github.com:your-username/oss-template-2025.git
```

（`your-username` は自分のGitHubユーザー名に置き換えてください）

修正後，再度確認：

```bash
git remote -v
```

`git@github.com:` で始まるURLになっていればOKです．その後，再度pushしてください．

---

### ステップ3-F：GitHubでの確認

#### 1. ブラウザでリポジトリページを開く

自分のGitHubリポジトリ（Origin）のページをブラウザで開きます．
（または，既に開いている場合は再読み込みします）

#### 2. 成功の確認

以下のような黄色い通知バーが表示されれば，**Pushは成功です！**

```
add-my-keyword had recent pushes X minutes ago
[Compare & pull request] ボタン
```

#### 3. ブランチの確認

リポジトリページ上部の「main」と書かれたボタン（ブランチ切り替え）をクリックすると，`add-my-keyword` ブランチが表示されるはずです．

#### 4. ファイルの確認

`add-my-keyword` ブランチに切り替えて，`keywords` ディレクトリを開き，自分が作成したファイル（例：`open-source.md`）が正しくアップロードされているか確認しましょう．

**これで，次回の講義（Pull Request）の準備はすべて整いました！**

---

## 課題の詳細と評価基準

### 課題：キーワード解説の作成

この実習で作成したファイルが，第7回の課題となります．

### 提出方法

ブラックボードの指定された課題に，**作成したブランチのkeywordsディレクトリへの直接リンク（URL）を提出**してください．

#### ステップ1：URLを作成する

以下のパターンでURLを作成します：

```
https://github.com/[あなたのGitHubユーザー名]/oss-template-2025/tree/add-my-keyword/keywords
```

**例**：

- GitHubユーザー名が `taro-yamada` の場合
- ブランチ名が `add-my-keyword` の場合

```
https://github.com/taro-yamada/oss-template-2025/tree/add-my-keyword/keywords
```

**重要**：

- `[あなたのGitHubユーザー名]` を自分のGitHubユーザー名に置き換えてください
- `add-my-keyword` を自分が作成したブランチ名に置き換えてください（通常は `add-my-keyword`）

#### ステップ2：URLの確認

作成したURLをブラウザで開いて，以下を確認してください：

- ✅ 自分のリポジトリの `keywords` ディレクトリが表示される
- ✅ ブランチ名が `add-my-keyword`（または自分が作成したブランチ名）になっている
- ✅ 自分が作成したファイル（例：`open-source.md`）が表示されている

#### ステップ3：ファイルの内容を確認

`keywords` ディレクトリに表示されている自分のファイルをクリックして，内容を確認してください：

- ✅ キーワード名（タイトル）が正しく表示されている
- ✅ 説明が200文字以上ある
- ✅ 参考文献が記載されている
- ✅ 作成者情報（氏名・学籍番号）が正しい

#### ステップ4：ブラックボードに提出

確認したURLを，ブラックボードの指定された課題に提出してください．

**提出するURL例**：

```
https://github.com/taro-yamada/oss-template-2025/tree/add-my-keyword/keywords
```

**注意事項**：

- URLは必ず自分のGitHubアカウントのもの（`あなたのユーザー名/oss-template-2025`）を提出してください
- 元のリポジトリ（`tomohitoy/oss-template-2025`）のURLではありません
- ブランチ名は `main` ではなく，作成したブランチ名（通常は `add-my-keyword`）にしてください

#### トラブルシューティング

**「404 Page not found」と表示される場合**：

1. GitHubユーザー名が正しいか確認
2. リポジトリ名が `oss-template-2025` になっているか確認
3. ブランチ名が正しいか確認（`add-my-keyword`）
4. 実習3-Eで `git push` が成功しているか確認

**ファイルが表示されない場合**：

1. ブランチが `main` ではなく `add-my-keyword` になっているか確認
2. `git push` が成功しているか確認（実習3-E）
3. GitHubのリポジトリページで，ブランチを切り替えて確認

### 評価基準（ルーブリック）

この課題は以下の3つの観点で評価されます：

#### 1. 実習の手続き（できた/できなかった）

- ✅ **クリア**：Forkの後，自分のoriginに所定の名前のブランチを作成し，そのブランチに作成したキーワードの解説ファイルをコミット・プッシュできている
- ❌ **未達成**：上記の手順が完了していない

#### 2. キーワードの数

- キーワードを1つ作成（基本）
- キーワードを2つ作成（より高い評価）

#### 3. 解説の内容（4段階評価）

- **レベル1**：100文字未満の解説
- **レベル2**：100文字以上の解説
- **レベル3（期待水準）**：200文字以上の解説
- **レベル4**：200文字以上の解説 + 自分で調べた内容とその引用リスト（参考文献）を含む

**目標**：レベル3以上を目指しましょう！

---

## まとめ

### この回で学んだコマンド

- **`ssh-keygen -t ed25519 -C "email"`**：SSH鍵のペアを生成
- **`cat ~/.ssh/id_ed25519.pub`**：公開鍵の内容を表示
- **`ssh -T git@github.com`**：SSH接続のテスト
- **`git clone [URL]`**：リモートリポジトリをPCにコピー
- **`git push origin [ブランチ名]`**：ローカルのコミットをリモートにアップロード

### この回で学んだこと

1. **Git**は「道具（ソフトウェア）」，**GitHub**は「場所（Webサービス）」
2. OSS開発では，**Upstream, Origin, Local** という3つのリポジトリを使う
3. **SSH鍵**は，公開鍵（南京錠）と秘密鍵（家の鍵）のペア
4. **Fork**で他人のリポジトリを自分のGitHubにコピーする
5. **Clone**で自分のGitHubからPCにダウンロードする
6. **Push**でPCから自分のGitHubにアップロードする

### 重要なポイント

- 秘密鍵は**絶対に他人に漏らさない**
- CloneするURLは**SSH形式**を使う（`git@github.com:...`）
- Forkするのは，書き込み権限がないため
- `git clone` すると，`origin` というリモートが自動設定される

---

## 発展：もっと学びたい人へ

### リモートの確認

自分のリポジトリに設定されているリモートを確認できます：

```bash
git remote -v
```

**出力例**：

```
origin  git@github.com:your-username/repository-name.git (fetch)
origin  git@github.com:your-username/repository-name.git (push)
```

### Upstreamの追加

元のリポジトリ（Upstream）をリモートとして追加することもできます：

```bash
git remote add upstream [元のリポジトリのURL]
```

これで，元のリポジトリの最新の変更を取得できるようになります：

```bash
git fetch upstream
```

### ブランチのPushとトラッキング

初めてブランチをPushする時，トラッキング設定を同時に行うこともできます：

```bash
git push -u origin [ブランチ名]
```

`-u` オプションをつけると，次回以降は `git push` だけでPushできるようになります．

### SSHの設定ファイル

`~/.ssh/config` ファイルを作成すると，SSH接続の設定をカスタマイズできます：

```
Host github.com
  User git
  IdentityFile ~/.ssh/id_ed25519
```

---

## 参考資料

- [GitHub公式ドキュメント - SSH接続について](https://docs.github.com/ja/authentication/connecting-to-github-with-ssh)
- [GitHub公式ドキュメント - リポジトリのフォーク](https://docs.github.com/ja/get-started/quickstart/fork-a-repo)
- [Git公式ドキュメント - リモートでの作業](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E5%9F%BA%E6%9C%AC-%E3%83%AA%E3%83%A2%E3%83%BC%E3%83%88%E3%81%A7%E3%81%AE%E4%BD%9C%E6%A5%AD)

お疲れさまでした！GitHubとの連携ができるようになりましたね．
次回は，Pull Requestの作成と，OSSプロジェクトへの貢献方法について学びます．
