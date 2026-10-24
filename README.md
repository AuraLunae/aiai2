# aiai2
角度を変えることで、1番濡れにくいという角度を探すプロジェクトです。

# 作成者による解説
## ファイル構成
```txt
umbrella-rain-project/
├── umbrella_rain_v2.py   # 本体(前処理・干渉解決・雨シミュレーション・映像・CSV)
├── rain_fx.py            # 映像合成エンジン(PyTorch。GPU/CPU)
├── soft_render.py        # CPUソフトレンダラ(Genesis描画のフォールバック兼 検証用)
├── obj_loader.py         # 高速OBJローダー(パーツ・材質色を保持)
├── fbx_loader.py         # バイナリFBXローダー(階層変換込み。OBJを使うなら不要)
├── umbrella_sph.py       # (実験的)Genesis SPH流体による見た目用の雨
├── pyproject.toml        # uv用の依存関係
└── assets/                 # モデルの置き場所(自動検出される。実行フォルダ直下や ./models でも可)
    ├── couple.obj
    └── Umbrella.obj
```

## セットアップ
1. VS Codeをインストール(任意▶︎エディタはなんでもいい(最悪vimでも))
2. このリポジトリをクローン
3. uvをセットアップ
```powershell
# まずはuvをwindowsにインストール
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
# それができたら、cdコマンドで、プログラム直下に移動して，lsコマンドでproject.tomlがあることを確認した上でパッケージをインストール
uv sync
```
4. 実際に動かす
```powershell
uv run python umbrella_rain_v2.py
```
6. 完了  
out/rain/　に全てのファイルが出力されるはずです。




## README.mdを作成中につき、情報量が皆無
結果はこちら↓<br>
https://xgf.nu/6GLrV

一言めも.  
iPadだとバッククオートが打てなくて悲しい
