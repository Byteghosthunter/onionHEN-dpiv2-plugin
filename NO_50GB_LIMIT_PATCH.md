# No-50GB WebUI limit patch

This fork removes the artificial 50 GiB file-selection cap from the DPIv2 WebUI.

## What was changed

- Removed `MAX_FILE_SIZE = 50 * 1024 * 1024 * 1024` from `webui/src/App.jsx`.
- File selection now accepts any non-empty `.pkg` file.
- Removed the 50 GB wording from all WebUI translations.
- Updated the GitHub Actions build to run `npm ci` and `npm run build` before the PS5 ELF build, ensuring `webui/dist/index.html` is fresh and embedded in the ELF.

## Server-side audit

No 50 GB cap exists in `third_party/pkgserver/pkg_server.c`. The transfer path already uses 64-bit sizes (`uint64_t`, `strtoull`) for `Content-Length`, package size, offsets and totals. Chunked ranged uploads use 128 MiB browser blocks and server-side offsets are passed to `lseek` as `off_t`.

## Important

Removing the browser cap does not create free storage. Uploads are staged under `/user/data/OnionHEN/pkgs`, so the console needs enough free staging space for the complete PKG before final installation.
