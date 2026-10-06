# KernelSU-Next (legacy) in the nasgorOS kernel

- Upstream: https://github.com/KernelSU-Next/KernelSU-Next, branch `legacy`
- Imported: tag `v3.4.0-legacy`, commit `2d99a2da126f4df6d607d8917e244fd533fc61bf`
  (3016 commits, reported version 33305 = 30000 + 3016 + 289)
- Layout follows upstream `kernel/setup.sh`: this directory holds `kernel/`,
  `uapi/` and `LICENSE`; `drivers/kernelsu` is a symlink to `../KernelSU-Next/kernel`.
- One local change: the top of `kernel/Kbuild` sets `KSU_VERSION_OVERRIDE` and
  `KSU_VERSION_TAG_OVERRIDE`, because this copy is not a separate git repository.
- Hook mode: `CONFIG_KSU_SYSCALL_TABLE_HOOK` (non-GKI 4.19; upstream advises
  against kprobes below 5.10). Enabled in `arch/arm64/configs/vendor/kernelsu.config`.

Update: replace `kernel/` and `uapi/` with the new upstream revision, then
update the commit, tag and version here and re-add the two lines at the top of
`kernel/Kbuild`.
