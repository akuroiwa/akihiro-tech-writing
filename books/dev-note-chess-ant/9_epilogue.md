---
title: "おわりに：そして「汎用化（mcts-gen）」と「自動化（spec-craft）」へ"
---

2019年に「人工アリ問題 (Artificial Ant problem)」のサンプルコードをコピーした日から、2025年末の `EngineWorker` による非同期スレッド競合の根本解決に至るまで、`chess-ant` を取り巻く個人開発の旅路は足かけ5年以上に及びました。

最初は Git すら使わず、手作業でファイルを複製していた孤独な開発が、対話型 AI の登場によって壁打ちデバッグの時代へと移り変わり、最終的にはワークスペースを自ら操作する AI エージェントとの「仕様駆動開発」へと結実していきました。

---

## 手作業で理解していたからこそ、AIを正しく導けた

この5年間の開発を通じて得られた最も強力な知見は、**「どれほど AI 技術が進化し、自律的にコードを書くようになっても、開発者本人がアルゴリズムの根底や泥臭いエラーの実態を本質的に理解していなければ、AI との真の協業は成り立たない」** という確信です。

第5章や第7章で触れたように、当時の対話型AI（ChatGPT や Bard）が提示する並列処理や非同期通信のコードには、無数の「動かない提案」や「隠れたレースコンディション（デッドロックの罠）」が含まれていました。

もし私が、MCTS Solver の論文アルゴリズムや、Python のスレッドモデル、JavaScript のイベントループの挙動について、手作業でのデバッグやコードリーディングを通じて深く理解していなければ、AI のもっともらしい誤提案に振り回され、プロジェクトは早々に空中分解していたでしょう。

自分の頭で考え、手で実装し、失敗し続けた「パスタコミット（試行錯誤を繰り返した大量の細かなコミット）の日々」があったからこそ、AI の提案の善し悪しを即座に見極め、正しい設計方針へと AI を軌道修正し、最高の相棒として使いこなすことができたのです。

---

## 次なる挑戦へ：`mcts-gen` と `spec-craft`

`chess-ant` の長い格闘と、AIエージェントとの協業の成功は、私にさらなる次のステップへと進む勇気を与えてくれました。

チェスという個別具体的なゲーム木探索から、さらにメタで汎用的な AI エージェントの連携システムへと、開発はすでにシフトしています。

* **開発ノート #2**: 『mcts-gen 開発ノート：AIエージェントと構築する汎用モンテカルロ木探索フレームワーク』
  `chess-ant` の遺伝的プログラミング (GP) エンジン部分を、最新のLLM（AIエージェント）による盤面評価および方策枝切り（Policy Pruning）に置き換える、汎用型モンテカルロ木探索フレームワークへの挑戦。
* **開発ノート #3**: 『spec-craft 開発ノート：AIエージェントと実践する仕様駆動開発』
  AIエージェントとの協業プロセスの構造化。Obsidian（人間の戦略）と spec-kit（エージェントの戦術）をシームレスに結合し、AIの迷走やコンテキスト消失、トークン制限を防ぐ仕組みの確立。

手作業の価値を知っているからこそ、自動化の価値が分かる。
この開発ノートシリーズが、これからの AI 時代を生きるエンジニアや個人開発者にとって、技術の継承と協業のあり方を考える一つの道標になれば幸いです。

長きにわたる試行錯誤の物語に、最後までお付き合いいただきありがとうございました。

---

## 謝辞：開発・執筆を共にしたAIたち

本プロジェクトの開発、検証、デバッグ、および本技術ノートの執筆・編集にあたっては、以下のAIツール・モデルからの多大な支援と助言を受けました。ここに感謝の意を表します。

* **ChatGPT (OpenAI)**：並列処理の設計、Promiseを用いた非同期連携、およびGUIスレッドとエンジンの非同期協調設計におけるデバッグの壁打ち相手。
* **Google Bard / Gemini**：インポートエラーの解消や、JavaScript/Python間の通信における不具合の解析。
* **Claude (Anthropic)**：本技術ノートの推敲、事実関係の確認、表現のブラッシュアップ。
* **Perplexity**：過去の対話履歴のインデックス化と、技術要素の関連文献・時系列整理。

以下は**Gemini**の生成したカバー画像出力コードです：

```python
import os
from PIL import Image, ImageDraw, ImageFont
import math

# Zenn recommended ratio for Book cover: 1 : 1.4 (1000x1400 px)
width = 1000
height = 1400
image = Image.new("RGBA", (width, height), "#0B0F19")
draw = ImageDraw.Draw(image)

# 1. Background Gradient (Deep Navy to Dark Indigo, vertical flow)
for y in range(height):
    # Smooth transition from #0B0F19 to #1E1B4B
    r = int(11 + (30 - 11) * (y / height))
    g = int(15 + (27 - 15) * (y / height))
    b = int(25 + (75 - 25) * (y / height))
    for x in range(width):
        draw.point((x, y), fill=(r, g, b, 255))

# 2. Draw Abstract Chess Grid Perspective (Cyber Style - Adjusted for tall canvas)
grid_color = (14, 165, 233, 45) # Light cyber blue with alpha
horizon_y = 600
vp_x = width // 2
vp_y = 100

for i in range(-15, 16):
    start_x = vp_x + i * 90
    draw.line([(vp_x, vp_y), (start_x, height)], fill=grid_color, width=1)

# Horizontal lines of perspective grid
for i in range(15):
    y = horizon_y + int((height - horizon_y) * (i / 14) ** 1.8)
    draw.line([(0, y), (width, y)], fill=grid_color, width=1)

# 3. Draw MCTS / AI Network (Nodes and connections centered dynamically)
nodes = [
    (500, 750), (350, 900), (650, 850), (250, 1100),
    (450, 1050), (600, 1120), (750, 1000), (850, 1200)
]
# Draw connections (Circuit/Network style)
conn_color = (245, 158, 11, 80) # Gold with alpha
for i, n1 in enumerate(nodes):
    for j, n2 in enumerate(nodes):
        if 0 < abs(i - j) <= 2:
            draw.line([n1, n2], fill=conn_color, width=2)

# Draw Node circles
for x, y in nodes:
    draw.ellipse([x-10, y-10, x+10, y+10], fill="#F59E0B", outline="#0B0F19", width=3)
    # Outer glow
    draw.ellipse([x-20, y-20, x+20, y+20], outline=(245, 158, 11, 50), width=1)

# 4. Stylized "Ant" (Improved: Head + Thorax + Abdomen, 6 Legs + Antennae)
ant_x, ant_y = 450, 1050  # 中心座標（胸の位置）

# (1) 体節の描画（腹 -> 胸 -> 頭 の順で重ねて描画）
# 腹部 (Abdomen/Gaster): 下側に大きめの楕円
draw.ellipse([ant_x - 18, ant_y + 8, ant_x + 18, ant_y + 38], fill="#0284C7")

# 胸部 (Thorax): 中央の体節
draw.ellipse([ant_x - 12, ant_y - 12, ant_x + 12, ant_y + 12], fill="#0EA5E9")

# 頭部 (Head): 上側に小〜中サイズの楕円
draw.ellipse([ant_x - 10, ant_y - 32, ant_x + 10, ant_y - 16], fill="#38BDF8")

# (2) 触角（Antennae）の描画
draw.line([(ant_x - 4, ant_y - 28), (ant_x - 16, ant_y - 42), (ant_x - 22, ant_y - 40)], fill="#38BDF8", width=2)
draw.line([(ant_x + 4, ant_y - 28), (ant_x + 16, ant_y - 42), (ant_x + 22, ant_y - 40)], fill="#38BDF8", width=2)

# (3) 脚（Legs）の描画（胸部から左右3対＝計6本）
leg_color = "#0EA5E9"
leg_width = 3

# 前脚 (Front legs - 上向きに伸びて曲がる)
draw.line([(ant_x - 8, ant_y - 6), (ant_x - 28, ant_y - 18), (ant_x - 34, ant_y - 28)], fill=leg_color, width=leg_width)
draw.line([(ant_x + 8, ant_y - 6), (ant_x + 28, ant_y - 18), (ant_x + 34, ant_y - 28)], fill=leg_color, width=leg_width)

# 中脚 (Middle legs - 横方向に展開)
draw.line([(ant_x - 10, ant_y), (ant_x - 32, ant_y - 2), (ant_x - 40, ant_y + 8)], fill=leg_color, width=leg_width)
draw.line([(ant_x + 10, ant_y), (ant_x + 32, ant_y - 2), (ant_x + 40, ant_y + 8)], fill=leg_color, width=leg_width)

# 後脚 (Hind legs - 下向きに長く伸ばす)
draw.line([(ant_x - 8, ant_y + 6), (ant_x - 28, ant_y + 18), (ant_x - 36, ant_y + 34)], fill=leg_color, width=leg_width)
draw.line([(ant_x + 8, ant_y + 6), (ant_x + 28, ant_y + 18), (ant_x + 36, ant_y + 34)], fill=leg_color, width=leg_width)


# 5. Accent Overlay / Modern Frame Lines for book cover look
draw.rectangle([60, 60, 940, 1340], outline=(255, 255, 255, 15), width=2)

# Save the generated vertical cover image
output_path = "cover_vertical.png"
image.convert("RGB").save(output_path, "PNG")
print(f"Success: Saved to {output_path}")
```
