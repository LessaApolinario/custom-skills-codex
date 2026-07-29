# Docker Templates

Use these templates exactly. Replace only the placeholders named in each section.

## `.docker/Dockerfile.main`

Replace:

- `<SERVER_PORT>`
- `<NODE_VERSION>`

Do not change any other line, instruction, stage, command, or structural spacing.

```dockerfile
# syntax=docker/dockerfile:1

ARG SERVER_PORT=<SERVER_PORT>
ARG NODE_VERSION=<NODE_VERSION>

FROM node:${NODE_VERSION:-24-alpine} AS base

WORKDIR /usr/src/app

FROM base AS deps

RUN --mount=type=bind,source=package.json,target=package.json \
    --mount=type=bind,source=yarn.lock,target=yarn.lock \
    --mount=type=cache,target=/root/.yarn \
    yarn install --production --frozen-lockfile

FROM deps AS build

RUN --mount=type=bind,source=package.json,target=package.json \
    --mount=type=bind,source=yarn.lock,target=yarn.lock \
    --mount=type=cache,target=/root/.yarn \
    yarn install --frozen-lockfile

COPY . .

RUN yarn run build

FROM base AS final

USER node

COPY package.json .
COPY public ./public
COPY next.config.ts .

COPY --from=deps /usr/src/app/node_modules ./node_modules
COPY --from=build /usr/src/app/.next ./.next

EXPOSE ${SERVER_PORT}

CMD ["sh", "-c", "yarn start -p ${SERVER_PORT}"]
```

## `.dockerignore`

Use exactly:

```dockerignore
# Include any files or directories that you don't want to be copied to your
# container here (e.g., local build artifacts, temporary files, etc.).
#
# For more help, visit the .dockerignore file reference guide at
# https://docs.docker.com/go/build-context-dockerignore/

**/.classpath
**/.dockerignore
**/.env
**/.git
**/.gitignore
**/.project
**/.settings
**/.toolstarget
**/.vs
**/.vscode
**/.next
**/.cache
**/*.*proj.user
**/*.dbmdl
**/*.jfm
**/charts
**/docker-compose*
**/compose.y*ml
**/Dockerfile*
**/node_modules
**/npm-debug.log
**/obj
**/secrets.dev.yaml
**/values.dev.yaml
**/build
**/dist
LICENSE
README.md
```

## `docker-compose.yaml`

Replace:

- both `<SERVICE_NAME>` occurrences with the same value;
- both `<NODE_VERSION>` occurrences with the selected Node image tag;
- all `<SERVER_PORT>` occurrences with the selected port.

Do not add other properties.

```yaml
services:
  <SERVICE_NAME>:
    build:
      context: .
      dockerfile: .docker/Dockerfile.main
      args:
        NODE_VERSION: ${NODE_VERSION:-<NODE_VERSION>}
        SERVER_PORT: ${SERVER_PORT:-<SERVER_PORT>}
    container_name: <SERVICE_NAME>
    restart: always
    ports:
      - "${SERVER_PORT:-<SERVER_PORT>}:${SERVER_PORT:-<SERVER_PORT>}"
    env_file:
      - .env
```

## `.env` and `.env.example`

Create the file with:

```dotenv
NODE_VERSION=<NODE_VERSION>
SERVER_PORT=<SERVER_PORT>
```

If the file exists, preserve unrelated lines and update only those two keys.
