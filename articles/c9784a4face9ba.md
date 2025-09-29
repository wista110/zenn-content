---
title: "gh CLI完全ガイド：GitHubをコマンドラインで効率化する方法"
emoji: "🚀"
type: "tech"
topics: ["github", "cli", "gh", "git", "効率化"]
published: true
---

# gh CLI完全ガイド：GitHubをコマンドラインで使えるようになりたい。

GitHubをブラウザを開いてポチポチやる面倒なので、公式CLIツール「gh」を使って、**GitHubの操作をコマンドでできるようになりたい**と思い、記事にまとめました。

## はじめに

gh CLIは、GitHubの操作をコマンドラインで行える公式ツールです。ブラウザでの操作と比べて、以下のメリットがあります：

**主なメリット:**
- ブラウザを開かずにGitHub操作が可能
- キーボードのみでの高速操作
- コマンド履歴による操作の再現
- スクリプト化による自動化

## インストールと初期設定
一応インストール載せておきます

### インストール方法

**Windows (winget):**
```bash
winget install GitHub.cli
```

**macOS (Homebrew):**
```bash
brew install gh
```

**Linux:**
```bash
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
sudo apt update
sudo apt install gh
```

### 初期設定

```bash
# GitHubにログイン
gh auth login
```
ブラウザで認証をしたら準備完了です！

## 1. リポジトリ操作

### 基本的なコマンド

```bash
# リポジトリ一覧表示
gh repo list

# リポジトリ作成
gh repo create my-new-repo --public

# リポジトリクローン
gh repo clone username/repository-name

# リポジトリの詳細表示
gh repo view username/repository-name
```

### 本題、なぜ便利なのか？

ブラウザの場合は下記の手順を行うと思います。
**従来の方法（ブラウザ）:**
1. GitHub.comにアクセス
2. ログイン
3. 「New repository」をクリック
4. フォームに入力
5. 「Create repository」をクリック

いちいち画面を切り替えなければいけないのはめんどうですよね！
それがコマンドで実行すれはエディタを離れずに実行できます。

**gh CLIの場合:**
```bash
gh repo create my-project --public --description "新しいプロジェクト"
```

**便利なポイント:**
- **1コマンドで完了**: ブラウザを開く必要がない
- **一貫性**: 毎回同じ手順でリポジトリ作成
- **スクリプト化**: 複数リポジトリの一括作成が可能
- **設定の標準化**: テンプレートやデフォルト設定を統一

### 実用例：プロジェクト作成

```bash
# 新プロジェクトの完全な初期化
gh repo create my-new-project --public --description "新しいプロジェクト"
gh repo clone my-new-project
cd my-new-project

# README.mdの自動作成
echo "# my-new-project" > README.md
echo "新しいプロジェクト" >> README.md

# 初回コミット
git add README.md
git commit -m "Initial commit"
git push origin main
```

## 2. Issue管理

### 基本的なコマンド

```bash
# Issue一覧表示
gh issue list

# Issue作成
gh issue create --title "バグ修正" --body "詳細な説明"

# Issue詳細表示
gh issue view 123

# Issueにコメント
gh issue comment 123 --body "コメント内容"

# Issueをクローズ
gh issue close 123
```

### なぜ便利なのか？

**従来の方法（ブラウザ）:**
1. リポジトリページに移動
2. 「Issues」タブをクリック
3. 「New issue」をクリック
4. タイトルと本文を入力
5. ラベルやアサイニーを設定
6. 「Submit new issue」をクリック

**gh CLIの場合:**
```bash
gh issue create --title "バグ報告" --body "エラーの詳細" --label bug --assignee @me
```

**便利なポイント:**
- **テンプレート活用**: 事前に準備したテンプレートファイルを使用可能
- **一括操作**: 複数のIssueを一度に作成
- **フィルタリング**: 状態やラベルで絞り込みが簡単
- **コメント追加**: コマンドラインから直接コメント可能

### 実用例：バグレポートの自動化

```bash
# バグレポートテンプレート作成
cat > bug-template.md << EOF
## 概要
バグの簡潔な説明

## 再現手順
1. 手順1
2. 手順2

## 期待される動作
正常な動作の説明

## 実際の動作
エラーの詳細

## 環境
- OS: 
- ブラウザ: 
- バージョン: 
EOF

# テンプレートを使用してIssue作成
gh issue create \
  --title "バグ報告: ログイン機能" \
  --body-file bug-template.md \
  --label bug \
  --assignee @me
```

## 3. プルリクエスト操作

### 基本的なコマンド

```bash
# PR一覧表示
gh pr list

# PR作成
gh pr create --title "新機能追加" --body "変更内容の説明"

# PR詳細表示
gh pr view 456

# PRマージ
gh pr merge 456 --merge

# PRレビュー
gh pr review 456 --approve
```

### なぜ便利なのか？

**従来の方法（ブラウザ）:**
1. リポジトリページに移動
2. 「Pull requests」タブをクリック
3. 「New pull request」をクリック
4. ブランチを選択
5. タイトルと説明を入力
6. レビュアーを指定
7. 「Create pull request」をクリック

**gh CLIの場合:**
```bash
gh pr create --title "新機能追加" --body "変更内容" --reviewer username
```

**便利なポイント:**
- **ブランチ自動検出**: 現在のブランチから自動でPR作成
- **レビュー依頼**: コマンドラインから直接レビュアー指定
- **一括操作**: 複数のPRを同時に管理
- **マージ操作**: コマンドラインから直接マージ可能

### 実用例：開発フローの効率化

```bash
# 機能開発の完全なフロー
# 1. ブランチ作成と切り替え
git checkout -b feature/new-login

# 2. 開発作業（コード変更）
# ... コードを書く ...

# 3. コミットとプッシュ
git add .
git commit -m "Add new login feature"
git push origin feature/new-login

# 4. PR作成（自動でブランチを検出）
gh pr create \
  --title "新機能: ログイン機能追加" \
  --body "## 変更内容
- ユーザー認証機能を追加
- セキュリティ強化

## テスト
- [x] 単体テスト
- [x] 統合テスト" \
  --reviewer team-lead \
  --assignee @me

# 5. レビュー完了後、マージ
gh pr merge --merge --delete-branch
```

## よく使う便利なオプション

### フィルタリング機能

```bash
# 自分のIssueのみ表示
gh issue list --assignee @me

# 特定のラベルのIssue
gh issue list --label bug,urgent

# 最近のPR
gh pr list --state merged --limit 5

# レビュー待ちのPR
gh pr list --state open --review-requested @me
```

### 出力のカスタマイズ

```bash
# 簡潔な表示
gh pr list --json number,title,author

# 詳細情報付き
gh issue list --json number,title,author,createdAt,updatedAt
```

## まとめ

gh CLIを使うことで、GitHubの作業時間を多少は短縮できると思います。
初学者の方もリポジトリの作成とか簡単なコマンドから初めて慣れていきましょう！

## 参考リンク

- [gh CLI公式ドキュメント](https://cli.github.com/)
- [GitHub CLI リファレンス](https://cli.github.com/manual/)