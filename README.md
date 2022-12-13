# Advanced-Database-Management-System
This is Advanced Database Management System studies


## The Physical Database System 

1. Architecture 
2. File & Indexes 
3. A Cost Model 
4. Secondary Memory Hardware 
5. File, Page, & Record Formats 
6. The Managers (Layers) 

## Introduction 

- DBMS stores vast quantities of data. 
- Data is stored on external storage devices called disks and fetched into main memory as needed for processing. 
- The page is the unit of information read from or written to an I/O disk. (In DBMS, a page may have a size of, say 4KB or 8KB). 

## Overview of the DBMS Architecture 

![image](https://user-images.githubusercontent.com/79100627/207213694-c686e8ad-da83-4728-8399-a953180fc625.png)


1. Disk Space manager manages persistent data 
2. Bufferpool manager stages pages from external storage to the main memory buffer pool
3. The file and access methods "API" / layer makes calls to the buffer-pool manager. 

## File & Indexes 

### File Organization

- Method of arranging a file of records on external sotrage 
- A record id (RID) is sufficient to locate physically the record -> it points to the actually memory address. 

### Indexes 
- An Index is an external data structure that allows one to find records by rid with given data values over the index's search key


### Type of Files 

- Heap File: is the random order file that suitable when typical access is a file scan to retrieve all records.
- Sorted File: Best if records must be retrieved in some order, or if only a range of records is needed. 
- Index File: A data structure to organize records to optimize certain kinds of retrieval operations. Speed up searches for a subset of records based on values in certain (Search Key value) fields

### Data Entries in an Index

Data Entry is the records stored in an index file
- Given Search Key value with K, provide for efficient retrieval of all data entries k* with value k. 

![image](https://user-images.githubusercontent.com/79100627/207219796-12962628-3da9-48e8-9290-d3ae0ba6565e.png)

Some of questions might be ask: What is the difference between Data Records and Data Entries?

- Data records is a complete row of your database stored somewhere in the file system.
- Data entries only stores the columns that you specificed in the index. Data entries usually keep a pointer to the data record in the file system where the record resides

## Alternatives 

In data entry, we can store following types:

1. The full data records itself with the key value k
2. <K, rid of data record with search key value k> 
3. <K, the list of rid's of data records with search key k>

### Alt #1 
- Index structure is the file organization for the data records (instead of a Heap file or sorted file). 
- At most one index on a given collection of data reocrds can use Alt#1 
  - Otherwise, data records are duplicated, leading to redundant storage and potential inconsistency.
- If data records are large, this implies that the size of auxiliary information in the index is also large. 
- This means that Whole data records (Not partial) will be our indexes 

### Alt2 & 3 
- Data entries are typically much smaller than the data records themselves.
- Since it only stores the partial columns.

### Comparison 
- Alt#2,Alt#3 are better than Alt#1 when the data records are large.
- Alt#3 is more compact than Alt#2 (Why? because it stores the list of rids) 
- However, Alt#3 leads to variable sized data entries even when search keys are fixed length! (of course since it stores the list of rids, you do not know how many rid's will be in the index search key k). 

## Clustered vs Unclustered Index

Alt#1 implies the index is clustered-> A file can be clustered on (at most) one search key. (Remember that Alt#1 is the whole data record)

![image](https://user-images.githubusercontent.com/79100627/207221656-0d75325b-4ea4-4957-8f5a-fcf8c12d7504.png)

As you can see clustered data should be sorted data file by Data Entry keys (Which also Index key). However, unclustered can be distributed all over the place. 

## Types of Indexes
- B+- Tree
- Hash

## Cost Model

- Ignore CPU costs for simplicity 
- Measuring Number of page I/O's ignores gains by prefetching a sequence of pages. 
- I/O cost is only approximated 
- Average case analysis based on simplistic assumptions. 

### Cost Model Variables 

- B: The number of data pages
- R: The number of records per page 
- D: Average time to read or write a page (I/O) 

### Operation to Compare 
- Scan (fetch all records from disk)
- Equality search 
- Range Selection
- Insert a record
- Delete a record 

### Cost of Operations 
![image](https://user-images.githubusercontent.com/79100627/207222754-79330234-7782-4904-b11f-fb076ca56506.png)


## Overview of Index and Cost Model 

- If selection queries are frequent, sorting the file or building index is important.
  - Hash based indexes only good for equality search.
  - Sorted files and tree based indexes best for range search; also good for equality search (But should notice that Cluster vs Uncluster. Cluster requires only one page to fetch, Uncluster requires to fetch whole records per Data Entries) 
- Index is a collection of data entries plus a way to quickly find entries with given key values. 


## Secondary Memory Hardware 

- DBMS stores information on "Hard" disks. 
- This has major implications for DBMS design
  - Read: Transfer data from disk to main memory 
  - Write: Transfer data from RAM to disk 
  - Both are high-cost operations, relative to in memory operations

## Disks (HDD)

- Secondary storage device of choice still
- Main advantage over tapes: random access vs sequential
- Data is stored and retrieved in units called disk blocks or pages
- Unlike RAM, time to retreive a disk page varies depending upon location on disk
  - Therefore, relative placement of pages on disk has major impact on DBMS performance 

![image](https://user-images.githubusercontent.com/79100627/207223973-eed01196-c985-4923-8a54-1fca8f6f7a3c.png)

- Platter: Circular magnetic plate that is used for storing data in a hard disk. Each plater has two working surfaces. These surfaces of the platters hold the recorded data. 
- Spindle: Motor that holds platter 
- Tracks: working surface of the platter is divided into a number of concentric rings, which are called tracks. 
- Cylinder: Collection of all tracks that are the same distance, from the edge of the platter (Cylinder) 
- Read-Write heads: The read/write arms that can read the data from platter. 

![image](https://user-images.githubusercontent.com/79100627/207224509-0f455141-fe57-4e15-aa0c-ddfb1e2fd0bd.png)

## Platter 

- Each platter is broken into thousands of tracks. 
- Each track is further broken down into smaller units called sectors. As sector is the basic unit of data storage on a hard disk, each track has the same number of sectors 
- Each sectors are often grouped together to form clusters. A cluster is the smallest possible unit of storage on a hard disk. If contigious clusters are not available, the data is written elsewhere on the disk. (FRAGMENTED) 

![image](https://user-images.githubusercontent.com/79100627/207225093-b2598ad8-f73b-44fb-8ab7-872e9f670a94.png)


## Accessing a disk page 

Time to access (read/write) a disk page. 
- Seek Time: moving arms to position disk head on tracks 
- Rotational Delay: waiting for block to rotate under head 
- Transfer time: Actually moving data to / from disk surface 

## Seek Time 

The time required by the read-write head assembly to move/reach/ find the desire track move to one track

![image](https://user-images.githubusercontent.com/79100627/207226165-5debd73e-e65a-4a12-9999-a87030996396.png)

- If we are looking for Track-2 Platter-1 only the top of the arm activated and other ones are deactivated. This whole time process is the seek time. 
- Seek time varies from about 1 to 20m sec 

## Roational Delay

![image](https://user-images.githubusercontent.com/79100627/207226402-35ddd7c3-a426-4d39-8695-cbafcb7e98bd.png)

- Since after we find the right platter, we have to access the data by rotating the disk.
- Rotational delay varies from 0 to 10mssec 

## Arrange pages on disk carefully 

- Next block 
  - Blocks on same track, followed by 
  - Blocks on same cylinder, followed by 
  - Blocks on adjacent cylinder 

Ideally, blocks in a file should be arranged sequentially on disk (by next) to minimize seek and rotational delay. 

For a sequential scan, prefteching several pages at a time is a big win. 

## SSD: Solid State Drives 

- Random access but also by block (page) 
- Lots of erros 
  - Write of one block may cause an error of nearby block
  - Only be written a limited number of times 
- Sequential writes improve throughput 

### SSD vs HDD 

1. Random Access Time : SSD Wins
2. Data transfer rates: SSD is 100 - 600 mb, HDD is 100 to 200mb, once the head is psotitioned
3. Reliability: SSD is vulnerable to power outages (Critically fail), Risk of sudden, catastropic data loss is higher, "wear out" under long-term, heavy write load, but higher end SSD now have better reliability than HDD. HDD: average failture rate is 6 years, life expectancy is 9-11 years. 

## RAID 

Redundant Array of Independent Disks 
- Disk array: arrangement of multiple disks that provides the abstraction of a single, large disk. 
- Can increase performance and reliability 
- Provides
  - Data striping: Data is partitioned in stripes 
  - Redundancy: can survive a disk (or two) failing 

## RAID Levels 

### Level 0 
![image](https://user-images.githubusercontent.com/79100627/207229173-ee68f91b-8c79-4939-a8b8-910d9a12bacb.png)

- If one disk dies, system is broken. 
- Speeding wise, it could be fast (We use two disks) 

### Level 1 
![image](https://user-images.githubusercontent.com/79100627/207229367-2ac85c2d-93f1-45f5-a18b-ee44dfd20e30.png)

- Eventhough one disk fails it can survives 

### Level 5 

![image](https://user-images.githubusercontent.com/79100627/207229519-572ab757-5838-474e-8a01-d5f1e31db3c2.png)

- Parity is used rebuild the data due to disk failure (Precious information) 
- The equivalent of an entire disk is used to store parity 
- If 4 terabytes, we can only store 3 terabytes 
- Only can handle one disk failure, if two broken, still break the whole system 

### Level 1+0
![image](https://user-images.githubusercontent.com/79100627/207229724-4c0815ff-ca29-4580-a1b6-bac06f077da5.png)

- Benefits from the fault tolerance of RAID 1 and the speed of RAID 0 
- Can only use 50% of for data storage 

### Level 6 

![image](https://user-images.githubusercontent.com/79100627/207230012-b739122b-b50b-4e43-bfa8-e2495f6f52fe.png)

- Can handle two disk failure 
- Store two parities equivalently
- Only 50% can be used. Write is much slower than level 5 

## File, Page & Record Formats 

- Rid = <Pag-ID, slot#>

## Record Formats: Fixed Length 

![image](https://user-images.githubusercontent.com/79100627/207230405-4adc493a-d59b-4f5c-80ff-0a644f35de37.png)

Information about field types same for all records in a file. This is stored in the system catalog 

Finding ith field requires a scan of the record 


