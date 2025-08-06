FROM python:3.13-alpine

WORKDIR /app

#make uv available
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/



# Install dependencies via cache mounts that disappear after uv sync is run
# this is nice for speeding up the image creation because project may change frequently but the dependencies dont so often
# so when using uv sync and cache you end up speeding up creation of the image till the project part
#remember to mount using bind type for uv.lock and pyproject.toml
RUN --mount=type=cache,target=/root/.cache/uv \
  --mount=type=bind,source=uv.lock,target=uv.lock \
  --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
  uv sync --locked --no-install-project --no-editable

#add user to the image
#adduser and addgroup because its sh shell
RUN addgroup -S -g 1000 app && adduser -S -u 1000 -G app app

# copy files to the image
COPY . /app

# Sync the project from local cache mount
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --locked

EXPOSE 22112

#Change to user app instead of root
USER app

#execute backend
CMD ["/app/.venv/bin/cd-playground-backend"]
