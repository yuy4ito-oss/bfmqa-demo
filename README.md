# BFMQA Demo（blackbox: $f(x)=x^T Q x$ ）

## 概要（日本語）
このリポジトリは、**BFMQA（Bayesian Factorization Machine + Simulated Annealing）** の最小デモです。  
ブラックボックス関数として、乱数で生成した対称行列 \(Q\) に対する二次形式

- $f(x)=x^T Q x, \  ( x \in \{0,1\}^d)$

を用います。Bayesian Factorization Machine（PyMC + ADVI）で代理モデルを学習し、事後分布サンプルから構築した QUBO を Simulated Annealing（`neal`）で解いて次の候補を生成します。

## Google Colab で実行

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/yuy4ito-oss/bfmqa_demo/blob/main/bfmqa_demo.ipynb)

## ローカルで実行

### 1) uv のインストール（未導入の場合）
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### 2) 依存関係のインストール
```bash
uv venv
source .venv/bin/activate
uv pip install -r requirements.txt
```

### 3) ノートブックを起動
```bash
uv run jupyter lab
# または
uv run jupyter notebook
```


