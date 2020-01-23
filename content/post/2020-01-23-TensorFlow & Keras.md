---
title: "TensorFlow & Keras"
date: 2020-01-23T11:20:18+09:00
categories:
  - MachineLearning
tags:
  - Tensorflow
  - Keras
draft: true
---

Knowlege
============

Model
------------
### References ###
* [Freeze Tensorflow models and serve on web](https://cv-tricks.com/how-to/freeze-tensorflow-models/)
* [Keras FAQ: Kerasに関するよくある質問](https://keras.io/ja/getting-started/faq/)


TF2.0
============

Model save and load
------------

* .jsonと.h(.hdf5)で読み込み.pbで保存

~~~python
import logging
import tensorflow as tf
from tensorflow.compat.v1 import graph_util
from tensorflow.python.keras import backend as K
from tensorflow import keras

# necessary !!!
tf.compat.v1.disable_eager_execution()

# read the model
json_string = open('./model/octClsf_1912280210_model.json').read()
model = tf.keras.models.model_from_json(json_string)
model.summary()
# read the weights
model.load_weights('./model/octClsf_1912280210_weights.hdf5')
# save pb
with K.get_session() as sess:
    output_names = [out.op.name for out in model.outputs]
    input_graph_def = sess.graph.as_graph_def()
    for node in input_graph_def.node:
        node.device = ""
    graph = graph_util.remove_training_nodes(input_graph_def)
    graph_frozen = graph_util.convert_variables_to_constants(sess, graph, output_names)
    tf.io.write_graph(graph_frozen, './model', 'model.pb')
logging.info("save pb successfully！")
~~~


TF1.0
============
Model save and load
------------

* .jsonと.h(.hdf5)で読み込み.pbで保存。読み込みできないのでウソの可能性がある。[site](https://tutorialmore.com/questions-139135.htm) 

~~~python
import tensorflow as tf
from keras import backend as K
from keras.models import model_from_json

# This line must be executed before loading Keras model.
K.set_learning_phase(0)
from keras.models import load_model

# model = load_model('./model/keras_model.h5')
# read the model
# json_string = open('./model/octClsf_1912280210_model.json').read()
# model = keras.model.model_from_json(json_string)
# model = model_from_json(json_string)

# read the weights
# model.load_weights('./model/octClsf_1912280210_weights.hdf5')
#model.load_model('./model/octClsf_1912280210_weights.hdf5')
# model = load_model('./model/octClsf_1912280210_weights.hdf5')
model = load_model('./model/unet_3class.hdf5')
# model.summary()
def freeze_session(session, keep_var_names=None, output_names=None, clear_devices=True):
    """
    Freezes the state of a session into a pruned computation graph.
    Creates a new computation graph where variable nodes are replaced by
    constants taking their current value in the session. The new graph will be
    pruned so subgraphs that are not necessary to compute the requested
    outputs are removed.
    @param session The TensorFlow session to be frozen.
    @param keep_var_names A list of variable names that should not be frozen,
                          or None to freeze all the variables in the graph.
    @param output_names Names of the relevant graph outputs.
    @param clear_devices Remove the device directives from the graph for better portability.
    @return The frozen graph definition.
    """
    from tensorflow.python.framework.graph_util import convert_variables_to_constants
    graph = session.graph
    with graph.as_default():
        freeze_var_names = list(set(v.op.name for v in tf.global_variables()).difference(keep_var_names or []))
        output_names = output_names or []
        output_names += [v.op.name for v in tf.global_variables()]
        # Graph -> GraphDef ProtoBuf
        input_graph_def = graph.as_graph_def()
        if clear_devices:
            for node in input_graph_def.node:
                node.device = ""
        frozen_graph = convert_variables_to_constants(session, input_graph_def,
                                                      output_names, freeze_var_names)
        return frozen_graph


frozen_graph = freeze_session(K.get_session(),
                              output_names=[out.op.name for out in model.outputs])
tf.train.write_graph(frozen_graph, "model", "tf1_model.pb", as_text=False)
logging.info("save pb successfully！")
~~~

### References ###
* [How to freeze a graph in Tensorflow](https://laid.delanover.com/how-to-freeze-a-graph-in-tensorflow/)
* [How to restore Tensorflow model from .pb file in python?](https://stackoverflow.com/questions/50632258/how-to-restore-tensorflow-model-from-pb-file-in-python)

Keras
============
Model save and load
------------
* 重みからmodel読み込むとエラーになる。JSON→HDF5の順なら問題ない

~~~python
model.save_weights('model.hdf5')
~~~

* load_modelでパスを指定して読み込むことでエラーを解消。[site](https://stackoverflow.com/questions/47266383/save-and-load-weights-in-keras)

~~~python
from keras.models import load_model
model = load_model('model.h5')
~~~

* カスタムされたモデルは引数をつけないとロードできない。[site](https://teratail.com/questions/112052?link=qa_related_pc)

### References ###
* [[TF]KerasでModelとParameterをLoad/Saveする方法](https://qiita.com/supersaiakujin/items/b9c9da9497c2163d5a74)
* [Kerasで可視化いろいろ](https://www.slideshare.net/bathtimefish/keras-75584966)  
  * よさそうなのでやってみる