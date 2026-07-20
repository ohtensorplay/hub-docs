# GGUF Models

MEGA can store GGUF files in a model repository, but a file extension alone
does not declare a complete runtime contract. Document the producer, quantized
variant, tokenizer assumptions, prompt format, license, and recommended client
in the model card.

Pin a tag or commit for every example download. Use the runtime that supports
the released GGUF variant; MEGA does not convert, validate, or infer a GGUF
configuration automatically.
