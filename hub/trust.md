# Signing and Trust


MEGA treats downloaded artifacts as untrusted until both integrity and provenance checks pass.

## Trust model

A trusted artifact requires:

1. A payload hash bound to the artifact bytes.
2. A canonical signed statement.
3. A code-signing leaf certificate.
4. A valid certificate chain.
5. A trusted root supplied by verifier policy.

Artifacts may embed the leaf certificate and intermediate chain. They do not embed the trust root.

## Sign during conversion

```bash
mega convert ./Qwen3.5-0.8B \
  --output-dir ./Qwen3.5-0.8B/mega \
  --sign-bundle ./signing/release-bundle.pem
```

The PEM bundle contains:

```text
-----BEGIN PRIVATE KEY-----
...
-----END PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
leaf certificate
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
optional intermediate
-----END CERTIFICATE-----
```

## Sign an existing artifact

```bash
mega sign ./Qwen3.5-0.8B/mega/model.mega.index.json \
  --bundle ./signing/release-bundle.pem
```

Signing the index signs all resolved shards.

## Verify a release

The example script shows the expected verification flow:

```bash
python megatensors/examples/sign_and_verify.py \
  Qwen3.5-0.8B/mega/model.mega.index.json \
  --bundle ./signing/release-bundle.pem \
  --trusted-roots ./signing/root-ca.pem \
  --model-id qwen3.5-0.8b
```

## Risk states

| State | Meaning |
| --- | --- |
| Valid signature, trusted root | Artifact passes provenance policy. |
| Valid signature, unknown issuer | Cryptography passed, source policy failed. |
| Payload hash mismatch | Artifact bytes changed after signing. |
| Expired or invalid certificate | Signature is not acceptable for current policy. |
| Missing signature | Treat as unsigned community content. |

## Key handling

- Store private keys outside the repository.
- Rotate signing certificates without changing trusted roots unless policy requires it.
- Keep release signing separate from developer SSH keys.
- Record signer identity in release notes for operational audit.
