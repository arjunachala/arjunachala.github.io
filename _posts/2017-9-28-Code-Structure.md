After reading the ECL Foundation topic, you might be wondering if the topic was a bit advanced to a beginner ECL developer. I compare it to attempting to learn Java without having a good foundation knowledge about OO concepts such as encapsulation, inheritance and polymorphism. Without understanding the data flow concepts, ECL programmers will make little progress in mastering the language.

Speaking of encapsulation, ECL provides a few techniques of its own that resemble encapsulation.

## MODULE

```ECL
EXPORT schema := MODULE
//Your data structure definitions go here

//Your code that works with the above structures go here
END;
```

#### NOTE:  *As a general rule, always use a module to encapsulate your code*

Module's are typically declared within a file resembling the module name. For example a 'schema' module is declared in a file called schema.ecl

## RECORD

```ECL
employeeRecord := RECORD
    INTEGER id,
    STRING50 firstName,
    STRING50 lastName
END;
```

Mostly everything that we do with ECL and HPCC is to work with data. Data that starts out as raw input files that then is converted to a cleaner and analyzed version. For example, you can start with employee data from multiple sources (HR systems, finance systems etc.), and consolidate them into a single standard ***DATASET***. A DATASET that can be queried for salary, attrition etc. 

A RECORD defines the structure of this input or processed dataset. 

## DATASET

A DATASET is a fundamental data structure in HPCC and ECL. It is an immutable collection of data records. Each DATASET is divided into logical partitions, that could exist on different nodes. For those of you who are familiar with RDDs in Spark, an ECL DATASET is analogous to a Spark RDD. A DATASET can process data in-memory or spill to disk if the data exceeds the available memory. 

```ECL
employeeDS := DATASET(filename, employeeRecord);
```

When you bring all these elements together, it is very important to understand a few of these key building blocks before diving too deep into solving a problem with ECL. These building blocks will help you organize your code for maximum reuse and efficiency. 

Many of us come from a background of database technologies related to RDBMS, where tables, relationships and keys are all organized into a single logical unit called a schema. This makes it easy for developers to understand the domain, relationships and data without a significant R&D effort. In ECL, you can accomplish similar results by creating a module to host all the dataset declarations.

Expanding on the examples above, we can consolidate it to:


```ECL
EXPORT hr_schema := MODULE

    EXPORT employeeFileName = 'employee_file_name';
    
    EXPORT employeeRecord := RECORD
        INTEGER employeeId,
        STRING50 firstName,
        STRING50 lastName
    END;

    EXPORT employeeDS := DATASET(employeeFileName, employeeRecord);

    EXPORT salaryFileName = 'salary_file_name';
    
    EXPORT salaryRecord := RECORD
        INTEGER employeeId,
        REAL base,
        REAL bonus,
        REAL health,
        INTEGER salaryYear
    END;

    EXPORT salaryDS := DATASET(salaryFileName, salaryRecord);

END;
```

This module can be accessed from another ECL file:

```ECL
import hr_schema;

OUTPUT(hr_schema.employeeDS);
OUTPUT(hr_schema.sarayDS(base > 100000));
```

As you can see, ECL provides an elegant way of expressing what you want to do once you have the boilerplate (i.e. schema) part coded. If you can cleanly separate the schema from the code, you will make development easy, elegant and reduce any undesired side effects.

