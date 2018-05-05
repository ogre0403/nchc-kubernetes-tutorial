# Distributed tensorflow with GPU

## Create notebook using GPU

```sh
$ kubectl create -f tf-ps.yaml
$ kubectl create -f tf-worker.yaml
```


If you use web-based jupyter, modify `worker.ipynb` to use GPU in tensorflow code, and you can see 
that the example use GPU device from log:


```python
...
 with tf.device('/gpu:0'):
      a = tf.Variable(tf.truncated_normal(shape=[2]),dtype=tf.float32)
      b = tf.Variable(tf.truncated_normal(shape=[2]),dtype=tf.float32)
      c=a+b
...
sess = tf.train.MonitoredTrainingSession(
          master=server.target,
          config=tf.ConfigProto(log_device_placement=True),
          is_chief=is_chief)
...
```

```sh
$ kubectl logs worker-5f68fc6c85-j45jk
...
report_uninitialized_variables/Const: (Const): /job:ps/replica:0/task:0/device:CPU:0
GradientDescent/learning_rate: (Const): /job:worker/replica:0/task:0/device:GPU:0
gradients/sub_grad/Shape_1: (Const): /job:worker/replica:0/task:0/device:GPU:0
```

If you can not use browser, execute the python code in the pod directly.

```sh
$ kubectl exec -ti ps-65487c86f4-tbwsb -- ipython /notebooks/ps.py

$ kubectl exec -ti worker-5f68fc6c85-j45jk -- ipython /notebooks/worker.py
```

## Cleanup

```sh
$ kubectl delete -f tf-ps.yaml
$ kubectl delete -f tf-worker.yaml
```
