# P3B restack evidence

The historical report is preserved on the original branch. The source ledger pins its exported bytes, including the original build receipts, raw-to-export provenance and failed runs. Historical guest counts do not qualify this build.

This restack starts at upstream #49 `13ab52b`. The original #50 is
`45c5c2b`; its second parent `dc773cf` defined the old transport delta.
The original P3B source parent is `687b6b1` and the P3B evidence commit is
`928f37c`. The two Issue #37 files in the old transport delta were excluded.
The Notes Range and protection implementation, the Qt build helpers, and the
native watchdog join were already in the new base. Normalizing the three
type names in `dd8cc87` gives the exact current pipe header. The existing
single-source page decoder body now lives in the shared adapter; both clients
use it. The Browser lock and frozen Aurora tree are unchanged.

Current QEMU used the recorded #49 system image and freshly compiled
payloads. The independent baseline verified 34 pages / 267 Browser nodes and
4,114 Notes UTF-16 units. Both default denials passed, and each one-sided
refusal preserved the other source. The independent isolation check verified
four foreign and four stale token denials. After Notes exited, Browser still
provided all 267 nodes. These runs restored the resource baseline. The
ordinary run observed both sources without Oracle diagnostics, and a
short same-boot repeat passed 3/3. The historic 48-session gate was not rerun:
this restack is **PARTIAL**. The historic exit race is **UNRESOLVED**.

The runtime receipt ties the provider and supervisor ELF hashes to the
guest results. The local build used the existing Qt SDK; the system SDK's
seven material checks passed, but the Qt SDK manifest lacks the required
`licenses` inventory for exact-copy audit. Member-level review and release
qualification remain open. No kernel or public ABI change is included.

The shared decoder also passed the single-source A/B QEMU regression:
two 34-page, 267-node baselines, independent structure and Diff checks,
and resource restoration. Two prior invocations selected the recovery
runner with the simple Oracle validator, which expected two captures
instead of the five emitted by the interrupted recovery; those attempts
did not qualify. The final run used the A/B runner and matching verifier.
