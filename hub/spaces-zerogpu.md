# ZeroGPU Spaces

MEGA does not currently offer ZeroGPU. Hugging Face ZeroGPU requires a
function-scoped GPU lease for Gradio requests; MEGA's connected GPU capacity is
an asynchronous Job provider and cannot satisfy that runtime contract.

GPU work can be run through [Jobs](/docs/hub/jobs). This page will document the
actual lease, queue, quota, and supported Gradio integration when compatible
interactive GPU capacity becomes available.
