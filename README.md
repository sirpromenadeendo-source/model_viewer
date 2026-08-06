# 3D モデルビューアー（GitHub Pages 用）

OBJ モデル＋テクスチャを GitHub に置くだけで、誰でもブラウザで閲覧できる
「閲覧専用」の Web アプリです。Three.js を同梱しているので外部 CDN に依存せず、
オフラインでも GitHub Pages でも同じように動きます。

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

1. あなたの `.obj` / `.mtl` / テクスチャ画像（.png / .jpg など）を
   `model/` フォルダに入れます。テクスチャは `.mtl` の中で参照されるため、
   **同じ `model/` フォルダ**に置いてください。

2. `index.html` の上のほうにある次の部分を、あなたのファイル名に書き換えます：

   ```js
   window.MODEL_CONFIG = {
     obj: "model/あなたのモデル.obj",
     mtl: "model/あなたのマテリアル.mtl",   // .mtl が無い場合は "" にする
   };
   ```

3. 変更を GitHub に push すれば、公開ページも自動で更新されます。

### 補足

- `.mtl` が無いモデルは `mtl: ""` にすれば、マテリアル無し（グレー表示）で読み込めます。
- モデルが表示されない場合は、ブラウザの開発者ツール（F12）の Console で
  読み込めていないファイルのパスを確認してください。パスの綴りや、
  テクスチャが `model/` フォルダに入っているかがよくある原因です。
- 大きすぎる／小さすぎるモデルでも、起動時に自動でカメラ位置を合わせます。

## 機能

- 回転・ズーム・パン（マウス／タッチ対応）
- 背景色の切り替え（プリセット＋カスタム色）
- ライトの明るさ・環境光の調整
- 自動回転のオン／オフ
- 視点リセット

## ローカルで確認したいとき

ファイルを直接ダブルクリックすると、ブラウザのセキュリティ制限で
モデルが読み込めないことがあります。簡易サーバー経由で開いてください：

```bash
cd obj-viewer
python3 -m http.server 8000
# ブラウザで http://localhost:8000 を開く
```
