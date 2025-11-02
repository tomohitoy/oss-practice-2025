# 第6回：Gitブランチ実習

## この回で学ぶこと

この回では，Gitの「ブランチ」機能について学びます．
ブランチを使うことで，複数の作業を並行して進めたり，メインの作業に影響を与えずに新しい機能を試したりできるようになります．

## ブランチとは？

ブランチとは，日本語で「枝」という意味です．
まるで木の幹から枝が分かれるように，メインの開発ラインから別の作業ラインを分岐させることができます．

### ブランチを使う理由

- **安全に新機能を開発できる**：メインの作業に影響を与えずに，新しい機能を試せる
- **複数の作業を並行できる**：バグ修正と新機能開発を同時に進められる
- **元に戻しやすい**：うまくいかなかったら，その枝を捨てればいい

## 実習を始める前に（セットアップ）

この実習は `main-06` ブランチで行います。
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

### 最新のリモートブランチを取得

以下のコマンドをコピーして，ターミナルで実行してください：

```bash
git fetch origin
```

「Already up to date.」と表示された場合は，何もする必要はありません。次に進んでください。

### ステップ2：実習ブランチに移動

以下のコマンドをコピーして，ターミナルで実行してください：

```bash
git switch main-06
```

「Switched to a new branch 'main-06'」または「Switched to branch 'main-06'」と表示されればOKです！

### ステップ3：確認

正しいブランチにいるか確認しましょう：

```bash
git branch
```

`* main-06` のように，main-06の前に `*` がついていれば成功です。

これで実習の準備が整いました！

---

## 実習1：並行作業シミュレーション（分岐の作成）

この実習では，main-06 ブランチから2つのブランチを作成し，それぞれで異なる作業を行います．

### ステップ1-A：新機能用ブランチでの作業

まず，学習記録を追加するためのブランチを作成します．

#### 1. ブランチを作成する

実習のディレクトリに移動します

```bash
cd chap06
```

ブランチを作成します

```bash
git branch feature-add-learning-record-md
```

このコマンドで `feature-add-learning-record-md` という名前のブランチが作成されます．
まだ移動はしていません．

#### 2. 作成したブランチに移動する

```bash
git switch feature-add-learning-record-md
```

**ポイント**：`git switch` は，ブランチを切り替えるコマンドです．

#### 3. 今いるブランチを確認

```bash
git branch
```

`feature-add-learning-record-md` の前に `*` マークがついていれば，そのブランチにいることがわかります．

#### 4. 新しいファイルを作成

`LEARNING_RECORD.md` というファイルを作成し，これまでの「オープンソースソフトウェア」の講義で学んだ内容のうち，いま記憶に残っていることを５つ，箇条書きにして記録しましょう．

以下のコマンドで作成できます：

```bash
touch LEARNING_RECORD.md
```

作成したファイルを開いて，記録をしましょう
以下の例をコピーして，箇条書きをします．

```markdown
# この講義で学んだこと
- 
- 
- 
- 
- 
```

#### 5. ファイルの中身を確認

```bash
cat LEARNING_RECORD.md
```

#### 6. 変更をステージング＆コミット

```bash
git add LEARNING_RECORD.md
git commit -m "feat: Add learning record"
```

**ポイント**：コミットメッセージの `feat:` は，「新機能（feature）を追加した」という意味の接頭辞です．

---

### ステップ1-B：バグ修正用ブランチでの作業

次に，別のブランチでドキュメントのtypoを修正します．
ここが重要なポイントです！

#### 1. main-06 ブランチに戻る

```bash
git switch main-06
```

#### 2. LEARNING_RECORD.md が消えたことを確認

```bash
ls
```

あれ？さっき作った `LEARNING_RECORD.md` がありませんね？
これは，`feature-add-learning-record-md` ブランチにいたときだけ存在するファイルだからです．
**ブランチごとに，ファイルの状態が隔離されています．**

#### 3. 新しいブランチを作成

main-06 ブランチから，今度はバグ修正用のブランチを作成します．

```bash
git branch bugfix-typos-on-doc-md
```

#### 4. バグ修正ブランチに移動

```bash
git switch bugfix-typos-on-doc-md
```

#### 5. DOC.md を確認

このディレクトリには `DOC.md` というファイルがあります．
中身を確認してみましょう：

```bash
cat DOC.md
```

このファイルには，いくつかのtypo（タイプミス）や間違いがあります．
探してみましょう．

#### 6. DOC.md を編集

typoを修正します．以下のいずれかの方法で編集してください：

**方法：テキストエディタで編集**

- テキストエディタで DOC.md を開き，誤字や間違いを修正しましょう．そして，保存しましょう．

#### 7. 修正内容を確認

```bash
cat DOC.md
```

または，修正箇所だけを確認：

```bash
git diff
```

#### 8. 変更をステージング＆コミット

```bash
git add DOC.md
git commit -m "fix: Fix typos in DOC.md"
```

**ポイント**：コミットメッセージの `fix:` は，「バグ修正」という意味の接頭辞です．

---

## 実習2：分岐の「可視化」

さて，ここまでで2つの異なるブランチで作業を行いました．
では，この履歴がどうなっているのか見てみましょう！

### ステップ2-A：git log でブランチの分岐を確認

#### すべてのブランチの履歴を表示

```bash
git log --oneline --graph --all
```

**オプションの意味**：

- `--oneline`：1行で簡潔に表示
- `--graph`：分岐をアスキーアートで表示
- `--all`：すべてのブランチを表示

#### 出力例

```
* a1b2c3d (bugfix-typos-on-doc-md) fix: Fix typos in DOC.md
| * e4f5g6h (feature-add-learning-record-md) feat: Add learning record
|/
* 9f2cabc (HEAD -> main-06) feat: Add chapter 6 - Git branch tutorial
```

**見方**：

- `*` がコミットを表しています
- `|` や `/` が分岐を表しています
- main-06 のコミットから，歴史が2つに分かれているのが見えます！

---

### ステップ2-B：ブランチ間の「差分」を確認

#### main-06 と feature-add-learning-record-md ブランチの差分

```bash
git diff main-06 feature-add-learning-record-md
```

これで，main-06 ブランチと feature-add-learning-record-md ブランチの間で何が違うかが表示されます．
`LEARNING_RECORD.md` が追加されていることがわかります．

#### main-06 と bugfix-typos-on-doc-md ブランチの差分

```bash
git diff main-06 bugfix-typos-on-doc-md
```

DOC.md の修正内容が表示されます．

---

## 実習3：探求：ブランチ（ポインタ）の「実体」

最後に，ブランチの「正体」を探求してみましょう．
実は，ブランチはとてもシンプルな仕組みで動いています．

### ステップ3-A：`.git/refs/heads` を覗く

#### ブランチファイルの一覧を表示

```bash
cd .. # 作業ディレクトリのルートに移動
ls -a # 作業ディレクトリのルートにいるかを確認
ls .git/refs/heads
```

**出力例**：

```
main
main-06
feature-add-learning-record-md
bugfix-typos-on-doc-md
```

見てください！私たちが作ったブランチ名のファイルがありますね．

---

### ステップ3-B：ポインタの中身を見る

#### main-06 ブランチの中身を確認

```bash
cat .git/refs/heads/main-06
```

**出力例**：

```
9f2cabc1234567890abcdef1234567890abcdef
```

これは**コミットID**（コミットハッシュ）です！

#### bugfix-typos-on-doc-md ブランチの中身を確認

```bash
cat .git/refs/heads/bugfix-typos-on-doc-md
```

**出力例**：

```
a1b2c3d567890abcdef1234567890abcdef1234
```

これも**コミットID**です！

#### bugfix-typos-on-doc-md の最新コミットを確認

```bash
git show [コミットID]
```

#### feature-add-learning-record-md ブランチの中身も確認

```bash
cat .git/refs/heads/feature-add-learning-record-md
```

**出力例**：

```
e4f5g6h890abcdef1234567890abcdef12345678
```

これも**コミットID**です！

#### feature-add-learning-record-md の最新コミットを確認

```bash
git show [コミットID]
```

### ステップ3-C：HEAD の中身を見る

最後に，`HEAD` という特殊なポインタの中身を見てみましょう．

```bash
cat .git/HEAD
```

**出力例**：

```
ref: refs/heads/bugfix-typos-on-doc-md
```

`HEAD` は，「今いるブランチ」を指すポインタです．
この例では，`bugfix-typos-on-doc-md` ブランチにいることがわかります．

別のブランチに切り替えて，もう一度確認してみましょう：

```bash
git switch main-06
cat .git/HEAD
```

**出力例**：

```
ref: refs/heads/main-06
```

`HEAD` の指す先が変わりましたね！

---

### ブランチの正体

つまり，**ブランチの正体とは，単にコミットIDが書かれたテキストファイル**だったのです！

- ブランチは「軽量なポインタ」と呼ばれます
- 実際には，特定のコミットを指し示す小さなテキストファイルに過ぎません
- `HEAD` は，「現在いるブランチ」を指すポインタです
- だからこそ，ブランチの作成や切り替えが非常に高速にできるのです

---

## まとめ

### この回で学んだコマンド

- **`git branch ブランチ名`**：新しいブランチを作成
- **`git switch ブランチ名`**：ブランチを切り替え
- **`git branch`**：ブランチの一覧を表示（`*` が現在のブランチ）
- **`git log --oneline --graph --all`**：ブランチの分岐を可視化
- **`git diff ブランチ1 ブランチ2`**：ブランチ間の差分を表示
- **`cat .git/refs/heads/ブランチ名`**：ブランチの実体（コミットID）を表示
- **`cat .git/HEAD`**：現在いるブランチを表示
- **`git show コミットID`**：コミットの詳細を表示

### ブランチの仕組み

1. ブランチは，コミットを指すポインタ
2. 実体は `.git/refs/heads` にあるテキストファイル
3. ブランチを切り替えると，作業ディレクトリのファイルも変わる
4. 複数のブランチで並行作業ができる
5. `HEAD` は，現在いるブランチを指す特殊なポインタ

---

## 発展：もっと学びたい人へ

### ブランチの削除

使わなくなったブランチは削除できます：

```bash
git branch -d ブランチ名
```

### ブランチの作成と移動を同時に行う

`git switch` に `-c` オプションをつけると，ブランチの作成と移動を一度にできます：

```bash
git switch -c 新しいブランチ名
```

### すべてのブランチを見る

```bash
git branch -a
```

これで，ローカルだけでなく，リモートのブランチも表示されます．

### ブランチの履歴をより詳しく見る

```bash
git log --oneline --graph --all --decorate
```

`--decorate` オプションで，ブランチやタグの情報がより詳しく表示されます．

---

## 参考資料

- [Git公式ドキュメント - ブランチとは](https://git-scm.com/book/ja/v2/Git-%E3%81%AE%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E6%A9%9F%E8%83%BD-%E3%83%96%E3%83%A9%E3%83%B3%E3%83%81%E3%81%A8%E3%81%AF)
- [Gitブランチの図解](https://learngitbranching.js.org/?locale=ja)

お疲れさまでした！ブランチの基本を理解できましたか？
次回は，ブランチを統合する「マージ」について学びます．
