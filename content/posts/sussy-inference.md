+++
title = "Your Weights Are Static. The Execution Graph Is Not."
date = "2026-11-09T10:00:00+05:30"
url = "/sussy-inference/"
tags = ["ml", "inference", "benchmarking"]
+++

Around 500 BC in Ephesus, Heraclitus observed that you cannot step into the same river twice. The water moves on. Had Heraclitus spent his career deploying computer vision models to consumer laptops instead of writing aphorisms, he would have phrased it differently: you cannot execute the same forward pass twice. Certainly not across two hardware targets. Sometimes not even twice on the same GPU, if the driver favors non-deterministic atomic reductions.

Then he would have spent four days dumping intermediate tensors to `.npy` files to prove to a silicon vendor's developer relations team that their compiler pass was silently dropping a clamp op.

This post is about shipping small computer vision models for photo editing onto customer machines. It covers what happens when you stop assuming user hardware looks like your local workstation, and how a deterministic PyTorch graph turns into four distinct artifacts before it ever touches a pixel.

## How a `state_dict` Survives Four Compilers (Mostly Mangled)

When you finish training a model, you have a set of FP32 weights and a Python definition of how tensors move through them. You validate it, the PSNR looks great, and you hand it over to the deployment pipeline. You might think you are shipping that artifact. You are actually shipping a polite suggestion to a chain of downstream compilers, each with its own parser bugs and aggressive optimization passes.

First, you trace and export the graph. As PyTorch migrated `torch.onnx.export` from the legacy TorchScript C++ tracer toward the `torch.export` and TorchDynamo stack, how dynamic axes get captured and where initializers live in the graph changed underneath everyone. Half our downstream tooling choked on the new `SymInt` shape guards. We pinned the legacy exporter path immediately. Nobody scheduled an architecture review for that decision; the CI pipeline turned red on a Tuesday and we slapped `dynamo=False` onto the call like electrical tape over a check-engine light.

Second, you run an offline converter. Your ONNX graph at opset 18 gets lowered into an engine-specific representation: a Core ML `.mlpackage`, an OpenVINO IR pair, or an ExecuTorch `.pte`. This stage runs constant folding, fuses `Conv + BatchNorm + ReLU` blocks, inserts `NCHW` to `NHWC` layout transposes, and quietly downcasts weights if you forgot to pass a flag. Your graph now contains nodes you never wrote, operating on memory layouts you never tested.

Third, the runtime on the user's machine compiles the intermediate representation *again* into device-specific binaries. DirectML, Metal Performance Shaders, or the Qualcomm QNN backend takes your portable graph, runs hardware-specific kernel selection for the local silicon rev, and writes a binary blob to a local cache directory. On your development machine, this happens once and hides in `~/.cache` forever. On a customer's laptop, this manifests as the application freezing for twelve seconds the first time they click a button.

Fourth, the runtime schedules execution. It inspects the compiled kernels, realizes the target accelerator cannot handle three of your ops, and splits your graph into subgraphs.

By the time an image runs through your model, your original PyTorch code has been translated four times. You only ran your evaluation suite against the first one.

## Graph Partitioning and the Silent Fallback Tax

Developers new to on-device inference often believe that passing `CoreMLExecutionProvider` or `DmlExecutionProvider` to a session configuration forces the model onto the accelerator. In reality, selecting an execution provider only submits a wishlist to the runtime's graph partitioner.

During session initialization, the execution provider walks your graph node by node and claims the operators it supports. Whenever it hits an unsupported op—say, a `GridSample` with bicubic interpolation or a `ReduceMean` along a weird axis—it leaves that node unclaimed. The runtime assigns unclaimed nodes to the default CPU execution provider and stitches the subgraphs together with copy nodes.

You asked for the NPU. What the runtime actually built is five nodes on the NPU, a synchronization barrier, a memory copy back to system RAM, a layout transpose from tiled NPU memory to planar `NCHW`, a single CPU operator, another layout transpose, a copy back to NPU memory, and the rest of the network. That single fallback op costs nothing in compute and everything in memory bandwidth and pipeline stalls. Worse, a minor OS update can alter the execution provider's support table, shifting the partition boundaries underneath your already-shipped binary.

Before profiling latency or debugging accuracy, inspect the physical execution plan:

* **ONNX Runtime:** Enable `ort_session_options.enable_profiling = True` and inspect the generated JSON trace in Perfetto to see which Execution Provider actually claimed each node.
* **Core ML:** Open the model in Xcode's Instruments or check the Core ML Performance Report to see the exact CPU / GPU / ANE split per layer.
* **OpenVINO:** Run `benchmark_app` with `-pcsort` to dump per-layer execution counters and real kernel names (`jit_avx2` vs `ocl` vs `npu`).

Half of our "the NPU is slow" tickets and a huge chunk of our numerical regressions turned out to be silent CPU fallbacks bifurcating the graph six times per forward pass.

Floating-point addition is non-associative: (a + b) + c ≠ a + (b + c). Change the reduction tree inside a matrix multiplication kernel, or swap a fused multiply-add (`FMA`) for separate multiply and add instructions, and the low-order mantissa bits diverge. Across thirty layers, those ulp differences compound. Chasing bit-exact parity across hardware backends is a great way to burn a quarter with nothing to show for it.

## Hardware-in-the-Loop CI: Herding Four Laptops With Thunderbolt Ports

You cannot catch hardware-specific compiler regressions in a containerized Linux cloud runner. You need physical consumer laptops wired into your CI system.

You also do not need a rack of fifty machines. A lot of it can be covered with the vast majority of desktop execution paths with four boxes on a shelf:

1. **Apple Silicon (M-series):** Covers macOS, Metal, and the Apple Neural Engine via Core ML.
2. **Windows x86 + Discrete NVIDIA GPU:** Covers CUDA / TensorRT / DirectML over PCIe with split memory.
3. **Windows x86 + Integrated GPU / NPU (Intel Core Ultra or AMD Ryzen AI):** Covers shared-memory DirectML, OpenVINO, and x86 NPU drivers.
4. **Windows-on-Arm (Snapdragon X Elite):** Covers Arm64 compilation, QNN execution providers, and Hexagon NPU quantization quirks.

Every release candidate triggers a pipeline that exports the model, dispatches the artifacts to these four hardware runners, and executes both the domain-aware parity suite and the cold/warm performance harness in a single job. The pipeline writes a unified telemetry record: `(model_sha, device_id, os_build, driver_version, runtime_version, power_source, p50_ms, p95_ms, cold_init_ms, peak_mem_mb, p99_delta_e)`. Putting accuracy and latency in the exact same database row makes it impossible for an engineer to celebrate a 2× speedup that secretly fell back to a broken fast-math kernel.

Crucially, this hardware suite also runs on a nightly cron job against *already shipped* production models. With Windows ML and modern runtimes dynamically pulling execution provider updates via the OS store, and GPU drivers auto-updating in the background, a model you shipped six months ago can break on a Wednesday without a single commit to your repository.

Building the runner scripts takes a week. Keeping the physical laptops alive is a permanent tax. Windows Update will reboot a machine mid-benchmark. macOS will pop up a modal dialog asking to enable Siri that blocks UI automation. Batteries will swell if left at 100% on AC power for a year without charge limiters. Put smart plugs on the chargers, disable sleep and auto-updates via MDM or group policy, enforce a 120-second idle cool-down between benchmark jobs, and monitor chassis temperature sensors before starting a timing pass.
