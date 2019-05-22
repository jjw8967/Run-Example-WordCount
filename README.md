# Run Example WordCount 

## Setting Class Path 
```shell
export HADOOP_CLASSPATH=$(hadoop classpath)
```

## Make __directory__ and __sample file__ in hdfs
```shell
hdfs dfs -mkdir /user
hdfs dfs -mkdir /user/jinu
hdfs dfs -put local_input /user/jinu
```
__local_input__ directory exists in local server

You can check the files as Webbrowser. 
* connect localhost:50070 -> utillities (top menu) -> Browse the file system.

## Compile a Sample MapReduce java file

```shell
javac -classpath ${HADOOP_CLASSPATH} -d <directroy name> WordCount.java
jar -cvf WordCount.jar -C <directory name> .

```

## Test & Check

```shell
hadoop jar WordCount.jar WordCount /user/jinu/input /user/jinu/output
```
hadoop jar <JAR_FILE> <CLASS_NAME> <HDFS_INPUT_DIRECTORY> <HDFS_OUTPUT_DIRECTORY>


```shell
hdfs dfs -cat /user/jinu/output/*
```

