# BFMQA Demo

## 概要
このリポジトリは、**BFMQA（Bayesian Factorization Machine + Simulated Annealing）** の最小デモです。  
BFMQAは、ブラックボックス関数 $f(x)$ を「直接」最適化するのではなく、**代理モデル（サロゲート）**を学習して、その代理モデルを使って「次に評価すべき候補」を効率よく提案することで、少ない評価回数で良い解に近づくことを狙う手法です。

### ブラックボックス（今回のデモ）
本デモではブラックボックスとして、乱数で生成した対称行列 $Q$ を用いた二次形式

- $f(x)=x^T Q x \quad (x \in \{0,1\}^d)$

を採用します。ここで $x$ は $d$ 個のバイナリ決定変数（0/1）からなるベクトルです。  
$Q$ は乱数で生成した後、対称化し、平均を引いて中心化した行列として扱います（再現性のため `bb_seed` で固定可能）。

### BFMQAの流れ（このデモでやっていること）
このノートブックでは、以下を **サイクル（反復）**で繰り返します。

1. **初期データの生成**  
   まずランダムな $x$ を `n0` 個生成し、ブラックボックス $f(x)$ を評価して学習データを作ります。

2. **Bayesian Factorization Machine（BFM）で代理モデルを学習**  
   これまでに集めたデータ $(x, f(x))$ を用いて、Bayesian FM を回帰モデルとして学習します。  
   推論は PyMC の **ADVI（変分推論）**で行い、モデルパラメータの事後分布を近似します。  
   （学習を安定させるため、目的関数値 $y=f(x)$ は毎サイクル全データで mean/std を取り直して標準化しています。）

3. **事後分布サンプル → QUBOの構築**  
   学習したBFMからパラメータをサンプルし、そのサンプルを使って「候補探索用のQUBO」を構築します。  
   直感的には「BFMが小さくなると予測する $x$ を見つけるための目的関数」を二次形式として作ります。

4. **Simulated Annealing（`neal`）でQUBOを最小化し、候補 $x$ を生成**  
   `neal` のSAでQUBOを解き、次に評価する候補 $x$ を `qubos_per_cycle` 個生成します。  

5. **ブラックボックスで実評価してデータに追加**  
   生成した候補をブラックボックス $f(x)$ で実評価し、データセットに追加します。  
   この繰り返しにより、ランダム探索より効率的に小さい $f(x)$ を目指します。

### このデモで確認できること
- 代理モデル学習（Bayesian FM）と候補生成（SA）を組み合わせた探索の基本ループ
- サイクルごとの最良値 `cycle_best` と累積最良 `overall_best` の推移
- `plot_all_points` による「全評価点」と「累積最良」の可視化

※本リポジトリは **アルゴリズムの動作確認と説明を目的としたデモ**です。問題設定（ブラックボックス）やパラメータは簡単化しており、実問題への適用にはスケーリングや制約の取り扱い等の追加設計が必要になります。

（BFMQAを用いた確率計画法については、後日公開予定）

## Google Colab で実行

最初のセルのコメントアウトを解除してください。

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/yuy4ito-oss/bfmqa-demo/blob/main/bfmqa_demo.ipynb)

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


