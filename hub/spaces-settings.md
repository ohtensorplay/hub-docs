# Space Settings

Space settings control runtime hardware, sleep behavior, variables, secrets,
and lifecycle actions. Open the Space page or use the CLI:

```bash
mega spaces hardware
mega spaces settings OWNER/SPACE --hardware cpu-upgrade
mega spaces settings OWNER/SPACE --sleep-time 15m
mega spaces pause OWNER/SPACE
mega spaces restart OWNER/SPACE
```

Only use hardware identifiers returned by MEGA. A paid setting requires enough
prepaid compute credit. See [Billing](/docs/hub/billing) and
[Space Hardware](/docs/hub/spaces-hardware).

Repository visibility and Space runtime settings are separate. A private Space
requires repository access even when its runtime is otherwise ready.
