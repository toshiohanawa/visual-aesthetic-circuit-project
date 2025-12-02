# 開発環境ガイド

## ハードウェア環境

- **MacBook Pro M4 Max** ローカル開発、EDA、小規模解析、可視化
- **NVIDIA DGX Spark** SAE 学習、Patching 解析などの大規模 GPU 計算

## 開発環境構成（DGX Spark / CUDA 専用）

本プロジェクトでは、すべてのコード実行・モデル学習・解析を **DGX Spark（CUDA GPU）上で実行する**。

MacBook Pro はドキュメント作成・結果閲覧のみに使用する。

### 接続構成

MacBook Pro →（Tailscale）→ DGX Spark

Cursor Remote Workspace を利用して **DGX 内に直接接続し開発を実施**する。

### CUDA 前提の実行環境

* PyTorch / bitsandbytes / SAE / Patching / Steering は **すべて CUDA を前提とした依存環境**で構築
* Python 仮想環境（uv）は DGX 側にのみ構築
* CPU / MPS / Mac 上での実行は想定しない

### コード生成について

本 README を参照して開発を行う自動生成ツール（Cursor / Codex）も
**CUDA 固定のコードパス（`device="cuda"`）を採用することを期待する**。

## 実行環境は CUDA 前提（DGX Spark の GPU を使用）

本プロジェクトのすべての実験（SAE / Activation Patching / Steering）は
**DGX Spark 上の NVIDIA GPU（CUDA）でのみ実行する設計**とする。

### 方針

* **PyTorch, bitsandbytes, SAE ライブラリなどは CUDA ビルドを前提とする**
* 開発対象コードは **GPU デバイス `"cuda"` 固定** （MPS/CPU はサポート対象外）
* 依存関係はすべて DGX Spark 上で解決し、Mac 側にはインストールしない

### これにより達成されること

* Codex / Cursor が **CUDA 向けコードを自動生成する**
* 不要な CPU/MPS 分岐が消える
* 再現性の高い単一環境を保証
* 大規模モデルの実行が最適化される

### コード中の明示的前提

```python
import torch

# 本プロジェクトは CUDA 前提。CPU/MPS フォールバックは行わない。
device = torch.device("cuda")

model = model.to(device)
```

このように README レベルで「CUDA 固定」を宣言しておくと、
Cursor/Codex が自動的にその方針を引き継ぐ。

## CUDA / 依存関係に関する注意（バージョンは Phase 0 で確定）

DGX Spark には標準で最新の CUDA（例：CUDA 13 系）がインストールされている可能性がありますが、
**VLM・SAE・Patching 解析で利用するライブラリの一部は最新 CUDA との互換性が未検証のものがあります。**

そのため、本プロジェクトでは次の方針を取ります：

* **CUDA バージョンは README 時点では固定しない**
* **Phase 0 にて、PyTorch / bitsandbytes / SAE / Patching 系ライブラリで互換性検証を行う**
* **最も安定して動作する CUDA / PyTorch バージョンを Phase 0 の成果物として確定する**

必要に応じて、 **CUDA 12.x 系の NGC コンテナを使用して実行環境を構築する可能性がある**。

## Python仮想環境の設計方針

本プロジェクトでは、**プロジェクト全体で1つの仮想環境**を使用します。

### 推奨する理由

1. **依存関係の共通性**

   - 全Phaseで同じVLM（LLaVA-OneVision、Qwen2-VL）を使用
   - 共通の解析フレームワーク（SAE、Patching、Steering）を使用
   - データ処理ライブラリ（pandas、numpy、PILなど）は全Phaseで必要

2. **コードの再利用性**

   - Phase 1で抽出した特徴をPhase 2・3で使用
   - Phase 2で特定した回路をPhase 3で操作
   - 共通ユーティリティ関数の共有が容易

3. **環境管理の簡素化**

   - 依存関係の競合を避けやすい
   - バージョン管理が一元化される
   - 環境の再現性が向上

4. **開発効率**

   - Phase間の切り替えが容易
   - Jupyterカーネルの設定が1回で済む
   - IDEの設定が統一される

### 仮想環境の作成方法

本プロジェクトでは `uv` を使用して仮想環境を管理します：

```bash
# 仮想環境の作成と依存関係のインストール
uv venv
uv pip install -r requirements.txt

# Jupyterカーネルの設定
uv pip install ipykernel
uv run python -m ipykernel install --user --name=visual-aesthetic-circuit
```

### 依存関係の管理

各Phaseで必要な依存関係が異なる場合は、`pyproject.toml`でオプショナル依存関係として管理します：

```toml
[project]
dependencies = [
    # 共通依存関係
]

[project.optional-dependencies]
phase0 = ["pandas", "matplotlib", ...]  # EDA用
phase1 = ["sae-training-lib", ...]       # SAE学習用
phase2 = ["causal-tracing-lib", ...]     # Patching用
phase3 = ["steering-lib", ...]           # Steering用
```

インストール例：

```bash
uv sync --extra phase0  # Phase 0の依存関係を追加
uv sync --extra phase1  # Phase 1の依存関係を追加
```

## Cursor Remote Workspace の設定

### 接続方法

1. Tailscale で DGX Spark に接続
2. Cursor の Remote Workspace 機能を使用して DGX Spark に接続
3. プロジェクトルートを DGX Spark 上のワークスペースとして開く

### 開発フロー

1. MacBook Pro で Cursor を開く
2. Remote Workspace で DGX Spark に接続
3. DGX Spark 上でコード編集・実行
4. 結果は DGX Spark 上に保存され、MacBook Pro から閲覧可能

