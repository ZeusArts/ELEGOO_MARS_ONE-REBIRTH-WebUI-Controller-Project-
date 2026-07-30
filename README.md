# ELEGOO MARS ONE REBIRTH (WebUI Controller Project)

2019年製の初代Elegoo Marsの3.5インチタッチスクリーン操作パネルが故障し、さらにリペアパーツの入手が困難になってきたことをきっかけにスタートした個人プロジェクトです。
通常の操作パネルを取り外した状態でも、**Raspberry Pi Zero W** と **ChituboardのUART通信** を利用し、スマホやPCのブラウザ（WebUI）から直接プリンターを操作・印刷可能にするシステムを構築しました。

もちろん、操作パネルが故障していない正常なElegoo Marsでも、Wi-Fiを通じてWebブラウザから快適に遠隔操作できるようになります。

## ⚠️ 免責事項 (Disclaimer)
WebUIコントローラーの開発には極力安全対策（Z軸の絶対座標指定、印刷後の安全ロック機能など）を施していますが、バグやユーザーの操作ミス・予想外の操作によるトラブル・機器の破壊なども充分にあり得ます。
**本プログラムのご使用はすべてユーザーの自己責任とし、開発者は本プログラムの使用によって生じた如何なるトラブル・損害の責任も負わないものとします。**

## 🔧 プロジェクトに必要なハードウェア
* **Elegoo Mars 初代 3Dプリンター本体**
* **Chituboard**（※開発に使用しているメインボードは古いBタイプです）
* **Raspberry Pi Zero W (V1.1)** （※現時点ではこれ以外のモデルは検証不可能なため動作保証外です）
* **LM2596 電圧レギュレータ DCコンバータ** (DC 12V ➔ 5V Step/down) ※Pi Zero Wの電源降圧用
* **MicroSDカード 4GB〜8GB推奨**（※カスタムOSイメージは超軽量で約2.1GBしか使用しません）
* Raspberry Pi、LM2596コンバーター、Chituboardを繋げるためのジャンパーワイヤー 5本
  * 接続先: `5V`, `GND x2`, `TX`, `RX`

## ⚙️ システムの仕組みと開発理由
**【仕組み】**  
Raspberry Pi Zero W と Chituboard を物理的に繋ぎ、UART通信(115200bps)経由で専用のGcode（M6030等）をやり取りして制御を行う方式です。

**【独自開発に至った理由】**  
当初は既存のOctoPrintで制御を行おうとしましたが、Raspberry Pi Zero Wの単核心CPUでは性能不足で重すぎること、またサーバー起動に不可解な問題が多発したため廃止しました。結果として、超軽量で動作するオリジナルのPython/FlaskベースのWebUIコントローラーを独自開発するアプローチに切り替え、見事に成功しました。

## 📌 GPIOピン接続図 (Pi Zero W ➔ ChituBoard)
* **Pin 4 (5V Power)** ➔ `LM2596 (5V+)` ＜Pi電源供給用＞
* **Pin 6 (Ground)** ➔ `LM2596 (GND-)` ＜Pi電源供給用＞
* **Pin 8 (GPIO 14 / TXD)** ➔ `Chituboard (RX)` ＜UART通信用＞
* **Pin 10 (GPIO 15 / RXD)** ➔ `Chituboard (TX)` ＜UART通信用＞
* **Pin 14 (Ground)** ➔ `Chituboard (GND)` ＜UART通信共通GND＞

*(※注意：LM2596変圧器はRaspberry Piに接続する前に、必ず電圧テスターで正確に5Vに調整してください。また、Piに接続するUSBケーブルの5V端子をテープ等で物理遮断しないとPiが破壊される危険があります。)*

## 🛠️ 使用するソフトウェアツール
* **Raspberry Pi Imager** ➔ MicroSDカードへのOS書き込み用【必須】
* **Raspberry Pi OS (Legacy, 32-bit) Lite** ➔ 【必須】(※通常版では動作不良を起こすため必ずLegacy版を使用)
* **PuTTY** ➔ Wi-Fi経由のSSH接続用【必須】
* **WinSCP** ➔ GUIでのファイル転送や編集用（MicroSDを抜き差しする手間が省けて超便利）
* **NetScan_Portable** ➔ ネットワーク上のPiのIPアドレス検索用
* **Win32DiskImager** ➔ 構築したカスタムOSのバックアップ作成用（※同容量・同メーカーのSDカードへの復元を推奨）

## 🚀 導入方法 (How to Install)
詳しいビルド方法とコマンドは、同梱の `Installation_Manual.txt` に記載しています。ファイルの手順に従ってセットアップを行ってください。  
初心者向けに、YouTubeにてセットアップと動作の解説動画もアップロード予定です。疑問がある場合はそちらも参考にしてください。

## 📊 パフォーマンス実測値 (IDLE待機時)
独自開発のWebUIコントローラーは、Pi Zero Wの限られたスペックでも極めて安定して動作することが証明されました。

* **OS:** Raspberry Pi OS Lite 32-bit (Legacy)
* **ストレージ使用量:** `Used: 2.1G` / `Avail: 4.4G` (約33%使用)
* **メモリ(RAM)使用量:**
  `total: 427MB` | `used: 112MB` | `free: 224MB` | `available: 315MB`
  *(※デスクトップGUI無しのLite版OSを使用しているため、Flaskサーバー稼働中でもRAM消費はごくわずかです)*
* **システム温度:** `temp=29.9'C` (熱暴走の心配なし)

## 💬 サポートに関して
* 個人開発のオープンソースプロジェクトであるため、開発者による直接の個別サポート・トラブルシューティングは行いません。
* プログラムのビルドや使用中に発生した機器の故障、発火、不具合などはすべて自己責任となります。
* アイデアや改善のPull Request、コメントなどは歓迎いたします！

---
*Created by [あなたのユーザー名/ZEUS-ARTS] - 2026*
