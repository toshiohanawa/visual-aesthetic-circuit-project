# データセットガイド（仮）

## PARA (Personalized Image Aesthetics Assessment)

### 概要

- 約 3 万枚の画像
- Composition / Lighting / Color / Emotion などの属性
- 美的スコアと詳細属性ラベルを含む

### データ構造

（Phase 0 で詳細を確定予定）

### 使用する属性

本プロジェクトでは以下の属性を中心に使用します：

- **Composition（構図）**
- **Lighting（光）**
- **Color（色）**

### データの取得方法

（Phase 0 で確定予定）

## AADB (Aesthetic and Attributes Database)

### 概要

- 美的スコア + 属性ラベル
- "どちらが美しいか" のペア比較あり

### データ構造

（Phase 0 で詳細を確定予定）

### 使用する属性

本プロジェクトでは以下の属性を中心に使用します：

- **Composition（構図）**
- **Lighting（光）**
- **Color（色）**

### データの取得方法

（Phase 0 で確定予定）

## データの配置場所

```
visual-aesthetic-circuit-project/
├── data/
│   ├── raw/          # 生データ
│   ├── processed/    # 前処理済みデータ
│   └── metadata/     # メタデータ
└── Phase0/
    └── data/
        └── raw/      # Phase 0 用の生データ
```

## Phase 0 でのデータ健全性評価

Phase 0 では以下の評価を実施します：

1. **美的スコアの分布可視化**
2. **属性スコアの分布可視化**
3. **カテゴリ偏り（人物/風景/建物）の算出**
4. **解像度分布の算出**
5. **欠損値チェック**
6. **アノテータ間一致度（ICC/Kappa）の算出**（可能な場合）

詳細は `doc/phase0/phase0_design.md` を参照してください。
