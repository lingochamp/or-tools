package(default_visibility = ["//visibility:public"])

load("@com_llsapp_git_common_bazel//build_rules/grpc/go:rules.bzl", "go_proto_library")
load("@com_llsapp_git_common_bazel//build_rules/grpc/cpp:rules.bzl", "cc_proto_library")

config_setting(
    name = "with_glpk",
    values = {"define": "USE_GLPK="},
)

config_setting(
    name = "with_bop",
    values = {"define": "USE_BOP="},
)

config_setting(
    name = "with_cplex",
    values = {"define": "USE_CPLEX="},
)

config_setting(
    name = "with_cbc",
    values = {"define": "USE_CBC="},
)

config_setting(
    name = "with_clp",
    values = {"define": "USE_CLP="},
)

config_setting(
    name = "with_gurobi",
    values = {"define": "USE_GUROBI="},
)

filegroup(
    name = "linear_solver_proto",
    srcs = ["linear_solver.proto"],
)

cc_proto_library(
    name = "linear_solver_cc_proto",
    proto_deps = ["//ortools/util:optional_boolean_cc_proto"],
    protos = [":linear_solver_proto"],
)

go_proto_library(
    name = "linear_solver_go_proto",
    proto_deps = ["//ortools/util:optional_boolean_go_proto"],
    protos = [":linear_solver_proto"],
)

# You can include the interfaces to different solvers by invoking '--define'
# flags. By default only GLOP interface is included.
#
# For instance, if you want to use the GLPK solver, build with
# '--define USE_GLPK=' (or add it to your bazel.rc file). This will download,
# build and link to GLPK.
#
# Currently compiling with '--define USE_BOP=' flag is broken due to the
# circular dependency:
# .-> //ortools/linear_solver:linear_solver
# |   //ortools/bop:integral_solver
# |   //ortools/bop:bop_solver
# |   //ortools/bop:complete_optimizer
# |   //ortools/sat:optimization
# `-- //ortools/linear_solver:linear_solver

cc_library(
    name = "linear_solver",
    srcs = [
        "linear_expr.cc",
        "linear_solver.cc",
        "model_exporter.cc",
        "model_validator.cc",
        "glop_interface.cc",
        "glop_utils.cc",
    ] + select({
        ":with_bop": ["bop_interface.cc"],
        "//conditions:default": [],
    }) + select({
        ":with_cbc": ["cbc_interface.cc"],
        "//conditions:default": [],
    }) + select({
        ":with_clp": ["clp_interface.cc"],
        "//conditions:default": [],
    }) + select({
        ":with_cplex": ["cplex_interface.cc"],
        "//conditions:default": [],
    }) + select({
        ":with_glpk": ["glpk_interface.cc"],
        "//conditions:default": [],
    }) + select({
        ":with_gurobi": ["gurobi_interface.cc"],
        "//conditions:default": [],
    }),
    hdrs = [
        "glop_interface.cc",
        "glop_utils.h",
        "linear_expr.h",
        "linear_solver.h",
        "model_exporter.h",
        "model_validator.h",
    ],
    defines = [
        "USE_GLOP=",
    ],
    deps = [
        ":linear_solver_cc_proto",
        "//ortools/base",
        "//ortools/base:accurate_sum",
        "//ortools/base:canonical_errors",
        "//ortools/base:hash",
        "//ortools/base:map_util",
        "@com_google_absl//absl/synchronization",
        "@com_google_absl//absl/types:optional",
        "//ortools/base:status",
        "//ortools/base:status_macros",
        "//ortools/base:stl_util",
        "@com_google_absl//absl/strings",
        "//ortools/base:timer",
        "//ortools/glop:lp_solver",
        "//ortools/glop:parameters_cc_proto",
        "//ortools/port:file",
        "//ortools/port:proto_utils",
        "//ortools/util:fp_utils",
    ] + select({
        ":with_bop": [
            "//ortools/bop:bop_parameters_cc_proto",
            "//ortools/bop:integral_solver",
        ],
        "//conditions:default": [],
    }) + select({
        ":with_glpk": ["@glpk//:glpk"],
        "//conditions:default": [],
    }),
)
