---
title: MapReduce z Apache Hadoop w usłudze HDInsight
description: Dowiedz się, jak uruchamiać zadania Apache MapReduce na Apache Hadoop w klastrach usługi HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 33d96262ca2e2ff3003fbf2b40ce9ceb496337ae
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944256"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Korzystanie z technologii MapReduce na platformie Apache Hadoop w usłudze HDInsight

Dowiedz się, jak uruchamiać zadania MapReduce w klastrach usługi HDInsight.

## <a name="example-data"></a>Przykładowe dane

Usługa HDInsight oferuje różne przykładowe zestawy danych, które są przechowywane w `/example/data` `/HdiSamples` katalogu i. Te katalogi znajdują się w domyślnym magazynie klastra. W tym dokumencie używany jest `/example/data/gutenberg/davinci.txt` plik. Ten plik zawiera notesy Leonardo da Vinci.

## <a name="example-mapreduce"></a>Przykład MapReduce

Przykładowa aplikacja MapReduce Count Word jest dołączona do klastra usługi HDInsight. Ten przykład znajduje się w `/example/jars/hadoop-mapreduce-examples.jar` domyślnym magazynie klastra.

Następujący kod Java jest źródłem aplikacji MapReduce zawartej w `hadoop-mapreduce-examples.jar` pliku:

```java
package org.apache.hadoop.examples;

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
import org.apache.hadoop.util.GenericOptionsParser;

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
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Instrukcje dotyczące pisania własnych aplikacji MapReduce można znaleźć w temacie [Programowanie aplikacji Java MapReduce dla usługi HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

## <a name="run-the-mapreduce"></a>Uruchamianie MapReduce

Usługa HDInsight może uruchamiać zadania HiveQL przy użyciu różnych metod. Skorzystaj z poniższej tabeli, aby zdecydować, która metoda jest dla Ciebie odpowiednia, a następnie postępuj zgodnie z linkiem do przewodnika.

| **Użyj tego**... | **... Aby to zrobić** |  ... z tego **systemu operacyjnego klienta** |
|:--- |:--- |:--- |:--- |
| [Protokół SSH](apache-hadoop-use-mapreduce-ssh.md) |Korzystanie z polecenia Hadoop za pośrednictwem protokołu **SSH** |Linux, UNIX, Mac OS X lub Windows |
| [Odsłon](apache-hadoop-use-mapreduce-curl.md) |Zdalne przesyłanie zadania przy użyciu usługi **rest** |Linux, UNIX, Mac OS X lub Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Zdalne przesyłanie zadania przy użyciu **programu Windows PowerShell**  |Windows |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat pracy z danymi w usłudze HDInsight, zobacz następujące dokumenty:

* [Opracowywanie programów MapReduce w języku Java dla usługi HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Korzystanie z programu Apache Hive z usługą HDInsight](./hdinsight-use-hive.md)
