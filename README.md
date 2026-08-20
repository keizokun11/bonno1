# 煩悩オークション Supabase版 v5

## v5の修正

- `/submit` の左上から「投稿・運営・スクリーン」ボタンを完全に非表示にしました。
- v3の履歴、NG BOX、表示を空にする、完全消去機能は維持しています。

## アップロードするファイル

GitHubには以下の3つだけを入れてください。

```text
index.html
vercel.json
README.md
```

ZIPファイルそのものはアップロードしないでください。

## 反映手順

1. ZIPを解凍
2. GitHubの既存の `index.html`、`vercel.json`、`README.md` を上書き
3. Commit changes
4. VercelでRedeploy
5. `/submit` を開き直す

スマホやPCのブラウザに古い画面が残る場合は、更新ボタンを長押し・ハードリロード・別ブラウザで確認してください。
