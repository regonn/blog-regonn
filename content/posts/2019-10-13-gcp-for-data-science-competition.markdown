---
title: "重めのデータを扱う時のGPUを有効にしたGCP(GCE)の設定"
date: 2019-10-13 9:00:00 +0900
categories: "data-science"
slug: "data-science"
tags:
  - "gcp"
  - "gce"
  - "kaggle"
desc: "Kaggle等で大きめのデータをGCP(GCE)のJupyterNotebookで利用したい場合の構築メモ"
keywords: "GCP,データサイエンス"
---

最近の Kaggle 等のデータサイエンスコンペティションでは、データ量が大きく、クラウドで JupyterNotebook 用のインスタンスを立ち上げる際にも、メモリの関係等から GCP の設定をいじる必要があったので構築の際に気をつけていることをメモしておく。

## 目標

GPU が有効になった LightGBM を JupyterNotebook 上で動かせるようにする。
Docker は利用しない。

### メモリを最大限に使えるようにする ※機械学習専用設定向けなので、一般のサーバーでは非推奨

```
sudo sh -c "echo 'vm.overcommit_memory=1' >> /etc/sysctl.conf && sysctl -p"
```

### GCP には Swap が無いので、Swap を追加する

```
sudo dd if=/dev/zero of=/swapfile bs=1M count=10000
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

### 先に古い nvidia cuda 系を削除

```
sudo apt remove nvidia-*
sudo apt remove cuda-*
```

### GPU ドライバーインストール

[GPU の追加または削除  \|  Compute Engine ドキュメント  \|  Google Cloud](https://cloud.google.com/compute/docs/gpus/add-gpus?hl=ja) の記事の 「GPU ドライバのインストール」を参考に最新版を入れる

### Python 環境

[Pyenv を使用して Ubuntu に Python をインストールする \- Qiita](https://qiita.com/micheleno13/items/39ad85cfe44ca32f53ee) を参考に pyenv を入れて、 miniconda の最新版を入れている

### JupyterNotebook や GCP のネットワーク

[Anaconda を利用して Compute Engine のインスタンス上に Jupyter Notebook を導入する \- Qiita](https://qiita.com/tk_01/items/307716a680460f8dbe17) を参考に Jupyter の設定や GCP のネットワーク側の設定をする

#### JupyterNotebook での利用メモリを 10GB にする

`~/.jupyter/jupyter_notebook_config.py` に以下を追加

```
c.NotebookApp.max_body_size = 10737412742
c.NotebookApp.max_buffer_size = 10737412742
```

### JupyterNotebook のデーモン化

[jupyter を systemd に登録してデーモン化 \| KIN の雑記帳](https://kinformation.sakura.ne.jp/20170704-01) を参考にして GCP インスタンスが立ち上がったら、JupyterNotebook も立ち上がるようにしておく

### LightGBM インストール

[LightGBM GPU Tutorial](https://lightgbm.readthedocs.io/en/latest/GPU-Tutorial.html) を参考にして LightGBM の GPU 有効版をインストールする(XGBoost や CatBoost はそのまま pip で入れれば有効になる)
