# 平均曲率流シミュレータ / Mean Curvature Flow Simulator

ブラウザ上で**平均曲率流 (mean curvature flow, MCF)** をインタラクティブに可視化する Web アプリです。2次元ユークリッド空間内の閉曲線の運動（曲線短縮流）と、3次元ユークリッド空間内の閉曲面の運動の2つを実装しています。外部ライブラリ・ビルド・サーバを一切必要とせず、単一の HTML ファイルだけで動作します。

> An interactive, dependency-free web visualizer for mean curvature flow — 2D curve shortening flow and 3D surface flow, each in a single self-contained HTML file.

## ▶ ライブデモ

- **2次元（曲線短縮流）**: https://nagajin.github.io/mean-curvature-flow/
- **3次元（曲面）**: https://nagajin.github.io/mean-curvature-flow/surface3d.html

## 概要

平均曲率流は、部分多様体の1パラメーター族 $\{M_t\}_{t\in[0,T)}$ が各時刻においてその平均曲率ベクトル場 $\mathbf{H}_t$ の方向へ変形していく流れで、はめ込みの族 $\{f_t\}$ を用いて

$$\frac{\partial F}{\partial t} = \mathbf{H}_t$$

で定義されます。これは体積（面積）汎関数のマイナス勾配流であり、面積・体積を最も速く減少させる流れと解釈されます。本ソフトウェアは、凸化・1点への崩壊・特異点の発生といった現象を直接観察できるようにすることを目的としています。

数学的な定義は、東京理科大学 小池直之先生の講義資料「平均曲率流とは」にもとづいています。

## 機能

### 2次元（曲線短縮流）
- 初期曲線プリセット：円・楕円・星形・ダンベル形・花形・角つき四角・うねり曲線・十字形
- マウスによる閉曲線の自由描画
- 曲率の色分け表示、時刻列 $\{M_t\}$ の残像（入れ子状）表示
- 周長・囲む面積・$dA/dt$ などの計測量表示
- 時間刻み $\Delta t$・点数・速度の調整

### 3次元（曲面）
- 初期曲面プリセット：球・楕円体・ダンベル形・トーラス・でこぼこ球・丸み四角柱
- マウスで回転、ホイールで拡大縮小
- 平均曲率の色分け、ワイヤーフレーム、自動回転
- メッシュ細分レベルの変更（642〜5120 面）
- 表面積・$dA/dt$・囲む体積の計測量表示、崩壊先（点／曲線）の自動判定

## 数理・数値解法

### 2次元：Dziuk の半陰的有限要素法
曲線を $N$ 頂点の多角形で離散化し、弱形式を区分1次要素で離散化、弧長を旧曲線から評価する半陰的スキーム

$$(M + \Delta t\,L)\,x^{n+1} = M\,x^{n}$$

を解きます（$M$：集中質量行列、$L$：剛性行列）。閉曲線では係数行列が巡回三重対角となり、Sherman–Morrison 公式で厳密に解けます。各ステップで頂点を弧長について再配置して安定化しています。

### 3次元：Cotangent ラプラシアン
頂点における平均曲率ベクトルを cotangent 公式

$$(\Delta_S F)_i = \frac{1}{2A_i}\sum_{j\in\mathcal{N}(i)}\bigl(\cot\alpha_{ij}+\cot\beta_{ij}\bigr)(x_j - x_i)$$

で離散化し、2次元と同じ半陰的スキームを適用します。大規模疎対称正定値系は、対角前処理付き共役勾配法 (CG) で求解します。

## 数値検証

理論値との突き合わせ結果：

| 形状 | 量 | 理論値 | 数値結果 |
|---|---|---|---|
| 円 (2D, $R_0=1.6$) | 消滅時刻 $A_0/2\pi$ | $1.280$ | $1.281$ |
| 円 (2D) | $dA/dt$ | $-2\pi=-6.283$ | $-6.28$ |
| 球 (3D, $R_0=1.3$) | 消滅時刻 $R_0^2/4$ | $0.4205$ | $0.421$ |
| 球 (3D) | $dA/dt$ | $-16\pi=-50.27$ | $-49.9$ |
| ダンベル (3D) | くびれ | ネックピンチ | $t\approx0.066$ |
| トーラス (3D) | 崩壊先 | 中心円（点でない） | 管がピンチ |

- **2次元**：単純閉曲線は面積が一定速度 $-2\pi$ で減少し、$T=A_0/2\pi$ で1点へ崩壊（Gage–Hamilton–Grayson）。非凸曲線も凸化を経て崩壊します。
- **3次元の対比**：同じ「ダンベル形」でも、2次元曲線は Grayson の定理によりピンチせず凸化し、3次元曲面はくびれがピンチします。**トーラスは凸でないため1点には崩壊せず**、管が中心円へピンチします（凸閉曲面のみ丸い1点へ収束：Huisken の定理）。

## 使い方

ブラウザでライブデモの URL を開くだけです。ローカルで動かす場合は、リポジトリを取得して `index.html` をブラウザで開いてください（ビルド・依存関係は不要）。

```bash
git clone https://github.com/nagajin/mean-curvature-flow.git
cd mean-curvature-flow
open index.html   # またはブラウザにドラッグ&ドロップ
```

## ファイル構成

| ファイル | 内容 |
|---|---|
| `index.html` | 2次元・曲線短縮流のシミュレータ |
| `surface3d.html` | 3次元・曲面のシミュレータ |
| `LICENSE` | MIT ライセンス |

## ライセンス

[MIT License](LICENSE) © 2026 長塚 悠仁 (Yuto Nagatsuka)

## 参考

- 小池直之，講義資料「平均曲率流とは」（東京理科大学）
- G. Dziuk, *An algorithm for evolutionary surfaces*, Numer. Math. 58 (1990).
- M. Desbrun, M. Meyer, P. Schröder, A. H. Barr, *Implicit fairing of irregular meshes using diffusion and curvature flow*, SIGGRAPH 1999.
- M. Meyer, M. Desbrun, P. Schröder, A. H. Barr, *Discrete differential-geometry operators for triangulated 2-manifolds*, 2003.
