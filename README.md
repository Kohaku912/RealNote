# ふせんホワイトボード（RealNote）

> **付箋を「引き裂ける」ホワイトボード。** ドラッグで動かし、ダブルクリックで編集し、
> 右クリックでメニュー。そして**何度でも引き裂けます** — 破いた破片はそれぞれ独立した
> 付箋として動かせます。
>
> **単一 HTML ファイル・外部ライブラリ 0 個**で実装。多角形分割と
> **Douglas–Peucker 法**によるパス単純化を自前で書いています。

![JavaScript](https://img.shields.io/badge/JavaScript-vanilla-F7DF1E?logo=javascript&logoColor=black)
![SVG](https://img.shields.io/badge/rendering-SVG-FFB13B?logo=svg&logoColor=white)
![依存](https://img.shields.io/badge/dependencies-0-brightgreen)
![行数](https://img.shields.io/badge/1%20file-1%2C405%20lines-blue)
![受賞](https://img.shields.io/badge/ZEN%20Study%20動くwebページコンテスト-優秀賞-gold)

**English summary** — A sticky-note whiteboard whose signature feature is *tearing*: you can rip
a note repeatedly, and each fragment becomes an independently movable note. Implemented in a
single 1,405-line HTML file with **zero external libraries** — including polygon splitting by a
line, Douglas–Peucker path simplification, and SVG clip-path geometry.

![スクリーンショット](Screenshot%202025-08-31%20233123.png)

---

## 操作

| 操作 | 動作 |
|---|---|
| ダブルクリック | 付箋を編集 |
| ドラッグ | 付箋を移動 |
| 右クリック | メニュー表示（色変更・複製・削除など） |
| **引き裂き** | **何度でも可能。破片は独立した付箋になる** |
| （自動保存） | ボードの状態を `localStorage` に保存 |

---

## 技術的なポイント

外部ライブラリを一切使わず、以下を自前で実装しています。

### 1. 多角形の分割（引き裂きの中核）

ドラッグした軌跡を直線とみなし、付箋の多角形を**その直線で2つに分割**します。

- `splitPolygonByLine` — 多角形を直線で分割する本体
- `lineIntersection` — 辺と直線の交点計算
- `getPointEdge` / `snapToNearestEdge` — 端点を最寄りの辺にスナップし、
  分割後に隙間や重なりが出ないようにする

### 2. Douglas–Peucker 法によるパス単純化

ドラッグ軌跡は生の点列のままだと点数が多すぎて破片の形状がギザギザになります。
**Douglas–Peucker 法**で許容誤差内の点を間引き、自然な「破れ目」の輪郭を得ています。

- `douglasPeucker` / `perpendicularDistance` — アルゴリズム本体
- `simplifyPath` — 前処理を含めた適用
- `processPointsForTear` — 引き裂き用の点列処理

### 3. SVG clip-path による破片の描画

破片は多角形として計算し、**SVG の `clip-path`** で付箋の内容を切り抜いて描画します。
これにより、テキストや画像を含む付箋でも、破片側に内容が正しく残ります。

- `buildTearPolygons` — 破片の多角形を構築
- `createTearClipPaths` / `createSubsequentTearClipPaths` — clip-path の生成
- `parsePolygonFromClipPath` — 既存の clip-path から多角形を復元（＝再引き裂きのため）
- `createSimpleTearFromExisting` / `performSimpleTear` / `performTear` — 引き裂きの実行

### 4. 破片の分離

破いた直後に2つの破片が自然に離れるよう、分離ベクトルを計算します
（`getTearSeparationVector`）。

### 5. 永続化

`saveBoard` / `loadBoard` で、付箋の位置・色・内容・**破片の形状（clip-path）**まで
`localStorage` に保存し、リロード後も復元します。

---

## 実装規模

| 項目 | 値 |
|---|---|
| ファイル数 | 1（`index.html`） |
| 行数 | 1,405 |
| 外部ライブラリ | **0** |
| 主要な関数 | 34 |
| 描画 | SVG + `clip-path` |
| 保存 | `localStorage` |

---

## 実行

`index.html` をブラウザで開くだけです。ビルドもサーバーも不要です。

```bash
# ローカルで開く場合
start index.html        # Windows
open index.html         # macOS
```

---

## 受賞

**ZEN Study 動く web ページコンテスト 優秀賞**

---

## ライセンス

© Kohaku912. All rights reserved.
