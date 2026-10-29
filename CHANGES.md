- **Fix: the header did not say "Release 1.0.0" in a launcher running the released build.** The launcher asks the
  stable channel's `RELEASES.md` which release its own build is, and the reader of that table only accepted builds
  written as `major.build` (`1.63`), not as `major.minor.patch` (`0.0.86`), so it never found the row. Builds with
  three numbers are read now (old two-number ones too), and the tests use the real table of the first release.
  **A launcher that already runs 0.0.86 cannot learn this by itself** (the fault is inside that exe): it keeps saying
  "Ver. 0.0.86" until it is replaced by a build with this fix; the next release will show "Release X.Y.Z" as intended.
- **The GitHub Release is titled "DCS World Launcher X.Y.Z"** (it was "Release X.Y.Z"). GitHub puts the word
  "Release" in front of the title in link previews (Discord...), which showed "Release Release 1.0.0"; now it reads
  "Release DCS World Launcher 1.0.0". The release workflow sets it when it creates or republishes a release; an
  existing release (1.0.0) can be renamed by hand on GitHub (pencil icon) or is renamed the next time it is republished.
