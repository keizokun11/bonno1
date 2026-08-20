# 煩悩オークション Supabase版 v6

## v6の追加機能

- 投稿者側 `/submit` に、スクリーン表示中の煩悩へ価値づけできる欄を追加しました。
- 通貨単位は `bonno` です。
- 参加者は `1 bonno`、`10 bonno`、`100 bonno`、`1000 bonno`、`10000 bonno` のボタンから選べます。
- 全参加者から送られた bonno を合計し、投稿者側・運営側・スクリーン側に表示します。
- v5までの「NG BOX」「燃焼済み履歴」「表示を空にする」「完全消去」機能も維持しています。

## 追加で必要なSupabase SQL

v6では `bonno_values` テーブルを追加します。SupabaseのSQL Editorで、以下を実行してください。

```sql
create table if not exists bonno_values (
  id uuid primary key default gen_random_uuid(),
  bonno_id uuid not null references bonnos(id) on delete cascade,
  amount integer not null check (amount in (1, 10, 100, 1000, 10000)),
  created_at timestamp with time zone default now()
);

alter table bonno_values enable row level security;

drop policy if exists "bonno_values_select" on bonno_values;
drop policy if exists "bonno_values_insert" on bonno_values;
drop policy if exists "bonno_values_delete" on bonno_values;

create policy "bonno_values_select" on bonno_values
for select
using (true);

create policy "bonno_values_insert" on bonno_values
for insert
with check (true);

create policy "bonno_values_delete" on bonno_values
for delete
using (true);

do $$
begin
  begin
    alter publication supabase_realtime add table bonno_values;
  exception when duplicate_object then
    null;
  end;
end $$;
```

## アップロードするファイル

GitHubには以下の3つだけを入れてください。

```text
index.html
vercel.json
README.md
```

ZIPファイルそのものはアップロードしないでください。

## 反映手順

1. SupabaseのSQL Editorで上のSQLを実行
2. ZIPを解凍
3. GitHubの既存の `index.html`、`vercel.json`、`README.md` を上書き
4. Commit changes
5. VercelでRedeploy
6. `/submit`、`/admin`、`/screen` を開き直す

ブラウザに古い画面が残る場合は、更新ボタンを押すか、別ブラウザ・シークレットモードで確認してください。
