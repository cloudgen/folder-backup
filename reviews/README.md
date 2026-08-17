# Reviews — folder-backup

Public product review surface (peer of `tests/`).

| File | Role |
|------|------|
| `what-to-review.md` | Living review plan / checklist |
| `test-plan.md` | TP-* status map |
| `requirement-test-matrix.md` | Requirement → TP families |
| `lessons.md` | Durable failure modes to re-check |
| `index.md` | Report index |
| `reports/` | Dated review run reports |

**Ship unit:** `src/folder-backup` (**VERSION 1.8.2**)  
**Suite:** `./tests/run.sh`  
**Last suite baseline:** see `test-plan.md` and `reports/` (2026-08-15 public inbound)  

**Privilege review focus (1.8.2):** trust tier **S13**, project-sudoers-file, `submit-sudoer-request` public inbound, **host-probe add/update (AC-22)**, inbound fidelity (AC-21), `print-sudoers-install-script`, `remove-project-sudoers` (draft only).
