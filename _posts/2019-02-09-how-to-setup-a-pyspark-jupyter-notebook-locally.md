---
layout: post
title:  "How to Setup a PySpark Jupyter Notebook Locally"
date:   2019-02-09
---

Here is a quick tutorial on how to setup PySpark on your local computer with a Jupyter notebook environment.

## Downloading Spark

Go [here](http://spark.apache.org/downloads.html) and pick out the latest Spark version. The default option of "Pre-built for Apache Hadoop 2.7 and later" is OK to select. Proceed to download.

```shell
# Untar spark and put it into /opt
$ tar xvfz spark-2.4.0-bin-hadoop2.7.tgz -C /opt

# Give it a cleaner name
$ sudo mv /opt/spark-2.4.0-bin-hadoop2.7 /opt/spark
```

## Run the REPL

You can start a PySpark REPL by running `/opt/spark/bin/pyspark`.

```shell
$ /opt/spark/bin/pyspark
Python 3.6.5 |Anaconda, Inc.| (default, Apr 29 2018, 16:14:56)
[GCC 7.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /__ / .__/\_,_/_/ /_/\_\   version 2.4.0
      /_/

Using Python version 3.6.5 (default, Apr 29 2018 16:14:56)
SparkSession available as 'spark'.
>>> sc
<SparkContext master=local[*] appName=PySparkShell>
>>>
```

## Run a Jupyter notebook

Often people will suggest to use environmental variables directly to run PySpark in a Jupyter notebook; however, this is a messy approach. It is much better instead to setup a custom Jupyter kernel.

I can check what kernels I have available with `jupyter kernelspec list`.

```shell
$ jupyter kernelspec list
Available kernels:
  julia-1.0    /home/suzil/.local/share/jupyter/kernels/julia-1.0
  scala        /home/suzil/.local/share/jupyter/kernels/scala
  python3      /home/suzil/anaconda3/share/jupyter/kernels/python3
  python2      /usr/share/jupyter/kernels/python2
```

Unfortunately, these auto-created kernels were put into various places which isn't ideal, but whatever. You can create your custom kernel at any of these paths, but I will stick it in the `anaconda3` path where the `python3` kernel is.

All I have to do is create a file called `kernel.json` and put into a folder that I will create at `/home/suzil/anaconda3/share/jupyter/kernels/pyspark`.

```json
{
 "display_name": "PySpark (Spark 2.4)",
 "language": "python",
 "argv": [
  "/home/suzil/anaconda3/bin/python",
  "-m",
  "ipykernel",
  "-f",
  "{connection_file}"
 ],
 "env": {
  "SPARK_HOME": "/opt/spark",
  "PYTHONPATH": "/opt/spark/python:/opt/spark/python/lib/py4j-0.10.7-src.zip",
  "PYTHONSTARTUP": "/opt/spark/python/pyspark/shell.py",
  "PYSPARK_PYTHON": "/home/suzil/anaconda3/bin/python"
 }
}
```

These path values are for me, so make sure you modify to the path of your own Python interpreter.

Now, try to run `jupyter kernelspec list` again.

```shell
$ jupyter kernelspec list
Available kernels:
  julia-1.0    /home/suzil/.local/share/jupyter/kernels/julia-1.0
  scala        /home/suzil/.local/share/jupyter/kernels/scala
  pyspark      /home/suzil/anaconda3/share/jupyter/kernels/pyspark
  python3      /home/suzil/anaconda3/share/jupyter/kernels/python3
  python2      /usr/share/jupyter/kernels/python2
```

`pyspark` is now available as a kernel! I can just run:

```shell
$ jupyter notebook
```

I select the PySpark kernel option when creating a new notebook.

Let me verify that I can create a RDD and execute a command on it. Note that `sc` should already be in scope without any import.

```python
words = sc.parallelize([
   'scala',
   'java',
   'hadoop',
   'spark',
   'akka',
   'spark vs hadoop',
   'pyspark',
   'pyspark and spark',
])
words.count()
```
	8

_Voilà!_ PySpark is running locally in a Jupyter notebook.
