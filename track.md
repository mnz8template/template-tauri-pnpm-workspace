create `scripts/clean.sh` `package.json` `pnpm-workspace.yaml` `Cargo.toml` `.gitignore`

`pnpm create vite@latest`: `ui-desktop` `ui-mobile`

`pnpm i`

`pnpm i -wD @tauri-apps/cli@latest`

`pnpm tauri init --frontend-dist "../ui-desktop/dist" --dev-url "http://localhost:25000" --before-dev-command "pnpm -F ui-desktop dev" --before-build-command "pnpm -F ui-desktop build"`

modify `tauri.conf.json` identifier

`pnpm tauri android init`

create `src-tauri/tauri.mobile.conf.json`

modify `ui-desktop/vite.config.ts` `ui-mobile/vite.config.ts`

`pnpm tauri dev`

`pnpm tauri android dev -c src-tauri/tauri.mobile.conf.json`

> package.json

```json
{
  "name": "template-tauri-pnpm-workspace",
  "private": true,
  "version": "0.0.1",
  "scripts": {
    "tauri": "tauri",
    "clean": "sh ./scripts/clean.sh"
  }
}
```

> pnpm-workspace.yaml

```yaml
packages:
  - 'ui-desktop'
  - 'ui-mobile'
```

> Cargo.toml

```toml
[workspace]
members = ["src-tauri"]
resolver = "2"
```

> .gitignore

```
node_modules
/target/
```

> tauri.mobile.conf.json

```json
{
  "$schema": "../node_modules/@tauri-apps/cli/config.schema.json",
  "productName": "template-tauri-pnpm-workspace",
  "version": "0.0.1",
  "identifier": "com.template-tauri-pnpm-workspace",
  "build": {
    "frontendDist": "../ui-mobile/dist",
    "devUrl": "http://localhost:26000",
    "beforeDevCommand": "pnpm -F ui-mobile dev",
    "beforeBuildCommand": "pnpm -F ui-mobile build"
  }
}
```

> vite.config.ts

```ts
import { defineConfig } from 'vite';
import react, { reactCompilerPreset } from '@vitejs/plugin-react';
import babel from '@rolldown/plugin-babel';

const host = process.env.TAURI_DEV_HOST;

// https://vite.dev/config/
export default defineConfig({
  plugins: [react(), babel({ presets: [reactCompilerPreset()] })],
  // Vite options tailored for Tauri development and only applied in `tauri dev` or `tauri build`
  //
  // 1. prevent Vite from obscuring rust errors
  clearScreen: false,
  // 2. tauri expects a fixed port, fail if that port is not available
  server: {
    port: 25000,
    strictPort: true,
    host: host || false,
    hmr: host
      ? {
          protocol: 'ws',
          host,
          port: 25001,
        }
      : undefined,
  },
});
```
