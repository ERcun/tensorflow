# Description:
# Java Native Interface (JNI) library intended for implementing the
# TensorFlow Java API using the TensorFlow C library.

package(default_visibility = [
    "//tensorflow/java:__pkg__",
    # TODO(ashankar): Temporary hack for the Java API and
    # //third_party/tensorflow/contrib/android:android_tensorflow_inference_jni
    # to co-exist in a single shared library. However, the hope is that
    # //third_party/tensorflow/contrib/android:android_tensorflow_jni can be
    # removed once the Java API provides feature parity with it.
    "//tensorflow/contrib/android:__pkg__",
])

licenses(["notice"])  # Apache 2.0

load("//tensorflow:tensorflow.bzl", "tf_cuda_library", "tf_copts")

tf_cuda_library(
    name = "native",
    srcs = glob(["*.cc"]) + select({
        # The Android toolchain makes "jni.h" available in the include path.
        # For non-Android toolchains, generate jni.h and jni_md.h.
        "//tensorflow:android": [],
        "//conditions:default": [
            ":jni.h",
            ":jni_md.h",
        ],
    }),
    hdrs = glob(["*.h"]),
    copts = tf_copts(),
    includes = select({
        "//tensorflow:android": [],
        "//conditions:default": ["."],
    }),
    deps = select({
        "//tensorflow:android": [
            "//tensorflow/core:android_tensorflow_lib",
        ],
        "//conditions:default": [
            "//tensorflow/c:c_api",
            "//tensorflow/core:all_kernels",
            "//tensorflow/core:direct_session",
            "//tensorflow/core:ops",
        ],
    }),
    alwayslink = 1,
)

# Silly rules to make
# #include <jni.h>
# in the source headers work
# (in combination with the "includes" attribute of the tf_cuda_library rule
# above. Not needed when using the Android toolchain).
#
# Inspired from:
# https://github.com/bazelbuild/bazel/blob/f99a0543f8d97339d32075c7176b79f35be84606/src/main/native/BUILD
# but hopefully there is a simpler alternative to this.
genrule(
    name = "copy_jni_h",
    srcs = ["@bazel_tools//tools/jdk:jni_header"],
    outs = ["jni.h"],
    cmd = "cp -f $< $@",
)

genrule(
    name = "copy_jni_md_h",
    srcs = select({
        "//tensorflow:windows": ["@bazel_tools//tools/jdk:jni_md_header-windows"],
        "//tensorflow:macos": ["@bazel_tools//tools/jdk:jni_md_header-darwin"],
        "//conditions:default": ["@bazel_tools//tools/jdk:jni_md_header-linux"],
    }),
    outs = ["jni_md.h"],
    cmd = "cp -f $< $@",
)

filegroup(
    name = "all_files",
    srcs = glob(
        ["**/*"],
        exclude = [
            "**/METADATA",
            "**/OWNERS",
        ],
    ),
    visibility = ["//tensorflow:__subpackages__"],
)
