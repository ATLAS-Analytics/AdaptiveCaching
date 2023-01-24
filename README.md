# AdaptiveCaching

There are two places where caching decisions can be made:

* in VP, I could use dataset name to aid decision to make a VP replica or not.
* in Rucio when deciding to prepend or not xcache to the path. Decisions would be based on filename.

Questions to be answered:

* are there datasets that need not be cached at all ?
    Yes. ds=panda.um.* always have cache hit rate: 1.
* why are there differences in caching efficiency between sites ?
* can we optimize VP placements to create more datasets that are more reused ?

## data sources

* per file:
    from xcache g-stream.
    does not have dataset information.

* dataset lookups:
    VP reports which datasets are asked for.

## To do

* find out what are .log.tgz files for. These are almost never accessed twice.
    These are pmerge jobs. They have two input datasets: one containing log files and other one containing root files. Both datasets and files always start with "panda.um.". Files are accessed only once. Fill factor is 100%.
* find out when, why, and where from .lib.tgz files are read.

* make a code that uses Rucio to get file to DS mapping.

## Conclusions

* VP has been set to not create Virtual Placement for datasets starting with "panda.um.". Source for pmerge jobs is always scratch disk. All of the VP queues have scratch disk mounted for r/w. That's why even with no VP replica, local pmerge jobs will run in VP queue. As long as scratch disk is local to the VP queue, these should not use xcache and be visible in gStream. Sites mounting remote scratch disk will still see pmerge jobs in xcache.
