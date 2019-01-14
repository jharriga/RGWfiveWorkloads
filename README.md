# RGWfiveWorkloads
automation for extended RGW test sequence (fill, age, deleteWrite)
Based on RGWtest automation software (https://github.com/jharriga/RGWtest)

Uses COSbench to issue RGW operations.
All scripts create timestamped logfiles in RESULTS directory. Logfiles contain:
- OSD system resource usage statistics (eg. load average, ceph-osd process cpu and memory)
- Garbage collection statistics
- COSbench start and stop timestamps

# Inventory of scripts:
- writeXML.sh       writes the COSbench XML workload files from the Templates (found in 'XMLtemplates' dir)
- resetRGW.sh       resets the RGW env. Deletes pools and creates new user. Inserts passwd into XML files
- fillCluster.sh    invokes fillWorkload.xml (fills the cluster with numOBJ and OBJsizes as spec'd in vars.shinc)
- runIOworkload.sh  invokes ioWorkload.xml (main test script. Executes IOworkload and logs results in RESULTS dir)
- copyPasswd.sh     inserts the RGW password into the COSbench XML workload files
- Utils/poll.sh     called by runIOworkload.sh to periodically log statistics (garbage collection, loadAvg, ps)

NOTE: host IPaddresses and ceph login credentials in vars.shinc will need to be replaced for your cluster

# RUN PROCEDURE:
  - edit vars.shinc
  - writeXML.sh        <-- afterwards you must run either 'resetRGW.sh' or 'copyPasswd.sh'
  - resetRGW.sh        <-- creates pools
  - fillCluster.sh     <-- fills cluster (Workload #1)
  - hybridSS initial   <-- initial measurement run (Workload #2)
  - 48hr hybrid age    <-- cluster aging run (Workload #3)
  - hybridSS aged      <-- aged measurement run (Workload #4)
  - 6hr delete-write   <-- delete-write (Workload #5)

# Calculating the number of objects to use
Based on a cluster with these parameters:
- 174TB of raw capacity
- 10 buckets/containers
- 1MB objext size
- three way replication
the following object count (per container) produces the following fill levels:
- 25% full == 1.45M obj per container, 14.5M total objects
- 50% full == 2.9M objects per container, 29M total objects

Those values were calculated using this formula:
- Raw Capacity of 174TB / 3 = 58TB  (factor in 3way repl)
- 58TB / 10 = 5.8TB   (factor in 10 containers)
- 5.8TB / 1MB = 5.8M object count capacity per container
- 25% of 5.8M is 1.45M objects per container
- 50% of 5.8M is 2.9M objects per container
