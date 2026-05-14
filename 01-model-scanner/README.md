# Supply Chain Protection — Model Scanner

Model Scanner analyzes AI/ML models for malware, backdoors, vulnerabilities, and integrity issues before they reach production.

## Prerequisites

Complete the [top-level setup](../README.md#quick-start) (venv, `pip install`, `.env`) before running this notebook.

## Notebook

| Notebook | Description |
|----------|-------------|
| [`scan.ipynb`](./scan.ipynb) | Scan a local model file and a HuggingFace community model |

## Test Model

`test_models/safe_model.pkl` — a benign pickle used to demonstrate a clean scan result.

## SDK Methods Used

```python
import hiddenlayer
from hiddenlayer.lib import CommunityScanSource

client = hiddenlayer.HiddenLayer(client_id="...", client_secret="...")

# Scan a local file
client.model_scanner.scan_file(
    model_name="my-model",
    model_path="/path/to/model.pkl",
    model_version="1",
    wait_for_results=True,
)

# Scan a community model on HuggingFace
client.community_scanner.community_scan(
    model_name="org/model",
    model_path="org/model",
    model_source=CommunityScanSource.HUGGING_FACE,
    wait_for_results=True,
)
```

The SDK also supports `scan_folder(...)` for directories and `scan_s3_model(...)` for models in S3.

## Supported Model Formats

Pickle, PyTorch (`.pt`, `.bin`), Keras, TensorFlow, ONNX, Safetensors, HDF5, NumPy, JobLib, GGUF, NeMo, R (`.rds`), Cloudpickle/Dill, Skops.

## Resources

- [Model Scanner Docs](https://docs.hiddenlayer.ai/docs/products/model-scanner/overview)
- [Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python)
