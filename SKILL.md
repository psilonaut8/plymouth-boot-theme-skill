---
name: plymouth-boot-theme
description: Create, preview, safely activate, and roll back original Plymouth boot themes on Ubuntu or other Linux systems using Plymouth.
---

# Plymouth Boot Theme

Use this skill for a custom Plymouth boot splash, from visual design through safe activation. Preserve the user's existing boot chain: do not install a separate boot manager or replace Plymouth unless explicitly asked.

## Start with the real boot setup

Audit before changing anything. Identify the current Plymouth alternative and theme path, the installed theme and plugin directories, the active kernel command line, the source that supplies `quiet` and `splash`, the initramfs generator in use, and every installed kernel image. Also determine whether the current environment can gain root privileges and whether it is a real host or a restricted container.

Do not change GRUB merely because a theme is being added. Keep `quiet splash` if it is already present and Plymouth is expected to run. Do not remove stock themes.

## Design and implementation

Translate the requested visual direction into an original composition. Do not copy protected logos, characters, insignia, or artwork. Favor native Plymouth capabilities: the script plugin, Pango-rendered text, sprites, and small local PNG assets. Keep the display readable at the requested resolution and use restrained motion; account for quick boots where only the first part of an animation may appear.

For themes that rely on text, use a font known to be present in the target initramfs or declare/copy it through the platform's supported initramfs mechanism. Include usable password and question callbacks in script themes so encrypted-volume and recovery prompts remain visible.

Treat a theme as an installable directory containing at least:

- `<theme>.plymouth`, with its module, asset directory, and script path.
- `<theme>.script` when using the script module.
- Only the local assets the theme needs.

Use the system's real theme directory, usually `/usr/share/plymouth/themes/<theme>`. Register a custom theme with the platform's supported selection mechanism; on Debian and Ubuntu this is commonly the `default.plymouth` alternatives group. Do not assume `plymouth-set-default-theme` exists.

## Preview and validate

Preview through Plymouth when a usable graphical test session is available. When it is unavailable, produce and visually inspect a 1920×1080 design proof, make that limitation explicit, and validate theme syntax, script references, module availability, image integrity, and archive contents before installation.

Read the actual initramfs generator implementation before rebuilding. `update-initramfs` may delegate to dracut, whose inclusion behavior differs from the classic initramfs-tools hook. Verify the selected theme's script, assets, and required plugin occur in every rebuilt initramfs image with the appropriate image-listing tool.

## Activation and rollback

Before the first modification, save a timestamped rollback set under a root-owned backup directory. Capture:

- Current theme-alternative metadata, link, and selection mode.
- Plymouth configuration and any boot configuration that will actually be edited.
- Exact copies of all existing initramfs images that will be replaced.

Use an install transaction that refuses to overwrite an existing custom theme, stops on errors, and restores the previous selection and initramfs images if a later step fails. Rebuild every installed kernel image, then verify the active alternative and the rebuilt images. Never reboot automatically.

If privilege escalation is unavailable, finish the source, preview, validation, and a root-run installer plus rollback script. Do not claim activation or initramfs verification occurred. Give the user the exact command to run from a root-capable terminal and explain what it will back up and verify.

At handoff, report the created and modified files, backup location, active theme, exact rollback command, whether GRUB changed, verification results, and whether rebooting is safe.
