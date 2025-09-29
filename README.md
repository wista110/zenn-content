# Zenn CLI

* [📘 How to use](https://zenn.dev/zenn/articles/zenn-cli-guide)

## 基本的な操作手順

### 記事の作成

```bash
# 新しい記事を作成
npx zenn new:article

# スラッグを指定して記事を作成
npx zenn new:article --slug my-awesome-article
```

### ブックの作成

```bash
# 新しいブックを作成
npx zenn new:book

# スラッグを指定してブックを作成
npx zenn new:book --slug my-awesome-book
```

### プレビュー

```bash
# ローカルでプレビューを表示
npx zenn preview
```

ブラウザで `http://localhost:8000` にアクセスして記事を確認できます。

### 公開

#### 方法1: GitHub連携（推奨）

1. GitHubリポジトリにプッシュ
2. [Zenn設定ページ](https://zenn.dev/settings)でリポジトリ連携を有効化
3. 記事の`published: true`に設定してコミット・プッシュ

#### 方法2: 手動公開

1. 記事を編集して`published: true`に設定
2. [Zenn.dev](https://zenn.dev/)にログイン
3. 「投稿する」から記事をコピペして公開

## 記事の基本構造

```markdown
---
title: "記事のタイトル"
emoji: "🚀"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["tag1", "tag2"]
published: false # true: 公開 / false: 下書き
---

# 記事のタイトル

記事の内容をここに書きます。
```

## よく使うコマンド

```bash
# 記事一覧表示
npx zenn list:articles

# ブック一覧表示
npx zenn list:books

# ヘルプ表示
npx zenn --help
```
```