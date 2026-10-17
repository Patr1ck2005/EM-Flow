# Build and deployment notes

[English README](../README.md) · [中文 README](../README.zh-CN.md)

## Verified way to use the application

Open **https://patr1ck2005.github.io/EM-Flow/** in a WebGL-capable browser. The public deployment was exercised on 2026-10-17. The screenshots in [the gallery](showcase/gallery.html) come from that deployment, not a local rebuild.

## What is present in this source snapshot

- Java sources under `src/com/falstad/ripple/`.
- GWT module `com.falstad.ripple.Ripple`, entry point `client.Applets`, output module name `ripple`.
- `war/Ripple.html`, CSS, WebGL JavaScript and shader source.
- Electron wrapper in `app/`.
- No Maven/Gradle build file, pinned GWT SDK, or generated `war/ripple/ripple.nocache.js`.

The GWT XML DTD references 2.7.0. This is a module-schema reference, not evidence that the current source was tested with that SDK. The in-source UI version string is 1.4; glMatrix is 0.9.5.

## Original Eclipse / GWT workflow

The earlier project README specifies **Eclipse Oxygen** with the **GWT Eclipse plugin**. These are historical instructions. The checkout does not supply a complete, verified IDE project configuration.

1. Configure the Java sources and GWT module in an Eclipse/GWT project.
2. Use the GWT plugin's development mode and open `http://127.0.0.1:8888/Ripple.html`.
3. Run **GWT Compile Project…** for `com.falstad.ripple.Ripple`, with output under `war/`.
4. Verify `war/ripple/ripple.nocache.js`, compiled permutations, module public resources, examples and `setuplist.txt` exist.

A typical compiled layout is:

```text
war/
  Ripple.html
  ripple.js
  glMatrix-0.9.5.min.js
  webgl-utils.js
  ripple/
    ripple.nocache.js
    ...compiled GWT files...
    setuplist.txt
    examples/
```

Only **after successful GWT compilation**, a local static server can serve the browser build:

```bash
python -m http.server 8000 --directory war --bind 127.0.0.1
# Open http://127.0.0.1:8000/Ripple.html
```

Python here is only the static server. Stop it with Ctrl+C. This command does not compile Java or replace the missing GWT output. A local source build remains unverified.

## Deployment

After compiling and verifying the application, the original workflow copies the browser assets from `war/`, excluding `WEB-INF/`, to a static web host. Confirm that the generated module resources and examples use the expected relative paths.

The source supports `startExample=<filename>` for a preset in the module's examples directory, `rol=<string>` for an encoded setup, and an eight-color `colorScheme=` list. Preset names may differ between source branches and the public deployment; use the deployment UI rather than assuming a filename is available.

## Legacy Electron packaging

`app/package.json` declares package version 0.0.1 and `electron >=9.3.1`; the earlier README described a prebuilt Electron 9.3.2 package. This route is historical and was not exercised here.

`app/main.js` loads `app/war/Ripple.html`. A compiled browser build must therefore be copied into `app/war/` before the wrapper can load it. With an already prepared compatible Electron environment, the declared start command is:

```bash
cd app
npm start
```

The repository does not pin a current supported Electron release or provide a verified packaging recipe. No dependency installation or Electron runtime was added during this documentation pass.

## Attribution and license

Paul Falstad's source notices state GPL-2.0-or-later. Preserve those notices and the applicable GPL terms. The earlier README credits Immortalin for the initial Electron-wrapper work adapted from CircuitJS1.

