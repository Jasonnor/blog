title: 以 tf.data 優化訓練資料輸入管道 丨 2018 Google 開發者大會
author: Jasonnor
tags:
  - Machine learning
  - TensorFlow
  - Python
categories:
  - 機器學習
date: 2018-09-21 19:30:00
---
![preview](https://i.imgur.com/nBLUAAs.png)

> 本篇文章受[掘金邀請撰寫](https://juejin.im/post/5ba4b2a7f265da0ab719aa7e)，更多GCC 2018演講介紹歡迎前往：[2018 Google 開發者大會 掘金專題](https://juejin.im/e/gdd)

GDD 2018 第二天的 9 月 21 日 ，陳爽（Google Brain 軟體工程師）為我們帶來了《以 tf.data 優化訓練數據》，講解如何使用 tf.data 為各類模型打造高性能的 TensorFlow 輸入渠道，本文將摘錄演講技術干貨。

<!-- more -->

<span hidden itemprop="image" itemscope itemtype="https://schema.org/ImageObject">
  <img src="https://i.imgur.com/nBLUAAs.png"/>
  <meta itemprop="url" content="https://i.imgur.com/nBLUAAs.png">
  <meta itemprop="width" content="60">
  <meta itemprop="height" content="60">
</span>

## 資料輸入管道

- 大多人將時間和金錢花在神經網路架構上，資料輸入容易被忽略
- 沒有好的資料輸入管道，GPU 再強速度也不會顯著提高
- 目標：高效、靈活、易用

## ETL 系統

![](https://i.imgur.com/ugbv7zC.png)

1. 提取資料（Extract）：將訓練資料從存取器（硬碟、雲端等）提取
2. 轉換資料（Transform）：將資料轉換為模型可讀取的資料，同時進行數據清洗等預處理
3. 裝載資料（Load）：將處理好的資料裝載至加速器

## tf.data：為機器學習設計的資料輸入系統

![](https://i.imgur.com/xVuatj7.png)

圖中程式分別對應 ETL 系統的三個步驟，使用 tf.data 即可輕鬆實現。

## tf.data 優化手段：以上圖程式為例

1. 多線程處理（使用 **num_parallel_reads**）

```python
files = tf.data.Dataset.list_files("training-*-of-1024.tfrecord")
dataset = tf.data.TFRecordDataset(files, num_parallel_reads=32)
```

2. 合併轉換步驟（如 **shuffle_and_repaeat**, **map_and_batch**）

```python
dataset = dataset.apply(tf.contrib.data.shuffle_and_repaeat(10000, NUM_EPOCHS))
dataset = dataset.apply(tf.contrib.data.map_and_batch(lambda x: ..., BATCH_SIZE))
```

3. 流水線化（使用 **prefetch_to_device**）

```python
dataset = dataset.apply(tf.contrib.data.prefetch_to_device("/gpu:0"))
```

![](https://i.imgur.com/GIq5KCE.png)

最終程式如下圖所示，更多優化手段可以參考 [tf.data 性能指南](https://www.tensorflow.org/performance/datasets_performance)：

![](https://i.imgur.com/MUtSvAA.png)

## tf.data 的靈活性

### 支持函數式編程

![](https://i.imgur.com/5iTCJQu.png)

如上圖，可以用自定義的 map_fn 處理 TensorFlow 或兼容的函數，同時支持 AutoGraph 處理過的函數。

### 支持不同語言與資料類型

- 使用 Dataset.form_generator() 支持 Python 程式生成 Dataset
- 使用 DatasetOpKernel 和 tf.load_op_library 支持自定義 C++ 資料處理程式

如下圖，使用 Python 自帶的 urllib 獲取伺服器資料，存入 dataset：

![](https://i.imgur.com/lQpPU4m.png)

### 支持多種資料來源

如普通文件系統、GCP 雲儲存、其他雲儲存、SQL 數據庫等。

讀取 Google 雲儲存的 TFRecord 文件示例：

```python
files = tf.contrib.data.TFRecordDataset(
  "gs://path/to/file.tfrecord", num_parallel_reads=32)
```

使用自訂 SQL 資料庫示例：

```python
files = tf.contrib.data.SqlDataset(
  "sqllite", "/foo/db.sqlite", "SELECT name, age FROM people", 
  (tf.string, tf.int32))
```

## tf.data 的易用性

### 在 Eager 執行模式下，可以直接使用 Python for 循環：

```python
tf.enable_eager_execution()
for batch in dataset:
    train_model(batch)
```

### 為 TF Example 或 CSV 提供現有高效配方

![](https://i.imgur.com/kpSFS08.png)

上圖可以簡單替換為一個函數：

```python
dataset = tf.contrib.data.make_batched_features_dataset(
  "training-*-of-1024.tfrecord",
  BATCH_SIZE, features, num_epochs=NUM_EPOCHS)
```

使用 CSV 資料集的情境：

```python
dataset = tf.contrib.data.make_csv_dataset(
  "*.csv", BATCH_SIZE, num_epochs=NUM_EPOCHS)
```

### 使用 AUTOTUNE 自動調節管道

可以簡單的使用 AUTOTUNE 找到 prefetching 的最佳參數：

```python
dataset = dataset.prefetch(tf.contrib.data.AUTOTUNE)
```

### 支持 Keras 和 Estimators 相互兼容

對於 Keras，可以將 dataset 直接傳遞使用；對於 Estimators 訓練函數，將 dataset 包裝至輸入函數並返回即可，如下示例：

```python
def input_fn():
    dataset = tf.contrib.data.make_csv_dataset(
      "*.csv", BATCH_SIZE, num_epochs=NUM_EPOCHS)
    return dataset

tf.estimator.Estimator(model_fn=train_model).train(input_fn=input_fn)
```

## 實際運用經驗

- 原始 tf.data 資料輸入程式： ~150 圖像 / 秒
- 管道化的 tf.data 資料輸入程式： ~1,750 圖像 / 秒 => **12倍的性能！**
- Cloud TPU 上使用 tf.data： ~4,100 圖像 / 秒
- Cloud TPU Pod 上使用 tf.data： ~219,000 圖像 / 秒

## 結論

本場演講介紹了 tf.data 這個兼具高效、靈活與易用的 API，同時了解如何運用管道化及其他優化手段來增進運算效能，以及許多可能未曾發現的實用函數。

## 資源

- 入門指南
  - [www.tensorflow.org/guide/datasets](https://www.tensorflow.org/guide/datasets)
  - [www.tensorflow.org/performance/datasets_performance](https://www.tensorflow.org/performance/datasets_performance)
- 示例程式
  - [github.com/tensorflow/benchmarks](https://github.com/tensorflow/benchmarks)
  - [github.com/tensorflow/models](https://github.com/tensorflow/models)
  - [github.com/tensorflow/tpu](https://github.com/tensorflow/tpu)
