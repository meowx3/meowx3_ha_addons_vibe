# Home Assistant app development rules

These rules apply to every Home Assistant app in this repository. Follow the
Home Assistant app documentation and the repository's CI workflows when they
conflict with assumptions below.

## Repository structure

- Treat each top-level app directory as an independently discoverable app. Keep
  app-specific files inside that directory.
- Use the example app as the structural template: `config.yaml`, `Dockerfile`,
  optional `apparmor.txt`, `DOCS.md`, `README.md`, `CHANGELOG.md`,
  `translations/`, and `rootfs/`.
- The directory name, `config.yaml` `slug`, and service directory name must
  agree. For a slug named `my_app`, services belong under
  `rootfs/etc/services.d/my_app/`.

## App metadata and configuration

- Keep `config.yaml` valid and complete. At minimum, review `name`, `version`,
  `slug`, `description`, `arch`, `init`, and `image` for every app.
- Declare every user-configurable option in both `options` and `schema`. Keep
  the corresponding UI label and description in `translations/en.yaml`.
- Only list architectures that the Dockerfile and runtime support. In Docker
  build logic, map Docker `TARGETARCH=arm64` to the Home Assistant
  architecture name `aarch64`, and fail explicitly for unsupported targets.
- Preserve the `map` declaration when an app needs persistent Home Assistant
  storage; use the mapped path at runtime (the example uses `/share`).
- Keep app URLs, image names, labels, and repository metadata pointing to the
  actual fork or project rather than the example repository.
- When forking this template, update the hard-coded repository condition in
  `.github/workflows/build-app.yaml` and change each `image` value to the
  intended registry path. For local Supervisor builds, temporarily comment out
  `image`; restore it before pushing a release.

## Runtime and image rules

- Base images must use the `BUILD_FROM` argument, and builds must fail when
  required build arguments such as `TARGETARCH` are missing.
- Copy the app filesystem with `COPY rootfs /`. Keep executable service and
  program files executable and use a valid shebang.
- Run long-lived work as an s6 service under
  `rootfs/etc/services.d/<slug>/run`; use `exec` for the supervised process.
  Add a `finish` script when non-zero exits must halt the app.
- Use Home Assistant `bashio` helpers for configuration access and logging.
  Configuration keys must match the keys in `config.yaml`.
- Keep Docker labels and published metadata consistent with `config.yaml`.
  The build workflow supplies the `io.hass.*` labels, so do not duplicate or
  contradict those labels in the Dockerfile.

## CI and validation

- Every app must pass `frenck/action-addon-linter@v2.21`; the lint workflow
  runs it for every app on pull requests, pushes to `main`, and its schedule.
- The builder workflow discovers app directories automatically. A build is
  triggered when an app's `config.json`, `config.yaml`, `config.yml`,
  `Dockerfile`, or `rootfs` changes, or when the builder/build-app workflow
  changes.
- Pull requests build without publishing. Pushes to `main` publish the
  versioned and `latest` multi-architecture images to the configured registry.
- Before proposing changes, run the narrowest available app lint and build
  check. For changes to monitored files, verify the affected app; for shared
  workflow changes, consider every app affected.
- Do not remove CI checks or weaken workflow permissions and conditions without
  explaining the compatibility and security impact.

## Releases and documentation

- When merging a release to `main`, update the app `version` and add a matching
  entry to its `CHANGELOG.md`.
- Keep `DOCS.md` focused on user-visible configuration, behavior, storage, and
  troubleshooting. Update it when configuration or runtime behavior changes.
- Keep README and repository metadata accurate when adding, renaming, or
  removing an app.

## AI and contribution policy

- Follow `AI_POLICY.md`. A human contributor must review and understand every
  generated change before submission and be able to explain it.
- Do not use an autonomous agent to submit pull requests, issues, or maintainer
  responses. Do not post unreviewed generated content.
- Keep changes focused, preserve existing user work, and do not commit changes
  unless explicitly requested.