# 煩悩オークション Supabase連動版

この版は、参加者スマホ・運営PC・会場スクリーンをSupabaseで共有します。

## 1. SupabaseでSQLを実行

SupabaseのSQL Editorで以下を実行してください。

```sql
create table if not exists bonnos (
  id uuid primary key default gen_random_uuid(),
  text text not null check (char_length(text) <= 50),
  category text,
  nickname text,
  status text not null default 'pending',
  created_at timestamp with time zone default now()
);

create table if not exists display_state (
  id int primary key,
  current_bonno_id uuid references bonnos(id),
  mode text not null default 'idle',
  updated_at timestamp with time zone default now()
);

insert into display_state (id, mode)
values (1, 'idle')
on conflict (id) do nothing;

alter table bonnos enable row level security;
alter table display_state enable row level security;

drop policy if exists "bonnos_select" on bonnos;
drop policy if exists "bonnos_insert" on bonnos;
drop policy if exists "bonnos_update" on bonnos;
drop policy if exists "display_state_select" on display_state;
drop policy if exists "display_state_update" on display_state;

create policy "bonnos_select" on bonnos for select using (true);
create policy "bonnos_insert" on bonnos for insert with check (true);
create policy "bonnos_update" on bonnos for update using (true) with check (true);

create policy "display_state_select" on display_state for select using (true);
create policy "display_state_update" on display_state for update using (true) with check (true);

alter publication supabase_realtime add table bonnos;
alter publication supabase_realtime add table display_state;
```

もし `alter publication` で「already member」系のエラーが出た場合は、すでにRealtime対象なので無視して大丈夫です。

## 2. SupabaseのURLとanon keyを入れる

Supabaseの Project Settings → API から以下をコピーします。

- Project URL
- anon public key

`index.html` の上部にあるこの2行を置き換えてください。

```js
const SUPABASE_URL = "PASTE_SUPABASE_URL_HERE";
const SUPABASE_ANON_KEY = "PASTE_SUPABASE_ANON_KEY_HERE";
```

## 3. GitHubにアップロード

GitHubには以下の3ファイルだけを入れてください。

```text
index.html
vercel.json
README.md
```

## 4. Vercelでデプロイ

VercelでGitHubリポジトリをImportしてDeployしてください。

## 5. 動作確認

以下を別端末で開きます。

```text
/submit   参加者スマホ
/admin    運営PC
/screen   会場PC
```

参加者が投稿すると、運営画面の承認待ちに反映されます。
運営が表示・燃やすを押すと、会場スクリーンに反映されます。

## 注意

このMVP版は、管理画面の認証をまだ入れていません。
本番公開時は、URLを参加者に見せない、または次の段階で管理者認証を追加してください。
