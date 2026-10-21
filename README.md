# Operations Report Hub – setup guide

A single-file web portal (`index.html`) with SHEQ, Admin and Engineering tabs.
Everyone signs in with their Zululand Filters Microsoft 365 account, uploads their own files,
and chooses exactly who can view or edit each one.

---

## How it works

- **Anyone signed in can upload** a .pbix (or PDF, Excel, etc.) or add a Power BI report link to a tab.
- **The file is saved in the uploader's own OneDrive**, in `PowerBI Portal/<Section>/`.
- **Right after uploading, the uploader picks who can see it** by typing email addresses and choosing:
  - **Can view** – see and download the file
  - **Can edit** – also replace it with a new version
- **People who haven't been given access don't see the file at all.** Microsoft OneDrive enforces this, so the file can't be reached another way either.
- **The portal owner (`ownerEmail`) automatically gets Can edit on every file.** Uploaders see this as "Can edit · portal owner" and can't remove it in the portal. If it's removed directly in OneDrive, the portal adds it back the next time the uploader opens that tab. The owner sees every file, labelled "Uploaded by …", and can replace any file with the upload button on its row.
- The uploader can change access any time with the **Manage access** button (person icon) next to their file, and is the only one who can delete it.
- Uploading a file with the same name as one you can see asks whether to **replace** it. Replacing someone else's file only works if they gave you **Can edit**.

### The index folder
So the portal knows which files exist, it keeps a small index in the portal owner's OneDrive (`PowerBI Portal Index`). It holds one tiny entry per file containing only IDs, with no file names or content. Everyone needs edit access to this folder so their uploads can be listed.

---

## One-time setup (portal owner)

Steps 1–4 from before (Entra app registration, configuration, GitHub Pages hosting, redirect URI) stay the same. Two things change:

### A. Check the app permissions
In **entra.microsoft.com → your app → API permissions**, make sure these Microsoft Graph *Delegated* permissions are listed and admin consent is granted:
- `User.Read`
- `Files.ReadWrite.All` (now used by everyone, because everyone can upload and share)

### B. Create and share the index folder
1. Open the portal and sign in as **Akhil@zululandfilters.co.za**. The portal creates `PowerBI Portal Index` in your OneDrive automatically.
2. In OneDrive, select that folder → **Share** → settings → **People in Zululand Filters** → **Can edit** → **Apply** → **Copy link**.
3. Send the link to Claude, or paste it into `indexFolderUrl` in `index.html` yourself, then upload the updated file to GitHub.

You no longer need to share the `PowerBI Portal` files folder. Leave it private. Each file is shared individually.

---

## Configuration reference (top of `index.html`)

| Setting | What it does |
|---|---|
| `clientId`, `authority` | Entra app ID and your tenant |
| `ownerEmail` | Portal owner; their OneDrive holds the index |
| `filesFolder` | Folder created in each uploader's OneDrive |
| `indexFolderName` / `indexFolderUrl` | The index folder and its "Can edit" share link |
| `allowedDomains` | Email domains that can be given access (`zululandfilters.co.za`) |
| `sections` | The tabs; add `{ id, name, icon, blurb }` for more |

---

## Things to know

- **Your OneDrive sharing policy applies.** If IT has turned off sharing in OneDrive, giving access will fail with a message in the access window.
- **Someone leaving the company:** their uploads live in their OneDrive. Before their account is removed, they (or IT) should hand over important files, or those files will disappear from the portal when the OneDrive is deleted.
- **Index tidiness:** anyone with the index link could delete entries, which would hide files from the portal listing (the files themselves stay safe and private). Uploading the file again restores it.
- **Owner access on older uploads:** files uploaded before this change get the owner's edit access added the next time their uploader opens that tab in the portal.
- **Existing files from the earlier version** (in your `PowerBI Portal/SHEQ` etc.) won't show up until they're uploaded again through the portal.

## Troubleshooting

| Message | Fix |
|---|---|
| *AADSTS50011 redirect URI mismatch* | The browser address must exactly match the SPA redirect URI in Entra (including trailing `/`). |
| *Need admin approval* | IT must grant admin consent for `Files.ReadWrite.All`. |
| "The index folder link is missing" | Do step B and put the link in `indexFolderUrl`. |
| "You can only view …, so it wasn't replaced" | Ask the uploader for **Can edit**, or rename your file. |
| A colleague can't see a file | The uploader needs to add their email in **Manage access**. |
