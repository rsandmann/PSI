#
# Copyright 2020 the authors listed in CONTRIBUTORS.md
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
load("//tools:package.bzl", "VERSION_LABEL")
load("//tools:common.bzl", "template_rule")

package(default_visibility = ["//visibility:public"])

PSI_LINKOPTS = select({
    "@bazel_tools//src/conditions:darwin": [],
    "//conditions:default": [
        # Needed on some Linux systems. See also
        # https://github.com/google/cctz/issues/47
        # https://github.com/tensorflow/tensorflow/issues/15129
        "-lrt",
    ],
})

template_rule(
    name = "package_h",
    src = "package.h.templ",
    out = "package.h",
    substitutions = {
        "@VERSION_LABEL@": "%s" % VERSION_LABEL,
    },
)

cc_library(
    name = "package",
    srcs = ["package.cpp"],
    hdrs = [":package_h"],
)

cc_test(
    name = "package_test",
    srcs = ["package_test.cpp"],
    deps = [
        ":package",
        "@com_google_googletest//:gtest_main",
    ],
)

cc_library(
    name = "bloom_filter",
    srcs = ["bloom_filter.cpp"],
    hdrs = ["bloom_filter.h"],
    visibility = ["//visibility:private"],
    deps = [
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/types:span",
        "@com_tencent_rapidjson//:rapidjson",
        "@private_join_and_compute//crypto:bn_util",
        "@private_join_and_compute//util:status",
    ],
)

cc_test(
    name = "bloom_filter_test",
    srcs = ["bloom_filter_test.cpp"],
    linkopts = PSI_LINKOPTS,
    deps = [
        ":bloom_filter",
        "//private_set_intersection/cpp/util:status_matchers",
        "@com_google_absl//absl/strings",
        "@com_google_googletest//:gtest",
        "@com_google_googletest//:gtest_main",
    ],
)

cc_library(
    name = "psi_client",
    srcs = ["psi_client.cpp"],
    hdrs = ["psi_client.h"],
    includes = ["."],
    deps = [
        ":bloom_filter",
        "@boringssl//:crypto",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/types:span",
        "@com_tencent_rapidjson//:rapidjson",
        "@private_join_and_compute//crypto:ec_commutative_cipher",
        "@private_join_and_compute//util:status",
    ],
)

cc_test(
    name = "psi_client_test",
    srcs = ["psi_client_test.cpp"],
    linkopts = PSI_LINKOPTS,
    deps = [
        ":psi_client",
        "//private_set_intersection/cpp/util:status_matchers",
        "@com_google_absl//absl/container:flat_hash_set",
        "@com_google_absl//absl/strings",
        "@com_google_googletest//:gtest",
        "@com_google_googletest//:gtest_main",
        "@com_tencent_rapidjson//:rapidjson",
        "@private_join_and_compute//crypto:ec_commutative_cipher",
    ],
)

cc_library(
    name = "psi_server",
    srcs = ["psi_server.cpp"],
    hdrs = ["psi_server.h"],
    includes = ["."],
    deps = [
        ":bloom_filter",
        "@boringssl//:crypto",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/types:span",
        "@com_tencent_rapidjson//:rapidjson",
        "@private_join_and_compute//crypto:ec_commutative_cipher",
        "@private_join_and_compute//util:status",
    ],
)

cc_test(
    name = "psi_server_test",
    srcs = ["psi_server_test.cpp"],
    linkopts = PSI_LINKOPTS,
    deps = [
        ":psi_client",
        ":psi_server",
        "//private_set_intersection/cpp/util:status_matchers",
        "@com_google_absl//absl/container:flat_hash_set",
        "@com_google_absl//absl/strings",
        "@com_google_googletest//:gtest",
        "@com_google_googletest//:gtest_main",
        "@com_tencent_rapidjson//:rapidjson",
    ],
)

cc_binary(
    name = "psi_benchmark",
    srcs = ["psi_benchmark.cpp"],
    linkopts = PSI_LINKOPTS,
    deps = [
        ":psi_client",
        ":psi_server",
        "@com_google_absl//absl/strings",
        "@com_google_benchmark//:benchmark_main",
    ],
)
