The foundation of the ECL Language is based on a data flow execution paradigm. This is similar to how SQL Engines optimize execution of the statement based on an optimized explain plan.

Three key points to remember when programming in ECL 

## 1. Unused code is never compiled, and hence not executed

```ecl
employeeDS := DATASET(...);
salaryDS := DATASET(...);
OUTPUT(employeeDS);
```


For this code example, the ECL compiler creates an execution graph:

Read Employee DS -> OUTPUT(read data);

<div class="mermaid">
graph TD;
    A[Read Employee DS]-->B[OUTPUT read employee data];
</div> 

NOTE: The salaryDS part is completely ignored as it is never used


## 2. Execution paths are automatically parallelized 

```ecl
employeeDS := DATASET(...);
salaryDS := DATASET(...);
OUTPUT(employeeDS);
OUTPUT(salaryDS);
```


For this code example, the ECL compiler creates two separate (parallel) paths in the execution graph:

<div class="mermaid">
graph TD;
    A[Read Employee DS]-->B[OUTPUT read employee data];
    C[Read Salary DS]-->D[OUTPUT read salary data];
</div> 

NOTE: The ECL compiler realizes that the OUTPUTs are independent of each other and hence can be executed in parallel

## 3. Execution path is automatically sequenced when there is a dependency

```ecl
employeeDS := DATASET(...);
salaryDS := DATASET(...);
OUTPUT(employeeDS);
joinedDS := JOIN(employeeDS, salaryDS, 
                 LEFT.employeeId=RIGHT.employeeId);
OUTPUT(joinedDS);
```


<div class="mermaid">
graph TD;
    A[Read Employee DS]-->C[JOIN];
    B[Read Salary DS]-->C
    C-->D[OUTPUT joined DS];
</div> 


NOTE: The ECL compiler realizes that the JOIN depends on both the datasets. Hence, the path starts out as parallel, and is then sequenced into the JOIN.

The key takeaway is that the ECL compiler optimizes the code based on the flow of the data through the program and NOT by the sequence of steps coded. Now, this contrasts with Java where the execution of statements is based on how the developer codes it. 