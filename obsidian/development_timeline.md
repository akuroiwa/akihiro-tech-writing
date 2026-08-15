# 開発年表 (Development Timeline)

これまでのソフトウェア開発およびAIとの協業に関する歴史と、各プロジェクトの時系列を以下にまとめる。

---

## 1. 開発時系列一覧

| 年月 | プロジェクト / 出来事 | 開発の背景・詳細 |
| :--- | :--- | :--- |
| **2019年5月以前** | **着想・計画** | チェスの問題を解くアプローチとして、遺伝的プログラミング（GP）と探索アルゴリズムの適用を構想。 |
| **2019年5月28日** | **開発開始 (`sim-chess`)** | `DEAP` のアリのGPシミュレーション (`deap/examples/gp/ant.py`) をコピーし、`sim-chess` というプロジェクト名で開発を開始。<br>※ 当時はGit未管理。一部の極初期コードが `dev-source/sim-chess/` に残存。 |
| **2019年〜2020年** | **MCTSの導入と試行錯誤** | ミニマックス等の探索アルゴリズムでの紆余曲折を経て、AlphaZeroに触発されMCTS（モンテカルロ木探索）を導入。<br>動作確認が容易なWeb-GUIである `FlaskChess` (`dev-source/FlaskChess/`) に組み込んで開発 (`chess_mcts.py`)。 |
| **2021年12月10日** | **Git管理本格化 (`chess-ant`)** | `chess-ant` プロジェクトとして正式にGit管理を開始。<br>初期はパスタの名前（`Cacio e pepe` など）をコミットメッセージに使用しつつ開発を進める。 |
| **2022年1月7日** | **盤面評価モデルの開発 (`chess-classification`)** | チェス盤面（FEN）から勝敗を予測するニューラルネットワークモデルを構築。<br>Google Colabで Simple Transformers や PyTorch を用いて分類モデルの学習（LLMの再学習等の試行錯誤）を行い、`chess-ant` にロードして評価関数として利用。 |
| **2022年6月25日** | **化学分野への応用 (`chem-ant`, `chem-classification`)** | MCTSとGPによる探索アプローチを分子探索・化学構造式生成に応用。<br>RDKitやGlobal-Chemを利用してターゲット分子に類似した分子を生成する仕組みを構築。 |
| **2022年8月10日** | **探索エンジンの共通化と独立 (`mcts-solver`)** | `chess-ant` と `chem-ant` に共通していた MCTS アルゴリズム部分を `mcts-solver` という別パッケージとして独立させ、Git管理を開始。<br>Winandsらの論文「Monte-Carlo Tree Search Solver」(2008) のミニマックス木探索風の枝切りアルゴリズムを導入し、チェスの問題等の解の収束と精度が大幅に向上。 |
| **2023年4月19日** | **AI（ChatGPT）との協業開始** | ChatGPTのアドバイスを得て、`mcts-solver` の並列処理化を達成。<br>コミットメッセージ: `Parallel processing is possible thanks to OpenAI's ChatGPT advice.`<br>これを契機にAIを本格的な開発パートナーとして迎える。 |
| **2023年11月28日** | **専用GUIの開発 (`py-chessboardjs`)** | `pywebview` と `chessboard.js` を利用してJavaScriptとPythonを連携させ、`chess-ant` 用の独自GUIを構築。<br>AIとの本格的な共同デバッグにより、`chess-ant` 連携のエラーや並行処理問題（GUIとエンジンのスレッド管理）を乗り越える。 |
| **2025年6月29日** | **`gemini-cli` インストール** | 初のコマンドラインAIエージェントの導入。エラーコピペ壁打ちからローカル連携への移行準備。作業ログは `dev-log/2025/gemini-*.txt` に保存開始。 |
| **2025年9月7日** | **エージェント指向の再設計 (`mcts-gen`)** | コマンドラインのAIエージェント (`gemini-cli`) を本格導入して開発。<br>`chess-ant` のGP（遺伝的プログラミング）エンジン部分をモダンなAIエージェント（LLM）に置き換えた汎用的なMCTSフレームワーク `mcts-gen` を開発。AIが「Policy Pruning（方策枝切り）」を行う仕組みを構築。同時に `spec-kit` を初導入（ログは `spec-kit-20250907.txt`）。 |
| **2025年11月1日** | **CI/CDとパッケージ化 (`fpocket-snap`)** | `fpocket`（分子の結合部位予測ツール）をSnapcraftを用いてパッケージングし、GitHub Actionsを用いた自動テスト・リリースのCI/CD自動化環境を構築。 |
| **2026年4月24日** | **仕様駆動開発の自動化 (`spec-craft`)** | 本エージェントとの本格的な協業。<br>Obsidianに置かれた戦略（仕様）と `spec-kit` を連携させ、仕様駆動開発（SDD）を自動化する `spec-craft` を開発。 |
| **2026年5月22日** | **`antigravity-cli` インストール** | `spec-craft` を通じた仕様駆動開発の本格稼働に伴い、自律性の高い新エージェント `antigravity-cli` (agy) を導入。 |
| **2026年8月3日〜15日** | **Prompt-MCTS への転換 (`mcts-gen`)** | GP由来の `if_then_else` 条件コード生成から、LLMの評価器としての強みを活かした自然言語プロンプト（ドメインヒューリスティクス）によるMCTS誘導手法「Prompt-MCTS」への転換。<br>Gymnasium環境との統合 (`specs/017-prompt-mcts-gymnasium`)、および `spec-craft` パッケージ群の最新化を達成。 |


---

## 2. 開発年表から得られる技術的知見・変遷

1. **探索エンジンの変遷**:
   * `GP (Genetic Programming)` $\rightarrow$ `MCTS (モンテカルロ木探索)` $\rightarrow$ `MCTS Solver (枝切り・収束向上)` $\rightarrow$ `AI-Augmented MCTS (AIによるPolicy Pruning/方策枝切りとValue予測)`
2. **GUI/インターフェースの変遷**:
   * `CLI/PGN出力` $\rightarrow$ `FlaskChess (Web-GUIの借用)` $\rightarrow$ `py-chessboardjs (pywebview + chessboard.js による独自デスクトップアプリ)` $\rightarrow$ `MCP Server / CLI Tools (AIエージェント用インターフェース)`
3. **AIとの協業プロセスの変遷**:
   * `AIなし` $\rightarrow$ `個別コードの相談 (ChatGPT: 並列処理の実装アドバイス等)` $\rightarrow$ `GUIデバッグの相棒 (ChatGPT: pywebviewのスレッド管理など)` $\rightarrow$ `コマンドラインAIによる自動開発 (gemini-cli / mcts-gen の開発)` $\rightarrow$ `仕様駆動開発によるAI開発の構造化 (antigravity-cli / spec-craft の開発)`
