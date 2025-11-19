**Status**
- `cargo tree --workspace --target=all --locked -i egui@0.31.0` currently fails because the workspace still requests `eframe = "^0.31.1"` and Cargo needs to reach crates.io to confirm that semver requirement. Without network it errors out before printing a tree, even though `Cargo.toml` pins every `egui` crate to your patched git commit `e05638c40ef7343…` (`notedeck/Cargo.toml:110-115`) and the lockfile confirms that the same git commit is used for all `egui` components and `emath 0.31.1` (`notedeck/Cargo.lock:1423-1578`).

**Plan**
- Drop the bandaid workaround commit `2e78dbf` from the patched `egui` repo so we can reproduce the true behavior.
- Reproduce the Android auto-shutdown (premature `EventResult::Exit`) without the bandaid and capture logs around the event loop/exit paths.
- Run `git bisect` across the patched repos (`egui`, `android-activity`, etc.) to pinpoint the exact patch that introduced the regression; bisect each repo’s custom patch stack as needed.
- Diff the culprit patch (and any related files) against upstream/earlier versions to understand what changed and why it causes the early exit, then prepare the fix based on that analysis.
