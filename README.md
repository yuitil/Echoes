# Echoes（エコーズ）

「分身」を呼び出し、共に戦うスタイリッシュ 3D アクションゲームです。
このリポジトリには、チーム制作のうち **中里 優太が担当したプレイヤー制御と UI の C++ ソースコード** を掲載しています。

🎬 プレイ動画：https://youtu.be/z92gqHkPjhk

🎮 ゲームのダウンロード（Windows）：https://drive.google.com/drive/folders/1aakYY7uh3LtfY4ta9syRPcU0edkJ5XFC?usp=drive_link
　zip をダウンロード・解凍し、フォルダ内の Echoes.exe を起動してください。操作方法は同梱の「操作方法.txt」をご覧ください。

| 項目 | 内容 |
| --- | --- |
| ジャンル | スタイリッシュ 3D アクション |
| 使用ツール・言語 | Unreal Engine 5.7 / C++ |
| 制作期間 | 2026年5月・8月・10月〜11月（現在も開発中） |
| 制作人数 | 4名（プログラマー3名 / デザイナー1名） |
| 担当範囲 | プレイヤー制御全般、ゲージ技、ジャスト回避、UI |

---

## ゲーム紹介

敵に攻撃を当てるとエネルギーが溜まり、**分身の召喚**や強力な**ゲージ技**に使えます。
分身は数的有利を生み出す強力な味方ですが、時間が経つと消えてしまいます。

エネルギーを分身に使うか技に使うか、分身をどう管理するか。
有利な状況を作り続けることが、このゲームの核となる駆け引きです。

---

## フォルダ構成

```
Player/   プレイヤーの各機能（コンポーネント）
UI/       HUD・ステータス表示・ボタンガイド
```

> 実際のプロジェクトでは Unreal Engine の標準どおり `Public/`（.h）と `Private/`（.cpp）に分けています。
> ここでは読みやすさのため、.h と .cpp を同じフォルダに並べています。

---

## 担当したプログラム

### プレイヤー（`Player/`）

| クラス | 内容 |
| --- | --- |
| [ActionCharacter](Player/ActionCharacter.h) | プレイヤー本体。入力の受け取り、カメラ制御、各コンポーネントへの命令。HP・回復・被弾リアクション・死亡とリスポーン時の状態リセット。エネルギー管理と UI への通知。ジャスト回避通知の受け取り、重力の異常を監視して自動で戻す処理 |
| [ActionMovementComponent](Player/ActionMovementComponent.h) | 移動・オートダッシュ・二段ジャンプ・空中制御・着地硬直（イージングによる速度回復）。回避中や空中での技の間の重力・摩擦の制御 |
| [CombatComponent](Player/CombatComponent.h) / [ComboDataAsset](Player/ComboDataAsset.h) | 攻撃コンボ（コンボウィンドウと先行入力）、回避、回避による攻撃キャンセル。当たり判定、ヒットストップ、ヒット音 |
| [LockOnComponent](Player/LockOnComponent.h) | ロックオンと、内積を用いたターゲットの切り替え |
| [SkillComponent](Player/SkillComponent.h) / [SkillDataAsset](Player/SkillDataAsset.h) | エネルギーを消費するゲージ技3種（一閃・テンペスト・エクスカリバー）。調整値はデータアセットに分離。技の最中のカメラ演出と画面の揺れ |
| [JustDodgeComponent](Player/JustDodgeComponent.h) | ジャスト回避の演出（全体スロー・画面の彩度変化）と報酬（一定時間エネルギー2倍） |
| [GhostRecorderComponent](Player/GhostRecorderComponent.h) | 分身用に、プレイヤーの行動を記録する処理 |

### UI（`UI/`）

| クラス | 内容 |
| --- | --- |
| [EchoHUD](UI/EchoHUD.h) | ステータス UI の生成と、ロックオンマーカーの描画（距離に応じて大きさを変える） |
| [EchoStatusWidget](UI/EchoStatusWidget.h) | HP ゲージ（被弾時の赤い光・揺れ・減った分を追いかけるゲージ）、HP が少ないときの画面端の赤。エネルギーゲージ、分身の状態を表すカプセル、回復の残り回数 |
| [EchoButtonGuideWidget](UI/EchoButtonGuideWidget.h) | ボタンガイド。押したボタンの拡大縮小、二段ジャンプの表示切り替え、召喚に必要なエネルギーまで溜まっていく表示、技の消費エネルギーの表示 |

---

## 技術的なこだわり

### 1. 責務を分けたコンポーネント設計

プレイヤーの機能を役割ごとのコンポーネントに分割しています。

| クラス | 役割 |
| --- | --- |
| ActionCharacter | 入力・カメラ・各コンポーネントへの命令 |
| ActionMovementComponent | 移動・物理挙動 |
| CombatComponent | 攻撃・回避などの戦闘アクション |
|
