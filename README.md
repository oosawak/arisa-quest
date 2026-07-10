# アリサクエスト

GPS 位置情報と連動するブラウザ動作のターン制 RPG デモです。
現実の地図上を歩くと敵とエンカウントし、WebAssembly 製のバトルエンジンで戦闘が始まります。

🎮 プレイはこちら: https://oosawak.github.io/arisa-quest/

## 特徴

- GPS連動マップ: OpenStreetMap と Leaflet.js で現在地を表示
- WASMバトルエンジン: Rust 製バトルロジックを WebAssembly で実行
- サイバー空間演出: GPS 取得前はマトリックス雨演出
- 距離感応エンカウント: 敵との実距離に応じて遠近感のある表示
- タッチ対応: スマートフォンでのプレイに対応
- 山形城フォールバック: GPS 取得不可時は山形城（霞城公園）を起点
- 追加マーカー: 観光案内 (rullama/WASM) と船予約の入口を地図上に追加

## 技術構成

```
ブラウザ (index.html)
├── Leaflet.js + OpenStreetMap  ← GPS・地図表示
├── Canvas 2D (bg-canvas)       ← スプライト描画・バトルUI
└── WASM (Rust / pyxel-rust)    ← バトルロジック・HP/MP管理
```

### WASM (Rust) が担当する領域

| 機能 | 詳細 |
|------|------|
| バトルロジック | HP/MP管理、コマンド処理、状態遷移 |
| 状態エクスポート | `get_game_state()` `get_player_hp()` など |

### JavaScript が担当する領域

| 機能 | 詳細 |
|------|------|
| GPS・位置情報 | Geolocation API |
| 地図表示 | Leaflet.js + OpenStreetMap |
| スプライト描画 | Canvas 2D でキャラ・背景を合成 |
| UI・アニメーション | CSS アニメーション |
| サイバー演出 | マトリックス雨、GPSロックオン演出 |

> 設計原則: WASM はゲームコア、JS は現実世界とのブリッジです。

## ファイル構成

```
arisa-quest/
├── index.html
├── assets/
├── games/
├── pkg/
├── sendai_daikannon_wasm/
├── ship-reservation/
└── tour-guide/
```

## ローカル確認

```bash
python3 -m http.server 8000
# → http://localhost:8000/
```

## 補足

このリポジトリには、`pyxel-rust` 側で生成された配布用ファイルを同梱しています。
WASM を再生成する場合は、元の `pyxel-rust` ソースでビルドして `pkg/` に反映してください。
