workspace(
    name = "nguniversal",
    managed_directories = {"@npm": ["node_modules"]},
)

load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

http_archive(
    name = "io_bazel_rules_webtesting",
    sha256 = "e9abb7658b6a129740c0b3ef6f5a2370864e102a5ba5ffca2cea565829ed825a",
    urls = ["https://github.com/bazelbuild/rules_webtesting/releases/download/0.3.5/rules_webtesting.tar.gz"],
)

http_archive(
    name = "aspect_rules_js",
    sha256 = "2398682cbe4a9beb7d90a128b886c94067626d6cedebf6d66d6659a5aa840edc",
    strip_prefix = "rules_js-59e56921652a1942520d3f16283acf4b46853c6c",
    url = "https://github.com/aspect-build/rules_js/archive/59e56921652a1942520d3f16283acf4b46853c6c.tar.gz",
)

http_archive(
    name = "build_bazel_rules_nodejs",
    sha256 = "f10a3a12894fc3c9bf578ee5a5691769f6805c4be84359681a785a0c12e8d2b6",
    urls = ["https://github.com/bazelbuild/rules_nodejs/releases/download/5.5.3/rules_nodejs-5.5.3.tar.gz"],
)

load("@build_bazel_rules_nodejs//:repositories.bzl", "build_bazel_rules_nodejs_dependencies")
load("@aspect_rules_js//js:repositories.bzl", "rules_js_dependencies")

rules_js_dependencies()

build_bazel_rules_nodejs_dependencies()

# Setup the Node.js toolchain
load("@rules_nodejs//nodejs:repositories.bzl", "nodejs_register_toolchains")

nodejs_register_toolchains(
    name = "nodejs",
    node_version = "14.17.1",
)

load("@build_bazel_rules_nodejs//toolchains/esbuild:esbuild_repositories.bzl", "esbuild_repositories")

esbuild_repositories(
    npm_repository = "npm",
)

load("@build_bazel_rules_nodejs//:index.bzl", "yarn_install")

yarn_install(
    name = "npm",
    # Currently disabled due to:
    #  1. Incompatibilites with the `ts_library` rule.
    exports_directories_only = False,
    package_json = "//:package.json",
    # We prefer to symlink the `node_modules` to only maintain a single install.
    # See https://github.com/angular/dev-infra/pull/446#issuecomment-1059820287 for details.
    symlink_node_modules = True,
    yarn_lock = "//:yarn.lock",
)

load("@aspect_rules_js//npm:npm_import.bzl", "npm_translate_lock")

npm_translate_lock(
    name = "npm_integration",
    pnpm_lock = "//integration:pnpm-lock.yaml",
    verify_node_modules_ignored = "//:.bazelignore",
    bins = {
        "@angular/cli": { "ng": "./bin/ng.js" }
    },
    public_hoist_packages = {
        "tslib@2.4.0": ["integration", "integration/clover"]
    },
)

load("@npm_integration//:repositories.bzl", npm_integration_repositories = "npm_repositories")

# Declares npm_import rules from the integration/pnpm-lock.yaml file
npm_integration_repositories()
