# モデリングガイド（仮）

## 使用モデル（VLM）

### LLaVA-OneVision

- **特徴**：解析容易 / SAE実績あり
- **用途**：主要な解析対象モデル
- **内部構造**：Vision Encoder → Projector → LLM

### Qwen2-VL

- **特徴**：高解像度特化
- **用途**：高解像度画像の解析
- **内部構造**：（Phase 0 で確認予定）

### CLIP ViT-L/14

- **特徴**：視覚特徴抽出
- **用途**：視覚特徴の抽出と比較
- **内部構造**：ViT-L/14 アーキテクチャ

## Hook による内部アクセス

### Hook の目的

VLM の内部状態（activation）にアクセスするために、PyTorch の hook 機能を使用します。

### Hook の設定方法

```python
import torch

# Forward hook の例
def activation_hook(module, input, output):
    # output を保存
    activations.append(output.detach().cpu())

# Hook の登録
handle = model.vision_encoder.register_forward_hook(activation_hook)

# 実行
with torch.no_grad():
    output = model(image)

# Hook の削除
handle.remove()
```

### 主要な Hook ポイント

（Phase 0 で確認予定）

- Vision Encoder の各層
- Projector の出力
- LLM の各層
- Attention ヘッドの出力

## Phase 0 でのモデルアクセス性検証

Phase 0 では以下の検証を実施します：

1. **モデルロードコードの準備**
2. **Vision Encoder の層名（hook可能か）一覧出力**
3. **Projector の構造出力（Linear or MLP）**
4. **画像 → embedding → LLM-token の流れを可視化**
5. **中間層 hidden state の形状確認**
6. **注意機構の head 数と hookpoint の構造確認**
7. **hook による中間層取得テストコード（1枚画像でOK）**

詳細は `doc/phase0/phase0_design.md` を参照してください。

## デバイス設定

本プロジェクトでは、すべてのモデル実行を CUDA で行います：

```python
import torch

# CUDA 固定
device = torch.device("cuda")
model = model.to(device)

# CPU/MPS フォールバックは行わない
```

詳細は `doc/environment_guide.md` を参照してください。

## モデルの保存と読み込み

### チェックポイントの保存場所

```
visual-aesthetic-circuit-project/
└── models/
    ├── checkpoints/     # モデルチェックポイント
    ├── activations/     # 保存されたactivation
    └── features/        # 抽出された特徴
```

### モデルの読み込み

```python
# モデルの読み込み例
from transformers import AutoModel

model = AutoModel.from_pretrained("model_name")
model = model.to("cuda")
model.eval()
```

## 解析手法との連携

### SAE（Phase 1）

- Vision Encoder の activation を SAE で解析
- 美的特徴に対応する SAE unit を特定

### Patching（Phase 2）

- Activation Patching により因果関係を特定
- どの層・ヘッドが美的判断に寄与するかを同定

### Steering（Phase 3）

- 特定した美的特徴を操作
- 出力生成への影響を評価

詳細は各 Phase の設計ドキュメントを参照してください。
