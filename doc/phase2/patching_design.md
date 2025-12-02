# Phase 2 設計書：Patching（因果解析）

## 目的

Activation Patching / Causal Tracing により
美的判断に重要な層・ヘッドを同定する。

## 成果物

- 美的回路（Aesthetic Circuits）の因果マップ
- Vision → Projector → LLM の情報流の特定

## 手法

### Activation Patching

特定の層やヘッドの activation を操作し、美的判断への影響を測定します。

### Causal Tracing

情報の流れを追跡し、どの部分が美的評価に寄与しているかを特定します。

### 解析対象

- Vision Encoder の各層
- Projector の出力
- LLM の各層
- Attention ヘッド

## 評価指標

- Patch Restoration Rate
- 美的方向の活性変化量 ΔActivation
- 因果マップの可視化

詳細は Phase 0 で確定する評価指標を参照してください。

## 実装の前提

- Phase 1 で抽出された美的特徴を使用
- CUDA 環境での実行（DGX Spark）
- Phase 0 で確定したモデル構造と hook ポイントを使用

詳細は `doc/environment_guide.md` と `doc/modeling_guide.md` を参照してください。

