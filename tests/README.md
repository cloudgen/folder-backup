# Tests — folder-backup

## Run

```sh
./tests/run.sh
# or
sh tests/run.sh
```

Exit **0** when all assertions pass; **1** on failure; **2** if ship unit missing.

## Layout

| File | Focus | TP families |
|------|--------|-------------|
| `run.sh` | Entrypoint | — |
| `helpers.sh` | Asserts + isolated HOME | — |
| `test_cli.sh` | CLI surface, case 2 empty argv, numbered-list look, help grant-emit heading split, offline online-reject | **TP-CLI-*** (incl. **TP-CLI-17** heading · **TP-CLI-18** look) |
| `test_local_lifecycle.sh` | install / uninstall / where-is-me | **TP-LC-*** |
| `test_domain_folder_backup.sh` | backup ops + domain surface + sudoers print + JSON grant + submit inbound detect | **TP-FOLDER-BACKUP-*** (ops → `requirement-folder-archive-backup`; grant → `requirement-sudoer-json-file`; submit → three-layer §2.3.3c) |

## Isolation

- Temp `HOME` + `USER_BIN` for CLI and install tests (`ci_isolated_env` at the start of the CLI suite)  
- **No** public network  
- **No** write to `/etc/sudoers.d` (suite never installs sudoers)  
- **No** enqueue into live `/var/sudoer-cli/sudoer-request` (isolated `SUDOER_CLI` + temp queue trio; runner snapshot). If that inbound dir is **absent**, the fence records SKIP rather than a proof.  
- Deposit **fail-closed** forced with a PATH-local fake `sudo` (stays valid when host sudoers is installed)  
- Deposit **success** when root **or** allowlisted `sudo -n mkdir -p /var/backup/folder-backup` works (admin privilege escalate); otherwise SKIP 07/08 — those cases write live `/var/backup/folder-backup`  

PASS/FAIL/SKIP counts are **per assertion**, not per TP-ID. TTY menu cases (TP-CLI-13/14/16/18) SKIP without `python3`. Compact JSON `--json` twins remain **todo** (**TP-FOLDER-BACKUP-27**).

## Ship unit under test

`src/folder-backup`

## Maps

Product TP map: `reviews/test-plan.md`  
RTM: `reviews/requirement-test-matrix.md`
