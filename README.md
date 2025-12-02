# Visual Aesthetic Circuit Project

### — Vision-Language Model は "美しさ" をどのように判断しているのか？ —

Vision-Language Model（VLM）が画像を見たときに
「美しい」「構図が良い」「光がきれい」「色が魅力的」といった
美的評価（Aesthetic Judgment）をどのように内部で計算しているのかを
明らかにするための研究プロジェクトです。

本プロジェクトは、美的評価のうち以下の 3 要素を中心に扱います：

1. Composition（構図）
2. Lighting（光）
3. Color（色）

---

## プロジェクト概要

本研究は、VLM 内部に存在すると推測される
「美的評価を担う特徴」や「美的判断につながる計算経路」を
段階的に特定・解明することを目的とします。

全体は以下の 4 フェーズで構成します：

- **Phase 0：準備フェーズ（現在ここ）** データ、モデル、評価軸、ツール、環境の整備。
- **Phase 1：特徴抽出フェーズ（SAE）** 美的評価に反応する特徴量（monosemantic features）を抽出する。
- **Phase 2：因果分析フェーズ（Patching）** どの層・注意ヘッドが美的判断に寄与するか特定する。
- **Phase 3：介入フェーズ（Steering）** 美的特徴を強める/弱める介入で出力の変化を検証する。

詳細は [`doc/project_overview.md`](doc/project_overview.md) を参照してください。

## 研究対象とする Vision-Language Model (VLM) の選定方針

本プロジェクトでは、VLM を以下の 3 系統に分類し、それぞれの構造的特徴および解釈可能性を比較した上で、 **LLM 直結型（LLM-Fused VLM）を主要研究対象とする** 。

### 1. デュアルエンコーダ型（CLIP 系）

* 画像エンコーダとテキストエンコーダが完全に分離
* 融合は埋め込み空間での後段一致度評価のみ
* 生成能力を持たず、内部にマルチモーダルな統合表現が存在しない

  → 回路レベルの分析には不適

### 2. エンコーダ＋デコーダ統合型（BLIP 系）

* クロスアテンションにより画像とテキストを中間層で統合
* 理解と生成を一体化可能
* ただし構造が複雑でモデル間の一貫性が弱い

  → 回路解析のコストが大きい

### 3. LLM 直結型（LLaVA / BLIP-2 / Flamingo 系）

* 凍結画像エンコーダと凍結LLMを軽量アライメント層で接続
* 視覚トークンが Transformer 内部で言語トークンと同一空間で処理される
* LLM の既存 interpretability フレームワーク（TransformerLens など）が適用可能

  → **マルチモーダル表現の形成過程を回路単位で追跡できるため、本研究に最適**

## 本研究が LLM 直結型を採用する理由

本プロジェクトは、**「画像 → 意味表現 → 情緒・印象（Aesthetic Feature）」が Transformer 内部でどのように流れるか**を、回路レベルで解析することを目的とする。

この目的に対し、LLM 直結型は以下の利点を持つ：

* 視覚トークンとテキストトークンが同一のアテンション構造で処理される
* 層・ヘッド単位の寄与分析が可能
* 小規模モデル（7B〜13B）を DGX Spark 上で反復実験できる
* GPT-2 系の回路解析手法（activation patching, path patching など）をそのまま転用可能
* 美学的特徴・印象評価と説明性を両立できる

以上より、本研究では  **LLaVA 系を中心とした LLM-Fused VLM を主対象とする** 。

# 【Tier 1：最優先で扱うべきモデル（実験・解析両方に最適）】

## 1. **LLaVA-1.5（7B / 13B）**

* 最も広く使われている LLM-Fused VLM
* 画像→CLIP ViT→線形投影→LLM の最低限構造
* 13B でも DGX Spark で余裕で動く
* 回路解析：最も追いやすい構造
* 画像理解の性能も安定

 **用途** ：回路可視化・アテンション解析・画像トークンの流れの研究

---

## 2. **LLaVA-NeXT（7B / 13B / 34B）**

* LLaVA-1.5 の改良版。視覚トークン圧縮の改良、指示データ強化
* 特に 7B/13B は Spark と相性が良い
* 高精細画像、複雑推論で強い
* 1st tier だが、構造がやや複雑なので解析難度は LLaVA-1.5 より少し上

 **用途** ：LLaVA-1.5 で掴んだ回路をさらに発展させる比較研究

---

# 【Tier 2：本格派モデル（性能高いが解析難度が上がる）】

## 3. **BLIP-2（Flan-T5 ベース or OPT ベース）**

* Q-Former により視覚特徴を「テキスト条件付きで圧縮」
* 画像情報がどのように抽象化されるか分析可能
* LLM も小型〜中型が多く扱いやすい
* ただし LLaVA ほど構造は単純でない

 **用途** ：

* 視覚→言語変換のメカニズム分析
* Q-Former の attention head 解析

---

## 4. **InstructBLIP（BLIP-2 の指示チューニング版）**

* 指示応答能力が高い
* 学習データが公開されており再現性がある
* Q-Former + LLM の構造は保持
* BLIP-2 より応用タスクに強い

 **用途** ：自然言語生成の観点から“美学表現”を分析する場合に有効

---

# 【Tier 3：強力だが重い（DGX Spark でも注意が必要）】

## 5. **OpenFlamingo / OpenFlamingo-2**

* 本家 Flamingo（DeepMind）はクローズ
* これが唯一のオープン再現実装
* Few-shot マルチモーダル能力が強い
* ただしモデルが重く、解析難度は高い
* DGX Spark 1 台なら 9B〜12B クラスが現実ライン

 **用途** ：

* 大規模 VLM の構造比較
* LLaVA 系と Flamingo 系の回路差分分析

---

## 6. **Qwen-VL / Qwen-VL-Chat（Alibaba）**

* LLM（Qwen）と CLIP 系を接続した新世代モデル
* 性能は非常に高いが、解析用のフレームワークは LLaVA より整備が薄い
* とはいえ DGX Spark で問題なく動く

 **用途** ：高性能モダリティ統合モデルの比較対象

---

# 【Tier 4：研究補助（本筋ではないが比較用として有用）】

## 7. **LLaVA-InternVL / LLaVA-OneVision**

* 高精細・dense-caption・OCR に強い
* 美学的特徴を扱う場合に画像理解精度が有利
* ただし構造は LLaVA-NeXT 系より少し複雑

 **用途** ：美学解析タスクでの性能比較

---

## 8. **MiniGPT-4（研究向け）**

* LLaVA 系の初期系譜
* 今となっては性能低いが、構造は極めてシンプル
* 小さく・速く・追いやすい

  → 回路解析の “教材” としては使える

---

## リポジトリ構成

```text
visual-aesthetic-circuit-project/
├── README.md
├── Phase0/
│ └── data/
│     └── raw/
├── Phase1/
├── Phase2/
├── Phase3/
├── data/
├── doc/
│ ├── project_overview.md
│ ├── environment_guide.md
│ ├── data_guide.md
│ ├── modeling_guide.md
│ ├── phase0/
│ │   └── phase0_design.md
│ ├── phase1/
│ │   └── sae_design.md
│ ├── phase2/
│ │   └── patching_design.md
│ └── phase3/
│     └── steering_design.md
├── models/
└── notebooks/
```

---

## ドキュメント

詳細な情報は以下のドキュメントを参照してください：

- **[プロジェクト全体像](doc/project_overview.md)** - 研究の目的、4フェーズ構成、使用モデル、解析手法
- **[開発環境ガイド](doc/environment_guide.md)** - DGX Spark、CUDA、uv、Cursor Remote Workspace の設定
- **[データセットガイド](doc/data_guide.md)** - PARA/AADB データセットの仕様
- **[モデリングガイド](doc/modeling_guide.md)** - VLM と hook の扱い方

### Phase 別設計書

- **[Phase 0 設計書](doc/phase0/phase0_design.md)** - 準備フェーズの詳細計画
- **[Phase 1 設計書](doc/phase1/sae_design.md)** - SAE による特徴抽出の仕様
- **[Phase 2 設計書](doc/phase2/patching_design.md)** - 因果解析の仕様
- **[Phase 3 設計書](doc/phase3/steering_design.md)** - 介入実験の仕様

---

## クイックスタート

### 環境要件

- **NVIDIA DGX Spark**（CUDA GPU 環境）
- **Python 3.x**（`uv` で管理）
- **Cursor**（Remote Workspace 機能を使用）

### セットアップ

1. DGX Spark に Tailscale 経由で接続
2. Cursor Remote Workspace で DGX Spark に接続
3. 仮想環境の作成：

```bash
uv venv
uv pip install -r requirements.txt
```

詳細は [`doc/environment_guide.md`](doc/environment_guide.md) を参照してください。

---

## 現在のステータス

**現在の作業フェーズ：Phase 0**

次のタスク：

1. PARA の EDA と属性分布の確認
2. LLaVA-OneVision の内部 Hook 検証
3. 評価指標の確定
4. Phase1 用 SAE テンプレートの準備

詳細は [`doc/phase0/phase0_design.md`](doc/phase0/phase0_design.md) を参照してください。

---

## 研究の最終ゴール

- 美的評価の内部回路（Aesthetic Circuits）を特定する
- 美的要素（構図/光/色）を操作可能にする
- 美的バイアスの理解と制御方法を確立する
