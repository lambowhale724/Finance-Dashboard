# Getting the dashboard onto your phone, iPad and laptop

Two separate jobs. Do them in this order.

---

## 1 — Put it on the web (once, ~5 minutes)

The files in this folder are the whole app: `index.html`, `sw.js`,
`manifest.webmanifest` and the four `icon-*.png` files. They must all sit
in the same folder on the host.

**GitHub Pages**

1. Sign up at github.com (free).
2. Click **New repository**. Name it `finances`. Keep it **Public** — Pages
   needs that on the free plan. Nothing sensitive is in these files; your
   numbers live in your browser and your own Supabase table.
3. On the empty repo page click **uploading an existing file**, drag in all
   the files from this folder, then **Commit changes**.
4. Go to **Settings → Pages**. Under "Branch" pick `main` / `/ (root)` and
   press **Save**.
5. Wait about a minute, then open
   `https://YOURNAME.github.io/finances/` — no login prompt, ever.

**Add it to your home screen**

- *iPhone / iPad*: open the URL in Safari → Share → **Add to Home Screen**.
- *Mac*: Safari → File → **Add to Dock**. (Chrome: the install icon in the
  address bar.)

It then opens full screen with its own icon, and works with no signal.

**Updating it later**: I hand you a new `index.html`; drag it into the repo
to replace the old one. Devices pick it up next time they open with signal.

---

## 2 — Turn on sync (once per device, ~5 minutes total)

Until this is done, each device keeps its own separate copy.

1. Sign up at supabase.com (free) and create a project — any name, any
   region near you.
2. Left sidebar → **SQL Editor** → paste this and press **Run**:

   ```sql
   create table if not exists cfo_state (
     id text primary key,
     rev bigint default 0,
     device text,
     updated_at timestamptz default now(),
     data jsonb
   );
   alter table cfo_state enable row level security;
   create policy cfo_state_open on cfo_state
     for all to anon using (true) with check (true);
   ```

3. **Project Settings → API**. Keep this tab open; you need two values:
   the **Project URL** and the **anon public** key.
4. Open the dashboard → **Assumptions** tab → *Sync across your devices*.
   Paste both values, name the device (`iPhone`), press
   **Connect and sync**. Your current numbers upload.
5. Repeat step 4 on each other device with the same two values and a
   different device name. Each one pulls the shared copy down.

### How it behaves

- Every edit saves on the device instantly, then uploads about a second
  later. The dot in the header goes amber while uploading, green when done.
- Opening the dashboard, or switching back to it, checks the cloud first
  and pulls anything newer.
- No signal: it keeps working and uploads when you're back on.
- Two devices edited the same field? The newer edit wins. The status line
  tells you when it pulled someone else's change.
- **Pull cloud copy** / **Push this device** are the manual overrides if a
  device ever looks out of step.

### The honest caveat

The anon key sits inside `index.html`, so anyone with both your dashboard
URL and that key could read the record. Don't post the URL anywhere. If you
ever want it closed off, rotate the key in Supabase — the dashboard simply
stops syncing until you paste the new one.

Exports are still worth keeping. They're a real backup, independent of both
the device and the cloud record.
