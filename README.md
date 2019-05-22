# Run Example WordCount 

## Setting Class Path 
```shell
export HADOOP_CLASSPATH=$(hadoop classpath)
```

## Make __directory__ and __sample file__ in hdfs
```shell
hdfs dfs -mkdir /user
hdfs dfs -mkdir /user/jinu
hdfs dfs -mkdir /user/jinu/input
hdfs dfs -put /user/jinu/input/input local_input
```
__local_input__ exists in local server

You can check the files as Webbrowser. 
* connect localhost:50070 -> utillities (top menu) -> Browse the file system.

## Compile a Sample MapReduce java file

```shell
javac -classpath ${HADOOP_CLASSPATH} -d <directroy name> WordeCount.java
jar -cvf WordCount.jar -C <directory name>

```

## Test & Check

```shell
hadoop jar WordCount.jar WordCount /user/jinu/input/input /user/jinu/output
```
hadoop jar <JAR_FILE> <CLASS_NAME> <HDFS_INPUT_DIRECTORY> <HDFS_OUTPUT_DIRECTORY>


```shell
hdfs dfs -cat /user/jinu/output/*
```

## WordCount.java

```java
import java.io.IOException;
import java.util.StringTokenizer;
 
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
 
public class WordCount {
 
  public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{
 
    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();
 
    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      }
    }
  }
 
  public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();
 
    public void reduce(Text key, Iterable<IntWritable> values,
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
    }
  }
 
  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    Job job = Job.getInstance(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}
```