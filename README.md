![9781](https://github.com/user-attachments/assets/8426b6aa-ca83-4a60-8c90-19ad8286074b)


## 大西配列をアレンジしたキーマッピングを活用

<img width="822" height="379" alt="スクリーンショット 2026-03-10 16 40 48" src="https://github.com/user-attachments/assets/c858983f-129a-4eb0-a08c-a10710aed398" />







## ⌨️ 自作キーボード「k_grid」設定状況まとめ
### 🛠️ Current Configuration (2026-03-10)

### 📂 Branch Strategy
- **main**: 安定版（10列構成・黄金のバックアップ）
- **v2fromsub0309**: 実験版（左右セパレート表示 & ブートローダーキー実装）
- **sub0309**: 復旧用アーカイブ

### ✨ Special Features
- **Split Layout**: `k_grid.json` の調整により、中央に1列分のスペースを配置。
- **Software Bootloader**: 
  - **Left**: Layer 1 + Row 3, Far Left
  - **Right**: Layer 1 + Row 3, Far Right
  - *Note: USB接続時に1回押すだけで書き込みモードに移行可能。*

### 🔨 Hardware Mod
- **Weight**: 内部に鉛板を追加。
- **Damping**: ポロンシートによる消音加工。
- **Keycaps**: Chocfox LAK (Low Profile)
### 📂 Branch Strategy
- **main**: 安定版（10列構成・黄金のバックアップ）
- **v2fromsub0309**: 実験版（左右セパレート表示 & ブートローダーキー実装）
- **　※2026-03-10 左右セパレート表示をメインブランチにも反映
- **sub0309**: 復旧用アーカイブ

### ✨ Special Features
- **Split Layout**: `k_grid.json` の調整により、中央に1列分のスペースを配置。
- **Software Bootloader**: 
  - **Left**: Layer 1 + Row 3, Far Left
  - **Right**: Layer 1 + Row 3, Far Right
  - *Note: USB接続時に1回押すだけで書き込みモードに移行可能。*

### 🔨 Hardware Mod
- **Weight**: 内部に鉛板を追加。
- **Damping**: ポロンシートによる消音加工。
- **Keycaps**: Chocfox LAK (Low Profile)
> 最終更新: 2026-03-09 07:00

---
(2026-03-9)
## 1. 現在の物理的な状態（正常動作中）

| 部位 | 役割 | 書き込み済み設定 |
|------|------|----------------|
| 左手 | 親機（Central） | `CONFIG_ZMK_SPLIT_ROLE_CENTRAL=y` |
| 右手 | 子機（Peripheral） | `CONFIG_ZMK_SPLIT_ROLE_CENTRAL=n` |

- **ペアリング状態**: 左右の同期 ✅ / Mac との Bluetooth 接続 ✅

---

## 2. GitHub 上の設定ファイル（`config/k_grid.conf`）

```ini
CONFIG_ZMK_SPLIT_ROLE_CENTRAL=y
```

**この設定にしている理由:**  
将来のキーマップ変更時に、**左手用ファームウェア（`left.uf2`）を
ビルドするだけ**で済むようにするため。

---

## 3. なぜ左手だけ書き換えればいいのか？

ZMK（および多くの分割キーボード）には 
**「脳みそ（親機）は片方にしかない」** というルールがあります。

```
右手キーを押す
    ↓
「この場所のボタンが押された」という物理信号を左手に無線送信
    ↓
左手（親機）が .keymap を参照して「それは 'L' の文字だな」と判断
    ↓
Mac に送信
```

> **つまり、右手の動きを司っているのは左手の中にある設定ファイルです。**  
> キーマップ（どのキーが何の文字か）のデータはすべて親機（左手）が管理しています。

---

## 4. 今後のキーマップ変更の流れ

```
1. GitHub 上の .keymap ファイルを編集（左手分・右手分をまとめて記述）
2. Commit & Push → ビルド
3. left.uf2 だけをダウンロードして左手に書き込む
4. 完了！右手には一切触れずに、右手のキー配置も更新される
```

---

## 5. 今後のメンテナンス・ルール

### ✅ 通常のキーマップ変更

- `.keymap` ファイルを修正して Push する
- 生成された **`left.uf2` だけを左手に書き込む**（右手の書き換えは不要）

### 🔄 左右の通信が切れたとき

1. Mac の Bluetooth をオフにする
2. 左右を密着させる
3. 左手のリセットボタンを 1 回押して 1 分待つ

### ⚠️ 右手を書き換える必要があるとき（レアケース）

右手ファームウェアの書き換えが必要なのは、**「根本的な通信の仕組み」や「省電力設定」** などを変えた時だけです。  
「A のキーを B に変えたい」「レイヤーを増やしたい」といったキー配置の変更では**不要**です。

```
1. config/k_grid.conf を一時的に =n に書き換えて Push
2. right.uf2 をダウンロードして右手に書き込む
3. 終わったら設定を =y に戻す
```

---

## 6. ファームウェアの保管場所

- **右手専用ファームウェア**: 自作キーボードホルダーの中に保存済み
- **ビルド履歴**: ブランチのライト（GitHub Actions の履歴）にも残存
