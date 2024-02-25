实验一
1.实验目的
将论文提出的LU+DS技术与本地 Graphite ICM 进行了比较。实验将分别在两种配置下（ICM、LU+DS），评估六种时序图遍历算法（TR、SSSP、EAT、LD、FAST和 TMST）。目的是证明LU+DS技术可显著减少算法的平均运行时间。
2.实验预期输出
LU 技术预计会减少每个 TimeWarp 的报文数量，从而减少每个 warp 的时间。DS 技术预计会减少报文的数量，这不仅会减少通信时间，还会降低wrap的成本。
实验将给出三个衡量指标：
① makespan reduction。含义为相对于本地 ICM，LU+DS 所实现的时间跨度减少率。由以下公式得出：
② Reduction in message count。含义为message数量的平均下降率
③ Reduction in warp time。含义为warp时间的平均下降率
预期输出为，六种遍历算法都能获得较为优秀的makespan reduction、Reduction in message count、Reduction in warp time指标，达到论文图表数据。
使用 LU+DS 时，各种算法wallclock time明显降低（效率在百分之五十左右）。
3.实验步骤
1、运行 Graphite ICM 作业
1.1该实验参数有四个，在运行实验前需要将项目参数修改。
source ：遍历算法将从其开始的源顶点 ID（例如 0）
perfFlag ：设置为 true 以转储与性能相关的日志信息，否则设置为 false（例如 false） 
inputGraph ：输入图的 HDFS 路径（例如，sampleGraph.txt）
outputDir ：输出文件夹的 HDFS 路径（例如，输出）
1.2先将示例图形文件复制到 HDFS
hdfs dfs -copyFromLocal build/graphs/sampleGraph.txt
hdfs dfs -ls sampleGraph.txt
1.3运行脚本
runEAT.sh <source> <perfFlag> <inputGraph> <outputDir>
1.4运行source ID为0的ICM模式作业，使用算法EAT。其他五种算法实验步骤相同，略。
cd build
bash ./scripts/giraph/icm/runEAT.sh 0 false sampleGraph.txt output
	成功完成作业后，输出应出现在 build/ 下。
2、使用顶点局部优化（LU+DS）运行ICM
2.1除了上面 ICM 脚本的 4 个参数之外，这些脚本还有其他参数。
bufferSize ：用于 LU 优化的消息缓存的大小（例如 100） 
minMsg ：LU 优化的最小消息基数（例如 20）
2.2运行脚本
runEAT.sh <source> <bufferSize> <minMsg> <perfFlag> <inputGraph> <outputDir>
2.3运行source ID为0的ICM模式作业，使用算法EAT。其他五种算法实验步骤相同，略。
cd build
bash ./scripts/giraph/icm_luds/runEAT.sh 0 100 20 false sampleGraph.txt output
2.4成功完成作业后，输出应出现在 build/ 下。
3、对两次实验的output进行数据分析，看看是否符合预期输出，与实验纵表进行对比，分析每种遍历算法上的优化程度与优化特点。
实验二
1. 实验目的
将论文提出的WICM技术与本地 Graphite ICM 进行了比较。实验将分别在两种配置下（WICM、ICM），评估六种时序图遍历算法（TR、SSSP、EAT、LD、FAST和 TMST）。目的是证明WICM技术可显著减少算法的平均运行时间。
2.实验预期输出
按照WICM设计，通过窗口执行能减少wrap时间,使性能提高。
实验将给出三个衡量指标。（同实验一，不再赘述）
预期输出为，六种遍历算法都能获得较为优秀的makespan reduction、Reduction in message count、Reduction in warp time指标，达到论文图表数据。
使WICM 在所有图形和算法上的速度都比 ICM 快或相当，而且通常能平均减少 24-97% 的wallclock time。
3.实验步骤
1、运行 Graphite ICM 作业。实验与实验一相同，略。
	成功完成作业后，输出应出现在 build/ 下。
2、运行WICM
2.1除了上面 ICM 脚本的 4 个参数之外，这些脚本还有下面三个参数。
lowerE ：图表生命周期的开始时间（例如，0）
upperE ：图表生命周期的结束时间（例如，40） 
windows ：图生命周期的时间分区，指定为用分号分隔的时间点边界（例如 0;20;30;40）
2.2运行脚本
runEAT.sh <source> <lowerE> <upperE> <windows> <perfFlag> <inputGraph> <outputDir>
2.3示例图sampleGraph.txt的寿命为[0,40)。 我们假设某种分割策略为我们提供[0,20)、[20,30) 和 [30,40) 的窗口。使用souurce  ID 0， 来运行 WICM 作业。使用Eat算法，其他五种算法实验步骤相同，略。
cd build
bash ./scripts/runEAT.sh 0 0 40 "0;20;30;40" false sampleGraph.txt output
2.4成功完成作业后，输出应出现在 build/ 下。
3、对两次实验的output进行数据分析，看看是否符合预期输出，与实验纵表进行对比，分析每种遍历算法上的优化程度与优化特点。


实验三
1. 实验目的
将论文提出的WICM+LU+DS技术结合与本地 Graphite ICM 进行了比较。实验将分别在两种配置下（WICM+LU+DS, ICM），评估六种时序图遍历算法（TR、SSSP、EAT、LD、FAST和 TMST）。目的是证明WICM技术可显著减少算法的平均运行时间。
2.实验预期输出
当我们将 WICM 与 LU+DS 结合使用时，在所有图形和所有算法中，WICM 与 LU+DS 结合的性能始终优于 ICM。
实验将给出三个衡量指标。（同实验一，不再赘述）
预期输出为，六种遍历算法都能获得较为优秀的makespan reduction、Reduction in message count、Reduction in warp time指标，达到论文图表数据。
3.实验步骤
1、运行 Graphite ICM 作业。实验与实验一相同，略。
	成功完成作业后，输出应出现在 build/ 下。
2、使用顶点局部优化（LU+DS）运行WICM
2.1除了上面 ICM 脚本的 4 个参数之外，这些脚本还有下面三个参数。
owerE ：图表生命周期的开始时间（例如，0）
upperE ：图表生命周期的结束时间（例如，40）
windows ：图生命周期的时间分区，指定为用分号分隔的时间点边界（例如0;20;30;40）
bufferSize ：用于 LU 优化的消息缓存的大小（例如 100）
minMsg ：LU 优化的最小消息基数（例如 20）运行脚本
runEAT.sh <source> <lowerE> <upperE> <windows> <perfFlag> <inputGraph> <outputDir>
2.2运行脚本
runEAT.sh <source> <lowerE> <upperE> <windows> <buffersize> <minMsg> <perfFlag> <inputGraph> <outputDir>
2.3示例图sampleGraph.txt的寿命为[0,40)。 我们假设某种分割策略为我们提供了 [0,20)、[20,30) 和 [30,40) 的窗口。使用souurce  ID 0， 来运行 WICM 作业。使用Eat算法，其他五种算法实验步骤相同，略。
cd build
bash ./scripts/runEAT.sh 0 0 40 "0;20;30;40" 100 20 false sampleGraph.txt output
2.4成功完成作业后，输出应出现在 build/ 下。
3、对两次实验的output进行数据分析，看看是否符合预期输出，与实验纵表进行对比，分析每种遍历算法上的优化程度与优化特点。


实验四
1. 实验目的
用于为 WICM 使用的图形窗口找到良好的分割点。我们将为 WICM 选择窗口数及其分区边界的开窗启发式所实现的makespan reduction %，与从多个窗口数中采样所实现的名义最佳makespan reduction % 进行了比较。
2.实验预期输出
WICM 的makespan优势与窗口数呈 U 型曲线关系。最初，wrap的优势大于开销。但如果窗口数量较多，wrap调用次数的增加将超过每次wrap时间的减少。
我们期待寻找这个最小值,并与论文数据对比。
3.实验步骤
1、SparkSetup.md 中提供了设置 Apache Spark 的说明。 在运行 Spark 之前，使用上面的说明设置 Hadoop。
2、运行Pyspark 代码，用于获取运行启发式所需的时间点边缘分布。 它使用与实验一运行 Graphite ICM 作业中所述相同的输入图形格式。 该脚本有 3 个参数：
inputGraph ：输入图的 HDFS 路径（例如，sampleGraph.txt）
upperE ：输入图生命周期的结束时间，假设图生命周期的开始时间为0（例如40）
outputFile ：存储获得的边缘分布的本地路径（例如sampleGraph.bin）运行脚本
spark-submit --master yarn --num-executors 1 --executor-cores 1 --executor-memory 2G getGraphTimepoint_spark.py <inputGraph> <upperE> <outputFile> 
3、在生命周期为 (0,40] 的输入图 exampleGraph.txt 上运行此 pyspark 代码并将输出存储在 build/graphs/distribution 文件夹下的sampleGraph.bin 中
cd build/scripts/heuristic
spark-submit --master yarn --num-executors 1 --executor-cores 1 --executor-memory 2G getGraphTimepoint_spark.py sampleGraph.txt 40 sampleGraph.bin
4、使用此边缘分布运行 windows 分割启发式的代码。该脚本以边缘分布二进制文件作为输入，并在控制台上打印通过在分布上运行启发式获得的分割策略。
python split.py <edge_dist_file>
5、运行上面在sampleGraph.bin中返回的边缘分布的脚本
cd build/scripts/heuristic
python split.py ../../graphs/distributions/sampleGraph.bin
6、上述命令的输出是
Unscaled distribution (0.9199715190124998, '0;23;40')
Scaled distribution (0.907843090338, '0;21;40')
输出是一个元组 (β, heuristic_window_partitions)，其中 β 是论文中描述的额外消息复制成本，heuristic_window_partitions 是由图的启发式方法（没有分布修剪）确定的窗口分割。 heuristic_window_partitions 输出可用作上面 #5 和 #6 命令中 windows 参数的替换。
