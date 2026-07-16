---
title: "第1章：すべては1匹のアリから始まった（2019年〜2020年）"
free: true
---

本章では、チェス問題解決エンジン `chess-ant` プロジェクトの極初期である2019年から2020年にかけての試行錯誤を振り返ります。当時はまだAIとの対話という発想すらなく、テキストエディタとターミナルだけを相手にした、完全な手作業（ソロ開発）の時代でした。

---

## 始まりは「人工アリ問題」 (Artificial Ant problem)

2019年5月28日。私のローカル開発環境のターミナルで、あるコピーコマンドが実行されました。

```bash
akuroiwa@akuroiwa-FMVA42CWY:~/文書/develop/git$ cp DEAP/deap/examples/gp/ant.py chess/sim-chess/src/
```

このコマンドこそが、後に続く5年間の長い旅路の最初の一歩でした。当時はまだ `chess-ant` という名前ではなく、**`sim-chess`** というプロジェクト名で開発をスタートさせていました。

このプロジェクトの出発点は、Pythonの進化計算 (Evolutionary Computation) ライブラリである `DEAP` のサンプルコードに収録されていた **「人工アリ問題」 (Artificial Ant problem)** でした。遺伝的プログラミング (Genetic Programming: GP) の有名なベンチマークであるこのシミュレーションを眺めながら、私はこう考えました。

> **「遺伝的プログラミング (GP) でチェスエンジンを構築できないだろうか」**

当時はまだモンテカルロ木探索 (Monte Carlo Tree Search: MCTS) を知りませんでした。一般的なチェスエンジンと同様に、ミニマックス探索 (Minimax search) をベースとしたエンジンを GP によって構成できないかという試行錯誤から、開発は始まりました。

---

## 様々な試行錯誤：ラムダ計算 (Lambda calculus) の実験

GP は、数式やアルゴリズムの構造そのものを木構造として表現し、進化させることができます。その特徴を利用して、当時はさまざまな実験を行いました。その一例が、ラムダ計算 (Lambda calculus) の探索アルゴリズムへの応用です。

一般的なミニマックス法 (Minimax method) やアルファベータ枝切り (Alpha-Beta pruning) は、プログラム内部で再帰呼び出し (Recursive call) を用いてバックトラッキングを行います。そこで、GP に数式を構成させ、ラムダ計算を探索アルゴリズムとして応用できないかと試したのです。

初期のソースコードが残る `sim-chess/src/` の中には、`chess_ant_lambda.py` や `chess_ant_gp.py` などのファイルが残されています。ラムダ計算はその試行錯誤の一例に過ぎず、多くは失敗作でした。しかし、これらの実験を通じて GP の表現力の限界と可能性を掴んでいったのだと、今は思います。

---

## 初期の失敗：バージョン管理とパッケージ管理の欠如

今振り返ると、もっと早い段階から Git による履歴管理を始めておくべきでした。

当時は、オープンソースのチェスGUIプラットフォームである `FlaskChess` を動作検証用のGUIとして利用していました。GUI を一から開発するよりも探索アルゴリズムの検証を優先したかったため、探索エンジンのロジックを直接 `FlaskChess` のコード内部に埋め込んで開発を進めていたのです。

後にロジック部分を切り分けて `sim-chess/` として独立させたものの、構成やディレクトリ構造は現在と全く異なり、適切な履歴が残されませんでした。2019年から2020年にかけての多くのアイデアや失敗コードの正確な記録は、今や復元することができません。

---

## パッケージコマンドと直接起動の二重生活

開発中の試行錯誤では、コードを修正するたびにパッケージをビルド（`pip install`）するのはテンポが悪すぎます。

そのため開発現場では、ディレクトリ内のファイルを直接呼び出す手法（例：`python chess_ant.py` や `python similarity_mcts.py` にオプションを付与して起動）でデバッグを行い、動作が安定した段階で初めてパッケージングを行い、コマンド（`similarity-mcts` 等）として呼び出せるかを確認する、という流れを取っていました。

当時は AI に相談するという発想すらなく、テキストエディタでコードを書き換え、手動でコマンドを実行してはプリントデバッグを繰り返すという、きわめて泥臭いサイクルを繰り返していました。

その後、Perplexity や ChatGPT、Bard といった対話型 AI が登場し、さらに Gemini CLI や antigravity-cli のような AI エージェントへと発展していきます。しかしこの章で扱っている2019〜2020年当時は、すべてを一人で試行錯誤していた時代でした。

---

## DeepMind から MCTS を知り、論文へ辿り着く

GP によるミニマックス探索の実装に苦戦する中で、チェスAIの世界では別の潮流が起きていました。DeepMind が開発した AlphaZero (アルファゼロ) が、チェス・将棋・囲碁で人間を凌駕したのです。

DeepMind が **モンテカルロ木探索 (Monte Carlo Tree Search: MCTS)** を採用していることを知り、私は MCTS 自体を調べ始めました。

ここで技術的な違いを整理しておくと、AlphaZero ではニューラルネットワークが指し手予測の事前確率 $P(s, a)$ (Policy) と勝敗評価 $v$ (Value) を同時に出力し、それらを用いた PUCT（Polynomial Upper Confidence Trees）アルゴリズムで探索を制御しています。
一方、私の開発する `chess-ant` は事前確率（Policy）を使用しない、従来の UCT（Upper Confidence Bound applied to Trees）をベースとして設計しました。ニューラルネットワークは盤面評価値（Value）のみを推論し、UCTの探索定数 $C$ の制御（探索と活用のバランス調整）には GP を用いるという独自のアーキテクチャを採用したのです。

MCTS を理解する上で大きな助けとなったのが、将棋AI開発者である山岡忠夫氏のブログ「TadaoYamaokaの開発日記」です。AlphaZero 関連の論文は難解で、そのまま読み解くのは困難でしたが、このブログを通じて MCTS の実装上の考え方や探索定数の調整を理解することができました。

* [TadaoYamaokaの開発日記](https://tadaoyamaoka.hatenablog.com/entry/2018/12/08/191619)

さらに、Google DeepMind のオープンソース強化学習フレームワーク **OpenSpiel**[^1] のソースコードを読んでいると、`mcts.py`[^2] の docstring に重要な論文がいくつか引用されていました。

```
Some references:
- Sturtevant, An Analysis of UCT in Multi-Player Games, 2008,
  https://web.cs.du.edu/~sturtevant/papers/multi-player_UCT.pdf
- Nijssen, Monte-Carlo Tree Search for Multi-Player Games, 2013,
  https://project.dke.maastrichtuniversity.nl/games/files/phd/Nijssen_thesis.pdf
- Silver, AlphaGo Zero: Starting from scratch, 2017
  https://deepmind.com/blog/article/alphago-zero-starting-scratch
- Winands, Bjornsson, and Saito, "Monte-Carlo Tree Search Solver", 2008.
  https://dke.maastrichtuniversity.nl/m.winands/documents/uctloa.pdf
```

この中に含まれていた **「Monte-Carlo Tree Search Solver」 (Winands et al., 2008)**[^3] の論文が、後の `mcts-solver` パッケージ（第4章で詳述）の根幹となる枝切りアルゴリズムの出典です。OpenSpiel のような著名なフレームワークに引用されていることで、この論文の重要性を改めて認識しました。

こうして、GPによる探索定数 $C$ の動的選択という発想も徐々に形になっていきました。定数 $C$ を固定値とするのではなく、**GP を用いて盤面の状況に応じて最適な探索定数を動的に選択させる**という `chess-ant` 独自のアーキテクチャは、これらの学びを経て少しずつ洗練されていったのです。

```python
# chess-ant 独自のUCT選択処理のイメージ
# GP によって生成された木構造（C_gp）が、状況に応じて 1/√1 から 1/√9 までの定数を出力する
a_t = arg_max(Q(s_t, a) + C_gp * math.sqrt(2 * math.log(N(s_t)) / N(s_t, a)))
```

---

## コロナ禍と化学情報学 (Cheminformatics) への転換

開発の最初期について、私自身が Read the Docs のマニュアルに書いたこのような記述があります。

> *"I started writing chess-ant in 2019, but at first I was particular about minimax and the work did not proceed slowly. With the COVID-19 outbreak of the cruise ship Diamond Princess in 2020, when the pandemic was finally beginning to attract attention, I decided to use the chess-ant algorithm for the development of therapeutic agents. After that, I read the paper of MCTS solver and the performance improved. At the same time, I learned how to use the cheminformatics software."*
> — [Chess-Ant マニュアル](https://chess-ant.readthedocs.io/en/latest/o_camponoti_balzani.html)

ミニマックスへのこだわりで作業が思うように進まない中、2020年に新型コロナウイルス (COVID-19) が世界的に拡大し、クルーズ船「ダイヤモンドプリンセス号」の集団感染が連日報道される状況になりました。このとき、`chess-ant` のアルゴリズムを創薬（治療薬候補の探索）に応用できないかと考えました。

チェスの「駒の動きを選択して王手を目指す」プロセスは、化学における「原子や官能基を結合させて目的の性質を持つ分子を設計する」プロセスと、木探索という観点で共通しています。

応用のために必要となった化学情報学 (Cheminformatics) ソフトウェアの使い方は、**「化学の新しいカタチ」**[^4]というサイトで学びました。有機合成化学者向けのケモインフォマティクス入門として、RDKit の実践的な使い方が丁寧に解説されており、大変助かりました。

* [化学の新しいカタチ | 有機合成化学者のための計算化学・ケモインフォマティクス入門](https://future-chem.com/)

この経験が、後の `chem-ant` や `chem-classification` 開発の直接的なきっかけとなりました。

---

## 関連リポジトリ・マニュアル

第2章以降で本格化していく開発の全体像については、以下の公式リポジトリおよび Read the Docs のマニュアルに詳しく記載されています。

* **GitHub Repository**: [akuroiwa/chess-ant](https://github.com/akuroiwa/chess-ant)
* **Read the Docs マニュアル（日本語）**: [Chess-Ant ドキュメント](https://chess-ant.readthedocs.io/ja/latest/)
* **Read the Docs マニュアル（英語）**: [Chess-Ant Documentation](https://chess-ant.readthedocs.io/en/latest/)

[^1]: [google-deepmind/open_spiel](https://github.com/google-deepmind/open_spiel)
[^2]: [open_spiel/python/algorithms/mcts.py](https://github.com/google-deepmind/open_spiel/blob/master/open_spiel/python/algorithms/mcts.py)
[^3]: [Winands et al., Monte-Carlo Tree Search Solver, 2008](https://dke.maastrichtuniversity.nl/m.winands/documents/uctloa.pdf)
[^4]: [化学の新しいカタチ](https://future-chem.com/)

