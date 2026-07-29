---
name: setup-nextjs-docker
description: Create or update a strict Docker setup for Yarn-based Next.js projects. Use when Codex is asked to configure Docker for a Next.js app, create `.docker/Dockerfile.main`, create or update `docker-compose.yaml`, prepare a Next.js project to run with Docker, or compare/update an existing Docker setup. Do not use for non-Next.js projects, npm/pnpm projects, or requests that require `docker init`, building images, starting containers, changing `package.json`, or adding `output: "standalone"`.
---

# Setup Next.js Docker

## Core Rules

Work only in Next.js projects that use Yarn.

Create or update exactly these files:

```text
.docker/Dockerfile.main
.dockerignore
docker-compose.yaml
.env
.env.example
```

Do not run `docker init`, `docker compose build`, `docker compose up`, or `docker compose up -d`.
Do not change `package.json`, `next.config.ts`, application code, or the Dockerfile model to work around missing files.
Do not adapt the workflow to npm or pnpm.

Before editing files, read `references/docker-templates.md` and use its templates exactly.

## Required Checks

1. Locate `package.json`; treat its directory as the project root. If missing, stop without editing.
2. Confirm `next` exists in `dependencies` or `devDependencies`.
3. Confirm `yarn.lock` exists.
4. Confirm `scripts.build` and `scripts.start` exist in `package.json`; commands may include extra arguments.
5. Confirm these model-required paths exist: `package.json`, `yarn.lock`, `public/`, `next.config.ts`.
6. Run `docker --version`; stop if unavailable.
7. Run `docker info`; stop before editing if Docker Engine is not running.
8. Run `docker compose version`; require Docker Compose v2 and never use `docker-compose`.

If any required project file is missing, list the missing files and stop without editing. Do not rewrite the Dockerfile template to avoid the requirement.

## Collect Configuration

Use Codex interactive questions (`request_user_input`) when the tool is available. Do not collect values with terminal prompts such as `read`, `select`, `inquirer`, Node prompts, or stdin.

Collect:

- `service_name`: used both as the Compose service key and `container_name`.
- `server_port`: default option `3000`, with support for a custom value.
- `use_node_24_alpine`: default/recommended option `24-alpine`.
- `node_version`: only when the user does not choose `24-alpine`.

If `request_user_input` is not available in the current Codex mode, ask concise chat questions before editing and continue only after the user provides the values.

Normalize `node:<tag>` to `<tag>` because the Dockerfile already uses `FROM node:${NODE_VERSION:-...}`.

## Validate Values

Validate `service_name`:

- not empty;
- no spaces;
- starts with a letter or number;
- contains only letters, numbers, dots, underscores, and hyphens.

Check container name conflicts with:

```bash
docker ps -a --format "{{.Names}}"
```

If a container already uses the requested name, warn the user. Do not delete or modify the existing container.

Validate `SERVER_PORT`:

- numeric;
- between `1` and `65535`;
- preferably `1024` or higher.

Use the same `SERVER_PORT` and `NODE_VERSION` in `.docker/Dockerfile.main`, `docker-compose.yaml`, `.env`, and `.env.example`.

## Write Files

Create `.docker/` if needed. Do not create other directories.

Update files in this order:

1. `.docker/Dockerfile.main`
2. `.dockerignore`
3. `docker-compose.yaml`
4. `.env`
5. `.env.example`

Keep in-memory or temporary copies of previous contents. Do not create permanent backup files such as `.env.bak`, `Dockerfile.main.old`, or `docker-compose.yaml.backup`.

For `.env`, preserve comments, blank lines, other variables, and secrets. Update only `NODE_VERSION` and `SERVER_PORT`, adding them to the end if absent.

For `.env.example`, preserve comments and other variables. Update only `NODE_VERSION` and `SERVER_PORT`, adding them to the end if absent. Never copy secrets from `.env`.

## Validate Output

Confirm:

- service name and `container_name` are identical;
- `SERVER_PORT` is consistent across all four configured files;
- `NODE_VERSION` is consistent across all four configured files;
- Compose points to `.docker/Dockerfile.main`;
- all five generated files exist.

Then run:

```bash
docker compose -f docker-compose.yaml config
```

If validation fails, restore previous file contents, summarize the error, and report that the configuration was not applied.

## Final Report

Report:

- framework: Next.js;
- package manager: Yarn;
- service/container name;
- Node version;
- `SERVER_PORT`;
- Docker CLI, Engine, and Compose availability;
- each created/updated file;
- `docker compose config` result;
- that no image was built and no container was started.

Suggest only these manual commands:

```bash
docker compose -f docker-compose.yaml build
docker compose -f docker-compose.yaml up -d
docker compose -f docker-compose.yaml logs -f
docker compose -f docker-compose.yaml ps
docker compose -f docker-compose.yaml down
```
