# CLI routed-verb table — folder-backup

**Product:** folder-backup  
**Ship unit:** `src/folder-backup`  
**Dispatcher:** `app_main`  
**Scan date:** 2026-08-23  
**Mode:** full (label + purpose refresh)  
**Copied / re-checked:** 13 live copied · 2 re-checked (`menu`/`main` now routed) · 1 not-yet-wired  

Inventory from dispatcher case, not help. Human-readable is `{{short-descript}}: {{explain}}` (short-descript = routed-verb).

## Live

| verb | handler | privilege | last modified date | purpose | human-readable |
|------|---------|-----------|--------------------|---------|----------------|
| version | `app_version` | you | missing | diagnostics | version: Show the local version |
| about | `app_about` | you | 2026-08-03 | diagnostics | about: Show diagnostics including sudoers trust tier |
| help | `app_help` | you | missing | diagnostics | help: Show this help |
| install | `inst_local_install` | you | 2026-08-09 | self-managed | install: Copy this program into your bin or /usr/local/bin |
| uninstall | `inst_local_uninstall` | you | 2026-08-03 | self-managed | uninstall: Remove the managed binary (not the host grant) |
| where-is-me | `app_where_is_me` | you | 2026-08-03 | self-managed | where-is-me: Show running and install paths |
| backup | `fb_backup` | you (deposit needs change-the-computer after admin grant) | 2026-08-12 | operational | backup: Pack a named folder into a dated gzip archive under /var/backup/folder-backup |
| restore | `fb_restore` | you (stage fetch may need change-the-computer) | 2026-08-03 | operational | restore: Put an archive back onto the hard-disk projects tree |
| print-sudoers | `fb_print_sudoers` | you | 2026-08-14 | test-purpose | print-sudoers: Write a grant file an admin can install |
| print-sudoers-install-script | `fb_print_sudoers_install_script` | you | 2026-08-09 | test-purpose | print-sudoers-install-script: Write an admin script to install or remove the grant |
| remove-project-sudoers | `fb_remove_project_sudoers` | you | 2026-08-09 | operational | remove-project-sudoers: Remove the local grant draft only |
| generate-sudoer-request | `fb_generate_sudoer_request` | you | 2026-08-17 | test-purpose | generate-sudoer-request: Write a local JSON grant you can read without sudo |
| submit-sudoer-request | `fb_submit_sudoer_request` | you | 2026-08-17 | operational | submit-sudoer-request: Hand the JSON grant to the approval queue |
| menu | `app_main_menu` | you | 2026-08-23 | operational | menu: Show the numbered list of live work commands |
| main | `app_main_menu` | you | 2026-08-23 | operational | main: Same numbered list as menu |

## Not-yet-wired

| verb | handler | privilege | last modified date | purpose | human-readable | status |
|------|---------|-----------|--------------------|---------|----------------|--------|
| setup | — | — | — | — | — | forbidden (not this CLI; sibling inbound setup) |

Do not list `menu` / `main` as choices on their own menu.

**Main menu** uses only **operational** rows other than `menu`/`main`: backup, restore, remove-project-sudoers, submit-sudoer-request. Self-managed, diagnostics, and test-purpose stay off the numbered list.

## Honesty

Dispatcher tokens on 2026-08-23: version, about, help, install, uninstall, where-is-me, backup, restore, print-sudoers, print-sudoers-install-script, remove-project-sudoers, generate-sudoer-request, submit-sudoer-request, menu, main. Online `self-*` / `version-check` are absent by design. This product classifies `print-sudoers`, `print-sudoers-install-script`, and `generate-sudoer-request` as **test-purpose**.
