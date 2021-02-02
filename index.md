# Welcome to our coding interview
![Cartoon](./superman-sits-at-a-job-interview-jason-adam-katzenstein-01.png)
We want to get a sense of your ability to do some of more the specific tasks in the PennLINC laboratory. As a data analyst, a lot of your job will be data downloading, wrangling, and processing.

## Download functional connectivity matrices from the Human Connectome Project
### Connectome DB Account
Downloading this data requires making an account on their data downloader website [ConnectomeDB](https://db.humanconnectome.org/app/template/Login.vm).
### Aspera Connect Plug-In
You will also need a plug-in called [Aspera Connect](https://downloads.asperasoft.com/connect2/)
### Download the data! 
This specific dataset is called the [HCP1200 Parcellation + Timeseries + Netmats (1003 Subjects)](https://www.humanconnectome.org/storage/app/media/documentation/s1200/HCP1200-DenseConnectome+PTN+Appendix-July2017.pdf). We (i.e., you) are going to analyze the Netmats. They call them Netmats, which is short for network matrices, which are functional connectivity matrices. Once you are logged in to ConnectomeDB, and you have the plug-in active, you can download the data. It will be on [this page](https://db.humanconnectome.org/data/projects/HCP_1200). Click the boxes in red:
![hcp](./hcp_website.png)


This should take roughly 30 minutes and will require 10GB of space. Please let us know if your personal computing resources do not allow for this.

Unzip the file once it is done downloading. You will see multiple netmats_* files. For example, netmats_3T_HCP1200_MSMAll_ICAd200_ts2.tar.gz. The 200 here refers to the shape of the matrix, which is the number of *brain regions*. We refer to this paramter as the *resolution*. Row zero, column 1 entry is the connectivity strength between node 0 and node 1. Since functional connectivity is not directed, entry 0,1 and entry 1,0 are identical.

You should also grab the [behavioral data](https://db.humanconnectome.org/REST/search/dict/Subject%20Information/results?format=csv&removeDelimitersFromFieldValues=true&restricted=0&project=HCP_1200)

## Loading the matrices 

There are two scanning sessions, with a file for each (netmats1.txt,netmats2.txt). The matrices are in a bit of weird format, where it's one big file, and the first dimension is subjects (there are 812), second is connectivity. We reshape it to be a subject by region by region:

```python
import numpy as np
brain_regions = 200
group_matrix = np.loadtxt("HCP_PTN1200_recon2/netmats/3T_HCP1200_MSMAll_d%s_ts2/netmats1.txt"%(brain_regions)).reshape(812,brain_regions,brain_regions)
```

## Save subject-level matrices

Open up a blank python script.

A big matrix like this is nice for speed, but part of your job will be tidying up data so mistakes are less likely to be made by others. The kind people the HCP have made matrices at each resolution from 15-300 brain regions. Above you can see I loaded 200. We want a matrix saved for each subject, for both the 200 and 300 resolutions. Note that you will have to load netmats1.txt and netmats2.txt, and get the mean for each subject. Save the 200 region matrices in a sub-directory in ../HCP_PTN1200_recon2/netmats/3T_HCP1200_MSMAll_ICAd200_ts2/ and the 300 region matrices in a sub-directory in ../HCP_PTN1200_recon2/netmats/3T_HCP1200_MSMAll_ICAd300_ts2/ 

Write code to do this so it can be run automatically.

You will have to use the 'subjectIDs_recon2.txt' file to do this. These subject IDs are ordered in the same order as the matrix. So:

```
matrix_102006 = group_matrix[0]
matrix_100610 = group_matrix[1]
```

Storage also costs money. Let's go through and delete all the files except the original large *netmats1/2.txt* matrix files for the 200 and 300 resolutions. Write code to do this so it can be run automatically.

Now, in your netmats/ directory, you should have two directories, one for the 200 and one for the 300. Then, within those, you should have your *netmats1/2.txt*, and then another directory that has the subject matrices.

## Analyze subject-level behavior matrices

Clean up the behavior file by loading it as a dataframe with [pandas](https://pandas.pydata.org/pandas-docs/stable/index.html). We want to analyze the "WM_Task_2bk_Acc" and the "Language_Task_Story_Avg_Difficulty_Level" columns. You will also notice there are subjects in the behavior file with no matrices. Let's clean up the dataframe by only keeping the columns "Subject", "WM_Task_2bk_Acc", and "Language_Task_Story_Avg_Difficulty_Level", and then create a new fourth column that denotes if the subject has a matrix with True or False.

Okay, now we have a nice clean dataframe (should be shape 4,812), and subject level matrices, where each matrix is the mean across the two sessions for that subject.

A lot of the time, we want to know if variance in the strength of a connection (i.e., an entry in the matrices you have) correlates with a given behavior. Thus, run a Pearson *r* correlation between each connection in the 200 resolution matrix across subjects, and the "WM_Task_2bk_Acc" column in the dataframe you cleaned up. You should store these correlations in a 200x200 matrix, where matrix[0,1] is the Pearson *r* between the strength of the connection from region 0 to region 1 across subjects and WM_Task_2bk_Acc.

Now do this for 300 regions. Now do this for 200 and 300 regions for the "Language_Task_Story_Avg_Difficulty_Level". 

Plot your 4 matrix results! I use [seaborn.heatmap](https://seaborn.pydata.org/generated/seaborn.heatmap.html). Try to make the figure look as nice as possible. 
  
## GitHub
Save your script (or scripts) as a github repo. We should be able to clone your repo and, given that we have the same HCP Data, run your code to save the subject-level matrices, delete the data we don't need, and then generate the results and figure. Assume we have the basic [Anaconda](https://www.anaconda.com/products/individual) python packages, but feel free to include a dependency outside of that if you want.

### Support or Contact
Having trouble? Email whomever has been facilitating your interview if there are any technical issues. If you would prefer interview in a language other than python, please reach out to us.
