# Creating an EditText plugin

[Link to Application Repo](https://github.com/JacobT2006/EditText)

Plugins are ZIP files installed from the **Plugins** button in EditText. The guide and installer support two permission modes:

- **UI-only:** opens `ui.html` in a separate sandboxed window. It has no access to EditText notes or APIs, Node.js, local files, or network requests.
- **Full trust:** runs `main.cjs` in the EditText main process when the user chooses **Run**. It can access the user's files, network, and app APIs. Only share this mode with code you trust.

Installing a plugin does not run its code. Full-trust plugins show an additional warning before installation and run only after the user presses **Run**.

## 1. Create a plugin folder

Create a folder such as `hello-plugin` with a `manifest.json` and one entry file. The manifest is required for both modes:

```json
{
  "id": "hello-plugin",
  "name": "Hello Plugin",
  "version": "1.0.0",
  "description": "A small example plugin."
}
```

The `id` must be lowercase letters, digits, dots, underscores, or hyphens, and must start with a letter or digit. Keep it unique. Names are limited to 80 characters and descriptions to 500 characters.

## 2. Choose a plugin type

### UI-only plugin

Add `ui.html` beside `manifest.json`:

```html
<section>
  <h2>Hello from a plugin</h2>
  <p>This page runs separately from the note editor.</p>
  <button id="hello">Say hello</button>
</section>
<script>
  document.getElementById('hello').addEventListener('click', () => {
    document.querySelector('p').textContent = 'Hello!';
  });
</script>
```

UI-only pages may use inline HTML, CSS, and JavaScript. External resources, network requests, frames, and navigation are blocked. The page has no API for reading or changing notes.

### Full-trust plugin

Add `main.cjs` beside `manifest.json`:

```js
module.exports = async ({ note }) => {
  if (!note) {
    return;
  }

  // Return text to replace the active note, or return nothing to leave it unchanged.
  return note.content.toUpperCase();
};
```

The plugin receives a snapshot of the active note as `{ id, name, content }`. It may return a string to replace the note text, or return nothing to make no change. The user must have an active note open. The app marks returned text as an edit; the user's normal save and auto-save settings still apply.

Full-trust code runs with Node.js privileges in the app process. It can read or write files, access the network, and call app APIs. Keep code small, explain its behavior, and never install it unless you trust its author.

## 3. Package and install

Put `manifest.json` and the selected entry file directly in the ZIP root. For example:

```text
hello-plugin.zip
├── manifest.json
└── ui.html
```

Or, for full trust:

```text
hello-plugin.zip
├── manifest.json
└── main.cjs
```

Open EditText, select the **Plugins** puzzle-piece button, choose **UI-only** or **Full trust**, and select **Install ZIP…**. Full-trust installation requires a separate confirmation. Installed plugins are listed in the manager and can be opened/run or removed. To replace a plugin version, remove the old one before installing the new ZIP.

ZIP files may be at most 10 MB, expand to at most 20 MB, and contain at most 500 entries. Plugin IDs must be unique.

## 4. Submit a plugin

Use **GitHub submissions** in the plugin manager to open the community repository:
[JacobT2006/editText-plugins](https://github.com/JacobT2006/editText-plugins).

Submit a pull request containing the ZIP under `plugins/<plugin-id>.zip` and a short `README.md` describing its features, author, required permission mode, and any risks. Make sure the plugin ZIP follows the format above. Users can review and download submissions from that repository, then install the ZIP locally.
