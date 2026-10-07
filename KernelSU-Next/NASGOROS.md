# KernelSU-Next (legacy) + SUSFS in the nasgorOS kernel

- Upstream: https://github.com/KernelSU-Next/KernelSU-Next, branch `legacy`
- Imported: commit `8869bd768d66` (`v3.4.0-legacy-12`, 3017 commits), reported
  version 33306 = 30000 + 3017 + 289. Matching manager: KernelSU Next v3.4.0 (33294).
- Layout follows upstream `kernel/setup.sh`: this directory holds `kernel/`,
  `uapi/` and `LICENSE`; `drivers/kernelsu` is a symlink to `../KernelSU-Next/kernel`.
- Hook mode: `CONFIG_KSU_MANUAL_HOOK` (`arch/arm64/configs/vendor/kernelsu.config`).
  Calls under `#ifdef CONFIG_KSU_MANUAL_HOOK` in `fs/exec.c` (`__do_execve_file`),
  `fs/open.c` (`do_faccessat`), `fs/read_write.c` (`vfs_read`), `fs/stat.c`
  (`newfstatat`, `fstatat64`), `drivers/input/input.c` (`input_handle_event`) and
  `kernel/reboot.c` (`reboot`). The syscall-table mode was dropped: it never hooks
  `reboot()`, so the manager could not reach the kernel and showed "not installed".

## Local changes to upstream

1. Top of `kernel/Kbuild`: `KSU_VERSION_OVERRIDE` / `KSU_VERSION_TAG_OVERRIDE`,
   because this copy is not its own git repository.
2. `kernel/Kbuild` + `kernel/hook/patch_memory.c`: use `copy_to_kernel_nofault()`
   when the tree has it (only built in syscall-table mode).
3. SUSFS glue (upstream dropped SUSFS), taken from wahyu6070/surya-kernel
   (`gaming`, 732f46f8d), which follows sidex15/KernelSU-Next `legacy-susfs-v2`:
   `Kconfig` (SUSFS menu), `Kbuild` (version info), `core/init.c`,
   `feature/kernel_umount.c`, `hook/setuid_hook.c`, `selinux/rules.c`,
   `selinux/selinux.c`, `selinux/selinux.h`, `supercall/dispatch.c`,
   `supercall/supercall.c`.

## SUSFS

- Version v2.3.0: `fs/susfs.c`, `include/linux/susfs.h`, `include/linux/susfs_def.h`
  plus hooks in `fs/`, `mm/memory.c`, `kernel/`, `security/selinux/avc.c`.
- Source: the 4.19 port in wodanesdag/android_kernel_xiaomi_mt6877 (branch `susfs`,
  commits 9a8e25e0..9db30682), itself following sidex15's 4.14 port. Its two VFS
  reverts were not taken; the OPEN_REDIRECT readlink hook sits in the 4.19
  `vfs_readlink()` instead.
- sus_memfd is off: it needs `mm/shmem.c` hooks this port does not carry.
- Userspace: https://github.com/sidex15/susfs4ksu-module (KernelSU module).

## Updating

Replace `kernel/` and `uapi/` with the new upstream revision, re-apply the local
changes above (the SUSFS glue usually needs a 3-way merge), then update the
commit, version and tag here and at the top of `kernel/Kbuild`.
