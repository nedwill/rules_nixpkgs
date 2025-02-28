# Change Log

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/).

## [Unreleased]

[Unreleased]: https://github.com/tweag/rules_nixpkgs/compare/v0.9.0...HEAD

### Added
- nixpkgs_nodejs_configure_platforms for platform transparent npm_install
  See [#309]

## [0.9.0] - 2022-07-19

[0.9.0]: https://github.com/tweag/rules_nixpkgs/compare/v0.8.0...v0.9.0

### Added

- nixpkgs_cc_configure gained a flag to disable automatic registration of the
  toolchain it creates.
  See [#179]
- Support registering a Java toolchain for Bazel 5
  See [#185]
- Rust toolchain and example
  See [#178]
- python-container example
  See [#226]
- Allow to specify platform constraints in `nixpkgs_cc_configure`
  See [#164]
- add `fail_not_supported to `nixpkgs_go_configure`
  See [#167]
- add exec/target constraints parameters to python toolchain macro
  See [#169]
- Support codesigning on MacOs
  See [#224]

[#179]: https://github.com/tweag/rules_nixpkgs/pull/179
[#185]: https://github.com/tweag/rules_nixpkgs/pull/185
[#178]: https://github.com/tweag/rules_nixpkgs/pull/178
[#226]: https://github.com/tweag/rules_nixpkgs/pull/226
[#164]: https://github.com/tweag/rules_nixpkgs/pull/164
[#167]: https://github.com/tweag/rules_nixpkgs/pull/167
[#169]: https://github.com/tweag/rules_nixpkgs/pull/169
[#224]: https://github.com/tweag/rules_nixpkgs/pull/224

### Changed

- Split into separate components in preparation for [bzlmod](https://docs.bazel.build/versions/5.2.0/bzlmod.html)
  See [#182]
- Detect whether compiler is Clang at nix eval time
  See [#216]

  *Note*:  If you have previously passed a custom `cc` toolchain to [`nixpkgs_cc_configure`](https://github.com/tweag/rules_nixpkgs#nixpkgs_cc_configure) you need to provide a boolean `isClang` attribute now, e.g.:
    ```nix
    pkgs.buildEnv {
          name = "bazel-nixpkgs-cc";
          paths = [ cumstom-cc ];
          pathsToLink = [ "/bin" ];
          passthru = {
            isClang = custom-cc.isClang;
          };
        }
    ```

[#182]: https://github.com/tweag/rules_nixpkgs/pull/182
[#216]: https://github.com/tweag/rules_nixpkgs/pull/216

### Fixed

- Avoid error when include dirs list is empty
  See [#234]
- Ensure nixpkgs will work with Bazel build from source
  See [#231]
- Fix call to `repository_ctx.file` using `nix_file_content` with `nixpkgs_local_repository`
  See [#210]
- Fix python example build on macOS
  See [#189]
- `nixpkgs_local_repository` strips executable bit of nix files
  See [#160]

[#234]: https://github.com/tweag/rules_nixpkgs/pull/234
[#231]: https://github.com/tweag/rules_nixpkgs/pull/231
[#210]: https://github.com/tweag/rules_nixpkgs/pull/210
[#189]: https://github.com/tweag/rules_nixpkgs/pull/189
[#160]: https://github.com/tweag/rules_nixpkgs/pull/160

## [0.8.0] - 2021-02-11

[0.8.0]: https://github.com/tweag/rules_nixpkgs/compare/v0.7.0...v0.8.0

### Added

- Bazel Skylib is now included as a dependency on `rules_nixpkgs_dependencies`.
  See [#125][#125].
- `nixpkgs_python_configure` now exposes the `quiet` attribute for the
  underlying `nispkgs_package`.
  See [#131][#131].
- The default `BUILD` file of `nixpkgs_package` now finds more kinds of C/C++
  header files in the `include` target.
  See [#137][#137].

### Changed

- The implementation of `nixpkgs_cc_configure` has been replaced by a more
  hermetic version that no longer uses Bazel's builtin autodetection toolchain
  under the hood. The previous behavior is now available under the name
  `nixpkgs_cc_configure_deprecated`, if required.
  See [#128][#128].
- The values in the `nixopts` attribute to `nixpkgs_package` are now subject to
  location expansion. Any instance of `$(location LABEL)` in the `nixopts`
  attribute will be expanded to the file path of the file referenced by
  `LABEL`. To pass a plain `$` to Nix it must be escaped as `$$`.
  See [#132][#132].

### Deprecated

- The old implementation of `nixpkgs_cc_configure`, now available under the
  name `nixpkgs_cc_configure_deprecated`, has been marked as deprecated in
  favor of `nixpkgs_cc_configure` and will be replaced by it in future.
  See [#128][#128].

### Fixed

- Incorrect label contruction for Nix file dependencies.
  See [#124][#124].

[#124]: https://github.com/tweag/rules_nixpkgs/pull/124
[#125]: https://github.com/tweag/rules_nixpkgs/pull/125
[#128]: https://github.com/tweag/rules_nixpkgs/pull/128
[#131]: https://github.com/tweag/rules_nixpkgs/pull/131
[#132]: https://github.com/tweag/rules_nixpkgs/pull/132

## [0.7.0] - 2020-04-20

[0.7.0]: https://github.com/tweag/rules_nixpkgs/compare/v0.6.0...v0.7.0

### Added

- Define `rules_nixpkgs_dependencies` in `//nixpkgs:repositories.bzl`.
- Define `nixpkgs_go_configure` in `//nixpkgs:toolchains/go.bzl`
- `nixpkgs_package` now has a `quiet` attribute.

### Changed

- The constraint value for targets to detect whether Nix is available
  on the current platform is now called
  `@//nixpkgs/constraints:support_nix`. The associated constraint
  setting is `@//nixpkgs/constraints:nix`. The old constraint
  `@//nixpkgs/constraints:nixpkgs` constraint setting is still
  available. But it is highly recommended to migrate to the new
  constraint setting, and update platform definitions accordingly.
  This is a breaking change for users of `nixpkgs_python_configure`.
- Show Nix output by default, like in releases prior to v0.6.

### Fixed

- `nixpkgs_local_repository` now correctly invalidates the cache when
  Nix source files change. See
  [#123](https://github.com/tweag/rules_nixpkgs/issues/113).

## [0.6.0] - 2019-11-14

[0.6.0]: https://github.com/tweag/rules_nixpkgs/compare/v0.5.2...v0.6.0

### Added

- Check `nix_file_deps` and fail on undeclared dependencies (breaking change).
  See [#76][#76] and [#86][#86].
- Define a `nixpkgs` platform constraint. See [#97][#97].
- Define `nixpkgs_python_configure`. See [#97][#97].
- Define `nixpkgs_sh_posix_configure` to generate [`rules_sh`][rules_sh] POSIX
  toolchain. See [#95].

[#76]: https://github.com/tweag/rules_nixpkgs/pull/76
[#86]: https://github.com/tweag/rules_nixpkgs/pull/86
[#97]: https://github.com/tweag/rules_nixpkgs/pull/97
[#95]: https://github.com/tweag/rules_nixpkgs/pull/95
[rules_sh]: https://github.com/tweag/rules_sh

### Changed

- Hide Nix output, following Bazel best practices for a quiet build.
- Disable implicit `nixpkgs` configuration loading (breaking change).
  See [#83](https://github.com/tweag/rules_nixpkgs/pull/83).

### Fixed

- Improve distributed caching by not leaking user specific Bazel cache
  directory, see [#67](https://github.com/tweag/rules_nixpkgs/pull/67).

## [0.5.2] - 2019-01-28

[0.5.2]: https://github.com/tweag/rules_nixpkgs/compare/v0.5.1...v0.5.2

### Added
- `nixpkgs_package` now has a new optional argument `fail_not_supported`
  allowing the rule to _not_ fail on Windows (when set to `False`)
- `nixpkgs_cc_configure` now has a new optional argument `nixopts` which
  propagates extra arguments to the `nix-build` calls.

### Fixed
- The `nixpkgs_package` is now a no-op on non nixpkgs-supported platforms
  instead of throwing an error.

## [0.5.1] - 2018-12-18

[0.5.1]: https://github.com/tweag/rules_nixpkgs/compare/v0.4.1...v0.5.1

### Changed

- `nixpkgs_package` now has a new optional argument `nixopts`
  allowing to pass extra arguments to the `nix-build` calls

### Fixed

- The various `nix_*` rules are now only triggered when one of their dependency
  has changed and not each time the `WORKSPACE` is modified
- The `nixpkgs_cc_configure` macro is now much faster
- `nixpkgs_cc_configure` is now a no-op on non nixpkgs-supported platforms
  instead of throwing an error
- The `lib` filegroup provided in the default `BUILD` file for
  `nixpkgs_package` now also works on MacOS

## [0.4.1] - 2018-11-17

[0.4.1]: https://github.com/tweag/rules_nixpkgs/compare/v0.3.1...v0.4.1

### Added

* `nixpkgs_cc_configure` rule to tell Bazel to configure a toolchain
  from tools found in the given Nixpkgs repository, instead of from
  tools found in the ambient environment.
* `nixpkgs_local_repository` rule. Works like `nixpkgs_git_repository`
  but takes a checked-in Nix file or Nix expression as input.

### Changed

* The `repository` attribute is no longer deprecated. Most rules
  support both `repository` and `repositories` as attributes.

### Fixed

* Short repository labels work again. That is, you can say `repository
  = "@nixpkgs"` as a short form for `repository =
  "@nixpkgs//:default.nix"`.

## [0.3.1] - 2018-10-24

[0.3.1]: https://github.com/tweag/rules_nixpkgs/compare/v0.3.0...v0.3.1

### Fixed

* `repositories` is no longer a required argument to `nixpkgs_package`.

## [0.3] - 2018-10-23

[0.3]: https://github.com/tweag/rules_nixpkgs/compare/v0.2.3...v0.3

### Added

* `nixpkgks_package` now supports referencing arbitrarily named nix
  files. A bug previously only made it possible to reference
  `default.nix` files.

### Removed

* The `path` attribute has been removed. See `Migration` section
  in `README.md` for instructions.

### Changed

* `nixpkgs_packages` does not accept implicit `<nixpkgs>` version. See
   [#25](https://github.com/tweag/rules_nixpkgs/pull/25).

## [0.2.3] - 2018-07-01

### Added

* `sha256` attribute to `nixpkgs_git_repository`.
* Ability to point to a Nixpkgs fork via the new `remote` attribute to
  `nixpkgs_git_repository`.

## [0.2.2] - 2018-04-30

## [0.2.1] - 2018-03-18

## [0.2] - 2018-03-18

## [0.1] - 2018-02-21

## [0.1.1] - 2017-12-27
