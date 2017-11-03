I take it that you have read my previous posts on ECL and familiarized with the foundational concepts. In the next series of posts, we will develop an ECL application that ingests a raw file, applies the transformation on the data and creates a few useful views.

Part 1 of the post will demonstrate the following:

1. Select an interesting open dataset
1. Introduction to the topology that we are going to use to develop the example
1. A very brief introduction to the HPCC Management console called ECL Watch
1. Uploading a file
1. Importing the file onto the Thor cluster
1. Understanding the distribution of the file parts of the imported file for the current topology

## The Dataset

The input file can be downloaded from the [data.gov](http://data.gov) site. This is one of my favorite sites to download and explore publicly available data. The specific file we are interested contains the listing of all the government employees of the city of Chicago, their full names, job title, department, and salary. You can download the CSV from [here](https://catalog.data.gov/dataset/current-employee-names-salaries-and-position-titles-840f7). 

Once the file is downloaded, I would recommend that you rename it something that is more user-friendly. I renamed my file to "chicago_employees.csv". 

## Topology

Let us take a step back and understand the topology of the HPCC cluster that we are going to use. The Thor cluster, the batch-oriented analysis engine, is configured with one Master process and four Slave processes. The Roxie is setup as a single process. All of this, including the management nodes like Dali, run on a single AWS instance type. In a more performing setup, each of the components will be configured to run on its own instance type. I chose to run all of the components on a single instance, one, to illustrate the flexibility of the architecture, and two, to demonstrate it in an easily reproducible test environment for users to try. The test can be replicated on a single laptop or desktop. 

Our Architecture:
![](/assets/images/Slide5.PNG)

## ECL Watch Introduction

![](/assets/images/slide1.png)

The ECL Watch is HPCC's management console to view the performance of the cluster, the execution of jobs, managing files, deploying queries and much more. In this early introduction of ECL Watch, the goal is to understand the parts that enable us to start developing ECL programs quickly. Hence, we only familiarize ourselves with the views that display the execution output (Execute View), the file handling (File View) and cluster topology (System View).  

ECL Watch - Cluster Topology:

![](/assets/images/slide2.png)

The cluster topology view demonstrates the layout of our cluster. In this example, we see that Thor consists of five nodes - one Thor Master and four Thor Slaves. The IP addresses are identical because the cluster is configured on a single physical system. This view is used by the operations team to validate the setup of the cluster (called pre-flight), in addition to using it as a monitoring tool.    

ECL Watch - File Handling: 

Landing Zone

![](/assets/images/slide3.png)

This view contains the list of landing zones available to the cluster and the files on each of the landing zones. You can use this view to upload your own files and then import it into the cluster. The import process in HPCC is called as **Spray**. 

Logical Files

![](/assets/images/slide4.png)

Once a file is imported into Thor using the Spray process, a logical file entry is created in Dali, and the details of the file are reflected in the Logical File view. For example, you can click on a file entry and view how its parts are distributed across the slave nodes. 

## Uploading a file onto the HPCC landing zone

Thus far in this post, we have introduced the topology of the cluster and an overview of ECL Watch as it pertains to the example application that we are developing. It is time to now upload the file we downloaded on to the landing zone and importing it into the Thor cluster.

Uploading the file

In ECL Watch, click on the File View (1), next, click on the upload view (2), next, select the drop zone row (3) and finally click on the upload button (4) to select the file from your download location. After the upload is complete, the file chicago_employees.csv should appear under the landing zone as shown below. 

![](/assets/images/slide8.png)

## Importing the uploaded onto the Thor cluster

While still on the landing zone view, select the file chicago_employees.csv (1), click on the Spray delimited (CSV=delimited) button (2), provide a target scope(aka namespace) for the file (3), change the target name by removing the ".csv" (4), click on the overwrite (5) option and finally Spray the file (6).  

![](/assets/images/slide9.png)
![](/assets/images/slide10.png)

The Spray process creates a work unit (DLL) for the DFU Server which is responsible for identifying the file parts for each file and distributing the parts to the slave processing nodes (via the File Service). The work unit, in turn, provides the critical stats on the progress of the Spray operation that can be accessed in ECL Watch. Click on the File View (1), click on the work units view (2), and in the select the work unit that you are interested in and click on the ID. 

![](/assets/images/slide11.png)

The detailed view where you can observe the following key details.

1. Did the Spray operation finish or fail or other conditions
1. The percent complete indicates the progress of the operation. Large files can take some time to complete. 
1. The messages can indicate errors when the operation fails

![](/assets/images/slide12.png)

## Understanding the distribution of the file parts of the imported file for the current topology

As a final step of the file import process, let us observe how Thor treats the imported file. As shown below, click on the File View (1), click on the logical files (2), click on the filter set to list only the files you are interested in (3), enter the filter criteria in the name (4), click apply to execute the filter (5) and finally using the filtered list, click on the logical file we Sprayed. 

![](/assets/images/slide13.png)

The interesting information on this screen:

1. Directory - Location where a file part is stored on the server. I would advise you to access the location and review the files if you have access to the server. It helps in the broader understanding of the HPCC architecture.
1. Path Mask - The pattern used to store the file parts. For example, a file part that is processed by the Thor Slave Process 1 will be called "chicago_employees_1_of_4". Since there are 4 Slave processes in our configuration, the DFU Server will automatically divide the file into 4 parts and distribute it to the respective server nodes. In our example, all the file parts will reside on the same server as we are using a single server which hosts the 4 Slave processes.
1. File Size - Size of the File in bytes


![](/assets/images/slide14.png)

The other interesting is on the File Parts tab that shows the distribution of the file partitions.

![](/assets/images/slide15.png)

This concludes Part 1 of the series. I hope we have achieved the goal of understanding the architecture of the system to develop our example and the process of ingesting a file into a Thor cluster. 