# Map Reduce Tutorial

MapReduce is a software framework in Hadoop. It take an input and maps it to different nodes in our Hadoop setup.  Each node then does some processing on these files.  There is then a combine procedure that combines and reduces the result in an output.  The language used is Java but it doesn't have to be (see lecture notes). 

The code that does all this is shown below.  If you are familiar with Java and the concept of Object Oriented Programming, this may look understandable to you.  Otherwise, do not worry, we will simply use this as a tool to see what we can do with MapReduce

```
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

Now we need to compile this could and at a minimum we require a compiler.  But given the complexity of this code, we need more support and will use a Integrated Development Environment or IDE.  For this particular tutorial we will not be modifying the default code but we will in the future so it's best we download an IDE.

In Java, the most commonly used is the Eclipse IDE.  We would like to download the IDE, go to http://www.eclipse.org/downloads/packages/

There are many choices here - it seems confusing but in reality it is just a an alphabetical list.  I found it funny that a developers <a href="https://kichwacoders.com/2016/04/28/why-its-time-to-kill-the-eclipse-release-namesneon-oxygen-etc/">bemoans</a> all the different choices, but it's not surprising too many choices can lead to <a href="https://www.youtube.com/watch?v=VO6XEQIsCoM">paralysis</a>.  Choose the Oxygen version (Photon is a bit underdeveloped at this point) as shown below: 

<img src="EclipseDownload.jpg" alt="Choosing the latest version of Eclipse">

Open Eclipse

Choose a project directory (can be the default chosen for you)

In the next window choose File --> New Project

<img src="NewJavaProject1.jpg" alt="New Java Project in Eclipse" align="middle">

Choose Java Project, and call the project WordCount

<img src="NewJavaProject.jpg" alt="New Java Project in Eclipse" width="400" align="middle">

Click Next.

We now need to add the hadoop libraries.

<img src="AddingHadoopLibraries.jpg" alt="Adding Hadoop Libraries" width = "400" align="middle">

YOu should have already downloaded Hadoop 2.9 version for installing the Hadoop cluster on a single node.  The files were untarred (equivalent to unzipped) in the following folder on your virtual machine

```
/usr/local/hadoop/share/hadoop/common
```

But since we are installing Eclipse on the windows/mac platform, we cannot "see" these files.  One way is to access the files is to create a shared folder for your virtual machine and native OS. Easier solution is to download the file from the course website.

We now need to add the MapReduce core library, it is located in

```
/usr/local/hadoop/share/hadoop/common
```

You can access it from this folder, or download it from the course website.

Click the Finish botton.

<img src="NewJavaProject2.jpg" alt="New Java Project in Eclipse" width="400" align="middle">

In the Project development environment, right click on src --> New --> Class

<img src="NewJavaProject3.jpg" alt="New Java Project in Eclipse" width="400" align="middle">

Call the new class "WordCount" and click finish.

<img src="NewJavaProject4.jpg" alt="New Java Project in Eclipse" width="400" align="middle">

Now copy the mapper and reducer functions as written in Java:

```
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

Paste the entire above code in WordCount.java

<img src="NewJavaProject5.jpg" alt="New Java Project in Eclipse" width="400" align="middle">

Save this file (we will use it in a future tutorial).

## Running a MapReduce job on your single node cluster

Boot up your virtual machine with a single node cluster and log in as hduser.

We need to first set up another environment variable

> export HADOOP_CLASSPATH=$JAVA_HOME/lib/tools.jar

Next, we need to save our code to a file:

> gedit WordCounter.java

On the text editor that opens, copy and paste the code that is shown above.

At the console, now compile your code:

> hadoop com.sun.tools.javac.Main WordCounter.java

This will create several class files.  You can see these by typing "ls" and seeing the files created:

```
hduser@HadoopNode:~$ ls
Desktop           Music      WordCount.class
Documents         Pictures   WordCount$IntSumReducer.class
Downloads         Public     WordCount.java
examples.desktop  Templates  WordCount$TokenizerMapper.class
hadoop_data       Videos
```

We will now take all the class files that were produced and package them into a jar file.

> cf WordCount.jar WordCount*.class

If you type "ls" again, you should see a file called WordCount.jar (it should be in red)

Make an input directory for our job.

> hdfs dfs -mkdir /input

We now need a file to parse using MapReduce.  There is a file called alice.txt posted on the course website.  Download this file to your directory where you are running your commands (you can check the directory you are in by typing pwd or present working directory).

Now take this file and copy it to your HDFS.

> hdfs dfs -copyFromLocal alice.txt /input/alice.txt

Display the results to make sure the save occured correctly:

> hdfs dfs -ls /input

Now run the job

> hadoop jar WordCount.jar WordCount /input /output

Look at the log of the output to make sure there is no errors.

Now take a look at what you have got.

> hadoop hdfs -cat /output/part-r-00000
