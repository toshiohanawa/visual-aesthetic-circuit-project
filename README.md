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
