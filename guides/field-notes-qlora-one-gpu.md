# Field notes: QLoRA on one 32 GB GPU

Five failures from fine-tuning a 27B model on a single RTX 5090, with the fix for each. They are about tooling, not the model. The code is in [qlora-single-gpu-playbook](https://github.com/CastelDazur/qlora-single-gpu-playbook).

**Setup:** 27B model, 4-bit QLoRA with Transformers, PEFT and bitsandbytes, RTX 5090 32 GB, single card. Not tested on several GPUs.

## 1. The allocator strangles itself

Free VRAM falls toward zero over the first few hundred steps and step time climbs from normal to about 485 seconds, with no OOM. Setting `expandable_segments:True` caused it here.

```bash
export PYTORCH_CUDA_ALLOC_CONF=expandable_segments:False
```

## 2. `resume_from_checkpoint` hangs

After a restart the run sat at the same step forever, with the GPU at 100% compute and no memory movement. We never found a setting that fixed it. Instead, load the adapter as trainable, give it a fresh optimizer and schedule, and set `max_steps` to the steps that are left.

## 3. A checkpoint that will not load after a power cut

The saved `trainer_state.json` was missing a block newer Transformers expects. The adapter weights were fine. Write a minimal valid `trainer_state.json` and drop the optimizer state.

## 4. It fit in the dry run, then ran out of memory

Something else grew on the GPU: a background inference server, a desktop app, a leaked process from the last run. At 27B on 32 GB the margin is thin enough that 1-2 GB elsewhere decides it. Stop everything else on the GPU first.

## 5. Choose the sequence length from free VRAM, before loading

| Free VRAM | Sequence length |
|---|---|
| 30,000 MiB or more | 2048 |
| 28,000 to 29,999 MiB | 1536 |
| less | stop with a clear message |

On our data 2048 covered 98.8% of examples and 1536 covered 95.4%. These thresholds are for this model and card; change them for yours.

Also read `progress.jsonl`, not the training log: the log misreports where you are after a resume.

## Related

[gpu-memory-guard](https://github.com/CastelDazur/gpu-memory-guard) does the free-VRAM check as a command-line tool.
