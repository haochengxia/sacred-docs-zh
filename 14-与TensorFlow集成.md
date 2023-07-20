与TensorFlow的集成

Sacred提供了与TensorFlow库交互的方式。其目标是提供一个API，允许跟踪有关如何在Sacred中使用TensorFlow的特定信息。收集到的数据存储在experiment.info["tensorflow"]中，可以通过各种观察者访问。

存储TensorFlow日志（FileWriter）
要将TensorFlow产生的摘要（由tensorflow.summary.FileWriter创建）的位置存储到由ex参数指定的实验记录中，请使用sacred.stflow.LogFileWriter(ex)装饰器或上下文管理器。每当在装饰器或上下文管理器的范围内检测到新的FileWriter实例化时，日志的路径会被复制到实验记录中，与传递给FileWriter的路径完全相同。

这些位置可以在实验的info["tensorflow"]["logdirs"]下找到。

重要提示：在调用装饰的方法或进入上下文之前，实验必须处于RUNNING状态。

示例用法：作为装饰器
LogFileWriter(ex)作为装饰器可以用于函数或类方法。

```python
from sacred.stflow import LogFileWriter
from sacred import Experiment
import tensorflow as tf

ex = Experiment("my experiment")

@ex.automain
@LogFileWriter(ex)
def run_experiment(_run):
    with tf.Session() as s:
        swr = tf.summary.FileWriter("/tmp/1", s.graph)
        # _run.info["tensorflow"]["logdirs"] == ["/tmp/1"]
        swr2 = tf.summary.FileWriter("./test", s.graph)
        #_run.info["tensorflow"]["logdirs"] == ["/tmp/1", "./test"]
```

示例用法：作为上下文管理器
有一个上下文管理器可用于在代码的较小部分中捕获路径。

```python
ex = Experiment("my experiment")
def run_experiment(_run):
    with tf.Session() as s:
        with LogFileWriter(ex):
            swr = tf.summary.FileWriter("/tmp/1", s.graph)
            # _run.info["tensorflow"]["logdirs"] == ["/tmp/1"]
            swr3 = tf.summary.FileWriter("./test", s.graph)
            # _run.info["tensorflow"]["logdirs"] == ["/tmp/1", "./test"]
        # This is called outside the scope and won't be captured
        swr3 = tf.summary.FileWriter("./nothing", s.graph)
        # Nothing has changed:
        # _run.info["tensorflow"]["logdirs"] == ["/tmp/1", "./test"]
```

在以上示例中，我们展示了如何使用Sacred与TensorFlow集成，并将TensorFlow产生的日志路径存储在实验信息中，以便进一步观察和记录。