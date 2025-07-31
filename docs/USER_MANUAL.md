# 📖 AI Fine-tuning Toolkit ユーザーマニュアル

## 📋 目次

1. [はじめに](#はじめに)
2. [システム要件](#システム要件)
3. [インストールと初期設定](#インストールと初期設定)
4. [Webインターフェースの基本操作](#webインターフェースの基本操作)
5. [ファインチューニング完全ガイド](#ファインチューニング完全ガイド)
6. [モデルの利用方法](#モデルの利用方法)
7. [大規模モデル（32B）のファインチューニング](#大規模モデル32bのファインチューニング)
8. [データ管理](#データ管理)
9. [トラブルシューティング](#トラブルシューティング)
10. [FAQ](#faq)
11. [高度な使用方法](#高度な使用方法)

---

## 🎯 はじめに

AI Fine-tuning Toolkitは、日本語大規模言語モデル（LLM）のファインチューニングを効率的に行うための包括的なソリューションです。このマニュアルでは、初心者から上級者まで、システムを効果的に活用するための詳細な手順を説明します。

### ✨ 主な特徴
- **簡単操作**: Webブラウザから直感的に操作
- **高性能**: GPU最適化とメモリ効率化
- **多様な手法**: フルファインチューニング、LoRA、QLoRA対応
- **日本語特化**: 日本語モデルに最適化された設定

---

## 💻 システム要件

### 🖥️ ハードウェア要件

#### 最小要件
- **CPU**: 4コア以上
- **メモリ**: 16GB以上
- **GPU**: NVIDIA GPU 8GB VRAM以上
- **ストレージ**: 50GB以上の空き容量

#### 推奨要件
- **CPU**: 8コア以上
- **メモリ**: 32GB以上
- **GPU**: NVIDIA RTX A5000 (24GB) x2 または同等
- **ストレージ**: 100GB以上のSSD

### 🔧 ソフトウェア要件
- **OS**: Ubuntu 18.04+ / Windows 10+ (WSL2) / macOS 10.15+
- **Docker**: 20.10+
- **Docker Compose**: 1.28+
- **NVIDIA Docker**: 2.0+ (GPU使用時)
- **CUDA**: 11.8+

---

## 🚀 インストールと初期設定

### ステップ 1: リポジトリのクローン

```bash
# GitHubからクローン
git clone https://github.com/kji-furuta/AI_FT.git
cd AI_FT
```

### ステップ 2: Docker環境の構築

```bash
# Dockerイメージのビルドと起動
cd docker
docker-compose up -d

# 起動確認
docker ps
```

### ステップ 3: GPU確認

```bash
# コンテナに入る
docker exec -it ai-ft-container bash

# GPU状況確認
nvidia-smi

# PyTorchでのGPU確認
python -c "import torch; print(f'CUDA available: {torch.cuda.is_available()}'); print(f'GPU count: {torch.cuda.device_count()}')"
```

### ステップ 4: Webインターフェースの起動

```bash
# Webアプリケーション起動
docker exec ai-ft-container bash /workspace/scripts/start_web_interface.sh
```

✅ **確認**: ブラウザで `http://localhost:8050` にアクセスしてダッシュボードが表示されることを確認

---

## 🌐 Webインターフェースの基本操作

### 🏠 ダッシュボード画面

ダッシュボードでは以下の情報を確認できます：

1. **システム状況**
   - CPU使用率
   - メモリ使用量
   - GPU情報（利用可能な場合）

2. **実行中のタスク**
   - ファインチューニングの進捗
   - 完了したタスクの履歴

3. **クイックアクセス**
   - データアップロード
   - ファインチューニング実行
   - テキスト生成

### 📊 ナビゲーション

画面上部のナビゲーションバーから各機能にアクセスできます：

- **ダッシュボード**: システム概要
- **ファインチューニング**: モデル学習
- **テキスト生成**: モデル利用
- **モデル管理**: モデル一覧・管理

---

## 🎓 ファインチューニング完全ガイド

### 📚 事前準備: データの作成

#### データ形式
ファインチューニングには以下の形式のデータを使用します：

```json
{"text": "質問: 日本の首都はどこですか？\n回答: 日本の首都は東京です。"}
{"text": "質問: 富士山の高さは何メートルですか？\n回答: 富士山の高さは3,776メートルです。"}
```

#### データ作成のガイドライン

1. **一貫性**: 同じ形式で質問と回答を記述
2. **品質**: 正確で自然な日本語を使用
3. **多様性**: 様々なトピックや表現を含める
4. **量**: 最低100件、推奨1000件以上

#### サンプルデータの活用
プロジェクトには以下のサンプルデータが含まれています：
- `data/raw/sample_training_data.jsonl`: 基本的なQ&A
- `data/raw/sample_chat_data.jsonl`: 会話形式
- `data/raw/sample_code_data.jsonl`: プログラミング関連

### 🔄 ステップバイステップ: ファインチューニング実行

#### ステップ 1: データアップロード

1. **ファインチューニング**タブをクリック
2. **トレーニングデータのアップロード**セクションで「ファイルを選択」
3. JSONL形式のファイルを選択
4. **アップロード**ボタンをクリック

✅ **確認**: アップロード完了メッセージとサンプルデータが表示される

#### ステップ 2: モデル設定

1. **ベースモデル**を選択：
   - `distilgpt2`: 軽量・テスト用（推奨：初回）
   - `japanese-stablelm-3b`: 日本語3Bモデル
   - `elyza-japanese-llama-7b`: 高性能7Bモデル

2. **LoRA設定**：
   - **LoRA Rank**: 16（標準）
   - 小さい値（8）：軽量・高速
   - 大きい値（32）：高精度・重い

3. **学習パラメータ**：
   - **学習率**: 0.0003（標準）
   - **バッチサイズ**: 4（GPU 8GB時）
   - **エポック数**: 3（標準）

#### ステップ 3: トレーニング実行

1. **ファインチューニング開始**ボタンをクリック
2. 進捗バーで学習状況を確認
3. 完了まで待機（通常10-30分）

✅ **確認**: 「ファインチューニング完了」メッセージが表示される

### ⚙️ パラメータ調整ガイド

#### GPU メモリ別推奨設定

| GPU VRAM | モデル | バッチサイズ | LoRA Rank |
|----------|--------|-------------|-----------|
| 8GB | distilgpt2 | 8 | 16 |
| 16GB | japanese-stablelm-3b | 4 | 16 |
| 24GB | elyza-japanese-llama-7b | 2 | 32 |

#### 学習時間の目安

| データ量 | モデルサイズ | 予想時間 |
|----------|-------------|----------|
| 100件 | 3B | 5-10分 |
| 1,000件 | 3B | 15-30分 |
| 5,000件 | 7B | 1-2時間 |

---

## 🤖 モデルの利用方法

### 📝 基本的なテキスト生成

#### ステップ 1: モデル選択

1. **テキスト生成**タブをクリック
2. **使用するモデル**でファインチューニング済みモデルを選択
3. 生成パラメータを調整：
   - **Temperature**: 0.7（標準）、低い値=確実、高い値=創造的
   - **最大長**: 100（生成するトークン数）

#### ステップ 2: プロンプト入力と生成

1. **プロンプト**欄に生成したいテキストの始まりを入力
2. **テキスト生成**ボタンをクリック
3. 結果を確認

### 💡 効果的なプロンプトの書き方

#### 良い例
```
質問: 機械学習とは何ですか？
回答: 
```

```
以下の文章を要約してください：
[文章内容]
要約: 
```

#### 避けるべき例
- 曖昧すぎるプロンプト：「何か教えて」
- 長すぎるプロンプト：500文字以上
- 文脈のないプロンプト：「それは」

### 🔧 生成パラメータの詳細

| パラメータ | 説明 | 推奨値 |
|-----------|------|--------|
| **Temperature** | ランダム性の制御 | 0.7-0.9 |
| **Top-p** | 語彙の絞り込み | 0.9 |
| **Max Length** | 最大生成長 | 50-200 |
| **Repetition Penalty** | 繰り返し抑制 | 1.1 |

---

## 📁 データ管理

### 📤 データアップロード方法

#### 方法1: Webインターフェース（推奨）
1. ファインチューニングページでファイル選択
2. JSONL形式のファイルをドラッグ&ドロップまたは選択
3. 自動検証により即座に結果を確認

#### 方法2: 直接コピー
```bash
# ホストからコンテナへファイルコピー
docker cp your_data.jsonl ai-ft-container:/workspace/data/raw/
```

#### 方法3: URL からダウンロード
```bash
# コンテナ内でダウンロード
docker exec ai-ft-container wget -O /workspace/data/raw/remote_data.jsonl https://example.com/data.jsonl
```

### 🔍 データ検証

アップロードされたデータは自動的に検証されます：

✅ **検証項目**:
- JSON形式の正確性
- 必須フィールドの存在
- 文字エンコーディング
- ファイルサイズ

❌ **よくあるエラー**:
- JSON構文エラー
- 改行区切りでない
- UTF-8以外のエンコーディング

### 📊 データ前処理

大量データや複雑な形式の場合は前処理スクリプトを使用：

```bash
# データの前処理
python scripts/prepare_training_data.py \
  --input data/raw/your_data.jsonl \
  --output data/processed/training_data.jsonl \
  --format text \
  --validate
```

---

## 🔧 トラブルシューティング

### ❗ よくある問題と解決方法

#### 問題1: GPU メモリ不足
**症状**: "CUDA out of memory" エラー

**解決方法**:
1. バッチサイズを減らす（4→2→1）
2. LoRA Rankを下げる（16→8）
3. QLoRAを有効にする
4. Gradient Checkpointingを有効にする

```python
# メモリ不足時の設定例
training_config = {
    "batch_size": 1,
    "gradient_accumulation_steps": 16,
    "gradient_checkpointing": True,
    "fp16": True
}
```

#### 問題2: Webインターフェースにアクセスできない
**症状**: ブラウザで画面が表示されない

**解決方法**:
1. ポート8000が使用中でないか確認
```bash
netstat -tulpn | grep :8000
```

2. Dockerコンテナが起動しているか確認
```bash
docker ps | grep ai-ft-container
```

3. ファイアウォール設定を確認

#### 問題3: モデルのロードエラー
**症状**: モデルファイルが見つからない

**解決方法**:
1. モデルパスを確認
```bash
docker exec ai-ft-container ls -la /workspace/lora_demo_*
```

2. Hugging Face認証を確認
```bash
docker exec ai-ft-container python -c "from huggingface_hub import whoami; print(whoami())"
```

#### 問題4: 学習が進まない
**症状**: 進捗が0%のまま

**解決方法**:
1. ログを確認
```bash
docker exec ai-ft-container tail -f /workspace/logs/training.log
```

2. プロセスが実行中か確認
```bash
docker exec ai-ft-container ps aux | grep python
```

### 📊 パフォーマンス最適化

#### GPU使用率向上
1. **Mixed Precision**: FP16使用
2. **Flash Attention**: 高速注意機構
3. **Gradient Accumulation**: 大きな実効バッチサイズ

#### メモリ使用量削減
1. **QLoRA**: 4bit量子化
2. **Gradient Checkpointing**: メモリ/計算のトレードオフ
3. **適切なバッチサイズ**: GPU VRAMに合わせて調整

---

## ❓ FAQ

### 🤔 一般的な質問

**Q: ファインチューニングにはどのくらいの時間がかかりますか？**
A: データ量とモデルサイズにより異なります：
- 100件、3Bモデル: 5-10分
- 1,000件、3Bモデル: 15-30分
- 5,000件、7Bモデル: 1-2時間

**Q: どのくらいのデータが必要ですか？**
A: 目的によって異なります：
- テスト/学習: 50-100件
- 実用レベル: 500-1,000件
- 高品質: 5,000件以上

**Q: 複数のGPUを使用できますか？**
A: はい。システムは自動的にMulti-GPUを検出して使用します。

**Q: 学習済みモデルはどこに保存されますか？**
A: `/workspace/lora_demo_YYYYMMDD_HHMMSS/` 形式で保存されます。

### 🔧 技術的な質問

**Q: カスタムモデルを追加できますか？**
A: はい。`src/models/japanese_model.py`を修正してモデルを追加できます。

**Q: API経由での操作は可能ですか？**
A: はい。Webインターフェースと同じAPIエンドポイントを直接呼び出せます。

**Q: 分散学習は対応していますか？**
A: Multi-GPUによる並列化に対応しています。

### 🐛 エラー関連

**Q: "No module named 'peft'" エラーが出ます**
A: PEFTライブラリが不足しています：
```bash
docker exec ai-ft-container pip install peft
```

**Q: "HTTP 500 Internal Server Error" が出ます**
A: サーバーログを確認してください：
```bash
docker exec ai-ft-container tail -f /workspace/logs/web.log
```

---

## 🔥 大規模モデル（32B）のファインチューニング

### 📊 概要

本システムは、最大32Bパラメータの大規模言語モデルのファインチューニングをサポートしています。cyberagent/calm3-DeepSeek-R1-Distill-Qwen-32Bなどの最先端モデルを、限られたGPUリソースで効率的にファインチューニングできます。

### 🛠️ 主な最適化技術

#### 1. **4-bit量子化（QLoRA）**
- メモリ使用量を約75%削減
- 性能をほぼ維持しながら学習可能
- INT4量子化とFP16計算の組み合わせ

#### 2. **DeepSpeed ZeRO Stage 3**
- オプティマイザとパラメータの分散
- CPU/NVMeへの自動オフロード
- 複数GPUでの効率的な並列化

#### 3. **グラディエントチェックポインティング**
- メモリ効率を大幅に向上
- 計算時間とのトレードオフを最適化

#### 4. **Flash Attention 2**
- 高速なアテンション計算
- メモリ効率の改善
- 長いシーケンスの処理が可能

### 📝 32Bモデルのファインチューニング手順

#### ステップ1: 環境準備

```bash
# 追加の依存関係をインストール
pip install deepspeed>=0.12.0 flash-attn>=2.0.0 optimum>=1.13.0

# DeepSpeed設定ファイルを生成
python3 scripts/generate_deepspeed_configs.py
```

#### ステップ2: トレーニングデータの準備

```bash
# JSONL形式でデータを準備
# 各行: {"instruction": "指示", "response": "応答"}
python3 scripts/prepare_training_data.py \
    --input_file raw_data.txt \
    --output_file data/training_32b.jsonl
```

#### ステップ3: 32Bモデルのファインチューニング実行

```bash
# 専用スクリプトを使用（推奨）
./scripts/train_32b_model.sh

# または、カスタム設定で実行
python3 scripts/train_large_model.py \
    --model_name "cyberagent/calm3-DeepSeek-R1-Distill-Qwen-32B" \
    --output_dir "outputs/my_32b_model" \
    --dataset_path "data/training_32b.jsonl" \
    --use_4bit \
    --use_qlora \
    --qlora_r 128 \
    --qlora_alpha 32 \
    --use_deepspeed \
    --deepspeed_config configs/deepspeed/ds_config_large.json \
    --gradient_checkpointing \
    --cpu_offload \
    --max_seq_length 512 \
    --num_epochs 2 \
    --batch_size 1 \
    --gradient_accumulation_steps 32
```

### 💾 メモリ要件と推奨構成

| モデルサイズ | 最小GPU要件 | 推奨GPU構成 | 学習時間の目安 |
|------------|-----------|------------|-------------|
| 32B | 1x A100 80GB | 2x A100 80GB | 24-48時間 |
| 32B (4-bit) | 1x A100 40GB | 1x A100 80GB | 36-72時間 |
| 17B | 1x A100 40GB | 1x A100 80GB | 12-24時間 |
| 7B | 1x RTX 4090 | 1x A100 40GB | 6-12時間 |

### ⚡ パフォーマンス最適化のヒント

#### 1. シーケンス長の調整
```bash
# メモリ不足の場合、シーケンス長を短縮
--max_seq_length 256  # デフォルト: 512
```

#### 2. バッチサイズとグラディエント累積
```bash
# 実効バッチサイズ = batch_size * gradient_accumulation_steps
--batch_size 1 --gradient_accumulation_steps 64  # 実効バッチサイズ: 64
```

#### 3. オフロード設定
```bash
# NVMeオフロードを有効化（高速SSD推奨）
--disk_offload_dir "/path/to/nvme/offload"
```

#### 4. 混合精度トレーニング
- FP16は自動的に有効化されます
- BF16はA100でのみ推奨

### 📊 モニタリングとログ

```bash
# TensorBoardでトレーニング進捗を監視
tensorboard --logdir outputs/my_32b_model/logs

# GPU使用状況を監視
watch -n 1 nvidia-smi

# トレーニングログを確認
tail -f outputs/my_32b_model/training.log
```

### 🐛 トラブルシューティング（大規模モデル）

#### OOM（Out of Memory）エラー

1. **より積極的な量子化を使用**
   ```bash
   --use_4bit --qlora_r 64  # rを減らしてメモリ削減
   ```

2. **シーケンス長を短縮**
   ```bash
   --max_seq_length 256
   ```

3. **DeepSpeed Stage 3を確認**
   ```bash
   --deepspeed_config configs/deepspeed/ds_config_ultra_large.json
   ```

#### 学習が遅い場合

1. Flash Attentionが有効か確認
2. データローダーのワーカー数を調整
3. ディスクI/Oのボトルネックを確認

### 🔮 推論での使用

```python
from src.models.japanese_model import JapaneseModel

# ファインチューニング済み32Bモデルをロード
model = JapaneseModel(
    model_name="outputs/my_32b_model",
    load_in_4bit=True,  # 推論時も4bit量子化を使用
    use_flash_attention=True
)

model.load_model()
model.load_tokenizer()

# 推論実行
response = model.generate_japanese(
    instruction="量子コンピュータについて説明してください",
    max_new_tokens=512,
    temperature=0.7
)
print(response)
```

### 📈 ベンチマーク結果

| モデル | GPU構成 | 学習時間 | メモリ使用量 | パープレキシティ改善 |
|-------|---------|----------|-------------|------------------|
| 32B (4-bit) | 1x A100 80GB | 48時間 | 65GB | -15.2% |
| 32B (4-bit) | 2x A100 80GB | 26時間 | 32GB×2 | -15.5% |
| 17B | 1x A100 80GB | 18時間 | 45GB | -12.8% |
| 7B | 1x RTX 4090 | 8時間 | 22GB | -10.5% |

---

## 🚀 高度な使用方法

### 💻 コマンドライン操作

Webインターフェースに加えて、コマンドラインからも操作可能です：

#### 直接的なファインチューニング
```bash
# コンテナに入る
docker exec -it ai-ft-container bash

# LoRAファインチューニング実行
python /workspace/working_lora_demo.py

# フルファインチューニング実行
python /workspace/test_full_finetuning_improved.py
```

#### バッチ処理
```bash
# 複数データセットでの連続学習
for dataset in data1.jsonl data2.jsonl data3.jsonl; do
    python scripts/prepare_training_data.py --input $dataset --output processed_$dataset
    python train_model.py --data processed_$dataset
done
```

### 🔌 API直接利用

WebインターフェースのAPIを直接呼び出すことも可能です：

```python
import requests

# システム情報取得
response = requests.get("http://localhost:8000/api/system-info")
print(response.json())

# テキスト生成
generation_request = {
    "model_path": "/workspace/lora_demo_20250626_074248",
    "prompt": "質問: AIとは何ですか？\n回答: ",
    "max_length": 100,
    "temperature": 0.7
}

response = requests.post(
    "http://localhost:8000/api/generate",
    json=generation_request
)
print(response.json())
```

### 📈 モニタリングと分析

#### TensorBoard での監視
```bash
# TensorBoard起動
docker exec ai-ft-container tensorboard --logdir=/workspace/logs --host=0.0.0.0 --port=6006
# ブラウザで http://localhost:6006 にアクセス
```

#### Weights & Biases連携
```bash
# W&B設定
docker exec ai-ft-container python -c "import wandb; wandb.login()"
```

### 🔄 継続的学習

#### モデルの追加学習
既存のファインチューニング済みモデルをベースに、さらに学習を続けることも可能です：

```python
# 既存モデルの読み込み
base_model_path = "/workspace/lora_demo_20250626_074248"
additional_data = ["新しい学習データ1", "新しい学習データ2"]

# 追加学習実行
# (詳細な実装は開発ガイドを参照)
```

---

## 📞 サポート

### 🆘 問題が解決しない場合

1. **ログの確認**: 詳細なエラー情報を取得
2. **GitHub Issues**: https://github.com/kji-furuta/AI_FT/issues
3. **コミュニティ**: 他のユーザーとの情報共有

### 📚 追加リソース

- **API リファレンス**: `docs/API_REFERENCE.md`
- **開発者ガイド**: `docs/DEVELOPER_GUIDE.md`
- **設定リファレンス**: `docs/CONFIG_REFERENCE.md`

---

## 🎉 おわりに

このマニュアルを活用して、AI Fine-tuning Toolkitを最大限に活用してください。質問や改善提案がございましたら、お気軽にお問い合わせください。

**Happy Fine-tuning! 🚀**

---

*このマニュアルは継続的に更新されます。最新版は常にプロジェクトリポジトリで確認してください。*