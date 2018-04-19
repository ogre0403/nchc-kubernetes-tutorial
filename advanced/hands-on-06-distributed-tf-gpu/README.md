
Use GPU in tensorflow code.  

```python
 with tf.device('/gpu:0'):
      a = tf.Variable(tf.truncated_normal(shape=[2]),dtype=tf.float32)
      b = tf.Variable(tf.truncated_normal(shape=[2]),dtype=tf.float32)
      c=a+b

```