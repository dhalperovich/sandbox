# DCOPS-3161
### Brief Description

| Heading      | Description                                                  |
| ------------ | ------------------------------------------------------------ |
| **Summary**  | Chassis swap for OPS05164                                    |
| **Ticket**   | [DCOPS-3161](https://saucedev.atlassian.net/browse/DCOPS-3161) |
| **Author**   | David Halperovich                                            |
| **Impact**   | Medium                                                       |
| **Risk**     | Medium                                                       |
| **Rollback** | Second chassis swap                                          |
| **Devices**  | OPS05164                                                     |
| **Duration** | 30 minutes                                                   |

## Executive Summary

OPS05164 reported about 2 missing DIMMs. We would like to swap the chassis with a spare hardware, 

to get the server back to work with full amount of healthy DIMMs

## Impact

Medium impacts, since most of the VMs running on this chalet are in HA mode.

The only one VM that doesn't have any pairing is librenms, and it can go down for 30 minutes, 

without affecting customers

## Risk

Medium risk - DCOPS team will verify that the spare chassis has the same CPU/Memory type as the current

one. If that is not the case, we most probably won't proceed with the maintenance.

## Plan

1. DCOPS will mark the network cable with tags corresponding to the ports on the chassis the cables were plugged in.
2. IAM and Cloud representatives will remove their systems from respective clusters.
3. OPS will turn off all the VMS.
4. OPS will shutdown the chassis and will turn on the chassis locator via IPMI.
5. DCOPS will unplug the power cables and proceed with the chassis swap.
6. As soon as the server is powered on, OPS will connect to the IPMI and verify the OS is up.
7. OPS will ssh to the chalet and verify that all VMs are running. In case any VM is not running - OPS will turn those on.
8. IAM and Cloud people will put their VMs back in service.

### List of VMs on the chalet

* dj-config-1.prod.sjc3.saucelabs.net
* foreman-proxy.mgmt.prod.sjc3.saucelabs.net
* haproxy-1.prod.sjc3.saucelabs.net
* k8s-lb-dcproxy1.prod.sjc3.saucelabs.net
* k8s-lb-ondemand3.prod.sjc3.saucelabs.net
* librenms1-sjc3.inet.saucelabs.net
* miso11.prod.sjc3.saucelabs.net

### Preparation

* Have the spare chassis IPMI configured and checked for access.
* Talk to IAM and Cloud to have one of their teams assist in this maintenance.

### Implementation

* OPS:

```
Start of maintenance:
Turn off Nagios notifications.
After IAM and Cloud have turned off their applications - turn off the VMs
Turn off the chassis from IPMI.
End maintenance:
Connect to the IPMI and check the OS is on.
Check all VMs are on, if not - turn them on.
After all the applications are back running, check Nagios if all is green and turn back notifications.
```

* IAM and Cloud:

```
Start maintenance - remove the system from cluster and turn application off 
End maintenance - turn application on and put back the system into cluster
```

* DCOPs:

```
Mark the network cables, check the chassis is down, unplug all cables, swap chassis.
Plug back all cables, turn on the chassis.
```



### Verification

* Verification action:

```
IAM, Cloud and OPs will verify that the applications are up and serving.
OPS will check the status pages of HAproxies
```

## Rollback

* In case the swapped chassis doesn't work, swap to the second spare chassis
