# Operations Report Hub – setup guide

A web portal (`index.html`) with SHEQ, Admin and Engineering tabs.
You send colleagues the website link; they sign in with their Zululand Filters Microsoft 365
account and use it like any other website. Nobody shares or opens OneDrive folders by hand.

---

## How it works

- **Anyone signed in can upload** a .pbix (or PDF, Excel, etc.) or add a Power BI report link.
- **Each file is stored in the uploader's own OneDrive** (`PowerBI Portal/<Section>/`), out of sight. Users never need to go there.
- **Right after uploading, the uploader chooses who can see it.** They type a name or email (suggestions come from the company directory) and pick:
  - **Can view** – see and download
  - **Can edit** – also replace with a new version
- **People without access don't see the file at all.** OneDrive enforces this, not just the website.
- **The portal owner automatically gets Can edit on every file** and sees everything.
- Uploading a file with an existing name asks whether to replace it.

### Behind the scenes
The portal keeps a small listing in the owner's OneDrive (`PowerBI Portal Index`) holding only file IDs (no names or content). Every time the owner opens the portal, it automatically gives every colleague in the company directory access to that listing. New staff are picked up the next time the owner opens the portal, or immediately when someone shares a file with them.

---

## One-time setup

1. **Entra app permissions** (entra.microsoft.com → your app → API permissions → Microsoft Graph → Delegated). Make sure these are listed and **Grant admin consent** is clicked:
   - `User.Read`
   - `User.ReadBasic.All` (new: company directory for name suggestions and automatic access)
   - `Files.ReadWrite.All`
2. **Upload `index.html` to GitHub** (Add file → Upload files → Commit changes).
3. **Open the portal and sign in as Akhil@zululandfilters.co.za.** This creates the listing and gives everyone access. You'll see "Portal access set up for N colleagues".
4. **Send colleagues the website link.** That's it.

If a colleague signs in before step 3, they can already upload and share their own files. A note tells them colleagues' files will appear once the owner has opened the portal.

---

## Configuration reference (top of `index.html`)

| Setting | What it does |
|---|---|
| `clientId`, `authority` | Entra app ID and your tenant |
| `ownerEmail` | Portal owner: gets edit access to all files and hosts the listing |
| `filesFolder` | Folder created in each uploader's OneDrive |
| `indexFolderName` | The listing folder in the owner's OneDrive |
| `oneDriveLibrary` | OneDrive library name; leave as `Documents` unless your tenant isn't in English |
| `allowedDomains` | Email domains that can be given access |
| `sections` | The tabs; add `{ id, name, icon, blurb }` for more |

---

## Things to know

- **Someone leaving the company:** their uploads live in their OneDrive. Hand over important files before their account is deleted.
- **Your OneDrive sharing policy applies.** If IT has blocked sharing, giving access fails with a message in the access window.
- **Earlier uploads** made with older versions of the portal need to be uploaded again.

## Troubleshooting

| Message | Fix |
|---|---|
| *AADSTS50011 redirect URI mismatch* | The browser address must exactly match the SPA redirect URI in Entra (including trailing `/`). |
| *Need admin approval* | IT must grant admin consent for the three permissions above. |
| Colleague sees "files will appear once the portal owner has opened the portal" | Open the portal as the owner once; their access is set up automatically. |
| "You can only view …, so it wasn't replaced" | Ask the uploader for **Can edit**, or rename your file. |
| A colleague can't see a file | The uploader needs to add them in **Manage access**. |
