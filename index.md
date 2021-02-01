# Welcome to our coding interview
![Cartoon](./superman-sits-at-a-job-interview-jason-adam-katzenstein-01.png)
We want to get a sense of your ability to do some of more specific tasks in the PennLINC labratory. As a data analyst, a lot of your job will be data downloading, wrangling, and processing. We also want to see your creative, scientific talents. 

## Download functional connectivity matrices from the Human Connectome Project
### Connectome DB Account
Downloading this data requires making an account on thier data downloader webiste [ConnectomeDB](https://db.humanconnectome.org/app/template/Login.vm).
### Aspera Connect Plug-In
You will also need a plug-in called [Aspera Connect](https://downloads.asperasoft.com/connect2/)
### Download the data! 
This specific dataset is called the [HCP1200 Parcellation + Timeseries + Netmats (1003 Subjects)](https://www.humanconnectome.org/storage/app/media/documentation/s1200/HCP1200-DenseConnectome+PTN+Appendix-July2017.pdf). We (i.e., you) are going to analyze that Netmats. They call them Netmats, which is short for network matrices, which are functional connectivity matrices. If you'd rather analyze the timeseries, or the parcellations, or everything, feel free to do that. Once you are logged in to ConnectomeDB, and you have the plug-in active, you can download the [brain data](https://db.humanconnectome.org/app/action/ChooseDownloadResources?project=HCP_Resources&resource=GroupAvg&filePath=HCP1200_Parcellation_Timeseries_Netmats_recon2.zip). This should take roughly 30 minutes and will require 10GB of space. Please let us know if your personal computing resources do not allow for this.

You should also grab the [behavioral data](https://db.humanconnectome.org/REST/search/dict/Subject%20Information/results?format=csv&removeDelimitersFromFieldValues=true&restricted=0&project=HCP_1200)

You should unzip the file once it is done downloading. You will see multiple netmats_* files. For example, netmats_3T_HCP1200_MSMAll_ICAd300_ts2.tar.gz. The 300 here refers to the shape of the matrix, which is the number of *brain regions*. So, row zero, column 1 entry is the connectivity strength between node 0 and node 1. 

## Loading the matrices 

There are two scanning sessions, with a file for each (netmats1.txt,netmats2.txt). The matrices are in a bit of weird format, where it's one big file, and the first dimension is subjects (there are 812), second is connectivity. We reshape it to be a subject by region by region:

```python
import numpy as np
brain_regions = 300
group_matrix = np.loadtxt('HCP_PTN1200_recon2/netmats/3T_HCP1200_MSMAll_d%s_ts2/netmats1.txt'%(brain_regions)).reshape(812,brain_regions,brain_regions)
```

## Save subject-level matrices

A big matrix like this is nice for speed, but part of your job will be tidying up data so mistakes are less likely to be made by others. The kind people the HCP have made matrices at each resolution from 15-300 brain regions. Above you can see I loaded 300. We want a matrix saved for each subject, each session, and each resolution. Write a python function to do this.

## Analyze subject-level matrices

1. Write a function you will (or others can) use to load the subject-level matrices.
2. Please generate two anaylses in python, with a figure for each analysis.
  2a: Relate variance in the connectivity matrices to variance in a metric (or metrics) in the behavioral data
  2B: Your choice. You can add your own twist to 1, or come up with your own
  You can pick a single resulution for the matrix s, or maybe you want to run an analysis that looks across the different numbers of brain regions. You have functional connectivity matrices and behavioral data for each subject. 
3. Save your script (or scripts) as a github repo. We should be able to clone your repo and, given that we have the same HCP Data, run your code to save the subject-level matrices and then generate the figures. Assume we have the basic [Anaconda](https://www.anaconda.com/products/individual) python packages, but feel free to include a dependency outside of that if you want.

### Support or Contact
Having trouble? Email Max Bertolero if there are any technical issues.
