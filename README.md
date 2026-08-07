# 3D モデルビューアー（GitHub Pages 用）

3Dモデル（**.obj / .glb / .gltf**）＋テクスチャを GitHub に置くだけで、誰でも
ブラウザで閲覧できる「閲覧専用」の Web アプリです。Three.js を同梱しているので
外部 CDN に依存せず、オフラインでも GitHub Pages でも同じように動きます。
容量の大きいモデルは GLB 形式が推奨です（後述）。

## 含まれるファイル

```
index.html                 ← ビューアー本体（これを開く）
model/
  ├─ sample.obj            ← サンプルモデル（差し替え対象）
  ├─ sample.mtl            ← マテリアル定義
  └─ sample_texture.png    ← テクスチャ画像
vendor/three/             ← Three.js 一式（さわらなくてOK）
```

## 使い方（3ステップで公開）

### 1. GitHub にリポジトリを作ってアップロード

このフォルダの中身を**まるごと**新しいリポジトリにアップロードします。
`index.html` がリポジトリの一番上の階層に来るようにしてください。

コマンドで行う場合：

```bash
cd obj-viewer
git init
git add .
git commit -m "3D viewer"
git branch -M main
git remote add origin https://github.com/<ユーザー名>/<リポジトリ名>.git
git push -u origin main
```

### 2. GitHub Pages を有効化

リポジトリの **Settings → Pages** を開き、

- **Source** を「Deploy from a branch」
- **Branch** を `main` / `/(root)` に設定して **Save**

1〜2分ほどで公開 URL が表示されます：

```
https://<ユーザー名>.github.io/<リポジトリ名>/
```

この URL を共有すれば、誰でも閲覧できます（閲覧専用）。

### 3. 完成

URL を開くとモデルが表示されます。
ドラッグで回転、ホイールでズーム、右ドラッグ（または2本指）で移動できます。

## 自分のモデルに差し替える

**GitHub Pages なら、ファイル名の変更も `index.html` の編集も不要です（自動検出）。**

1. あなたの `.obj` / `.mtl` / テクスチャ画像（.png / .jpg など）を
   `model/` フォルダに入れます。テクスチャは `.mtl` の中で参照されるため、
   **同じ `model/` フォルダ**に置いてください。
   （サンプルの `sample.*` は消しても残しても構いません。`.obj` が複数ある場合は
   名前順で最初のものが表示されます）

2. GitHub に push すれば完了です。ビューアーが `model/` フォルダの中の `.obj` を
   自動で見つけて表示します。

### 手動で指定したい場合（任意）

自動検出を使わず、特定のファイルを指定することもできます。
`index.html` 上部の `MODEL_CONFIG` を次のように編集します：

```js
window.MODEL_CONFIG = {
  autoDetect: false,                    // 自動検出をオフにする
  obj: "model/あなたのモデル.obj",
  mtl: "model/あなたのマテリアル.mtl",  // .mtl が無い場合は "" にする
};
```

### 補足

- `.mtl` が無いモデルは、自動検出時はそのままマテリアル無しで表示されます
  （手動指定時は `mtl: ""`）。
- テクスチャは **JPG / PNG / WebP** に対応しています。`.mtl` の `map_Kd` 行が
  指す画像を `model/` フォルダに入れておけば、形式は問いません。
- `.mtl` を使わずテクスチャ画像を直接貼りたい場合は、`MODEL_CONFIG` の
  `texture: "model/画像.jpg"` に画像パスを指定してください（.mtl より優先）。
- 大きすぎる／小さすぎるモデルでも、起動時に自動でカメラ位置を合わせます。

## 大きい OBJ は GLB に変換（推奨）

GitHub のアップロード制限は次のとおりです。

- **ブラウザ（ドラッグ＆ドロップ）：1ファイル 25MB まで**
- **GitHub Desktop / git コマンド：1ファイル 100MB まで**（それ以上は不可）

OBJ はテキスト形式でとても容量が大きいため、25MB や 100MB を超えることがあります。
その場合は **GLB（バイナリ形式）に変換**してください。多くの場合サイズが数分の一〜
数十分の一になり、表示も速くなります。このビューアーは `.glb` / `.gltf` に対応済みで、
GLB を `model/` フォルダに入れて push すれば自動で表示されます（テクスチャは GLB に
内包されるので別ファイルは不要）。

### 変換方法A：Blender（無料・GUI）

1. Blender で **ファイル → インポート → Wavefront (.obj)** でモデルを読み込む
2. **ファイル → エクスポート → glTF 2.0 (.glb)** を選ぶ
3. 右側の設定で **圧縮（Draco mesh compression）にチェック**を入れて書き出す

### 変換方法B：コマンド（Node.js が必要）

```bash
npm install -g obj2gltf gltf-pipeline
obj2gltf -i model.obj -o temp.glb
gltf-pipeline -i temp.glb -o model.glb --draco.compressionLevel 7
```

できあがった `model.glb` を `model/` フォルダに入れて push すれば完了です。

## うまく表示されないとき（重要）

「読み込み中」のまま、または「ファイルを読み込めませんでした：model/...」と
表示される場合、**`model` フォルダが GitHub に上がっていない**ことがよくある原因です。
GitHub のリポジトリを開き、`index.html` と同じ階層に `model` フォルダがあり、
その中に `.obj` などが入っているか確認してください。

- Web からドラッグしてアップロードすると、フォルダが一緒に上がらないことがあります。
  その場合は `model` フォルダを開いてから中身をアップロードするか、
  Git コマンドでまとめて push してください（`git add .` で全ファイルを追加）。
- どのファイルが読み込めていないかは、ブラウザの開発者ツール（F12）の
  Console / Network タブで確認できます。

## 機能

- 回転・ズーム・パン（マウス／タッチ対応）
- 背景色の切り替え（プリセット＋カスタム色）
- 自然な環境光（IBL）＋トーンマッピングで見やすく表示
- ライトの強さ・全体の明るさ（露出）の調整
- スケールバー表示（mm 単位・最大 10cm）のオン／オフ
- 回転中心マーカー（球）の表示オン／オフ
- 視点リセット（回転中心をモデル中央に戻す）
- Draco / meshopt 圧縮の両方の GLB に対応
- `model` フォルダ内の `.obj` を自動検出（GitHub Pages）

## ローカルで確認したいとき

ファイルを直接ダブルクリックすると、ブラウザのセキュリティ制限で
モデルが読み込めないことがあります。簡易サーバー経由で開いてください：

```bash
cd obj-viewer
python3 -m http.server 8000
# ブラウザで http://localhost:8000 を開く
```
