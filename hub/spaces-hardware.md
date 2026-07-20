# Space Hardware

MEGA Spaces run on the hardware flavors currently returned by the Space API and
CLI. Query before choosing a flavor:

```bash
mega spaces hardware
```

`cpu-basic` is the included baseline. Paid hardware charges by started runtime
minute and uses the same owner wallet as [Jobs](/docs/hub/jobs). A request can
fail with `402` when the wallet cannot cover the required admission cost.

GPU is not listed as a Space flavor because the currently connected GPU
provider runs asynchronous Jobs only; it cannot provide a stable Space URL or
function-scoped GPU leases. Use [Jobs](/docs/hub/jobs) for GPU work. A future
interactive GPU provider will be published in this catalogue with its real
capacity and pricing.

If an older Space still records `gpu-nano`, it is intentionally not restarted
or silently downgraded. Request `cpu-basic` or `cpu-upgrade` first, then restart
the Space. It may always be paused safely.

Choose the smallest flavor that meets the application's measured needs. Pause a
paid Space when it is not needed, and pin the application revision before
testing a more expensive configuration.
