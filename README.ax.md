## Axivion modifications to Bazel

This is a patched version containing fixes for:
- <https://github.com/bazelbuild/bazel/issues/11220>
- <https://github.com/bazelbuild/bazel/pull/15846>
- <https://github.com/bazelbuild/bazel/issues/14848>

Patched Source: <https://github.com/axivion/bazel/tree/axivion-7.0.0>

- Windows Command: `bazel build //src:bazel.exe --compilation_mode=opt --stamp src:bazel --embed_label 7.0.0-ax`
- Unix Command: `bazel build //src:bazel --compilation_mode=opt --stamp src:bazel --embed_label 7.0.0-ax`

**Note:** The Linux build should happen in a Docker image that is compatible with our Jenkins environment.

## Update bazel
- Clone this repo.
- Update the master from upstream using `git pull https://github.com/bazelbuild/bazel`.
- Create a new branch from an upstream release tag (e.g., from 7.0.0).
- Add the various patches we accumulated via `git cherry-pick`. You can find the hashes using
`git log 7.0.0..origin/axivion-7.0.0`.
- Build bazel using bazel (if your environment does not have bazel, you can use [bazelisk](https://github.com/bazelbuild/bazelisk)) for all platforms (i.e., Windows, Linux, MacOs).
- Upload the created bazel binary (can usually be found in `bazel-bin/src/bazel`) to our artifactory registry to the platform-specific paths, e.g., `generic-local/Windows-AMD64/bazel`.
- Update the bazel version in `axivion/dependencies/ax_deps/recipes/bazel/__init__.py`.
- Update compiler toolchains (or: check if its necessary)
  - Find these lines in bazel.rc and comment them out:

    ``` bash
    build --action_env=BAZEL_DO_NOT_DETECT_CPP_TOOLCHAIN=1
    build --host_action_env=BAZEL_DO_NOT_DETECT_CPP_TOOLCHAIN=1
    ```

  - Build until bazel started to do something (you can abort or run until the end)
  - Copy `bazel-bauhaus*/external/local_config_cc/windows_cc_toolchain_config.bzl` to `projects/support/bazel/toolchains/cpp/windows/windows_cc_toolchain_config.bzl`
  - Merge our modifications manually and ensure that they are still working (i.e., that none of the commands are deprecated).

- File a PR, check if everything works.




