# Dead by Daylight — Cheat Engine Process Handle Reference

## What is `0x000012E8` When Attaching Cheat Engine to Dead by Daylight?

When you attach **Cheat Engine** to the Dead by Daylight process and open the handle list
(**View → Open Handles**), you will see one or more open handles to `DeadByDaylight.exe`
with the access mask **`0x000012E8`** (decimal `4840`).

This handle belongs to **EasyAntiCheat (EAC)**, Dead by Daylight's anti-cheat system.
EAC opens the game process with this specific set of access rights in order to monitor it
for unauthorized memory modifications.

---

## Access Rights Breakdown

| Bit flag   | Name                              | Purpose |
|------------|-----------------------------------|---------|
| `0x0008`   | `PROCESS_VM_OPERATION`            | Perform operations on the process address space |
| `0x0020`   | `PROCESS_VM_WRITE`                | Write to the process memory |
| `0x0040`   | `PROCESS_DUP_HANDLE`              | Duplicate handles from/to the process |
| `0x0080`   | `PROCESS_CREATE_PROCESS`          | Use the process as parent for a new process |
| `0x0200`   | `PROCESS_SET_INFORMATION`         | Set process information (e.g. priority) |
| `0x1000`   | `PROCESS_QUERY_LIMITED_INFORMATION` | Query limited process information |

**Combined mask:** `0x0008 | 0x0020 | 0x0040 | 0x0080 | 0x0200 | 0x1000 = 0x000012E8`

---

## Why Does EAC Use This Exact Mask?

EasyAntiCheat intentionally avoids requesting `PROCESS_VM_READ` (`0x0010`) and
`PROCESS_QUERY_INFORMATION` (`0x0400`) in this particular handle.  Using
`PROCESS_QUERY_LIMITED_INFORMATION` instead of the full `PROCESS_QUERY_INFORMATION`
is a common anti-tamper technique: it still allows EAC to read basic process metadata
(image name, exit code, etc.) without exposing the wider query interface.

The write (`PROCESS_VM_WRITE`) and operation (`PROCESS_VM_OPERATION`) rights let EAC
perform integrity checks and, if necessary, terminate or modify the protected process.

---

## Summary

> **`0x000012E8` = EasyAntiCheat's monitoring handle on the Dead by Daylight process.**

If you see a handle to `DeadByDaylight.exe` carrying the access mask `0x000012E8` in
Cheat Engine's handle list, it was opened by the EAC service/driver as part of its
normal runtime protection — it is **not** a handle created by Cheat Engine itself.
