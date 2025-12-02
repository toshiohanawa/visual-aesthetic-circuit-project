# Phase 3 設計書：Steering（介入）

## 目的

美的特徴を強める/弱める介入で出力の変化を検証する。

## 成果物

- 構図/光/色の強調・抑制を行う Steering 機構
- 美的バイアス制御の検証ログ

## 手法

### Steering の実装

Phase 1 で抽出された美的特徴を操作し、出力生成への影響を評価します。

### 操作対象

- **Composition（構図）**の強調・抑制
- **Lighting（光）**の強調・抑制
- **Color（色）**の強調・抑制

### 介入方法

- Activation への加算・減算
- Attention の重み調整
- その他 Phase 2 で特定された重要なポイントへの介入

## 評価指標

- GPT-judge による Caption 美的強調度
- Steering Strength の Dose-Response 曲線
- KL Divergence による語彙シフト評価

詳細は Phase 0 で確定する評価指標を参照してください。

## 実装の前提

- Phase 1 で抽出された美的特徴を使用
- Phase 2 で特定された因果回路を使用
- CUDA 環境での実行（DGX Spark）

詳細は `doc/environment_guide.md` と `doc/modeling_guide.md` を参照してください。

