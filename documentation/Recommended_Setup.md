title:      Recommended System Setup
desc:       This guide offers a minimum recommended system setup for execution and development of ADORe.
date:       ${DOC_DATETIME}
version:    ${DOC_VERSION}
template:   document
nav:        Recommended Systems Setup __2__
percent:    100
authors:    opensource-ts@dlr.de

# Recommended System Setup 
The following article offers a recommended and minimum system configurations
for build, execution, and development of adore.
|     | Minimum | Recommended | Comments |
| --- | --- | --- | --- |
| **Storage** | ~18 GB | \> 50 GB transient |     |
| **Memory** | 6 GB to build | 32 GB |   Requires a minimum of 6 GB to build adore_if_ros  |
| **Network** | 40 Mbit download | 80 Mbit Download | First time builds of ADORe require significant data fetching from docker and ubuntu central repositories |
| **Processor** | 4 logical cores | 32 logical cores | More cores provides faster build times, requires a minimum of 4 cores to build |
| **Operating System** | Ubuntu 20.04 | Ubuntu 22.04 |     |


## Build Scenario
Give the following system configuration:
- **NO cache** 
- 6 GB ram
- 4 VCPUs
- 50 Mbit download network connectivity

```bash
$ docker system df
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE                                                                                                     
Images          0         0         0B        0B                                                                                                              
Containers      0         0         0B        0B                                                                                                              
Local Volumes   0         0         0B        0B                                                                                                              
Build Cache     0         0         0B        0B 
```


