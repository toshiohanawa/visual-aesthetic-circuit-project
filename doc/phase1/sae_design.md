# Phase 1 設計書：SAE（Sparse Autoencoder）

## 目的

SAE により「構図/光/色」に反応する特徴量（monosemantic features）を抽出する。

## 成果物

- 美的特徴リスト
- 可視化、活性マップ、Steering候補方向

## 手法

Sparse Autoencoder（SAE）を使用して、VLM の内部表現から美的評価に関連する特徴を抽出します。

### SAE の学習対象

- Vision Encoder の activation
- Projector の出力
- LLM の特定層の activation

### 美的要素との対応

抽出された SAE unit が以下の美的要素にどのように対応するかを分析：

- **Composition（構図）**
- **Lighting（光）**
- **Color（色）**

## 評価指標

- SAE unit の Monosemanticity 指標
- 美的方向の活性変化量 ΔActivation
- 可視化による定性的評価

詳細は Phase 0 で確定する評価指標を参照してください。

## 実装の前提

- CUDA 環境での実行（DGX Spark）
- Phase 0 で確定したモデル構造と hook ポイントを使用
- Phase 0 で検証済みのデータセットを使用

詳細は `doc/environment_guide.md` と `doc/modeling_guide.md` を参照してください。

