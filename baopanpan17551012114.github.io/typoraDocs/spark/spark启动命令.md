启动命令：

spark-submit --conf spark.hadoop.mapreduce.input.fileinputformat.input.dir.recursive=true \
             --conf spark.hive.mapred.supports.subdirectories=true \
             --master yarn \
             --deploy-mode client \
             --executor-memory 15G \
             --driver-memory 15G \
             --executor-cores 10 \
             --num-executors 50 \
             --conf spark.driver.maxResultSize=100G  \
             --conf spark.network.timeout=1800s \
             --conf spark.executor.heartbeatInterval=10000 \
             --conf spark.sql.broadcastTimeout=-1 \
             --conf spark.sql.shuffle.partitions=500 \
             --class com.weimob.alg.spark.embedding.WscEmbedding ./alg-spark-goods-output-recall-with-more-action.jar

