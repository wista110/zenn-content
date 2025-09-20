---
title: "Zenn CLI入門：記事投稿からGitHub連携まで初心者が躓いたポイント全解説"
emoji: "📝"
type: "tech"
topics: ["zenn", "cli", "github", "blog", "初心者"]
published: true
---

## はじめに

Zennの記事投稿には「Webエディタ」と「Zenn CLI + GitHub連携」の2つの方法がありますが、CLI方式は設定項目が多くて初心者には難しく感じますよね。

この記事では、実際にZenn CLIを使って記事投稿を行う中で**躓いたポイント**と**解決方法**を体験談として整理しました。

## 🤔 最初の疑問：どこに何を置けばいい？

### ディレクトリ構成の混乱

最初に混乱したのが、**既存のプロジェクト内**に記事を置くべきか、**別ディレクトリ**に置くべきかという点でした。

**❌ 混同していた構成:**

my-project/
├── content/ ← 既存プロジェクトのコンテンツ
├── zenn-content/ ← Zenn記事（同じディレクトリ内）
└── other-files...

**✅ 正しい構成:**

workspace/
├── my-project/ ← 既存プロジェクト
│ └── content/ ← プロジェクト用コンテンツ
└── zenn-content/ ← Zenn記事専用（完全に独立）
├── articles/
├── books/
└── package.json

### なぜ分けるべきか

| 項目 | my-project/content/ | zenn-content/ |
|------|-------------------|---------------|
| 目的 | プロジェクトのコンテンツ管理 | Zenn記事の執筆・投稿 |
| 形式 | プロジェクト固有のMarkdown | Zenn専用形式 |
| 連携先 | プロジェクト用リポジトリ | Zenn専用リポジトリ |
| 管理方法 | プロジェクトと一体 | 独立した記事管理 |

**結論**: **完全に分離**して管理するのが正解！

## 🛠 Zenn CLI セットアップの落とし穴

### よくある間違い

1. **パッケージ名のタイポ**
```bash
❌ npm install zen-cli      # よくある間違い
✅ npm install zenn-cli     # 正しいパッケージ名
```

2. **初期化の順番**
```bash
❌ npx zenn init → npm init  # 順番が逆
✅ npm init -y → npx zenn init # 正しい順番
```

### 正しいセットアップ手順

```bash
# 1. 記事専用ディレクトリを作成
mkdir zenn-content
cd zenn-content

# 2. Node.jsプロジェクト初期化
npm init -y

# 3. Zenn CLIをインストール
npm install zenn-cli

# 4. Zenn初期化
npx zenn init

# 5. 記事作成
npx zenn new:article --slug my-first-article
```

## 📄 記事投稿の3つの方法と使い分け

### 方法1: Webエディタ（初心者におすすめ）

**メリット:**
- 設定不要で即座に投稿可能
- プレビューが見やすい
- エラーが起きにくい

**手順:**
1. [Zenn.dev](https://zenn.dev/)でログイン
2. 「投稿する」をクリック
3. 記事内容をコピペ

**こんな人におすすめ:** 今すぐ記事を公開したい、設定が面倒

### 方法2: Zenn CLI + 手動投稿

**メリット:**
- ローカルでMarkdown管理
- プレビューで確認可能
- バージョン管理ができる

**手順:**
```bash
# 記事作成・編集
npx zenn new:article --slug article-name

# ローカルプレビュー
npx zenn preview

# Web上で手動公開
```

**こんな人におすすめ:** ローカル環境で記事を書きたい、でも設定は最小限

### 方法3: Zenn CLI + GitHub連携（上級者向け）

**メリット:**
- 完全自動化
- Git履歴で記事管理
- 複数記事を効率管理

**設定が必要:**
- GitHubリポジトリ作成
- Zennアカウント連携
- 正しいディレクトリ構造

**こんな人におすすめ:** 継続的に記事を書く、Git管理したい

## ⚠️ GitHub連携で躓いたポイント

### 記事をプッシュしても表示されない

**原因:** Zenn側でリポジトリ連携設定をしていない

**解決手順:**
1. [Zenn設定ページ](https://zenn.dev/settings)にアクセス
2. 「GitHubリポジトリ連携」を選択
3. 対象リポジトリを指定
4. 連携を有効化

### ファイル名・構造の問題

**❌ よくある間違い:**

my-repo/
├── zenn-articles/ ← 間違った名前
│ └── post1.md ← slug設定なし

**✅ 正しい構造:**

zenn-content/
├── articles/ ← 正確にarticles
│ └── my-post.md ← ファイル名=slug
├── books/ ← 書籍用（空でもOK）
└── package.json

## 🎯 実際の運用フロー

### 記事執筆の流れ

```bash
# 1. 新記事作成
npx zenn new:article --slug new-topic

# 2. 記事執筆
code articles/new-topic.md

# 3. ローカルプレビュー
npx zenn preview

# 4. Git管理
git add articles/new-topic.md
git commit -m "Add new article: new-topic"

# 5. 公開（GitHub連携の場合）
git push origin main
```

### おすすめの記事管理方法

**初心者 → 中級者への移行プラン:**

1. **Phase1**: Webエディタで数記事投稿
2. **Phase2**: Zenn CLI導入、ローカル執筆開始
3. **Phase3**: GitHub連携で自動化

無理に最初から全自動化する必要はありません！

## 🔧 トラブルシューティング

### よくあるエラーと対処法

```bash
# パッケージが見つからない
Error: Cannot find module 'zenn-cli'
→ npm install zenn-cli

# 記事が表示されない
→ Zennでリポジトリ連携確認

# プレビューが起動しない
→ npx zenn preview --port 3000
```

## まとめ

Zenn CLIは慣れると非常に便利ですが、**最初はWebエディタから始める**のがおすすめです。

**段階的なアプローチ:**
1. Webエディタで記事投稿に慣れる
2. Zenn CLIでローカル執筆を試す
3. GitHub連携で自動化を実現

記事を書くこと自体が目的なので、**ツールで悩みすぎず、書きやすい方法を選ぶ**ことが大切ですね。

## 参考リンク

- [Zenn CLI公式ドキュメント](https://zenn.dev/zenn/articles/zenn-cli-guide)
- [GitHubリポジトリ連携](https://zenn.dev/zenn/articles/connect-to-github)