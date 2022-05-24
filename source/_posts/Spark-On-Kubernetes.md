---
title: Spark-On-Kubernetes
date: 2020-02-22 16:00:43
tags:
---

## 1. 编译源代码

```
mvn -T 4C -Phive -Phive-thriftserver -Pkubernetes -Pkafka-0-8 -Pyarn -Phadoop-2.7 -Dhadoop.version=3.2.1 -DskipTests -Dcheckstyle.skip=true -Dmaven.compile.fork=true clean install
```

## 2. 构建 Docker 镜像

```
./bin/docker-image-tool.sh -r spark-on-kubernetes -t v2.4.6 build
```

## 3. 创建 Kubernetes Service Account

```
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

## 4. 提交任务

### 4.1 本地提交

```
./bin/spark-submit \
    --master local \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.ui.port=4040 \
    --conf spark.executor.instances=2 \
    local:///Users/wuzhiping/workspace/bigdata/spark/examples/target/scala-2.11/jars/spark-examples_2.11-2.4.6-SNAPSHOT.jar 100

./bin/spark-submit \
    --master local \
    --name hdfsTest \
    --class org.apache.spark.examples.HdfsTest \
    --conf spark.ui.port=4040 \
    --conf spark.executor.instances=2 \
    local:///Users/wuzhiping/workspace/bigdata/spark/examples/target/scala-2.11/jars/spark-examples_2.11-2.3.5-SNAPSHOT.jar hdfs://localhost:9000/spark/data/mllib/iris_libsvm.txt
```

### 4.2 Yarn 提交

```
./bin/spark-submit \
 --master yarn \
 --deploy-mode client \
 --name spark-pi \
 --class org.apache.spark.examples.HdfsTest \
 --conf spark.ui.port=4040 \
 --conf spark.executor.instances=2 \
 local:///Users/wuzhiping/workspace/bigdata/spark/examples/target/scala-2.11/jars/spark-examples_2.11-2.3.5-SNAPSHOT.jar /spark/data/mllib/iris_libsvm.txt
```

### 4.3 K8S 提交

```
./bin/spark-submit \
    --master k8s://https://kubernetes.docker.internal:6443 \
    --deploy-mode cluster \
    --name hdfsTest \
    --class org.apache.spark.examples.HdfsTest \
    --conf spark.ui.port=4040 \
    --conf spark.executor.instances=5 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark  \
    --conf spark.kubernetes.authenticate.driver.caCertFile=/opt/spark/cert/ca.crt \
    --conf spark.kubernetes.authenticate.driver.clientKeyFile=/opt/spark/cert/client.key \
    --conf spark.kubernetes.authenticate.driver.clientCertFile=/opt/spark/cert/client.crt \
    --conf spark.kerberos.keytab=/opt/spark/keytab/user.keytab \
    --conf spark.kubernetes.kerberos.krb5.path=/etc/krb5.conf \
    --conf spark.kubernetes.container.image.pullPolicy=Always \
    --conf spark.kubernetes.container.image.pullSecrets=default-secret \
    --conf spark.kubernetes.container.image=stayrascaldocker/spark:v2.4.6 \
    local:///opt/spark/examples/target/original-spark-examples_2.11-2.4.5-SNAPSHOT.jar /opt/spark/data/mllib/iris_libsvm.txt

    ./bin/spark-submit \
    --master k8s://https://kubernetes.docker.internal:6443 \
    --deploy-mode cluster \
    --name hdfsTest \
    --class org.apache.spark.examples.HdfsTest \
    --conf spark.ui.port=4040 \
    --conf spark.executor.instances=2 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark  \
    --conf spark.kubernetes.container.image=stayrascaldocker/spark:v2.4.6 \
    --jars local:///opt/spark/examples/target/original-spark-examples_2.11-2.4.5-SNAPSHOT.jar /opt/spark/data/mllib/iris_libsvm.txt

    ./bin/spark-submit \
    --master k8s://https://kubernetes.docker.internal:6443 \
    --deploy-mode cluster \
    --name hdfsTest \
    --class org.apache.spark.examples.HdfsTest \
    --conf spark.ui.port=4040 \
    --conf spark.executor.instances=2 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark  \
    --conf spark.kubernetes.container.image=stayrascaldocker/spark:v2.4.6 \
    local:///opt/spark/examples/target/original-spark-examples_2.11-2.4.5-SNAPSHOT.jar /spark/data/mllib/iris_libsvm.txt

    bin/spark-submit \
    --master k8s://https://kubernetes.docker.internal:6443 \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=5 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark  \
    --conf spark.kubernetes.container.image=spark-on-kubernetes/spark:v2.3 \
    --conf spark.kubernetes.container.image.pullPolicy=Always
    local:///opt/spark/examples/target/original-spark-examples_2.11-2.3.5-SNAPSHOT.jar /spark/data/mllib/iris_libsvm.txt
```

## 5. Spark On Angel

- 解压 angel-<version>-bin.zip
- 配置 angel-<version>-bin/bin/spark-on-angl-env.sh 下的 SPARK_HOME, ANGEL_HOME, ANGEL_HDFS_HOME 三个环境变量
- 将解压后的 angel-<version>-bin 目录上传到 HDFS 路径

```
source ./spark-on-angel-env.sh
$SPARK_HOME/bin/spark-submit \
    --master yarn \
    --deploy-mode client \
    --conf spark.ps.jars=$SONA_ANGEL_JARS \
    --conf spark.ps.instances=2 \
    --conf spark.ps.cores=2 \
    --conf spark.ps.memory=1g \
    --queue default \
    --jars $SONA_SPARK_JARS\
    --name "LR-spark-on-angel" \
    --driver-memory 2g \
    --num-executors 2 \
    --executor-cores 2 \
    --executor-memory 1g \
    --class com.tencent.angel.spark.examples.basic.LR \
    ./../lib/spark-on-angel-examples-${ANGEL_VERSION}.jar \
    input:${ANGEL_HOME}/data/angel/a9a/a9a_123d_train.libsvm \
    lr:0.1
```

## 6. PyTorch On Angel

```
source ./spark-on-angel-env.sh
$SPARK_HOME/bin/spark-submit \
       --master yarn \
       --deploy-mode client \
       --conf spark.yarn.queue=default \
       --conf spark.ps.instances=2 \
       --conf spark.ps.cores=1 \
       --conf spark.ps.jars=$SONA_ANGEL_JARS \
       --conf spark.ps.memory=1g \
       --conf spark.ps.log.level=INFO \
       --conf spark.driver.extraJavaOptions=-Djava.library.path=$JAVA_LIBRARY_PATH:.:./torch/torch-lib \
       --conf spark.executor.extraJavaOptions=-Djava.library.path=$JAVA_LIBRARY_PATH:.:./torch/torch-lib \
       --conf spark.executor.extraLibraryPath=./torch/torch-lib \
       --conf spark.driver.extraLibraryPath=./torch/torch-lib \
       --conf spark.executorEnv.OMP_NUM_THREADS=2 \
       --conf spark.executorEnv.MKL_NUM_THREADS=2 \
       --queue $queue \
       --name "deepfm for torch on angel" \
       --jars $SONA_SPARK_JARS  \
       --archives torch.zip#torch\
       --files deepfm.pt \
       --driver-memory 5g \
       --num-executors 5 \
       --executor-cores 1 \
       --executor-memory 5g \
       --class com.tencent.angel.pytorch.examples.supervised.RecommendationExample \
       ./pytorch-on-angel-*.jar \
       trainInput:$input batchSize:128 torchModelPath:deepfm.pt \
       stepSize:0.001 numEpoch:10 testRatio:0.1 \
       angelModelOutputPath:$output \
```