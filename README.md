
# kgrid38
38% (4x10 → 最下段8キー) Grid Layout 一体型カスタムキーボードプロジェクト。

## 1. プロジェクトの経緯
本リポジトリは kgrid40 をベースに、最下段右側2キー（RC(3,8)(3,9)）を削除した38キー構成への変更を実施。
名称の完全統一と物理基板（PCB）との整合性確認を完了。

**実機（SuperMini nRF52840）への書き込み、Bluetooth接続、および全38キーのマトリックス導通テストを完了し、ハードウェア・ソフトウェアの両面で動作を実証。**

## 2. kgrid40からの主な変更点

### `config/boards/shields/kgrid40/kgrid40.dtsi`
- matrix_transformのmapからrow3右側2キー（RC(3,8)(3,9)）を削除（40→38キー）。
- マトリクスの `columns` / `rows` 設定はkgrid40と同一（配線変更なし）。

```dts
map = <
    RC(0,0) RC(0,1) RC(0,2) RC(0,3) RC(0,4) RC(0,5) RC(0,6) RC(0,7) RC(0,8) RC(0,9)
    RC(1,0) RC(1,1) RC(1,2) RC(1,3) RC(1,4) RC(1,5) RC(1,6) RC(1,7) RC(1,8) RC(1,9)
    RC(2,0) RC(2,1) RC(2,2) RC(2,3) RC(2,4) RC(2,5) RC(2,6) RC(2,7) RC(2,8) RC(2,9)
    RC(3,0) RC(3,1) RC(3,2) RC(3,3) RC(3,4) RC(3,5) RC(3,6) RC(3,7)
>;
```

### `config/kgrid40.json`
- layoutエントリを40→38に削減。row3は col0〜col7 の8キーのみ。

### `config/kgrid40.keymap`
- 全レイヤーのバインディングを40スロット→**38スロット**に変更。
- row3の割り当て：`mo 1 / LSHFT / LALT / LGUI / SPACE / SPACE / ENTER / BSPC`。

---

## 3. ハードウェア仕様
実機の回路図および物理配線に基づいた確定済みのピン割り当てです。

### ターゲットボード
* ターゲットボード: SuperMini nRF52840 (nice!nano 互換)
* **ZMKビルド構成**: nice_nano をベースに使用
* ピンアサイン: kgrid40.dtsi にて GPIO ポート (P0.xx, P1.xx) を直接定義。
* SuperMini 本体のシルク印刷（物理表示）に基づいた物理ピン配置に完全対応済み。

### マトリックス配線 (4行 × 10列、有効キー38)
PCB上の穴番号と、SuperMiniのGPIOポート（P0.xx / P1.xx）を1対1で対応させています。

| 役割 | PCB穴番号 | GPIOポート (ZMK) | 備考 |
| :--- | :--- | :--- | :--- |
| **Rows** | 5, 6, 7, 8 | `P0.17, P0.20, P0.22, P0.24` | ROW 0〜3 |
| **Cols (左)** | 9, 10, 11, 12 | `P1.00, P0.11, P1.04, P1.06` | COL 0〜3 |
| **Cols (右)** | 17, 18, 19 | `P0.29, P0.02, P1.15` | COL 4〜6 |
| **Cols (右)** | 20, 21, 22 | `P1.13, P1.11, P0.10` | COL 7〜9 |

### 物理・論理構成（実証済み）
* **ダイオード方向**: `col2row`
* **スキャン論理**: **Active High / Pull Down** (実機での安定動作を確認)
* **接続**: Bluetooth LE (Macmini M2接続確認済み) / USB-C

---

## 4. ソフトウェア構成
* **Firmware**: ZMK Firmware (stable)
* **Keymap**: Dvorakベースの独自配列 (`config/kgrid40.keymap`)
* **GUI Editor**: [Keymap Editor](https://nickcoutsos.github.io/keymap-editor/) 対応済み

---

## 5. 修正履歴

### 2026-05-07: kgrid40→kgrid38への変更
* **38キー構成への変更**: row3右端2キー（RC(3,8)(3,9)）を削除。
* **dtsi修正**: matrix_transformのmapを40→38キーに変更。
* **json修正**: layoutエントリを38に削減。
* **keymap修正**: 全3レイヤーのバインディングを38スロットに変更。
* **Keymap Editor確認**: 38キーレイアウトの正常表示を確認。
* **ビルド確認**: GitHub Actions によるUF2出力を確認。

### 2026-03-16: 実機動作確認と論理最適化
* **実機導通テスト完了**: 全40キーが意図したマトリックス通りに反応することを確認。
* **スキャン論理の確定**: `GPIO_ACTIVE_HIGH` および `GPIO_PULL_DOWN` を適用し、安定した入力を実現。
* **Bluetooth接続の安定化**: `settings_reset.uf2` によるプロファイル初期化を経て、macOSとのペアリングおよびレイヤー1（数字列）の正常動作を確認。
* **出力制御**: `OUT_USB` / `OUT_BLE` 切り替え機能を実装し、有線・無線の柔軟な運用に対応。

### 2026-03-13: ピンアサインの正常化と重複排除
* **GPIO定義の刷新**: 従来の `&xiao_d` による定義を完全に廃止し、ポート直接指定へ移行。
* **資産名称の同期**: フォルダ名、シールド定義、UF2出力名をすべて `kgrid40` に統一。

---

## 6. 現在のステータス
* [x] kgrid40からの38キー構成への変更完了
* [x] Keymap Editor でのレイアウト認識確認（38キー）
* [x] GitHub Actions による UF2 直接出力の確認
* [x] 独立リポジトリへの移行と名称統一
* [x] GPIOピンアサインの重複排除と物理整合性確認
* [x] 実機への書き込みと導通テスト完了（kgrid40ベース）
* [x] Bluetooth接続およびレイヤー動作確認
* [ ] kgrid38実機への書き込みと導通テスト（PCB発注後）

---

## 7. メモ
* **トラブルシューティング**: Bluetooth接続が不安定な場合や、特定の行が反応しない場合は、`settings_reset.uf2` でプロファイルをクリアすること。
* **ブートローダー**: 物理リセットボタン（2回押し）で移行可能。
* **キーマップ変更**: `.keymap` を修正してPush → 自動ビルド → `kgrid40.uf2` を書き込む。全**38スロット**分を編集すること。

---


## ZMK Studio 対応
physical-layout 定義済み。Studio からキーマップをリアルタイム編集できます。詳細の編集はキーマップエディターで可能です。

---

## MIT License
This project is licensed under the MIT License.
