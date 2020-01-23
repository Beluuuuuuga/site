---
title: "TensorFlow & Keras"
date: '2020-01-23'
categories:
  - MachineLearning
tags:
  - Tensorflow
  - Keras
---

Knowledge
============

Model
------------
* Tensorflowのモデルはmodel-ckpt.meta, model-ckpt.data-0000-of-00001, model-ckpt.index, checkpointの4つの変数を持つ<sup><a href=#1a>*1</a><sup>
* ウェブサイトなどでデプロイするときに一つにまとめた.pbファイルにする
* kerasで保存する対象とそのコード。くわしくは下記サイトを参照<sup><a href=#2a>*2</a><sup>

保存の関数|読み込みの関数    |対象(拡張子)
----|------------|-----------------------------------------
model.save('hoge.h5')|model = load_model('hoge.h5')|アーキテクチャ + 重み + オプティマイザの状態（.hdf5 or .h）
json_string = model.to_json()  |model = model_from_json(json_string) |モデルのアーキテクチャ（weightパラメータや学習時の設定は含まない）(.json or .yml)
model.save_weights('hoge.h5')|model.load_weights('hoge.h5')|モデルの重みのみ(.hdf5 or .h)

### References ###
* <a href=#1a>*1</a>:[Freeze Tensorflow models and serve on web](https://cv-tricks.com/how-to/freeze-tensorflow-models/)
* <a href=#2a>*2</a>:[Keras FAQ: Kerasに関するよくある質問](https://keras.io/ja/getting-started/faq/)


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

* .jsonと.h(.hdf5)で読み込み.pbで保存。読み込みできないのでウソの可能性がある<sup><a href=#1c>*1</a><sup>

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
* <a href=#1c>*1</a>:[kerasモデルh5ファイルをテンソルフロー保存モデル（pb）に変換する方法](https://tutorialmore.com/questions-139135.htm) 
* <a href=#2c>*2</a>:[How to freeze a graph in Tensorflow](https://laid.delanover.com/how-to-freeze-a-graph-in-tensorflow/)
* <a href=#3c>*3</a>:[How to restore Tensorflow model from .pb file in python?](https://stackoverflow.com/questions/50632258/how-to-restore-tensorflow-model-from-pb-file-in-python)
* <a href=#4c>*4</a>:[How to convert trained Keras model to a single TensorFlow .pb file and make prediction](https://www.dlology.com/blog/how-to-convert-trained-keras-model-to-tensorflow-and-make-prediction/)

Keras
============
Model save and load
------------
* 重みからmodel読み込むとエラーになる。JSON→HDF5の順なら問題ない

~~~python
model.save_weights('model.hdf5')
~~~

* load_modelでパスを指定して読み込むことでエラーを解消<sup><a href=#1b>*1</a><sup>

~~~python
from keras.models import load_model
model = load_model('model.h5')
~~~

* カスタムされたモデルは引数をつけないとロードできない<sup><a href=#2b>*2</a><sup>

### References ###
* <a href=#1b>*1</a>:[Save and load weights in keras](https://stackoverflow.com/questions/47266383/save-and-load-weights-in-keras)
* <a href=#2b>*2</a>:[kerasのモデルのload_modelでエラー](https://teratail.com/questions/112052?link=qa_related_pc)
* <a href=#3b>*3</a>:[[TF]KerasでModelとParameterをLoad/Saveする方法](https://qiita.com/supersaiakujin/items/b9c9da9497c2163d5a74)
* <a href=#4b>*4</a>:[Kerasで可視化いろいろ](https://www.slideshare.net/bathtimefish/keras-75584966)  
  * よさそうなのでやってみる