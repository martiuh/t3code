# Working from this fork

This checkout is the [`martiuh/t3code`](https://github.com/martiuh/t3code) fork of [`pingdotgg/t3code`](https://github.com/pingdotgg/t3code). `origin` points to the fork and `upstream` points to the original repository. Keep feature work on separate branches so `main` can stay close to upstream.

## Build and run from source

Install [Vite+](https://github.com/pingdotgg/t3code/blob/main/docs/operations/development.md#first-checkout), then run these commands from the repository root:

```sh
PATH="$HOME/.local/share/vite-plus/fallback-bin:$PATH" vp i
vp run build:desktop
node apps/server/dist/bin.mjs serve --mode web --base-dir "$HOME/.t3-source"
```

Vite+ supplies the Node and pnpm versions pinned by this repository. The `PATH` prefix matters on this Mac because an older, separately installed pnpm otherwise takes precedence and cannot read the lockfile. The final command runs the built production server with its own persistent data directory. Open the pairing URL it prints; keep that URL private. Stop the server with Ctrl+C before rebuilding or updating it.

To validate a new build without touching the persistent data, start it with a temporary data directory and a free local port:

```sh
node apps/server/dist/bin.mjs serve --mode web --host 127.0.0.1 --port 48681 --base-dir "$(mktemp -d)"
```

In another terminal, check `curl -sS -o /dev/null -w '%{http_code}\n' http://127.0.0.1:48681/`; it should print `200`. Then stop the server with Ctrl+C. The production source build and this HTTP check passed on 2026-11-13 at commit `3521634e`.

## Bring in upstream changes

From a clean working tree, update this fork's `main`, reinstall dependencies, and rebuild:

```sh
git switch main
git fetch upstream main --filter=blob:none
git merge upstream/main
git push origin main
PATH="$HOME/.local/share/vite-plus/fallback-bin:$PATH" vp i
vp run build:desktop
```

This checkout is a shallow partial clone. Normal upstream fetches work; fetch full history only if a future Git operation needs commits older than the clone point. After rebuilding, repeat the temporary-data check above before restarting the persistent server.
