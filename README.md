1. Start Hadoop Services
start-dfs.sh
start-yarn.sh


To check running services:

jps

✅ 2. Create Input Directory in HDFS
hdfs dfs -mkdir /wordcount_input

✅ 3. Upload Input File to HDFS

Create a sample file in Linux:

nano input.txt


Add some text and save.

Upload to HDFS:

hdfs dfs -put input.txt /wordcount_input/

✅ 4. Create Python Mapper Program
nano mapper.py


Paste this:

#!/usr/bin/env python3
import sys

for line in sys.stdin:
    line = line.strip()
    words = line.split()
    for word in words:
        print(f"{word}\t1")


Make it executable:

chmod +x mapper.py

✅ 5. Create Python Reducer Program
nano reducer.py


Paste this:

#!/usr/bin/env python3
import sys

current_word = None
current_count = 0

for line in sys.stdin:
    word, count = line.strip().split("\t")
    count = int(count)

    if current_word == word:
        current_count += count
    else:
        if current_word:
            print(f"{current_word}\t{current_count}")
        current_word = word
        current_count = count

if current_word:
    print(f"{current_word}\t{current_count}")


Make it executable:

chmod +x reducer.py

✅ 6. Run Hadoop Streaming Word Count Job
hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar \
-input /wordcount_input \
-output /wordcount_output \
-mapper mapper.py \
-reducer reducer.py

✅ 7. View Output
hdfs dfs -cat /wordcount_output/part-00000

✅ Sample Output
bigdata   3
hadoop    5
mapreduce 2
python    4

✅ 8. Stop Hadoop (After Experiment)
stop-yarn.sh
stop-dfs.sh
