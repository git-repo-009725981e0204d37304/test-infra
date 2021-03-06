package(default_visibility = ["//visibility:public"])

licenses(["notice"])

# Usage:
#   bazel run //label_sync:image  # build image
#   bazel run //label_sync:push  # push image (after building)

load(
    "@io_bazel_rules_go//go:def.bzl",
    "go_binary",
    "go_library",
    "go_test",
)
load(
    "@io_bazel_rules_docker//docker:docker.bzl",
    "docker_build",
    "docker_bundle",
)
load(
    "@io_bazel_rules_docker//contrib:push-all.bzl",
    "docker_push",
)

docker_build(
    name = "image",
    base = "@distroless-base//image",
    entrypoint = ["/label_sync"],
    files = [":label_sync"],
)

docker_bundle(
    name = "bundle",
    images = {
        "{STABLE_DOCKER_REPO}/label_sync:{DOCKER_TAG}": ":image",
        "{STABLE_DOCKER_REPO}/label_sync:latest": ":image",
        "{STABLE_DOCKER_REPO}/label_sync:latest-{BUILD_USER}": ":image",
    },
    stamp = True,
)

docker_push(
    name = "push",
    bundle = ":bundle",
)

go_binary(
    name = "label_sync",
    embed = [":go_default_library"],
    importpath = "k8s.io/test-infra/label_sync",
    pure = "on",
    race = "off",
    tags = ["automanaged"],
)

go_test(
    name = "go_default_test",
    srcs = ["main_test.go"],
    data = [
        "//label_sync:test_examples",
    ],
    embed = [":go_default_library"],
    importpath = "k8s.io/test-infra/label_sync",
    tags = ["automanaged"],
)

go_library(
    name = "go_default_library",
    srcs = ["main.go"],
    importpath = "k8s.io/test-infra/label_sync",
    tags = ["automanaged"],
    deps = [
        "//prow/github:go_default_library",
        "//vendor/github.com/ghodss/yaml:go_default_library",
        "//vendor/github.com/sirupsen/logrus:go_default_library",
    ],
)

filegroup(
    name = "test_examples",
    srcs = glob(["*.yaml"]),
)

filegroup(
    name = "package-srcs",
    srcs = glob(["**"]),
    tags = ["automanaged"],
    visibility = ["//visibility:private"],
)

filegroup(
    name = "all-srcs",
    srcs = [":package-srcs"],
    tags = ["automanaged"],
)
