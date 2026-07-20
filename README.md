<p align="center"><a href="https://mega.tensorplay.cn/"><img src="https://mega.tensorplay.cn/assets/logo-D1t6EjrA.webp" alt="MEGA" width="420" /></a></p>
<p align="center"><i>Learn, build, and ship on the MEGA model platform.</i></p>
<p align="center">
  <a href="https://mega.tensorplay.cn/docs/hub/index"><img alt="Documentation" src="https://img.shields.io/website?url=https%3A%2F%2Fmega.tensorplay.cn%2Fdocs%2Fhub%2Findex&label=docs"></a>
  <a href="https://github.com/ohtensorplay/hub-docs/blob/main/LICENSE"><img alt="MIT License" src="https://img.shields.io/github/license/ohtensorplay/hub-docs"></a>
</p>

# MEGA Documentation

This repository contains the source pages for the public MEGA documentation at
<https://mega.tensorplay.cn/docs/hub/index>.

MEGA gives ML teams a single product surface for repositories, datasets,
Spaces, inference, compute, and storage. These guides turn that surface into
clear, dependable workflows—from the first upload to production automation—
with explicit API contracts, limits, and security guidance.

- `hub/` documents the MEGA Hub: repositories, community features, storage,
  compute, security, and public integrations.
- `inference-providers/` documents the MEGA Inference Providers product.
- Each source owns a `_toctree.yml` navigation file and Markdown pages whose
  first line is the page H1.

## Writing public documentation

Write for people using MEGA, not for people operating its services. Describe
the supported UI, CLI, SDK, public API, permissions, limits, and recovery
steps. Do not include implementation topology, service names, credentials,
deployment commands, infrastructure configuration, or private operational
procedures.

Use the public product name in examples and replace secrets with placeholders
such as `YOUR_MEGA_TOKEN`. Link to a public reference when an exact protocol or
schema matters, and state unsupported capabilities plainly.

## Validate documentation

From the MEGA Hub workspace, run the documentation check before publishing a
change:

```bash
npm run check:docs
npm run test:docs
```

The check verifies page titles, navigation, internal documentation links, and
documentation assets.
