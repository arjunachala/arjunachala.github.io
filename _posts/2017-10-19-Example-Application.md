I take it that you have read my previous posts on ECL and familiarized with the foundational concepts. This time we will develop an ECL application that ingests a raw file, applies transformation on the data and creates a few useful views.

The input file can be downloaded from the [data.gov](http://data.gov) site. This is one of my favorite sites to download and explore publicly available data. The specific file we are interested contains the listing of all the government employees of the city of Chicago, their full names, job title, department and salary. You can download the CSV from [here](https://catalog.data.gov/dataset/current-employee-names-salaries-and-position-titles-840f7). 

Once you download the file, I would recommend that you rename it something that is more user friendly. I renamed my file to "chicago_employees.csv". 

Let us step back a bit and understand the topology of the HPCC cluster that we are going to use. The Thor cluster, the batch oriented analysis engine, is setup with 4 slaves (aka executors) and a single management node. The ROXIE cluster, is setup as a single node. 

Our Architecture:
![](/assets/images/Architecture.001.jpeg)

