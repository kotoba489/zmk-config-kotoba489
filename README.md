<img src="https://github.com/user-attachments/assets/8d785153-dff7-4dc9-a56a-ffe95a8e65d5" width="65%">


<img src="https://github.com/user-attachments/assets/75219e2f-3976-46d7-83a5-a35a6d0d94ad" width="70%">

# ⌨️ 自作キーボード「k_grid」設定状況まとめ

## 🛠️ Current Configuration (2026-03-23)
20キー（10列×4行）分割レイアウトの完全復旧版。

## 📂 Branch Strategy
- **main**: 安定版（10列構成・黄金のバックアップ）
- **v2_0309bl**: 実験版（左右セパレート表示 & ブートローダーキー実装）
- **sub0323**: 復旧用アーカイブ

## ✨ Special Features
- **Split Layout**: `k_grid.json` の調整により、中央に1列分のスペースを配置。
- **Software Bootloader**: 
  - **Left**: Layer 1 + Row 3, Far Left
  - **Right**: Layer 1 + Row 3, Far Right
  - *Note: USB接続時に1回押すだけで書き込みモードに移行可能。*
- **Optimized Matrix**: 以前の成功時のピン定義（D10, D9等）を完全に復元。

## 🔨 Hardware Mod
- **Weight**: 内部に鉛板を追加。
- **Damping**: ポロンシートによる消音加工。
- **Keycaps**: Chocfox LAK (Low Profile)

> **最終更新: 2026-03-23 18:00 (20キー完全復活)**

---

## 1. 現在の物理的な状態（正常動作中）

| 部位 | 役割 | 接続先 / 設定 |
|:---:|:---:|:---|
| **左手** | 親機 (Central) | `ZMK_SPLIT_ROLE_CENTRAL=y` / PC接続担当 |
| **右手** | 子機 (Peripheral) | `ZMK_SPLIT_ROLE_CENTRAL=n` / 左手接続担当 |

- **ペアリング状態**: 左右間の同期 ✅ / PCとのBluetooth接続 ✅
- **マトリクス**: 左右それぞれ5列×4行（計20キー）すべて反応確認済み。

---

## 2. GitHub 上の設定ファイル構成

### `config/boards/shields/k_grid/Kconfig.defconfig`
左手を「親」、右手を「子」として明確に役割分担させる設定を導入。これによりBluetoothの認識率が大幅に向上しました。

### `config/boards/shields/k_grid/*.overlay`
- **Left**: `D4, D5, D10, D9, D8` の順で列を定義。
- **Right**: `D8, D9, D10, D5, D4` の逆順定義 ＋ `col-offset = <5>` を適用。

---

## 3. 今後のキーマップ変更の流れ

基本的に**左手（親機）の設定を書き換えるだけ**で、左右両方のキー配置を更新可能です。

1. GitHub 上の `.keymap` ファイルを編集（全40スロット分）
2. Commit & Push → 自動ビルド
3. `k_grid_left.uf2` をダウンロードして左手に書き込む
4. 完了！（右手は触らなくても配置が変わります）

---

## 4. メンテナンス・トラブルシューティング

### ✅ 通常のキーマップ変更
- `.keymap` を修正してPush。
- 生成された **`left.uf2` だけを左手に書き込む**（右手の書き換えは不要）。

### 🔄 接続が不安定になった場合（リセット儀式）
1. PC側のBluetooth設定から「k_grid」を一度削除。
2. 左手・右手両方に `settings_reset.uf2` を書き込む。
3. 左手に `left.uf2`、右手に `right.uf2` をそれぞれ書き込む。
4. 左右を至近距離に置いて同時起動し、ペアリングを待つ。

---

## 5. リポジトリ修正履歴（2026-03-23）

- **プロジェクト完全移行**: `kg10key` 依存の設定を全排除し、`k_grid` 専用の10列マトリクス構成を確立。
- **物理的整合性の死守**: 以前の成功時のピン配置（D10, D9等）を特定し、`overlay` を完全に修正。
- **Bluetooth復旧**: 役割分担の明文化（Kconfig）と設定リセットにより、PCから「k_grid」として正常に認識される状態を奪還。
- **ビルド成功**: GitHub Actions にて `left.uf2` / `right.uf2` の同時生成と正常動作を確認。
