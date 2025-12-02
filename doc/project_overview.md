# プロジェクト全体像（Grand Design）

## 研究の目的

Vision-Language Model（VLM）が画像を見たときに
「美しい」「構図が良い」「光がきれい」「色が魅力的」といった
美的評価（Aesthetic Judgment）をどのように内部で計算しているのかを
明らかにするための研究プロジェクトです。

本研究は、VLM 内部に存在すると推測される
「美的評価を担う特徴」や「美的判断につながる計算経路」を
段階的に特定・解明することを目的とします。

## 対象とする美的要素

本プロジェクトは、美的評価のうち以下の 3 要素を中心に扱います：

1. **Composition（構図）**
2. **Lighting（光）**
3. **Color（色）**

## 4フェーズ構成

全体は以下の 4 フェーズで構成します：

### Phase 0：準備フェーズ

データ、モデル、評価軸、ツール、環境の整備。

**目的：**

- 扱う美的要素（Composition / Lighting / Color）の定義
- PARA・AADB データセットの健全性評価
- VLM への内部アクセス（hook）の確認
- 評価指標の確定
- 使用ツールセットの確定
- DGX Spark の役割分担設計

**成果物：**

- `Phase0/` 以下のレポートとプロトタイプコード一式
- データ EDA、モデルアクセス検証、メトリクス文書

### Phase 1：特徴抽出フェーズ（SAE）

美的評価に反応する特徴量（monosemantic features）を抽出する。

**目的：**

- SAE により「構図/光/色」に反応する特徴量を抽出
- 反応パターンを可視化し、美的特徴の候補を列挙

**成果物：**

- 美的特徴リスト
- 可視化、活性マップ、Steering候補方向

### Phase 2：因果分析フェーズ（Patching）

どの層・注意ヘッドが美的判断に寄与するか特定する。

**目的：**

- Activation Patching / Causal Tracing により
  美的判断に重要な層・ヘッドを同定

**成果物：**

- 美的回路（Aesthetic Circuits）の因果マップ
- Vision → Projector → LLM の情報流の特定

### Phase 3：介入フェーズ（Steering）

美的特徴を強める/弱める介入で出力の変化を検証する。

**目的：**

- 美的特徴を操作して出力生成の変化を評価

**成果物：**

- 構図/光/色の強調・抑制を行う Steering 機構
- 美的バイアス制御の検証ログ

## 使用モデル（VLM）

- **LLaVA-OneVision**（解析容易 / SAE実績あり）
- **Qwen2-VL**（高解像度特化）
- **CLIP ViT-L/14**（視覚特徴抽出）

## 主な解析手法

- **Sparse Autoencoder (SAE)**
- **Logit Lens**
- **Activation Patching / Causal Tracing**
- **Attribution Patching**
- **Steering**（美的評価方向の操作）

## 研究の最終ゴール

- 美的評価の内部回路（Aesthetic Circuits）を特定する
- 美的要素（構図/光/色）を操作可能にする
- 美的バイアスの理解と制御方法を確立する
