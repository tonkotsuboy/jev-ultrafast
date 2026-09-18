# Jev Ultrafast（日本語UI・任意URL対応フォーク）

[browser-use/jev-ultrafast](https://github.com/browser-use/jev-ultrafast) のフォークです。
**仕組み・設計・性能データは[本家のREADME](https://github.com/browser-use/jev-ultrafast#readme)を参照してください。**
ここにはフォークでの差分と、実際に動かして詰まった記録だけを書いています。

紹介ツイート → [@tonkotsuboy_com](https://x.com/tonkotsuboy_com/status/2100861211000951110)

## 本家との違い

| 変更点 | 内容 |
| --- | --- |
| **任意のURLで実行** | 「開始URL」欄に任意のサイトを入力できます。本家は Google Flights と固定ページの3択のみ |
| **UIの日本語化** | インスペクタ画面の文言を日本語にしました。ライブラリ層のエラーは英語のままです |
| **空回りの検知** | 操作が一度も実行されないまま判断だけが15回続いたら停止し、理由を表示します。詰まった実行が **50秒→14秒** で止まります |
| **指示の追加** | 止まった位置から、ブラウザを開き直さずに指示を追加できます。過去の依頼は消えず積み上がります。`DONE`/`BLOCKED` からの復帰も可能 |
| **開始＝自動実行** | 「デモを開始」で最後まで自動実行します。1手ずつ確認したい場合は「1手ずつ開始」を使います |

## ⚠️ 既知の問題

**成功すれば非常に高速ですが、失敗する操作がかなり多いです。** 実用ツールではなく実験実装として扱ってください。

- 本家の計測は **Google Flights の1タスクを3回試しただけ**で、本家自身が `not a general reliability benchmark` と明記しています。一般的なサイトでの信頼性は未検証です。
- **オートコンプリートを持つサイトで詰まりやすい**です。候補リストが次の入力欄を覆っていると、実行側が「覆われた要素は押さない」と正しく拒否するため、モデルが候補を確定しない限り先に進めません（日本の旅行予約サイトで再現・確認済み）。
- Shadow DOM・iframe・canvas・ファイルアップロード・ポップアップタブは本家の時点で対象外です。
- 詰まったら「指示を追加」欄に手順のヒント（例:「候補リストから該当項目をクリックして確定してから次へ」）を入れて続行できます。

## 動かす

```bash
git clone https://github.com/tonkotsuboy/jev-ultrafast.git
cd jev-ultrafast
uv sync
cp .env.example .env    # TYPESAFE_API_KEY と TEXT_MODEL_API_KEY を記入
uv run jev              # http://127.0.0.1:8766
```

`.env` の値は**引用符や前後の空白を入れないでください**。`uv run jev` のパーサは `strip()` しないため、
`KEY="sk-xxx"` と書くと引用符ごとキーとして送られて 401 になります。

Chrome 136 以降は、初回に `chrome://inspect/#remote-debugging` で
「Allow remote debugging for this browser instance」を有効にする必要があります。
接続でつまずいたら `uv run browser-harness --doctor` を実行してください。

## ライセンス

MIT（本家を継承）
