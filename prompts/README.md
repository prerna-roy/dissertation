# Full text of the prompts

Full text of the 20 prompt versions that were eligible for selection in prompt development (dissertation Section 2.3.1, Table 6; Appendix G). One file per technique:

- [Zero-shot prompts](zero_shot.md) (8 versions; described in Appendix G, Table G1)
- [Few-shot prompts](few_shot.md) (6 versions; described in Appendix G, Table G2)
- [Chain-of-thought prompts](chain_of_thought.md) (6 versions; described in Appendix G, Table G3)

The system and user messages below were taken from the audit logs (`results/prompt_development/audit_log.jsonl`, `results/internal_validation/audit_log.jsonl`, `results/external_validation/audit_log.jsonl`), which record the text sent with every call. Every logged call of a version carried the same text, and the SHA-256 code recomputed from this text (as in `prompt_fingerprint` in `prompting_pipeline_final.ipynb`) matched the code logged with every call. The report PDFs are not reproduced. Versions that were not eligible are in `results/prompt_versions.jsonl`.
