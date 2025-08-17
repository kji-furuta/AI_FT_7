# MoE-RAG Hybrid Search System

土木工学専門のMoE（Mixture of Experts）とRAG（Retrieval-Augmented Generation）を統合したハイブリッド検索システム

## 🌟 概要

本システムは、ファインチューニング、継続学習、MoEにより学習したAIモデルを用いて、RAGシステムとのハイブリッド検索を実現します。土木・建設分野に特化した8つの専門エキスパートが、高精度な回答を提供します。

## 🏗️ システムアーキテクチャ

### 専門エキスパート
1. **構造設計** - 構造計算、耐震設計、荷重解析
2. **道路設計** - 線形設計、視距、設計速度
3. **地盤工学** - 土質調査、支持力、液状化対策
4. **水理・排水** - 流量計算、排水計画、治水
5. **材料工学** - コンクリート、鋼材、材料試験
6. **施工管理** - 工程管理、品質管理、安全管理
7. **法規・基準** - 道路構造令、設計基準、仕様書
8. **環境・維持管理** - 環境影響、維持管理、劣化診断

## 🚀 主要機能

- **ハイブリッド検索**: MoE専門知識とRAG文書検索の融合
- **適応的融合**: クエリタイプに応じた最適な結果統合
- **並列処理**: MoEとRAGの同時実行による高速化
- **信頼度スコア**: エントロピーベースの信頼度計算
- **WebSocket対応**: リアルタイムストリーミング回答

## 📦 インストール

```bash
# リポジトリのクローン
git clone https://github.com/kji-furuta/MoE_RAG.git
cd MoE_RAG

# 依存関係のインストール
pip install -r requirements.txt

# Dockerコンテナの起動（推奨）
docker-compose up -d
```

## 🎯 クイックスタート

### 1. MoEモデルの訓練

```bash
# Dockerコンテナ内で実行
docker exec ai-ft-container bash scripts/moe/train_moe.sh demo 1 2
```

### 2. APIサーバーの起動

```bash
python app/moe_rag_api.py
```

### 3. Web UIへのアクセス

ブラウザで以下にアクセス：
```
http://localhost:8050/static/moe_rag_ui.html
```

## 📚 API使用例

### ハイブリッドクエリ

```python
import requests

response = requests.post(
    "http://localhost:8050/api/moe-rag/query",
    json={
        "query": "設計速度80km/hの道路における最小曲線半径は？",
        "top_k": 5,
        "use_reranking": True
    }
)

result = response.json()
print(f"回答: {result['answer']}")
print(f"信頼度: {result['confidence_score']}")
print(f"選択エキスパート: {result['selected_experts']}")
```

### ストリーミングクエリ

```python
import json
import sseclient

response = requests.post(
    "http://localhost:8050/api/moe-rag/stream",
    json={"query": "橋梁の耐震設計について", "stream": True},
    stream=True
)

client = sseclient.SSEClient(response)
for event in client.events():
    data = json.loads(event.data)
    if data.get('chunk'):
        print(data['chunk'], end='')
```

## 🔧 設定

設定ファイル: `src/moe_rag_integration/config/integration_config.yaml`

```yaml
# MoE設定
moe:
  num_experts: 8
  num_experts_per_tok: 2
  
# 統合設定  
integration:
  weights:
    moe: 0.4      # MoE重み
    rag: 0.6      # RAG重み
```

## 📊 パフォーマンス

- **推論速度**: 平均200ms (P50)
- **スループット**: 100 QPS
- **精度**: 専門分野質問 95%以上
- **メモリ使用量**: < 15GB

## 🧪 テスト

```bash
# 統合テストの実行
python scripts/test_moe_rag_integration.py

# コンポーネントテスト
pytest tests/
```

## 📁 プロジェクト構造

```
MoE_RAG/
├── src/
│   ├── moe/                      # MoEモデル実装
│   ├── moe_rag_integration/      # 統合モジュール
│   │   ├── hybrid_query_engine.py
│   │   ├── expert_router.py
│   │   ├── response_fusion.py
│   │   └── moe_serving.py
│   └── rag/                      # RAGシステム
├── app/
│   ├── moe_rag_api.py           # FastAPI
│   └── static/
│       └── moe_rag_ui.html      # Web UI
├── scripts/
│   └── test_moe_rag_integration.py
└── docker-compose.yml
```

## 🤝 コントリビューション

プルリクエストを歓迎します！大きな変更の場合は、まずissueを開いて変更内容を議論してください。

## 📄 ライセンス

MIT License

## 👨‍💻 作者

- GitHub: [@kji-furuta](https://github.com/kji-furuta)

## 🙏 謝辞

- 帝国大学 土木工学科
- AI Fine-tuning Toolkit プロジェクト

---

**注意**: 本システムは研究・開発目的で作成されています。本番環境での使用前に十分なテストを行ってください。