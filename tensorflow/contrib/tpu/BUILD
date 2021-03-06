# Description: Operations defined for Cloud TPUs

licenses(["notice"])  # Apache 2.0

load(
    "//tensorflow:tensorflow.bzl",
    "tf_custom_op_library",
    "tf_gen_op_libs",
    "tf_gen_op_wrapper_py",
)
load("//tensorflow:tensorflow.bzl", "tf_custom_op_py_library")
load("//tensorflow:tensorflow.bzl", "tf_py_test")

package(
    default_visibility = [
        "//cloud/vmm/testing/tests/tpu:__subpackages__",
        "//learning/brain:__subpackages__",
        "//tensorflow:__subpackages__",
        "//third_party/cloud_tpu:__subpackages__",
    ],
)

cc_library(
    name = "all_ops",
    deps = [
        ":cross_replica_ops_op_lib",
        ":host_compute_ops_op_lib",
        ":infeed_ops_op_lib",
        ":outfeed_ops_op_lib",
        ":replication_ops_op_lib",
        ":tpu_configuration_ops_op_lib",
        ":tpu_embedding_ops_op_lib",
    ],
)

py_library(
    name = "tpu_estimator",
    srcs = [
        "python/tpu/tpu_config.py",
        "python/tpu/tpu_context.py",
        "python/tpu/tpu_estimator.py",
        "python/tpu/tpu_system_metadata.py",
        "python/tpu/util.py",
    ],
    srcs_version = "PY2AND3",
    deps = [
        ":tpu_lib",
        ":tpu_py",
        "//tensorflow/contrib/summary:summary_ops",
        "//tensorflow/core:protos_all_py",
        "//tensorflow/python:array_ops",
        "//tensorflow/python:control_flow_ops",
        "//tensorflow/python:framework_for_generated_wrappers",
        "//tensorflow/python:init_ops",
        "//tensorflow/python:math_ops",
        "//tensorflow/python:platform",
        "//tensorflow/python:state_ops",
        "//tensorflow/python:summary",
        "//tensorflow/python:training",
        "//tensorflow/python:variable_scope",
        "//tensorflow/python:variables",
        "//tensorflow/python/estimator",
        "//tensorflow/python/estimator:model_fn",
        "//tensorflow/python/estimator:run_config",
        "//tensorflow/python/estimator:util",
        "@six_archive//:six",
    ],
)

tf_gen_op_libs(
    op_lib_names = [
        "cross_replica_ops",
        "host_compute_ops",
        "infeed_ops",
        "outfeed_ops",
        "replication_ops",
        "tpu_configuration_ops",
        "tpu_embedding_ops",
    ],
    deps = [
        "//tensorflow/contrib/tpu/proto:tpu_embedding_config_proto_cc",
        "//tensorflow/core:lib_proto_parsing",
        "//tensorflow/core:protos_all_cc",
    ],
)

tf_custom_op_library(
    name = "python/ops/_tpu_ops.so",
    srcs = [
        "ops/cross_replica_ops.cc",
        "ops/host_compute_ops.cc",
        "ops/infeed_ops.cc",
        "ops/outfeed_ops.cc",
        "ops/replication_ops.cc",
        "ops/tpu_configuration_ops.cc",
        "ops/tpu_embedding_ops.cc",
    ],
    deps = [
        "//tensorflow/contrib/tpu/proto:tpu_embedding_config_proto_cc",
        "//tensorflow/core:lib_proto_parsing",
    ],
)

tf_gen_op_wrapper_py(
    name = "tpu_ops",
    deps = [
        ":cross_replica_ops_op_lib",
        ":host_compute_ops_op_lib",
        ":infeed_ops_op_lib",
        ":outfeed_ops_op_lib",
        ":replication_ops_op_lib",
        ":tpu_configuration_ops_op_lib",
        ":tpu_embedding_ops_op_lib",
    ],
)

py_library(
    name = "profiler",
    srcs = ["python/profiler/__init__.py"],
    srcs_version = "PY2AND3",
    deps = [
        "//tensorflow/contrib/tpu/profiler:trace_events_proto_py",
        "//tensorflow/python:util",
    ],
)

tf_custom_op_py_library(
    name = "tpu_py",
    srcs = glob(["python/ops/*.py"]) + ["__init__.py"],
    dso = [":python/ops/_tpu_ops.so"],
    kernels = [
        ":all_ops",
    ],
    srcs_version = "PY2AND3",
    deps = [
        ":profiler",
        ":tpu_ops",
        "//tensorflow/contrib/util:util_py",
        "//tensorflow/python:client_testlib",
        "//tensorflow/python:framework_for_generated_wrappers",
        "//tensorflow/python:platform",
        "//tensorflow/python:util",
    ],
)

py_library(
    name = "tpu",
    srcs = ["python/tpu/__init__.py"],
    srcs_version = "PY2AND3",
    deps = [
        ":tpu_estimator",
        ":tpu_lib",
    ],
)

py_library(
    name = "tpu_lib",
    srcs = [
        "python/tpu/__init__.py",
        "python/tpu/device_assignment.py",
        "python/tpu/topology.py",
        "python/tpu/tpu.py",
        "python/tpu/tpu_feed.py",
        "python/tpu/tpu_function.py",
        "python/tpu/tpu_optimizer.py",
        "python/tpu/tpu_sharding.py",
        "python/tpu/training_loop.py",
    ],
    srcs_version = "PY2AND3",
    deps = [
        ":datasets",
        ":profiler",
        ":tpu_py",
        "//tensorflow/contrib/tpu/proto:topology_proto_py",
        "//tensorflow/core:protos_all_py",
        "//tensorflow/python:array_ops",
        "//tensorflow/python:control_flow_ops",
        "//tensorflow/python:control_flow_util",
        "//tensorflow/python:dtypes",
        "//tensorflow/python:framework",
        "//tensorflow/python:framework_ops",
        "//tensorflow/python:tensor_shape",
        "//tensorflow/python:training",
        "//tensorflow/python:util",
        "//tensorflow/python:variable_scope",
        "//tensorflow/python/ops/losses",
    ],
)

py_library(
    name = "datasets",
    srcs = [
        "python/tpu/datasets.py",
    ],
    srcs_version = "PY2AND3",
    deps = [
        "//tensorflow/contrib/data/python/ops:transformation_ops",
        "//tensorflow/python:dtypes",
        "//tensorflow/python:function",
        "//tensorflow/python:functional_ops",
        "//tensorflow/python/data/ops:dataset_ops",
        "//tensorflow/python/data/ops:iterator_ops",
        "//tensorflow/python/data/ops:readers",
    ],
)

tf_py_test(
    name = "datasets_test",
    srcs = ["python/tpu/datasets_test.py"],
    additional_deps = [
        "//tensorflow/python:client_testlib",
        ":datasets",
    ],
    grpc_enabled = True,
)

tf_py_test(
    name = "tpu_test",
    size = "small",
    srcs = ["python/tpu/tpu_test.py"],
    additional_deps = [
        ":tpu",
        "//tensorflow/python:client_testlib",
        "//tensorflow/python:dtypes",
        "//tensorflow/python:framework",
        "//tensorflow/python:layers",
    ],
    tags = ["no_windows"],  # TODO: needs investigation on Windows
)

tf_py_test(
    name = "tpu_sharding_test",
    size = "small",
    srcs = ["python/tpu/tpu_sharding_test.py"],
    additional_deps = [
        ":tpu",
        "//tensorflow/python:client_testlib",
        "//tensorflow/python:framework",
    ],
)

tf_py_test(
    name = "tpu_infeed_test",
    size = "small",
    srcs = ["python/tpu/tpu_infeed_test.py"],
    additional_deps = [
        ":tpu",
        "//tensorflow/python:framework",
        "//tensorflow/python:framework_test_lib",
    ],
)

tf_py_test(
    name = "tpu_function_test",
    size = "small",
    srcs = ["python/tpu/tpu_function_test.py"],
    additional_deps = [
        ":tpu",
        "//tensorflow/python:framework",
        "//tensorflow/python:framework_test_lib",
    ],
)

tf_py_test(
    name = "tpu_config_test",
    size = "small",
    srcs = ["python/tpu/tpu_config_test.py"],
    additional_deps = [
        ":tpu_estimator",
        "//tensorflow/python:framework",
        "//tensorflow/python:framework_test_lib",
    ],
)

filegroup(
    name = "all_files",
    srcs = glob(
        include = [
            "**/*",
        ],
        exclude = [
            "**/METADATA",
            "**/OWNERS",
        ],
    ),
)
