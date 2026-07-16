# プロジェクト執筆方針 (Project Writing Policy)

本プロジェクトにおける技術記事（Qiita, Zenn, Dev.to）の執筆・管理方針を以下に定義する。

---

## 1. 媒体ごとの配置・言語・役割

本プロジェクトは、1つのGitHubリポジトリで以下の3つの技術情報発信サイトへの投稿を管理する。
GitHub Actionsによる自動デプロイが設定されており、`main` ブランチへ `git push` された時点で自動的に各サイトへ投稿・更新される。したがって、**コマンドラインツールからの直接投稿（publish/push）は行わない。**

| 媒体 | 配置ディレクトリ | 言語 | 主な役割・コンテンツ内容 |
| :--- | :--- | :--- | :--- |
| **Qiita** | `public/` | 日本語 | 個別の技術メモ、エラー解決、小ネタなど（検索流入重視） |
| **Zenn (記事)** | `articles/` | 日本語 | 深い技術解説、特定の技術テーマに関するシリーズ記事 |
| **Zenn (本/Book)** | `books/` | 日本語 | 開発したソフトウェアの紹介、開発ノート・体験談（ストーリー性重視） |
| **Dev.to** | `posts/` | 英語 | 英語でのOSS紹介、Zenn等で反響の大きかった技術記事の要約 |

---

## 2. コマンドラインツールの使用法とプレビュー

執筆作業の際は、インストール済みの各種CLIツールを用いて必ず手元でプレビューを行い、ユーザー（私）に確認させること。

### 使用するコマンド
* **Qiita CLI** (`@qiita/qiita-cli`):
  * 新規記事作成: `npx qiita new [記事のベースネーム]`
  * プレビュー起動: `npx qiita preview`
* **Zenn CLI** (`zenn-cli`):
  * 新規記事作成: `npx zenn new:article`
  * 新規本作成: `npx zenn new:book`
  * プレビュー起動: `npx zenn preview`
* **Dev.to CLI** (`devto-cli`):
  * 新規記事作成: `dev new [ファイル名]`
  * プレビュー（ドライランでの構成確認）: `dev push --dry-run`

### 参照文献
* [自分のエディタで記事投稿ができる、Qiita CLIの使い方](https://qiita.com/Qiita/items/666e190490d0af90a92b)
* [Qiitaの記事をGitHubリポジトリで管理する方法](https://qiita.com/Qiita/items/32c79014509987541130)
* [Zenn CLIの使い方](https://zenn.dev/ai4u_shunsuke/articles/zenn-cli-usage)
* [Zenn CLIで記事・本を管理する方法](https://zenn.dev/zenn/articles/zenn-cli-guide)
* [Zenn HTML変換ツール (GitHub)](https://github.com/zenn-dev/zenn-editor)
* [Qiita CLI (GitHub)](https://github.com/increments/qiita-cli)
* [Dev.to CLI (GitHub)](https://github.com/sinedied/devto-cli)

---

## 3. spec-craft / spec-kit の適用方針（ワークフローの議論結果）

### 適用プロセスについて
AIエージェント（Antigravity/agy）の使用制限を考慮し、以下の方針を採用する。
* **spec-kit コマンドによる厳密なワークフロー（Specify → Plan → Tasks → Implement）は毎回呼び出さない。**
* 代わりに **spec-craft の思想（Obsidianを唯一の情報源＝Single Source of Truthとして扱い、戦略を管理する）のみを適用する。**
* 具体的には、`obsidian/` 内のロードマップやタスク、本方針ドキュメントを読み合わせながら、AIは直接リポジトリ内の原稿ファイルを執筆・編集し、軽量かつ効率的に作業を進める。

### 毎回の執筆プロセス
1. **打ち合わせ（Alignment）**:
   * 執筆前にユーザーと対話し、仕様（spec-kit用に準拠した執筆方針）を定める。
   * AIが記事内容（または章構成）を提案し、ユーザーが承認した後に執筆を開始する。
2. **開発年表の作成**:
   * ソースコードの `git log` や資料（`dev-log/`）からソフトウェア開発の時系列（おおよその日付を含む「開発年表」）を割り出し、記事・本の内容候補とする。
3. **確認とコミットのルール**:
   * コマンドラインツールによるプレビューをユーザーに提示し、校正を得ること。
   * ユーザーの明示的な許可なく `git push` を行わない（`git commit` まではAIの判断で行ってよい）。

---

## 4. 執筆のための参照資料

* **開発ログ (`dev-log/`)**:
  * 過去のAI（ChatGPTやGemini等）とのやり取りが記録されている。
  * `chatgpt-20260621.txt`, `chatgpt-20260623.txt`, `chatgpt-20260629.txt`（特に20260629.txtにはZenn Booksのテンプレートや今後の進め方が記載されているため重要）。
  * ※ `dev-log/` は `.gitignore` に指定されており、コミット対象外。
* **ソースコード (`dev-source/`)**:
  * 開発してきたソフトウェアのソースコード一式。
  * 各プロジェクトの `docs/` にあるマニュアル、`README.md`、`specs/` 内に保存されている `spec-kit` で定義された仕様方針を参照すること。
  * ※ `dev-source/` は `.gitignore` に指定されており、コミット対象外。

---

## 5. 記事内容の分類とZenn Booksの重点

執筆する記事内容は以下の2種類に大別される。

### ① 個別の技術記事
* Zenn（記事）やQiita、Dev.toに投稿する。技術の深い解説や、エラーの解決、小ネタなどを扱う。

### ② これまで開発してきたソフトウェアの紹介 (Zenn Book)
* **1ソフトウェア＝1冊の本**としてまとめ、**「開発ノート」シリーズ**としてシリーズ化する。内容は徐々に追記していく。
* シリーズとしての統一感を出すため、タイトルの命名規則、表紙の雰囲気、各章の開始フォーマットなどを統一する（例: `開発ノート #1 py-chessboardjs` などのナンバリング）。
* 本の構成にあたっては、完成したコードの解説だけでなく、以下の**「開発のプロセスと物語（思考過程）」**を重視し、失敗談や現段階の課題も含めて正直に記述する。
* 基本的な章構成は [Zenn Book 構成テンプレート](file:///home/akihiro/文書/develop/git/akuroiwa/akihiro-tech-writing/obsidian/zenn-books-template.md) を参考にして執筆する。

> **Bookで重視すべき3つの重点項目:**
> 1. **開発経緯**（なぜ作ったのか、何に苦労したのか）
> 2. **設計判断**（どのように設計したのか、どのような意思決定を行ったか）
> 3. **失敗談**（何を失敗したのか、どのように乗り越えたか、現段階の課題）

---

## 6. Zenn CLI & Markdown の重要留意点 (仕様バグ防止)

これまでのプレビュー実行等で得られた警告・エラーに対する再発防止ルールを以下に定める。

### ① スラッグ (Slug) の命名規則
* **文字数制限**: 本のディレクトリ名（スラッグ名）は **12文字以上、50文字以下** とする。
  * `chess-ant`（9文字）などは短すぎてバリデーションエラーとなるため、`dev-note-chess-ant` のように文字数を満たすよう命名する。
* **使用可能文字**: 半角小文字英数字 (`a-z0-9`)、ハイフン (`-`)、アンダースコア (`_`) のみ。

### ② config.yaml 内の topics
* 記記号やスペースは使用不可（ハイフン `-` も不可）。
* 例: 「C++」は `cpp`、「C#」は `csharp` と記述する。

### ③ 本のカバー画像 (cover)
* 本のカバー画像 (`cover.png` または `cover.jpg`) は、必ず各本のスラッグフォルダの直下（例: `books/dev-note-chess-ant/cover.png`）に配置する。
* 画像ファイルのサイズが小さい（数百KB以下）場合は、Zenn GitHub連携デプロイの仕様上のトラブル（LFSポインタの未解決バグ等）を避けるため、Git LFSを使用せず通常のGit追跡対象として生ファイルをコミットする。

### ④ Zenn Markdown 仕様の最適化
* **リンクと脚注の併記**: 本文の流れを妨げないため、長いURLリンクはアンカーテキストとして貼るか、Zennの脚注記法を利用して文末にまとめる。
  * *脚注記法例*: `本文中に記述する[^1]` と `[^1]: リンク先URLや注記`
* 詳細な記法や仕様については、Zenn公式ガイドを参照する。
  * [ZennのMarkdown記法一覧](https://zenn.dev/zenn/articles/markdown-guide)
  * [Zennのスラッグ(slug)とは](https://zenn.dev/zenn/articles/what-is-slug)
  * [Zenn CLIで記事・本を管理する方法](https://zenn.dev/zenn/articles/zenn-cli-guide)

