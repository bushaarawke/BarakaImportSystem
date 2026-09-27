# Deployment

`index.html` is a static file and can be served by any web server. Two platform functions must be replaced when running outside claude.ai.

## What to replace

### 1. Database: `claude.use("db")`

Each module calls `window.claude.use("db")` and uses this API:

| Call | Used for |
|------|----------|
| `db.collection(name).onSnapshot(fn)` | Live list of a collection (`snap.docs[i].id`, `.data()`) |
| `db.collection(name).orderBy(f, dir).limit(n)` | Sorted shipments list |
| `db.collection(name).add(obj)` | Create with a generated id (returns `{id}`) |
| `db.doc("col/id").get()` | Read one document (`exists`, `data()`) |
| `db.doc("col/id").set(obj)` | Create or replace |
| `db.doc("col/id").update(obj)` | Merge fields |
| `db.doc("col/id").delete()` | Delete |
| `db.doc("col/id").onSnapshot(fn)` | Live single document |

The simplest approach is a small adapter that defines `window.claude = { use: async (name) => ... }` and returns an object with this shape, backed by your own database. Load it before the other scripts, and no module code has to change.

### 2. File download: `claude.use("downloads")`

Replace `downloads.save({filename, data})` with a standard browser download:

```js
function save({ filename, data }) {
  const blob = data instanceof Blob ? data : new Blob([data]);
  const a = document.createElement("a");
  a.href = URL.createObjectURL(blob);
  a.download = filename;
  a.click();
  setTimeout(() => URL.revokeObjectURL(a.href), 1000);
  return Promise.resolve({ status: "saved" });
}
```

### 3. Login

On claude.ai, access control comes from Claude accounts. Elsewhere, add a login (email and password, plus OTP) in front of the page and protect the database with rules so only signed-in staff can read or write.

## Options

| Option | Notes |
|--------|-------|
| **Firebase** (Firestore + Authentication + Hosting) | Firestore's API closely matches the one above, so the adapter is thin. Authentication supports email, phone OTP and multi-factor. |
| **PocketBase** | A single open-source program with database, login and real-time updates. Can run on an office server, so it keeps working during internet outages and data stays in Ethiopia. Create one collection per collection above, with a JSON field for arrays. |
| **Own server** | Node.js or PHP with PostgreSQL or MySQL, a REST API and WebSockets (or polling) for live updates. Best if the system will later connect to an ERP, accounting or bank systems. |

## Moving existing data

Download a backup file from the **Backup** button (see [backup.md](backup.md)). It contains every collection with its document ids. Import it into the new database keeping the same ids, so the links between collections still work. The Firebase version includes an import tool.
