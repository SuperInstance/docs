# NPU Setup — AMD Ryzen AI HX 370 (XDNA 2)

## Current Status (2026-05-20)

### ✅ Hardware — Ready
- **CPU:** AMD Ryzen AI 9 HX 370 w/ Radeon 890M
- **NPU:** "NPU Compute Accelerator Device" — PCI\VEN_1022&DEV_17F0
- **Device Manager Status:** OK
- **Driver:** Windows built-in XDNA driver (no AMD additional driver installed yet)

### ❌ Ryzen AI SDK — Not Installed
- Installer downloaded: `C:\Users\Casey\Downloads\ryzen-ai-lt-1.7.1.exe` (2.6 GB)
- **Installer has NOT been run yet** — no registry keys, no Program Files entries
- No conda environment created
- No `onnxruntime-vitisai` or `onnxruntime` installed on Windows

### ✅ WSL2 Bridge — Code Ready
- `plato-training/plato_training/npu_bridge.py` created
- Detects NPU hardware via PowerShell
- Will export PyTorch → ONNX and call Windows-side VitisAI EP once SDK is installed
- Graceful fallback if NPU unavailable

---

## Next Steps: Install Ryzen AI SDK

### Step 1: Run the Installer
1. On Windows, double-click `C:\Users\Casey\Downloads\ryzen-ai-lt-1.7.1.exe`
2. The installer will set up:
   - AMD XDNA drivers
   - Conda environment (`ryzen-ai`)
   - ONNX Runtime with VitisAIExecutionProvider
   - Example models and tools
3. Expected install location: `C:\Program Files\AMD\RyzenAI\` or similar

### Step 2: Verify Installation
Open **Windows PowerShell** (not WSL):
```powershell
conda activate ryzen-ai
python -c "import onnxruntime as ort; print(ort.get_available_providers())"
```
Should output: `['VitisAIExecutionProvider', ...]`

### Step 3: Test NPU Inference
```powershell
conda activate ryzen-ai
python -c "
import numpy as np
import onnxruntime as ort

# Create a minimal ONNX model
import onnx
from onnx import helper, TensorProto

X = helper.make_tensor_value_info('X', TensorProto.FLOAT, [1, 3])
Y = helper.make_tensor_value_info('Y', TensorProto.FLOAT, [1, 3])
node = helper.make_node('Relu', ['X'], ['Y'])
graph = helper.make_graph([node], 'test', [X], [Y])
model = helper.make_model(graph, opset_imports=[helper.make_opsetid('', 14)])
onnx.save(model, 'test_relu.onnx')

sess = ort.InferenceSession('test_relu.onnx', providers=['VitisAIExecutionProvider', 'CPUExecutionProvider'])
result = sess.run(None, {'X': np.array([[-1, 0, 1]], dtype=np.float32)})
print('Result:', result)
print('Provider:', sess.get_providers())
"
```

### Step 4: Test from WSL2
```bash
cd ~/plato-training
python -c "
from plato_training.npu_bridge import bridge
print('NPU available:', bridge.available)
print('Requires SDK install:', bridge.requires_sdk)
"
```

---

## Architecture

```
WSL2 (Ubuntu)                    Windows 11
┌─────────────────┐              ┌──────────────────────┐
│ PyTorch Model   │              │ Ryzen AI SDK          │
│       ↓         │              │  - onnxruntime        │
│ Export to ONNX  │──powershell→ │  - VitisAI EP         │
│       ↓         │              │  - XDNA 2 driver      │
│ npu_bridge.py   │              │       ↓               │
│       ↓         │←──json────── │  NPU Inference        │
│ Results back    │              │  (AMD Ryzen AI NPU)   │
└─────────────────┘              └──────────────────────┘
```

## Alternative: Direct WSL2 NPU Access
AMD has experimental WSL2 NPU support via `/dev/accel/accel0`. Check if available:
```bash
ls /dev/accel/
```
If the device exists, we could potentially skip the Windows bridge entirely and use a Linux-side ONNX Runtime build with VitisAI support. This would be much faster (no IPC overhead). However, this requires:
- WSL2 kernel with accel support
- Linux-side VitisAI ONNX EP build
- This is more experimental and may not be stable yet
