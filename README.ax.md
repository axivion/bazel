## Axivion modifications to Bazel

This is a patched version containing fixes for:
- <https://github.com/bazelbuild/bazel/issues/11220>
- <https://github.com/bazelbuild/bazel/pull/15846>
- <https://github.com/bazelbuild/bazel/issues/14848>

Patched Source: <https://github.com/axivion/bazel/tree/axivion-7.2.0>

- Windows Command: `bazel build //src:bazel.exe --compilation_mode=opt --stamp src:bazel --embed_label 7.2.0-ax`
- Unix Command: `bazel build //src:bazel --compilation_mode=opt --stamp src:bazel --embed_label 7.2.0-ax`

**Note:** The Linux build should happen in a Docker image that is compatible with our Jenkins environment, e.g. `axivion/dependencies/ax_deps/recipes/bazel_download/Dockerfile` .

## Update bazel
- Clone this repo.
- Update the master from upstream using `git pull https://github.com/bazelbuild/bazel`.
- Create a new branch from an upstream release tag (e.g., from 7.0.0).
- Add the various patches we accumulated via `git cherry-pick`. You can find the hashes using
`git log 7.2.0..origin/axivion-7.2.0`.
- Update the ax_deps recipe under `axivion_dependencies/ax_deps/recipes/bazel_download`
  - The sources item and previous bazel version in `__init__.py`
  - The embed_label flag in `build_bazel.bat` and `build_bazel.sh`
- Build bazel locally with `python3 -m ax_deps --build_pkg=bazel_download`
- Create a PR
  - Trigger Jenkins to build bazel with `@builduser build_pkg bazel_download` on all platforms
  - Check everything works
- Update compiler toolchains (or: check if its necessary)
  - Find these lines in bazel.rc and comment them out:

    ``` bash
    build --action_env=BAZEL_DO_NOT_DETECT_CPP_TOOLCHAIN=1
    build --host_action_env=BAZEL_DO_NOT_DETECT_CPP_TOOLCHAIN=1
    ```

  - Build until bazel started to do something (you can abort or run until the end)
  - Copy `bazel-bauhaus*/external/local_config_cc/windows_cc_toolchain_config.bzl` to `projects/support/bazel/toolchains/cpp/windows/windows_cc_toolchain_config.bzl`
  - Merge our modifications manually and ensure that they are still working (i.e., that none of the commands are deprecated).





