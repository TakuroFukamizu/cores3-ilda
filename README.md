# CoreS3 ILDA

M5Stack CoreS3を使うILDA評価基板と、今後開発する制御ソフトウェアを管理するモノレポです。

## 構成

```text
hardware/rev-a/       Rev.Aの設計説明・部品表
  kicad/             編集するKiCadプロジェクト・回路図・PCB・ローカルライブラリ
firmware/            CoreS3向けファームウェア（未実装）
software/            PC側ツール／アプリ（未実装）
docs/                共通仕様・設計判断
releases/rev-a/       製造・評価時点の生成物と検査記録
build/               作業用の生成物（Git管理外）
```

まず [ハードウェアREADME](hardware/rev-a/README.md) を読んでください。KiCad 10で `hardware/rev-a/kicad/CoreS3_ILDA_RevA.kicad_pro` を開きます。ライブラリはプロジェクト相対参照で同梱しています。

## Git運用

- 回路図・PCB・プロジェクト設定・ライブラリ・BOM・文書・ソースコードをコミットします。
- 通常のGerber、プレビュー、ビルド成果物は `build/` に出力します。KiCadの個人設定、ロック、バックアップは除外します。
- 製造に使用する生成物だけを `releases/<revision>/<date>/` に保存し、その設計と同じコミットで確定します。初回分は2026-10-02の記録です。
- 基板リビジョンを変更する際は `hardware/rev-b/` 等を作り、旧版を保持します。同じリビジョン内の調整はGit履歴で管理します。
- ファームウェアのフレームワーク、依存管理、CI、リモートリポジトリはまだ選定していません。

## 変更後の検証

KiCadでERCとDRC（回路図との一致検査を含む）を再実行し、ゾーンを再フィルして保存してください。CLIをPATHへ設定している場合のPowerShell例：

```powershell
New-Item -ItemType Directory -Force build/rev-a | Out-Null
kicad-cli sch erc --format json -o build/rev-a/ERC.json hardware/rev-a/kicad/CoreS3_ILDA_RevA.kicad_sch
kicad-cli pcb drc --format json --schematic-parity -o build/rev-a/DRC.json hardware/rev-a/kicad/CoreS3_ILDA_RevA.kicad_pcb
```

初回の検査はERC/DRC/未配線/回路図不一致0件ですが、実機動作と機械的嵌合は未検証です。初回電気評価はレーザー未接続で実施してください。制御ソフトウェアには起動時ENABLE Low、DAC初期値、異常時停止の実装が必要です。
