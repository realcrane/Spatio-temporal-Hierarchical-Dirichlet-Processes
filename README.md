# Spatio-temporal-Hierarchical-Dirichlet-Processes
Code for ECCV 2016 paper "Globally Continuous and Non-Markovian Crowd Activity Analysis from Videos"

1. Copyright
For research purpose only. Get in touch at h.e.wang@leeds.ac.uk if for any other purposes

2. Citation
Please cite the following paper if you would like to use the programme for your research projects:

@inproceedings{wang2016globally,
  title={Globally Continuous and Non-Markovian Crowd Activity Analysis from Videos},
  author={Wang, He and O'Sullivan, Carol},
  booktitle={European Conference on Computer Vision (ECCV) 2016},
  volume={9909},
  pages={527--544},
  year={2016},
  organization={Springer}
}

3. Platform
The program is compiled on windows 10 under Visual Studio 2013. External libs include log4c++, boost and tinyxml2. However, all the dlls are already included, so it should work. I also needs the c++ runtime libs.

4. Folder Structure
log: the default folder to store log files. Please don't remove/change it. Every time you start the program, it is re-written.
output: the default folder to store temporal/ultimate output files. Please don't remove/change it. Every time you start the program, it is re-written.
HDP.exe: the only executable. Double-clicking on it runs the program.
config.txt: the configuration file that needs to be set up before running the program

5. Configuration file setting
config.txt is written in the form of XML files. Below is the specification:

<Application>: The Application tag where everything is inside of the tag pair
<AppName>: Since I run all my apps using this xml file, this is where I specify the app name. Please don't change it. It should be CRPNpTOT
<Parameters>: Specify all the parameters within the tag.
<alphaA> and <alphaB>: the hyper-parameters values of the gamma distribution for the top level Dirichlet Process(DP) of the word HDP tree
<betaA> and <betaB>: the hyper-parameters values of the gamma distribution for the second level Dirichlet Process(DP) of the word HDP tree
<initClassNum>: initial time class numbers. It will be re-calculated after sampling
<vocabularySize>: the size of your vocabulary
<multEta>: initial values for the the multinomial distribution. Not really important, just for the purpose of having fund.
<alphaTA> and <alphaTB>: the hyper-parameters values of the gamma distribution for the top level Dirichet Process(DP) of the time HDP tree
<betaTA> and <betaTB>: the hyper-parameters values of the gamma distribution for the second level Dirichet Process(DP) of the time HDP tree
<initTClassNum>:initial time class numbers. It will be re-calculated after sampling
<gammaAlpha>,<gammaBeta> and <lambda>: not used.
<wordTableSampleNum>:the number of words to sample when doing table-level sampling on the word tree.
<docNum>: the number of training documents. In the paper, the docs are computed by segmenting data along time into even-length segments and converting them into documents. We found that the length of the docs don't really matter much. The naming of the documents are supposed to be: doc0.bin, doc1.bin......, docn.bin. if docNum is set to -1, the number will be computed.
<docFolder>: the doc folder. $docFolder/training is the folder for training data, $docFolder/testing is the folder for testing data.
<maxIter>: Maximal number of iterations to run.
<renderInterval>: how often do you want to output the result. 2 means every other step. -1 means never.
<niMean>, <niLambdaN>, <niShape>, <niScale>:the hyper-parameters of the normal-inverse-gamma distribution over the prior of the time HDP tree.
<testingDocNum>: the same as docNum except it is for testing.
<burnIn>: the iteration number for burn-in period.
<sampleInterval>: the sampling interval for sampling posterior distribution. For example, 2 means sampling the posterior likelihood at every other step.
<averageNum>: how many samples for posterior likelihood we would like to average before reporting it.
<splitMergeInterval>: how often we want to perform the split-merge operations.
<SMWithTime>: true or false, whether to so split-merge on the time tree too.
<SMMaxIter>: how many iterations of split-merge we would like to do.

6. File format:
To deal with large datasets, all files are binary files. I am fully aware that the current data structure is not ideal, but it is the legacy of a long learning process.

Document files:
Doc files are stored in binary format:
$docSize:int. The number of words.
$words:ints. $docSize ints representing words. Since words are just slots in the vocabulary, they are represented by their indices in the vocabulary. %timeStamps:doubles. $docSize doubles of time stamps.
$m:int. Indicating the following are $m pairs of integers, the first integer of which is a word index and the second indicates the number of the occurrences of that word in the document. Clearly, there is some redundancy here. I used it for trying different things. If you don't use it, just set the $m = 0

Sparsecode.bin:
There is a sparseCode.bin which should be under your $docFolder if <vocabularySize> is set to -1. This design is to deal with representation of documents. Since the initial vocabulary constructed from the image is big and the words are sparse, I used a sparse representation of the words. Only grids where there are words are used to make a new and smaller vocabulary. The sparseCode.bin is an indexing file mapping between the original and the new vocabulary. In the experiments, we discretise the location-velocity domain into a 3D matrix, the first and second dimension being the location and the last being the velocity. The format of sparseCode.bin is as follow:
$wordNum:int. The number of occupied grids in the original vocabulary. 
$Indices:an array of integer triplets. Each triplet consists of x, y and z indicating which grid it is in the original vocabulary. So the new vocabulary is of size length($Indices), much smaller.

Output File:
CRPTHDPRet.bin is the output file regularly created depending on your settings. Usually they are under folder iterationX under the output folder. Its format is as follow:

$clusterNum: int. Cluster number. How many word classes it has computed.
$wweights: ints,  $clusterNum ints representing weights of $clusterNum classes, unnormalised.
$multiEta: double, the eta value of multinomial. Not very useful.
$eta: double, the eta value used to initialise the Dirichlet distribution at the top level.
$dim: int, dimension of the word vocabulary
Next, it has some information for debug purpose, you might want to skip it:
$weights: doubles. $clusterNum doubles.
$clusterNum pairs. The first element of the pair is a structure of doubles containing $clusterNum word patterns, each of $dim long. The second element is also a structure where the first integer is a size, $d, and the second is an array of size $d of doubles.
Next, we have information about the time tree:
$tClusterNum: int. The class number of time tree.
The next $tClusternum+5 integers are to be skipped.
$tWeights:doubles. $tClusterNum doubles of weights of the time classes.
Then an array of structures of length $tClusterNum, each includes information of a Gaussian:
$mean: double. The mean.
$scale: double. The standard deviation
$num: int. Number of pairs following, each containing: $stamp:double. a time stamp and $n:int, the number of that time instances.

Have fun.

7. Disclaimer

This is the first version of the release and I am fully aware that much more work should be done to make it easier to use and run more efficiently. So I cannot guarantee that the code is completely bug-free or it will be under regular maintenance. 
