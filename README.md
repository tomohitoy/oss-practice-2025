# oss-practice-2025

拓殖大学工学部　2025年度『オープンソースソフトウェア』の実習用リポジトリです

## 講義内容

### [第5回：Gitの基本操作（add と commit）](./chap05/README.md)

Gitの基本的なコマンドである `git add` と `git commit` について学びます．
変更の記録方法や，コミットの仕組みを理解します．

### [第6回：Gitブランチ実習](./chap06/README.md)

Gitのブランチ機能について学びます．
並行作業のシミュレーション，分岐の可視化，ブランチの実体を見てみます．

### [第7回：GitHubとコラボレーション実習](./chap07/README.md)

GitHubを使ったリモートリポジトリとの連携について学びます．
SSH認証の設定，Fork・Clone・Pushの基本フローを実践します．

### [第8回：プルリクエストとピアレビュー実習](./chap08/README.md)

プルリクエスト（PR）の作成とピアレビューについて学びます．
OSS開発における協力的な開発フローを体験し，建設的なフィードバックの送り方を実践します．

## テスト用環境の立ち上げ

何も設定されていない環境を `docker` を利用することで起動できます．

### docker container のビルド

```bash
docker build -t git-ubuntu .
```

### ubuntu イメージの起動

```bash
docker run -it --rm -v "$(pwd):/work" git-ubuntu
```
