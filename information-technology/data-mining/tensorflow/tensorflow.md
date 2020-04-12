
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








> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTAzOTU4OTk4NSwtMTM4NDc5ODMyMl19
-->