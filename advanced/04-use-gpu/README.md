# Use GPU

## Verify GPU is available

```sh
$ kubectl create -f gpu.yml

$ kubectl logs gpu-pod
Thu May  3 05:33:23 2018
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 390.25                 Driver Version: 390.25                    |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce GTX 108...  Off  | 00000000:04:00.0 Off |                  N/A |
| 23%   27C    P8    16W / 250W |      0MiB / 11178MiB |      1%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```

## Use GPU in Jupyter

```sh
$ kubectl create -f gpu-notebook.yml

$ kubectl exec -ti master-65487c86f4-tbwsb -- ipython /notebooks/show_device.py
2018-05-03 05:52:47.078627: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: AVX2 FMA
2018-05-03 05:52:47.733638: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1105] Found device 0 with properties:
name: GeForce GTX 1080 Ti major: 6 minor: 1 memoryClockRate(GHz): 1.582
pciBusID: 0000:04:00.0
totalMemory: 10.91GiB freeMemory: 10.75GiB
2018-05-03 05:52:47.733730: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1195] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: GeForce GTX 1080 Ti, pci bus id: 0000:04:00.0, compute capability: 6.1)
[u'/device:CPU:0', u'/device:GPU:0']
```

## Use specific GPU

Create Pod which need P100-GPU label

```sh
$ kubectl create -f gpu-select-node.yml

$ kubectl get pod 
NAME                      READY     STATUS    RESTARTS   AGE
p100-gpu-pod              0/1       Pending   0          32s

# Label your nodes with the accelerator type they have.
kubectl label nodes <node-with-k80> accelerator=nvidia-tesla-p100 
```


## Cleanup

```sh
$ kubectl delete -f gpu.yml
$ kubectl delete -f gpu-notebook.yml
$ kubectl delete -f gpu-select-node.yml
```