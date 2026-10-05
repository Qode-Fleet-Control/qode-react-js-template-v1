# React (JavaScript) template

Provisioned from [`Qode-Fleet-Control/fleet-template-v1`](https://github.com/Qode-Fleet-Control/fleet-template-v1) — the fleet
lifecycle contract (`bin/`, `fleet.conf`, deploy workflows, `compose.yaml`) with a React 19 single-page app in plain JavaScript, built with Vite laid on top.

Listens on `0.0.0.0:$PORT` (default `3000`) and serves at the root (`/`) of its own hostname
(`https://<hash>.<FLEET_APP_DOMAIN>/`); the health check hits `/`. In the container: the static build (`dist/`) behind nginx.

## Origin

    npm create vite@latest qode-react-js-template-v1 -- --template react --no-interactive

Generated 2026-10-05 with create-vite 9.2.1 (host Node v22.12.0 / npm 10.9.0).

## Run it

### On the fleet

The fleet clones the repo, injects `PORT` (and the workspace's `DATABASE_URL`, `REDIS_URL`, ...) and runs
`bin/run`, which uses the docker runtime from `fleet.conf`: `docker compose build`, then `docker compose up --remove-orphans` in the foreground.

### With docker

    PORT=3000 bin/run                  # what the fleet does
    docker compose up --build        # or plain compose

### Without docker

`FLEET_RUNTIME=process bin/run` runs the plain commands from `fleet.conf`:

| step | command |
|---|---|
| install | `npm install` |
| build | `npm run build` |
| start | `npx vite preview --host 0.0.0.0 --port $PORT` |

    ./bin/run       # install, build, start in the foreground
    ./bin/start     # start from existing build artifacts
    ./bin/restart   # rebuild and restart
    ./bin/stop      # stop whatever holds the port

See `docs/fleet-lifecycle.md` for the full contract.

## Deviations from the generator output

- `vite.config.js` sets `server.allowedHosts` / `preview.allowedHosts` from `FLEET_APP_HOST` (any host when unset): Vite otherwise answers the fleet hostname with "Blocked request" in `vite dev` / `vite preview`.
- `package-lock.json` added (`npm install --package-lock-only`) so the image build can use `npm ci`.
- Added the fleet files: `bin/` (lifecycle scripts), `fleet.conf`, `Dockerfile`, `compose.yaml`, `.dockerignore`, `.env.example`, `.github/workflows/`, `docs/fleet-lifecycle.md`; fleet entries (`.fleet/`, `*.log`, ...) prepended to `.gitignore`.

## Verified

Verified 2026-10-05 against the fleet's docker runtime, on docker 29.8:

- `migrate.py audit` (the coordinator's own refusal checks): **READY**.
- `verify.sh <repo> 46010` — `bin/run` in the background, probe `HEALTH_PATH`, `bin/restart`, probe again,
  `bin/stop`: `run=200 restart=200 containers_after_stop=0`. Locally the image was built with `npm ci` from the committed lockfile.

---

# React + Vite

This template provides a minimal setup to get React working in Vite with HMR and some Oxlint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react) uses [Oxc](https://oxc.rs)
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react-swc) uses [SWC](https://swc.rs/)

## React Compiler

The React Compiler is not enabled on this template because of its impact on dev & build performances. To add it, see [this documentation](https://react.dev/learn/react-compiler/installation).

## Expanding the Oxlint configuration

If you are developing a production application, we recommend using TypeScript with type-aware lint rules enabled. Check out the [TS template](https://github.com/vitejs/vite/tree/main/packages/create-vite/template-react-ts) for information on how to integrate TypeScript and Oxlint's TypeScript related rules in your project.
