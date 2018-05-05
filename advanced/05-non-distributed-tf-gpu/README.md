# tensorflow with GPU

## Create notebook using GPU

```sh
$ kubectl create -f notebook-gpu.yaml
```

## Verify GPU usage

If you use web-based jupyter, chnage to use GPU in tensorflow code, and you can see that the 
example use GPU device from log: 

```python
 ...
 with tf.device('/gpu:0'):
      a = tf.Variable(tf.truncated_normal(shape=[2]),dtype=tf.float32)
      b = tf.Variable(tf.truncated_normal(shape=[2]),dtype=tf.float32)
      c=a+b
...
sess = sv.prepare_or_wait_for_session(config=tf.ConfigProto(log_device_placement=True))
...
```

```sh
$ kubectl logs master-6c987cbfc7-45p5f

2018-05-03 06:39:33.686909: I tensorflow/core/common_runtime/placer.cc:874] truncated_normal/mean: (Const)/job:localhost/replica:0/task:0/device:GPU:0
2018-05-03 06:39:33.686924: I tensorflow/core/common_runtime/placer.cc:874] truncated_normal/shape: (Const)/job:localhost/replica:0/task:0/device:GPU:0
GradientDescent/learning_rate: (Const): /job:localhost/replica:0/task:0/device:GPU:0
```

If you can not use browser, execute the python code in the pod directly.

```sh
$ kubectl exec -ti master-65487c86f4-tbwsb -- ipython /notebooks/Non-Distributed.py
```

## Cleanup

```sh
$ kubectl delete -f notebook-gpu.yaml
```
