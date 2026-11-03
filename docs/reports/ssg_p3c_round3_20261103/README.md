# P3C restack evidence

The historical report is preserved on the original branch. The source ledger pins its exported bytes, including the original build receipts, raw-to-export provenance and failed runs. Historical guest counts do not qualify this build.

The new Watch supervisor, diagnostic supervisor and contract fixture were cross-compiled against the existing Qt SDK. QEMU reused the #49 round-2 system image and the freshly rebuilt P3B diagnostic Browser and Notes. Real Notes edits, Browser scrolling, Notes revocation and cancellation during an in-flight Notes read each passed their independent scoped verifier. All four restored the active-display resource baseline; cancellation discarded 1,023 staged UTF-16 units. The independent contract probe passed 32/32.

The four dynamic cases ran on diagnostic fixtures. The historical 24-session stress gate was not rerun, so this restack is **PARTIAL**. The Watch transport is a bounded stdout event stream. `globalAtomicSnapshot=NOT_IMPLEMENTED`; the historical single-source exit race remains `UNRESOLVED`.

Before these runs, initial cross-compiles caught stale SDK names, and guest launch attempts failed before boot due to missing receipt arguments/files and a race compiling the shared host disk helper. The source names were aligned, the receipt was supplied after build completion, and the host helper was prepared before concurrent guest launches. Those attempts are not included in the pass counts.
