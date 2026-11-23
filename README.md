# Dynamo Qwen on 2× DGX Spark (k3s)

Kubernetes recipes for running **NVIDIA Dynamo 1.6** with **vLLM** on a two-node **k3s** cluster (GB10 “Spark” nodes: `gx10-1be4` control plane, `gx10-1ff3` GPU worker).

## Layout

| File | Purpose |
|------|---------|
| `agg-spark-fp4local/deploy.yaml` | **DynamoGraphDeployment** smoke path: `Qwen/Qwen3-0.6B` on ff3, frontend on be4, offline HF cache, Dynamo 1.6 pip bootstrap in the vLLM image |
| `agg-spark-fp4local/fix-preprocessor-job.yaml` | One-shot Job to copy image preprocessor configs into an **Inferact NVFP4** snapshot (NVFP4 full-model path) |

## Prerequisites

- Dynamo operator installed (`dynamo-system`)
- Namespace `dynamo-qwen`, `hf-token-secret` if downloading weights
- HF weights on ff3 host path (see comments in `deploy.yaml`)
- Node labels/selectors matching your hostnames

## Apply

```bash
kubectl apply -f agg-spark-fp4local/deploy.yaml
```

## Notes

- Full **Qwen3.8-Flash-Next NVFP4 + PLE** on a single Spark was blocked by host RAM / UniProc PLE offload behavior; this repo’s default deploy uses **Qwen3-0.6B** for a reliable interview/demo stack.
- Derived from [NVIDIA Dynamo](https://github.com/ai-dynamo/dynamo) recipes under `recipes/qwen3.8-flash-next/vllm/agg-spark-fp4local/`.