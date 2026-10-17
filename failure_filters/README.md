Failure filters
===============

`python -m xemutest --failure-filter <file>` reads `expected_failures`:
`{test class: {test name in results.json: [event, ...]}}`, where an event is
`{"level": "fail", "message": ...}` or a scoped group
`{"scope": ..., "events": [...]}`. Only the nxdk low-level suites use it.
Matching is exact on the test name and the message (`scope::message` for a
scoped event); other keys (`file`, `line`, `t_ms`, `fields`) are ignored. A
listed event that fails is suppressed (a warning), one that passes fails the
run (filter mismatch), and one that doesn't occur is ignored. Any failure
that isn't listed fails the run.

xemu-macos
----------
The `xemu-macos` branch of MichaelJSr/xemu-test, which the Roswell job in
MichaelJSr/xemu-macos's CI checks out, adds
`TestNxdkLowLevelCPUTests__TestfloatTestXiso` to `macos_roswell.json`: the
109 failure events that fork's macOS build produces (TestFloat F32 23,
F64 23, ExtF80 63) and nothing else. xemu-macos's arm64 hard FPU computes x87
in double precision and raises no x87 exception flags; TestFloat expects
80-bit results and flags, so every function hits TestFloat's 20-error cap
(`<function> (...): 20 error(s)`). The suite passes with `--no-hard-fpu`.
The entries copy those events verbatim, so regenerate them from a macOS
`results.json` when the TestFloat ISO (`CPU_TAG` in
`scripts/fetch_test_requirements.py`) changes.
