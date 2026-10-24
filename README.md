# aiai2
角度を変えることで、1番濡れにくいという角度を探すプロジェクトです。

# 作成者による解説
今回は実行方法メインで解説します

## ファイル構成
```txt
aiai2/
├── umbrella_rain_v2.py   # 本体(前処理・干渉解決・雨シミュレーション・映像・CSV)
├── rain_fx.py            # 映像合成エンジン(PyTorchを使用。GPU/CPUを自動選択)
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
1. VS Codeをインストール{任意(翠嵐じゃなくてほんとに), エディタはなんでもいい(最悪vimでも)}
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

## 実際の実行のされ方(umbrella_rain_v2.pyを実行すると自動で動くので安心してください)
```
couple.obj / Umbrella.obj
   │  obj_loader.py / fbx_loader.py(パーツ単位で読み込み)
   ▼
prep_model(前処理)
   ├─ ゴミ・地面パーツ除去、いらない面を除去
   ├─ couple : 上方向推定 → 単位推定(m/cm/mm) → 二人を検出(軸を x に、間を原点に、A/B分割)
   └─ umbrella: 任意方向の回転対称軸を探索 → 上下判定 → 単位推定 → 握り位置を原点・軸を +z に
   ▼
傘の3Dモデルを加工：表面ボクセル殻(ピッチ1cm、1ボクセル太らせ) + 衝突判定・評価用の殻
   ▼
place(): 傘を θ 傾け、人との干渉が無い最小の持ち上げ量を探す
   ▼
RainSim(E個の角度を非同期処理を使って同時に実行します): 上空から一定量を投下 → 衝突判定 → 水量集計
   ├─ 傘の縁からの滴下(スロット固定のプール)
   └─ 傘なし(パーツでいうとbaseline)に対する濡れ率 + 想定誤差を算出
   ▼
映像(角度ごと)
   ├─ ベース画像: Genesis(cam.render の rgb+depth) または soft_render(CPU) ← GPUがない時専用
   └─ rain_fx.RainRenderer(GPU): 空・路面反射・霧 → 濡れ・波紋・飛沫 → 雨(被写界深度) → 動画を生成
   ▼
out/rain/ に mp4 / CSV / グラフ
```

## 開発秘話
もともと、Genesisをドローンのai学習(https://github.com/AuraLunae/drone-genesis-project) で使っていて、それをこのプロジェクトで使えないかと考えました。  
Genesis worldは、高性能、高精度かつRust製なので軽いという、最高のパッケージなのでありがたく使っています。  
また、Genesisには、いろいろなソリッドというか、物質の表現方法があり、例えば、ここは布で、ここは粒子　みたいなことがいとも簡単にできるので、積極的に使いました。

結果のzipはこちら↓  
https://xgf.nu/6GLrV

一言めも.  
iPadだとバッククオートが~打てなくて~ 打ちにくくて 悲しい
