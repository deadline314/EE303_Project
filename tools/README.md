# Tools 目錄

此目錄包含完整的兩階段商品匹配系統的所有工具，按功能分類組織。

## 目錄結構

```
tools/
├── data_processing/           # 資料處理工具
│   ├── simple_correct_parser.py
│   ├── view_correct_data.py
│   ├── mysql_file_reader.py
│   └── README.md
├── embedding_processors/      # Embedding 處理器
│   ├── clip_image_processor.py
│   ├── text_embedding_processor.py
│   └── README.md
├── dataset_generators/        # 資料集生成器
│   ├── bm25_sampler.py
│   ├── classification_dataset_generator.py
│   ├── triplet_dataset_generator.py
│   ├── generate_training_datasets.py
│   └── README.md
├── model_training/           # 模型訓練
│   ├── train_triplet_model.py
│   ├── train_classification_model.py
│   └── README.md
├── model_testing/            # 模型測試
│   ├── test_models.py
│   └── README.md
├── pipelines/                # 整合管道
│   ├── train_and_test_pipeline.py
│   └── README.md
└── README.md                 # 本文件
```

## 快速開始

### 1. 資料處理

```bash
cd data_processing
python simple_correct_parser.py
python view_correct_data.py
```

### 2. 生成訓練資料

```bash
cd dataset_generators
python generate_training_datasets.py \
    --leaf_file ../../data/correct_structure_data/leaf_v1_correct.csv \
    --root_file ../../data/correct_structure_data/root_v1_correct.csv \
    --output_dir training_data \
    --k 5 \
    --sample_size 1000 \
    --embedding_dim 512 \
    --use_clip
```

### 3. 執行完整管道

```bash
cd pipelines
python train_and_test_pipeline.py \
    --training_data_dir ../dataset_generators/training_data \
    --output_dir pipeline_results \
    --text_embedding_dim 512 \
    --image_embedding_dim 512 \
    --hidden_dim 256 \
    --output_dim 128 \
    --batch_size 32 \
    --epochs 50 \
    --learning_rate 0.001 \
    --similarity_threshold 0.5
```

## 工作流程

### 階段 1: 資料準備

1. **資料處理** (`data_processing/`)

   - 解析 MySQL 資料檔案
   - 檢視和驗證資料結構
   - 準備正確格式的資料

2. **Embedding 生成** (`embedding_processors/`)
   - 生成文字 embedding (BERT/TF-IDF/自定義)
   - 生成圖片 embedding (CLIP)
   - 支援批次處理和錯誤處理

### 階段 2: 資料集生成

3. **樣本選擇** (`dataset_generators/`)
   - 使用 BM25 算法選擇正負樣本
   - 生成分類訓練集
   - 生成 Triplet 訓練集

### 階段 3: 模型訓練

4. **模型訓練** (`model_training/`)
   - 訓練 Triplet 相似度模型
   - 訓練分類模型
   - 自動保存最佳模型

### 階段 4: 模型測試

5. **模型評估** (`model_testing/`)
   - 測試單個模型性能
   - 測試兩階段組合性能
   - 生成詳細評估報告

### 階段 5: 整合管道

6. **自動化流程** (`pipelines/`)
   - 自動執行所有步驟
   - 生成完整報告
   - 錯誤處理和恢復

## 各模組詳細說明

### 📁 data_processing/

- **功能**: 處理和解析原始 MySQL 資料
- **主要工具**: 資料解析器、檢視器、檔案掃描器
- **輸出**: 正確結構的 CSV 檔案

### 📁 embedding_processors/

- **功能**: 生成文字和圖片的 embedding
- **支援方法**: BERT、TF-IDF、CLIP、自定義模型
- **特點**: 模組化設計，易於擴展

### 📁 dataset_generators/

- **功能**: 生成機器學習訓練資料集
- **資料類型**: BM25 樣本、分類資料集、Triplet 資料集
- **特點**: 可配置的參數和自動化流程

### 📁 model_training/

- **功能**: 訓練兩階段商品匹配模型
- **模型類型**: 相似度模型、分類模型
- **特點**: 完整的訓練監控和檢查點機制

### 📁 model_testing/

- **功能**: 測試和評估模型性能
- **評估類型**: 單模型測試、組合模型測試
- **輸出**: 詳細的性能指標和報告

### 📁 pipelines/

- **功能**: 整合所有步驟的自動化管道
- **特點**: 端到端的自動化流程
- **輸出**: 完整的訓練和測試報告

## 配置和參數

### 通用配置

- **設備**: 支援 CPU 和 GPU 訓練
- **批次大小**: 可根據記憶體調整
- **學習率**: 可調整以優化訓練效果

### 模型配置

- **文字 embedding 維度**: 512 (BERT) 或自定義
- **圖片 embedding 維度**: 512 (CLIP)
- **隱藏層維度**: 256 (可調整)
- **輸出維度**: 128 (相似度模型)

### 訓練配置

- **訓練輪數**: 50 (可調整)
- **驗證比例**: 20%
- **早停機制**: 基於驗證損失

## 自定義和擴展

### 添加新的 Embedding 方法

1. 在 `embedding_processors/` 中實現新的處理器
2. 繼承基類並實現必要方法
3. 在工廠函數中註冊新方法

### 修改模型架構

1. 在 `model_training/` 中修改模型類別
2. 調整網路層和參數
3. 更新訓練配置

### 添加新的評估指標

1. 在 `model_testing/` 中實現新的評估邏輯
2. 更新測試報告格式
3. 整合到管道中

## 故障排除

### 常見問題

1. **記憶體不足**: 減少 batch_size 或使用更小的模型
2. **訓練不收斂**: 調整學習率或增加訓練輪數
3. **模型載入失敗**: 檢查模型路徑和格式

### 性能優化

1. **GPU 加速**: 使用 CUDA 設備
2. **批次處理**: 調整批次大小
3. **並行處理**: 使用多進程處理

## 注意事項

- 確保所有依賴套件已正確安裝
- 檢查輸入資料的格式和路徑
- 根據硬體配置調整參數
- 定期備份訓練結果和模型

## 相關文檔

- [詳細技術文檔](../docs/README.md)
- [訓練資料集使用指南](../docs/training_dataset_guide.md)
- [兩階段模型使用指南](../docs/two_stage_model_guide.md)
