## Execution Steps

```
hadoop jar crunch-project-1.0-SNAPSHOT-job.jar inputFile outputFile
```

Execution starts from WordCount.java

```
public class WordCount extends Configured implements Tool
```

It expects 2 parameters inputFile (arg 1) and outputFile (arg 2).

arg1 is inputFilePath and arg2 is outputFilePath.

Steps in Detail

Create an object to coordinate pipeline creation and execution.
```
Pipeline pipeline = new MRPipeline(WordCount.class, getConf());
```

Reference a given text file as a collection of Strings.
```
PCollection<String> lines = pipeline.readTextFile(inputPath);
```

Define a function that splits each line in a PCollection of Strings into
a PCollection made up of the individual words in the file.
The second argument sets the serialization format.

```
PCollection<String> words = lines.parallelDo(new Tokenizer(), Writables.strings());
```

Take the collection of words and remove known stop words.

```
PCollection<String> noStopWords = words.filter(new StopWordFilter());
```

The count method applies a series of Crunch primitives and returns
a map of the unique words in the input PCollection to their counts.

```
PTable<String, Long> counts = noStopWords.count();
```

Instruct the pipeline to write the resulting counts to a text file.

```
pipeline.writeTextFile(counts, outputPath);
```

Execute the pipeline as a MapReduce.

```
PipelineResult result = pipeline.done();
```
