---
title: "第6章：py-chessboardjs誕生：PythonとJavaScriptの架け橋（2023年後半）"
---

探索速度の並列化と MCTS Solver による高速化を遂げた `chess-ant` でしたが、依然として「操作インターフェース」に大きな課題を抱えていました。

対局を行うには、CUI（コマンドライン）で盤面を入力するか、借り物の `FlaskChess` にロジックを直接埋め込んで起動するしかなく、非常に不便でした。「自分だけの、チェスエンジンと直接対話できる専用の軽量デスクトップGUIが欲しい」。その思いから、2023年後半、新たなプロジェクトが始動しました。それが **`py-chessboardjs`** です。

---

## GUI開発の前奏曲：JavaScript の Promise との遭遇

本格的なGUI開発に着手する前、私は大きな技術的課題に直面していました。Python中心の開発者であった私は、JavaScriptの非同期処理（Asynchronous processing）に不慣れだったのです。

2023年4月28日、私はすでに ChatGPT に対して JavaScript の `Promise` の仕組みそのものを質問していました。なぜなら、後に採用する `pywebview` が、Python 側の処理結果を JavaScript へと渡す際に `Promise` を返すという仕様だったからです。「Python 側の探索エンジンの思考が終わるのを待ってから、安全に JavaScript 側のチェス盤の描画を更新する」という連携の設計思想を理解する上で、この非同期 Promise の理解は不可欠な前哨戦（前段）でした。

この理解を経て、2023年後半に実際のデスクトップGUIプロジェクトが本格的に始動することになります。

---

## `pywebview` と `chessboard.js` という選択

2023年11月28日、私は `py-chessboardjs` のリポジトリに最初のコミットを行いました。[^1]

デスクトップアプリケーションのGUIを一から構築するのは非常に労力がかかります。Python には Tkinter や PyQt などがありますが、私が求めるチェス盤の描画やアニメーションを短期間で実装するには負担が大きいと感じました。

[^1]: `py-chessboardjs` のリポジトリは [akuroiwa/py-chessboardjs](https://github.com/akuroiwa/py-chessboardjs) です。`git log --reverse` コマンドを実行することで、最初期からのコミット履歴を確認できます。


そこで私が採用したのが、以下の組み合わせでした。

* **フロントエンド**: Webテクノロジーである `chessboard.js`（盤面描画用JSライブラリ）と `chess.js`（指し手検証・ルール管理用JSライブラリ）。
* **バックエンド**: Python で構築された `chess-ant` 探索エンジン。
* **架け橋（シェル）**: Python からローカルの HTML/CSS/JS をデスクトップウィンドウとしてレンダリングし、双方の関数を直接呼び出すことができる **`pywebview`**。

これにより、フロントエンドは洗練されたWeb技術で素早く構築し、重い思考処理はバックエンドの Python エンジンに任せるという、スマートなデカップリング（疎結合）構造が実現しました。

---

## Python と JavaScript の双方向通信設計

`pywebview` の最大の特徴は、JavaScript 側から Python の関数をあたかもローカルの JS 関数のように呼び出せる（そしてその戻り値を JS の `Promise` として受け取れる）点にあります。

バックエンドでは、以下のように `Api` クラスを定義して `pywebview` にエクスポートします。

```python
class Api:
    def __init__(self):
        self.engine = None  # chess-ant探索エンジン

    def make_move(self, fen):
        # JS側から現在のFENを受け取り、探索を実行して指し手を返す
        best_move = self.engine.search(fen)  # イメージ（実際のAPIとは異なります）
        return best_move
```

これを `webview.create_window` の `js_api` 引数に渡すことで、フロントエンドの JavaScript 側からは `pywebview.api.make_move(fen)` という形で呼び出すことができます。

```javascript
// JS側からの呼び出しイメージ
pywebview.api.make_move(currentFen).then(function(response) {
    // 探索エンジンから指し手（Best Move）が戻ってきた時の処理
    game.move(response);
    board.position(game.fen());
});
```

このシームレスな双方向通信こそが、`py-chessboardjs` の設計の核でした。

しかし、この「一見シンプルで美しい非同期設計」の裏には、GUIスレッドのフリーズとエンジンライフサイクルの競合という、極めて困難なバグが潜んでいました。それについては、次章以降で詳しく掘り下げていきます。
