Lei Jiang  (topcoder handle: lionelc)
lionelchange@gmail.com 

1. I modified the banner_source code 
2. Also, in its dependent library Mallet, I modified the source code of it too and thereby replaced the jar file in lib directory of banner_source. 

The new jar file is enclosed in lib: mallet.jar

The modified source code of mallet is also enclosed (src directory only), and the entire package can be downloaded at 

http://mallet.cs.umass.edu/dist/mallet-2.0.6.tar.gz

This solution runs better than the default on the validation data set (score visible to contestants immediately), with a submission score 821119.94. In the modified mallet.jar, L1 regularization is applied in CRF training (by using 0.045 as the weight of L1 term).

Algorithm description:

1. Tuning on training approach: in the original code, class banner.tagging.CRFTagger, it shows three rounds of warm-up training by using 20%, 50% and 80% of randomly-chosen data subset with 10 iteration by default. After trying different data. After a few trials, I figured out the following strategy works better: using 20%, 40%, 60% and 80% with 30, 50, 80 and 120 iterations. And then, the training iterations are changed to 500 to ensure sufficient convergence. 

2. Change on Mallet library: by default, the iterative training, as in Mallet library (2.0.6 used as downloaded from http://mallet.cs.umass.edu/dist/mallet-2.0.6.tar.gz ) has an early termination with the condition of lower-than-threshold progress in any iteration. As it may not help the training out of local minima, I removed this early termination condition in both src/cc/mallet/optimize/OrthantWiseLimitedMemoryBFGS.java and src/cc/mallet/optimize/LimitedMemoryBFGS.java . Besides, while L1 regularization would avoid overfitting, I also changed src/cc/mallet/fst/CRFTrainerByL1LabelLikelihood.java by giving a weight to SPARSE_PRIOR. With several trials, SPARSE_PRIOR = 0.045 seems to be a good one. Then the mallet library is re-compiled to a jar to replace the original one included in BANNER code.

 Note: as I see in the forum, many other contestants also said they se CRFTrainerByL1LabelLikelihood , but that's not really L1 regularization because SPARSE_PRIOR is default to 0.0 (for the L1 term). That change also makes a difference though, brought by the difference between OrthantWiseLimitedMemoryBFGS and LimitedMemoryBFGS. I tested all such combinations and found a non-zero weight of SPARSE_PRIOR (0.045) makes the score on provisional data better.

Unfortunately, in the contest, I didn't get to use mturk data but just stuck with the default training data set. 
