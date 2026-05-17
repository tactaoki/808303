# 808 — Rhythm Composer

スマホ横向き専用、TR-808 風のブラウザ・リズムマシン。Web Audio API のみで合成、サンプルなし、ビルドなし。配布は **`index.html` 一枚** または **ホストされた URL** のどちらでも。

## クイックスタート

### URL で使う

GitHub Pages を有効化すると、リポジトリの `index.html` がそのまま URL で配信されます (例: `https://<your-account>.github.io/808303/`)。手順:

1. GitHub の Repository → Settings → Pages
2. **Source** を `Deploy from a branch` にする
3. **Branch** を `main` (または `claude/808-rhythm-machine-ec66D` で先行公開してもよい) の `/ (root)` に設定 → Save
4. 数十秒待つと URL が発行される

### ファイル 1 個で使う

`index.html` を保存してダブルクリック、または任意の静的サーバーで配信:

```bash
# 例: macOS / Linux
cd 808303
python3 -m http.server 8765
# ブラウザで http://localhost:8765/ を開く (スマホからは同一LANのIP)
```

外部依存ゼロ — ファイルをコピーしたり、誰かに送って Safari/Chrome で開いてもらうだけで動きます。

## 使い方

- **画面**: スマホ横向き専用 (縦に戻すと案内が出ます)
- **起動**: 最初に画面のどこかをタップ (オーディオエンジン起動)
- **打ち込み**: 各セルを順にタップ → `Off` → `On` (橙) → `Accent` (赤) → `Off` を巡回
- **Play / Stop**: ヘッダー左
- **BPM**: 数値ディスプレイをタップで直接入力、または ± で 1 ずつ
- **M / S**: 各行左の小ボタン (Mute / Solo)。Solo は複数行で同時押し可能 (Mute と Solo は両立可能、Solo 優先)
- **マスター FX** (画面下):
  - **Drive**: サチュレーション (オーバーサンプル付き)
  - **Cutoff** / **Reso**: 4-pole ノンリニア ラダーフィルター。Reso を上げると過激な歪みと自己発振寸前のピーク
  - **Dly Mix** / **Dly Fb**: BPM 同期 8 分音符ディレイ + フィードバック
  - **Rev Mix**: 早期反射 + 指数減衰テールの合成 IR リバーブ

## 音色 (7 voices)

すべて Web Audio API で合成 (サンプル使用なし):

| | |
|---|---|
| **BD** | サイン + 急速ピッチドロップ + クリック (HP通過ノイズ) |
| **SD** | 180 / 330Hz 二音色トライアングル + バンドパスノイズ |
| **CH** | 6本 squareメタルクラスタ + BP 8kHz + HP 7kHz, 50ms decay |
| **OH** | 同 + 400ms decay。CH トリガで choke (実機動作模倣) |
| **CP** | 3 連ノイズバースト + 残響テール |
| **CB** | 540 / 800Hz square + BP |
| **CY** | メタルクラスタ + 9kHz ノイズスパークル + 1.5s decay |

## 技術メモ

- 単一の自己完結 HTML (約 43 KB)、外部リソース 0
- AudioWorklet (Moog 風ラダーフィルター + 2x オーバーサンプル サチュレーション) は **Blob URL** で読み込むため `file://` でも動作
- AudioWorklet 非対応の古いブラウザでは BiquadFilter + WaveShaper にフォールバック
- Chris Wilson 方式の lookahead スケジューラ (`AudioContext.currentTime` ベース)

## 将来の拡張: ベース音源

マスター 2-mix エフェクトはベース音源との共有を想定して作られています。起動後、ブラウザのコンソールから:

```js
const { ctx, master } = window.__rhythmMachine;
// 新しい音源を作って…
const osc = ctx.createOscillator();
osc.frequency.value = 55;
osc.connect(master.input);   // ← ここに繋ぐとマスター FX を通る
osc.start();
```

新しい画面 (TB-303 風ベース等) を追加するときは、`master.input` をターゲットに音源を構築してください。

## ブラウザ要件

- Safari 14.5+ / iOS 14.5+ (AudioWorklet 対応)
- Chrome / Edge / Firefox 最近版
- 非対応環境はフォールバックで動作 (フィルタの非線形挙動は控えめ)

## ライセンス

任意の用途で自由に使用可能。
