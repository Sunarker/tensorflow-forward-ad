# tensorflow-forward-ad
Forward-mode Automatic Differentiation for TensorFlow

# Installation
## Using pip
```bash
pip install tensorflow_forward_ad
```
## Build from source
```bash
git clone https://github.com/renmengye/tensorflow-forward-ad.git
cd tensorflow-forward-ad
python setup.py install
```

## Build from source using Bazel
```bash
git clone https://github.com/renmengye/tensorflow-forward-ad.git
cd tensorflow-forward-ad
bazel build //tensorflow_forward_ad:all
# Run all unit tests.
bazel test //tensorflow_forward_ad:all
```

# Forward-mode AD Usage
```python
import tensorflow as tf
from tensorflow_forward_ad import forward_gradients

# Automatic differentiation.
x = tf.constant(1.0)
y = tf.square(x)
dydx = forward_gradients(y, x)
sess = tf.Session()
print(sess.run(dydx))  # [2.0].

# Computes Jacobian-vector product.
x = tf.ones([5, 10])
y = tf.square(x)
v = tf.ones([5, 10]) * 2
Jv = forward_gradients(y, x, v)
sess = tf.Session()
print(sess.run(Jv))  # [array([[ 4.,  4.,  4.,  4., ...

# A list of inputs.
x1 = tf.ones([5, 10])
x2 = tf.ones([10, 8])
y1 = tf.square(tf.matmul(x1, x2))
y2 = tf.sigmoid(tf.matmul(x1, x2))
v1 = tf.ones([5, 10]) * 0.5
v2 = tf.ones([10, 8]) * 2.0
J1v, J2v = forward_gradients([y1, y2], [x1, x2], [v1, v2])
```

# Second-order Matrix Vector Product
```python
import tensorflow as tf

x = tf.ones([5, 10])
w = tf.ones([10, 8])
z = tf.square(tf.matmul(x, w))
v = tf.ones_like(x)
y_ = tf.eye(8)
f = tf.nn.softmax_cross_entropy_with_logits(labels=y_, logits=z)

# Hessian vector product
from tensorflow_forward_ad.second_order import hessian_vec_fw
Hv = hessian_vec_fw(f, x, v)

# Fisher vector product
Fv = fisher_vec_fw(f, x, v)

# Gauss-Newton vector product
Gv = gauss_newton_vec(f, z, x, v)
```
