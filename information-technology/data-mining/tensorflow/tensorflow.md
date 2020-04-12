
참조 
[https://www.tensorflow.org/tutorials/quickstart/beginner](https://www.tensorflow.org/tutorials/quickstart/beginner)
[https://www.tensorflow.org/tutorials/keras/classification](https://www.tensorflow.org/tutorials/keras/classification)

[구글 코랩(Colaboratory)](https://colab.research.google.com/notebooks/welcome.ipynb)
: 파이썬 프로그램을 브라우저에서 직접 실행할 수 있기 때문에 텐서플로를 배우고 사용하기에 간편한 도구입니다:

# Concept

## [Input](https://www.tensorflow.org/api_docs/python/tf/keras/Input)

`Input()` is used to instantiate a Keras tensor.

```
tf.keras.Input(
shape=None, batch_size=None, name=None, dtype=None,
sparse=False, tensor=None, ragged=False, **kwargs)
```

A Keras tensor is a tensor object from the underlying backend (Theano or TensorFlow), which we augment with certain attributes that allow us to build a Keras model just by knowing the inputs and outputs of the model.

For instance, if a, b and c are Keras tensors, it becomes possible to do:  `model = Model(input=[a, b], output=c)`

The added Keras attribute is:  `_keras_history`: Last layer applied to the tensor. the entire layer graph is retrievable from that layer, recursively.

## [Sequential](https://www.tensorflow.org/api_docs/python/tf/keras/Sequential)

Linear stack of layers. Inherits From:  [`Model`]

## [Model](https://www.tensorflow.org/api_docs/python/tf/keras/Model)

`Model`  groups layers into an object with training and inference features.

There are two ways to instantiate a  `Model`:

1 - With the "functional API", where you start from  `Input`, you chain layer calls to specify the model's forward pass, and finally you create your model from inputs and outputs:

```
import tensorflow as tfinputs = tf.keras.Input(shape=(3,))x = tf.keras.layers.Dense(4, activation=tf.nn.relu)(inputs)outputs = tf.keras.layers.Dense(5, activation=tf.nn.softmax)(x)model = tf.keras.Model(inputs=inputs, outputs=outputs)
```

2 - By subclassing the  `Model`  class: in that case, you should define your layers in  `__init__`  and you should implement the model's forward pass in  `call`.

```
import tensorflow as tfclass MyModel(tf.keras.Model):  def __init__(self):    super(MyModel, self).__init__()    self.dense1 = tf.keras.layers.Dense(4, activation=tf.nn.relu)    self.dense2 = tf.keras.layers.Dense(5, activation=tf.nn.softmax)  def call(self, inputs):    x = self.dense1(inputs)    return self.dense2(x)model = MyModel()
```

If you subclass  `Model`, you can optionally have a  `training`  argument (boolean) in  `call`, which you can use to specify a different behavior in training and inference:

```
import tensorflow as tfclass MyModel(tf.keras.Model):  def __init__(self):    super(MyModel, self).__init__()    self.dense1 = tf.keras.layers.Dense(4, activation=tf.nn.relu)    self.dense2 = tf.keras.layers.Dense(5, activation=tf.nn.softmax)    self.dropout = tf.keras.layers.Dropout(0.5)  def call(self, inputs, training=False):    x = self.dense1(inputs)    if training:      x = self.dropout(x, training=training)    return self.dense2(x)model = MyModel()
```








> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTI0NjgzNjc3LDEwMzk1ODk5ODUsLTEzOD
Q3OTgzMjJdfQ==
-->