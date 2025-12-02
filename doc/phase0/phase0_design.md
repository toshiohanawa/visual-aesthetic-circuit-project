# Phase 0 設計書

## Phase 0 の目的（What is Phase 0?）

Phase 0 の目的は、
**「美的評価を対象に VLM のメカニスティック解析を行うために、
必要なすべての初期条件を揃えること」**。

これは以下 4 つを満たすことを意味する：

1. **データが準備され、正しく理解されていること**
2. **対象モデル（LLaVA等）が内部操作可能であること**
3. **実験の評価軸と基準が明確であること**
4. **DGX Spark の CUDA / PyTorch / Triton / bitsandbytes などの下層依存関係の互換性検証と、安定バージョンの選定**

Phase 0 が終われば、
**Phase 1（SAE学習 & Feature Extraction）に即移行できる状態になる。**

## Phase 0 のアウトプット（Done の定義）

Phase 0 完了時に必ず存在するべき成果物は：

1. **美的要素（Composition / Lighting / Color）の明確な定義**
2. **データ健全性レポート（PARA/AADB）**
3. **LLaVA（候補モデル）の内部アクセス性レポート**
4. **使用解析ツールの適用可否レポート**
5. **評価指標（Metrics）の数値定義**
6. **計算環境の構成図とワークフロー**
7. **Phase 1 のための Python 実行ベースコード一式**

以上が揃って初めて Phase 1 に入れる。

## Phase 0 全体像（構造マップ）

以下の 5 ステップで構成される：

```
Phase 0
 ├─ Step 0.1　印象カテゴリの定義（完了）
 ├─ Step 0.2　データセット健全性評価
 ├─ Step 0.3　対象モデルのアクセス性検証
 ├─ Step 0.4　評価指標の設計
 └─ Step 0.5　計算環境＋ディレクトリ設計
```

## Phase 0 の詳細プラン（Cursor/Codex 実装用）

以下に各ステップを具体的タスクと目標に分ける。

---

## Step 0.1　印象カテゴリの定義（完了）

### タスク

* Composition / Lighting / Color の定義を記述（文章化）
* PARA / AADB の該当属性との対応表を作成する

### ゴール

→ 「この3つを美的要素として扱う」ことが
研究基盤として確定する。

**状態：完了**

---

## Step 0.2　データセット健全性評価（PARA）

### タスク（Cursor/Codex 実装向け）

1. **PARA のダウンロードファイルの読み込み**
2. **メタデータ読み込みコード（pandas）**
3. **美的スコアの分布可視化（matplotlib）**
4. **Composition / Lighting / Color のスコア分布可視化**
5. **カテゴリ偏り（人物/風景/建物）を算出**
6. **解像度分布の算出**
7. **欠損値チェック**
8. **アノテータ間一致度（ICC/Kappa）が得られる場合は算出**

### ゴール

以下が1つの Markdown アウトプットとしてまとまった状態：

* 美的スコアの分布
* 属性スコアの分布
* カテゴリ偏り
* 全体的なデータ品質

**→ Phase 1 の SAE 学習の前提として必須**

詳細は `doc/data_guide.md` を参照してください。

---

## Step 0.3　対象モデルの内部アクセス性検証（LLaVA-OneVision / LLaVA-Next）

### タスク（Codex実行想定のコード化必須）

1. **モデルロードコードの準備**
2. **Vision Encoder の層名（hook可能か）一覧出力**
3. **Projector の構造出力（Linear or MLP）**
4. **画像 → embedding → LLM-token の流れを可視化**
5. **中間層 hidden state の形状確認**
6. **注意機構の head 数と hookpoint の構造確認**
7. **hook による中間層取得テストコード（1枚画像でOK）**

### ゴール

→ 「このモデルに対して SAE や Patching が適用可能である」と
判断できるレベルのレポート（Markdown）が完成。

**Phase 1 の技術的通行証となる。**

詳細は `doc/modeling_guide.md` を参照してください。

---

## Step 0.4　評価指標の設計（数値的な定義まで落とす）

### タスク

1. **内部評価の数式化**

   * 美的方向の活性変化量 ΔActivation
   * Patch Restoration Rate の定義
   * SAE unit の Monosemanticity 指標定義

2. **外部評価の数式化**

   * GPT-judge による Caption 美的強調度
   * Steering Strength の Dose-Response 曲線
   * KL Divergence による語彙シフト評価

3. **以上を Markdown に整理**

### ゴール

→ 指標が数値で計算可能な形で確定する

**Phase 1〜3 すべての統一評価軸が完成する**

---

## Step 0.5　計算環境＋ディレクトリ構成の設計

### タスク

1. **CUDA / PyTorch / bitsandbytes / Triton / FlashAttention の互換性検証**
2. **最も安定して動作するバージョン組み合わせの選定**
3. **NGC コンテナの検討（必要に応じて）**
4. **ディレクトリ構成の確定**
5. **Phase 1 用のテンプレートコード作成**

### ゴール

→ Phase 1 以降の実験がスムーズに実行できる環境が整う

詳細は `doc/environment_guide.md` を参照してください。

## Phase 0 の全体タイムライン（目安）

| フェーズ | 期間   | 内容                                   |
| -------- | ------ | -------------------------------------- |
| Step0.1  | 1日    | 美的要素の確定（完了済）               |
| Step0.2  | 3〜5日 | データ健全性レポート作成               |
| Step0.3  | 2〜3日 | モデル内部アクセス性の確認             |
| Step0.4  | 2日    | 評価指標の数値化                       |
| Step0.5  | 2〜3日 | 計算環境・ディレクトリ設計と初期コード |

## 最終アウトプット（Cursor/Codex が使う仕様書）

Phase 0 完了時に以下が揃っている：

### （A） Phase0_Report.md（完成版）

* 美的要素定義
* データ健全性レポート
* モデルアクセス性レポート
* 解析ツール適用可否
* 評価指標
* 計算環境構成図
* ディレクトリ構成

### （B） scripts/

* dataset_stats.py
* model_probe.py
* visualize_distributions.py
* hook_test.py
* sae_train_template.py

### （C） notebooks/

* PARAデータのEDAノート
* LLaVA モデルの内部構造確認ノート

