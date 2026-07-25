---
title: "第4章：おわりに：仕様駆動がもたらす人間とAIの協調の未来"
---

`spec-craft` と `spec-kit` の導入によって、私たちの個人開発プロセスは、「AIとの壁打ち対話」から「仕様駆動による構造化された協調開発」へと完全に脱皮しました。本章では、これまでの成果を総括し、現在進行中のロードマップと今後の展望についてまとめます。

---

## 開発ノートシリーズの役割分担

本開発ノートシリーズ（『chess-ant』『mcts-gen』『spec-craft』）は、それぞれ異なる役割を持っています。

* **『chess-ant 開発ノート』：歴史書（レトロスペクティブ）**
  2019年から2025年にかけて何が起き、どのように開発が推移したかを記録した技術史です。原則として記述は固定され、事実の記録として残されます。
* **『mcts-gen 開発ノート』および『spec-craft 開発ノート』：進行中の研究ノート（リビングドキュメント）**
  現在進行形の研究開発記録であり、ソフトウェアのアップデートや検証結果に伴って成長・更新される性質を持っています。

---

## 今後の展望とロードマップ

本書の執筆時点において、`spec-craft` および関連エコシステムでは、以下の開発・検証計画が進行しています。

### 1. `spec-craft` の Obsidian CLI 対応
現在、`spec-craft` が `obsidian/` ディレクトリ配下に Markdown ファイルを新規作成する際、Obsidian CLI などの専用インターフェースを介さず、AIエージェントがファイルシステムにファイルを直接出力しています。このため、Obsidian 側で認識されるべきフロントマター（メタデータ）が自動で記述されず、単なる通常の Markdown ファイルとして生成されてしまうという課題（メタデータの欠落）が存在します。この問題を根本解決するため、Obsidian CLI と統合し、ファイルの生成・同期時に適切なフロントマターを自動付与する仕組みの構築を目指しています。


### 2. `mcts-gen` の Google Colab 上での動作検証
`mcts-gen` に実装された各種探索アルゴリズムやポリシー枝切り機構について、Google Colab 上で動作確認と再現実験を行えるノートブック環境を整備する予定です。これにより、ハードウェアに依存せず、誰でも手軽に検証可能なプレイグラウンドを提供することを目指しています。

### 3. AI 協業年表の継続的なアップデート
Perplexity や ChatGPT、その他各種エージェントとの相談・デバッグ履歴を `development_timeline.md` などのインデックスファイルに集約し、人間とAIの設計意思決定の軌跡を継続的にアーカイブしていきます。[^1]

---

## 結び：アーキテクトとしての人間、実務者としてのAI

仕様駆動開発（SDD）の本質は、人間が「アーキテクト（設計・戦略）」に専念し、AIエージェントに「実務者（実装・検証）」としての明確な責務を割り当てることにあります。

かつて `chess-ant` でデッドロックや同期ズレに手作業で苦しんだ経験があるからこそ、私たちはAIが提案するコードの落とし穴を事前に見抜き、正しい仕様書を書いてAIを的確にナビゲートすることができます。AIの自律性が高まるこれからの時代において、コードを丸投げするのではなく、「仕様書を通じてAIと対話する」能力こそが、開発者の最大の強みとなるはずです。

仕様と戦略の力を信じ、人間とAIのより洗練された協業を目指して、この開発の旅はこれからも続いていきます。

---

## 謝辞：開発・執筆を共にしたAIたち

本プロジェクトの開発、検証、デバッグ、および本技術ノートの執筆・編集にあたっては、以下のAIツール・モデルからの多大な支援と助言を受けました。ここに感謝の意を表します。

* **Gemini (Google)**：`gemini-cli` を用いた仕様駆動開発初期環境の検証、および本技術ノートシリーズの構成設計支援。
* **Claude (Anthropic)**：`antigravity-cli` (agy) に搭載されたエージェントによる、仕様駆動開発（SDD）の自動適用とタスク実行、および本稿の編集レビュー。
* **ChatGPT (OpenAI)**：仕様の段階的詳細化プロセス（Specify → Plan → Implement）に関する意見交換と推敲支援。
* **Perplexity**：SnapcraftやGitHub ActionsでのCI/CD環境構築時のトラブルシューティングに関する情報提供。

以下はGeminiの生成したカバー画像出力コードです：

```python
import os
from PIL import Image, ImageDraw

# Zenn recommended ratio for Book cover: 1 : 1.4 (1000x1400 px)
width = 1000
height = 1400
image = Image.new("RGBA", (width, height), "#0F172A")
draw = ImageDraw.Draw(image)

# 1. Background Gradient (Dark Blue-Slate to Indigo/Violet gradient)
for y in range(height):
    # Transition from #0F172A to #1E1B4B / #312E81
    r = int(15 + (49 - 15) * (y / height))
    g = int(23 + (46 - 23) * (y / height))
    b = int(42 + (129 - 42) * (y / height))
    for x in range(width):
        draw.point((x, y), fill=(r, g, b, 255))

# 2. Draw Blueprint Grid / Technical Spec Architectural Lines
grid_color = (99, 102, 241, 30) # Indigo tint blueprint grid
horizon_y = 520
vp_x = width // 2
vp_y = 70

for i in range(-15, 16):
    start_x = vp_x + i * 90
    draw.line([(vp_x, vp_y), (start_x, height)], fill=grid_color, width=1)

for i in range(15):
    y = horizon_y + int((height - horizon_y) * (i / 14) ** 1.8)
    draw.line([(0, y), (width, y)], fill=grid_color, width=1)

# 3. Draw Blueprint / Architectural Craft Elements (Layered Spec Blocks & Connections)
# Central Blueprint Blueprint Box (Specification Document / Design Crafting)
center_x, center_y = 500, 920

# Structured Layers (Specification Blocks)
layers = [
    # (x, y, w, h, color, label_type)
    (center_x - 180, center_y - 220, 360, 80, "#6366F1", "Requirement / Spec Root"),
    (center_x - 260, center_y - 110, 230, 75, "#8B5CF6", "Module Craft A"),
    (center_x + 30, center_y - 110, 230, 75, "#8B5CF6", "Module Craft B"),
    (center_x - 300, center_y + 10, 170, 70, "#A855F7", "Subsystem 1"),
    (center_x - 100, center_y + 10, 170, 70, "#EC4899", "Subsystem 2 (Core)"),
    (center_x + 100, center_y + 10, 200, 70, "#A855F7", "Subsystem 3"),
    (center_x - 220, center_y + 120, 440, 80, "#3B82F6", "Implementation & Test Craft"),
]

# Connecting Lines (Spec Architecture Blueprint Flow)
connections = [
    ((center_x, center_y - 140), (center_x - 145, center_y - 110)),
    ((center_x, center_y - 140), (center_x + 145, center_y - 110)),
    ((center_x - 145, center_y - 35), (center_x - 215, center_y + 10)),
    ((center_x - 145, center_y - 35), (center_x - 15, center_y + 10)),
    ((center_x + 145, center_y - 35), (center_x + 200, center_y + 10)),
    ((center_x - 15, center_y + 80), (center_x, center_y + 120)),
    ((center_x - 215, center_y + 80), (center_x - 100, center_y + 120)),
    ((center_x + 200, center_y + 80), (center_x + 100, center_y + 120)),
]

for p1, p2 in connections:
    draw.line([p1, p2], fill=(168, 85, 247, 180), width=2)

# Draw Layer Blocks (Blueprint Style with Outline & Accent Corners)
for lx, ly, lw, lh, color, _ in layers:
    # Semi-transparent fill block
    draw.rectangle([lx, ly, lx + lw, ly + lh], fill=(15, 23, 42, 210), outline=color, width=2)
    # Inner subtle glow outline
    draw.rectangle([lx + 4, ly + 4, lx + lw - 4, ly + lh - 4], outline=(255, 255, 255, 30), width=1)

    # Technical Corner Accents
    accent_len = 10
    draw.line([(lx, ly), (lx + accent_len, ly)], fill=color, width=3)
    draw.line([(lx, ly), (lx, ly + accent_len)], fill=color, width=3)
    draw.line([(lx + lw, ly + lh), (lx + lw - accent_len, ly + lh)], fill=color, width=3)
    draw.line([(lx + lw, ly + lh), (lx + lw, ly + lh - accent_len)], fill=color, width=3)

# Blueprint Drafting Measurement / Spec Marker lines
draw.line([(center_x - 330, center_y - 240), (center_x - 330, center_y + 220)], fill=(99, 102, 241, 100), width=1)
draw.line([(center_x - 340, center_y - 240), (center_x - 320, center_y - 240)], fill=(99, 102, 241, 150), width=2)
draw.line([(center_x - 340, center_y + 220), (center_x - 320, center_y + 220)], fill=(99, 102, 241, 150), width=2)

# 4. Accent Overlay / Frame Lines (Book Cover Look)
draw.rectangle([60, 60, 940, 1340], outline=(255, 255, 255, 15), width=2)

# Save output
output_path = "cover_spec_craft.png"
image.convert("RGB").save(output_path, "PNG")
print(f"Success: Saved to {output_path}")
```

[^1]: [`akuroiwa/akihiro-tech-writing`](https://github.com/akuroiwa/akihiro-tech-writing) の `obsidian/` に記述。
