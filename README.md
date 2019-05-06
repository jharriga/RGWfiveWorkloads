# RGWfiveWorkloads
Sample workloads for extended RGW test sequence (fill, age, deleteWrite)
Use with RGWtest automation software (https://github.com/jharriga/RGWtest)


# Inventory of workloads:
- fillWorkload.xml   fills cluster to prepare for other workload testing
- hybridSS.xml       1 hour runtime combination of operations: reads, lists, writes, deletes (no cluster growth)
- hybrid2x.xml       12 hour runtime " " " (2x numobjects to simulate aging)
- deleteWrite.xml    2 hour runtime

# Calculating the number of objects to use
Based on a cluster with these parameters:
- 174TB of raw capacity
- 10 buckets/containers
- 1MB object size
- three way replication
the following object count (per container) produces the following fill levels:
- 25% full == 1.45M obj per container, 14.5M total objects
- 50% full == 2.9M objects per container, 29M total objects

Those values were calculated using this formula:
- Raw Capacity of 174TB / 3 = 58TB  (factor in 3way repl)
- 58TB / 10 = 5.8TB   (factor in 10 buckets/containers)
- 5.8TB / 1MB = 5.8M object count capacity per container
- 25% of 5.8M is 1.45M objects per container
- 50% of 5.8M is 2.9M objects per container
