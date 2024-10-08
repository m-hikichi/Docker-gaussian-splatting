# 環境構築手順

## 初回時のみ

### リポジトリのクローン

リポジトリをローカルにクローンします。
```bash
git clone https://github.com/m-hikichi/Docker-gaussian-splatting.git
```

### Dockerイメージのビルド

`docker-compose`ディレクトリに移動します。
```bash
cd docker-compose
```

Dockerイメージをビルドします。
```bash
docker-compose build
```

## コンテナ起動時

### Dockerコンテナの起動

Dockerコンテナをバックグラウンドで起動します。
```bash
docker-compose up -d
```

次に、`gaussian-splatting`という名前のDockerコンテナに接続します。
```bash
docker-compose exec gaussian-splatting bash
```

### COLMAPのビルドとインストール

COLMAPのビルドディレクトリに移動します。
```bash
cd /workspace/colmap/build
```
次に、CMakeを使ってビルドファイルを生成し、Ninjaを使ってビルドとインストールを行います。
```bash
cmake .. -GNinja
ninja
ninja install
```

### Pythonパッケージのインストール

必要なPythonパッケージをインストールします。まず、`diff-gaussian-rasterization`パッケージをインストールします。
```bash
cd /workspace/gaussian-splatting/submodules/diff-gaussian-rasterization
python setup.py install
```

次に、`simple-knn`パッケージをインストールします。
```bash
cd /workspace/gaussian-splatting/submodules/simple-knn
python setup.py install
```

# 独自のデータで gaussian-splatting を行う

## データセットの作成

### シーンの全面をカバーする画像やビデオをキャプチャする
スマートフォンやカメラを使って、再構築したい3Dシーンの全面をカバーする画像や動画を撮影します。最も簡単な方法は、シーンを動き回りながら動画を撮影することです。モーションブラーを避けるため、ゆっくりと滑らかに動くようにしてください。COLMAPで一貫性のある再構成を行い、カメラのポーズ推定を簡単に行うには、焦点距離を一定に保ち、露光時間を一定にすることも重要です。焦点距離を一定に保つために、スマートフォンのオートフォーカスを無効にすることをお勧めします。<br>
<br>
動画を画像に変換するには、ffmpegをインストールして以下のコマンドを実行します。
```bash
ffmpeg -i <Path to the video file> -qscale:v 1 -qmin 1 -vf fps=<FPS> input/%04d.jpg
```
ここで`<FPS>`はビデオ画像の希望サンプリングレートである。FPS値が1の場合、1秒間に1枚の画像をサンプリングすることになります。サンプリングレートをビデオの長さに合わせて、サンプリングされる画像の数が100から300の間になるように調整することをお勧めします。
```
<location>
|---input
    |---<image 0>
    |---<image 1>
    |---...
```

### COLMAPでカメラのポーズを推定する
COLMAPとImageMagickがインストールされているなら、以下のコマンドを実行するだけで大丈夫です：
```bash
python convert.py -s <location>
```
実行すると、`<location>`には歪みのない、リサイズされた入力画像とともに、期待されるCOLMAPデータセット構造が含まれるようになります。さらに、元の画像と一時的な（歪んだ）データが `distorted` ディレクトリに追加されます。
```
<location>
|---input
|   |---<image 0>
|   |---<image 1>
|   |---...
|---distorted
    |---database.db
    |---sparse
        |---0
            |---...
```
<br>

COLMAPは、すべての画像を同じモデルに再構築することができず、複数のサブモデルを生成することがある。小さなサブモデルは、一般的に数枚の画像しか含んでいません。しかし、デフォルトでは、`convert.py`スクリプトは、最初のサブモデルに対してのみImage Undistortionを適用します。<br>
このような場合、簡単な解決策は、最大のサブモデルだけを残し、他を破棄することです。これを行うには、入力画像を含むソースディレクトリを開き、サブディレクトリ `<location>/distorted/sparse/` を開きます。`0/`、`1/`などという名前のサブディレクトリがいくつかあり、それぞれにサブモデルが含まれているはずです。一番大きなファイルを含むサブディレクトリを除いて、すべてのサブディレクトリを削除し、名前を`0/`に変更します。次に、`convert.py`スクリプトをもう一度実行しますが、マッチング処理はスキップします：
```bash
python convert.py -s <location> --skip_matching
```

## 3Dモデル作成

```bash
python train.py -s <path to COLMAP or NeRF Synthetic dataset>
```
<details>

- `--source_path / -s`<br>
    COLMAPまたはSynthetic NeRFデータセットを含むソース・ディレクトリへのパス。
- `--model_path / -m`<br>
    学習済みモデルを保存するパス (デフォルトは `output/<random>`).
- `--images / -i`<br>
    COLMAP画像の代替サブディレクトリ（デフォルトは`images`）。
- `--resolution / -r`<br>
    トレーニング前に読み込む画像の解像度を指定する。`1`,`2`,`4`,`8`を指定した場合、それぞれ`オリジナル`、`1/2`,`1/4`,`1/8`の解像度を使用する。それ以外の値の場合、画像のアスペクトを維持したまま、幅を指定された数値に再スケーリングする。未設定で入力画像の幅が1.6Kピクセルを超える場合、入力は自動的にこのターゲットに再スケーリングされる。
- `--data_device`<br>
    デフォルトでは`cuda`だが、大きな/高解像度のデータセットでトレーニングする場合は`cpu`を使うことを推奨。
- `--white_background / -w`<br>
    NeRF Syntheticデータセットの評価などで、黒（デフォルト）の代わりに白の背景を使用する場合は、このフラグを追加する。
- `--iterations`<br>
    トレーニングの総反復回数、デフォルトは `30_000`。
- `--save_iterations`<br>
    トレーニングスクリプトがガウスモデルを保存する反復回数、スペースで区切る。デフォルトは `7000 30000 <iterations>`。

</details>
<br>

# 参考文献
- [COLMAP](https://colmap.github.io/install.html)
- [gaussian-splatting](https://github.com/graphdeco-inria/gaussian-splatting)
