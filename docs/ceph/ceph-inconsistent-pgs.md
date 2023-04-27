---
hide:
  - toc
---

# CEPH PGS INCONSISTENT

Sometimes you come across issues with the ceph placement groups where you see inconsistent pg errors. This normally indicates a potential issue with the osd.

The below steps will help fix the error and identify the osd / disk that may be erroring.

Run a ceph status command to verify the error. Here you can see 1 pg is inconsistent.

```bash
[root@ceph_monitor_1 ~]# ceph -s
    cluster 46268b52-5b0e-11e6-8fac-525400e2a6d8
     health HEALTH_ERR
            1 pgs inconsistent
            1 scrub errors
     monmap e1: 3 mons at {ceph_monitor_1=10.10.1.1:6789/0,ceph_monitor_2=10.10.1.2:6789/0,ceph_monitor_3=10.10.1.3:6789/0}
            election epoch 1418, quorum 0,1,2 ceph_monitor_2,ceph_monitor_1,ceph_monitor_3
     osdmap e262059: 568 osds: 568 up, 568 in
            flags sortbitwise,require_jewel_osds
      pgmap v105732057: 25664 pgs, 7 pools, 113 TB data, 33762 kobjects
            346 TB used, 415 TB / 762 TB avail
               25660 active+clean
                   3 active+clean+scrubbing+deep
                   1 active+clean+inconsistent
  client io 8657 kB/s rd, 34724 kB/s wr, 1373 op/s rd, 4926 op/s wr
```

To identify which pg has the error run ‘ceph health detail’.
In this case we can see that pg 5.291b is the pg that has the issue. We can also identify the OSDs that the pg is part of. [130,496,354]

```bash
[root@ceph_monitor_1 ~]# ceph health detail
HEALTH_ERR 1 pgs inconsistent; 1 scrub errors; pool metrics has many more objects per pg than average (too few pgs?)
pg 5.291b is active+clean+inconsistent, acting [130,496,354]
1 scrub errors
pool metrics objects per pg (17848) is more than 13.2502 times cluster average (1347)
```

We issue the command to repair the pg ‘ ceph pg repair <pg name>‘
NOTE: The output will identify the affected OSD that is being repaired.

```bash
[root@ceph_monitor_1 ~]# ceph pg repair 5.291b
instructing pg 5.291b on osd.130 to repair
```

If you have a second session running on the ceph monitor, you can see the logs where the pg is being repaired.
This is done by issuing the ‘ceph health’ or ‘ceph -h’ command as below.

```bash
[root@ceph_monitor_1 ~]# ceph -w
    cluster 46268b52-5b0e-11e6-8fac-525400e2a6d8
     health HEALTH_ERR
            1 pgs inconsistent
            1 scrub errors
     monmap e1: 3 mons at {ceph_monitor_1=10.10.1.1:6789/0,ceph_monitor_2=10.10.1.2:6789/0,ceph_monitor_3=10.10.1.3:6789/0}
            election epoch 1418, quorum 0,1,2 ceph_monitor_2,ceph_monitor_1,ceph_monitor_3
     osdmap e262059: 568 osds: 568 up, 568 in
            flags sortbitwise,require_jewel_osds
      pgmap v105732231: 25664 pgs, 7 pools, 113 TB data, 33762 kobjects
            346 TB used, 415 TB / 762 TB avail
               25660 active+clean
                   3 active+clean+scrubbing+deep
                   1 active+clean+inconsistent
  client io 8482 kB/s rd, 112 MB/s wr, 334 op/s rd, 5012 op/s wr

<..>
2020-02-05 10:57:11.552607 mon.1 [INF] from='client.? 10.10.1.1:0/4080461660' entity='client.admin' cmd=[{"prefix": "pg repair", "pgid": "5.291b"}]: dispatch
2020-02-05 10:57:11.994610 mon.0 [INF] pgmap v105732235: 25664 pgs: 3 active+clean+scrubbing+deep, 1 active+clean+inconsistent, 25660 active+clean; 113 TB data, 346 TB used, 415 TB / 762 TB avail; 1995 kB/s rd, 42833 kB/s wr, 2095 op/s
2020-02-05 10:57:12.135573 osd.130 [INF] 5.291b repair starts
<..>
2020-02-05 10:57:44.379026 osd.130 [ERR] 5.291b shard 130: soid 5:d895133f:::rbd_data.b2d42a650cc047.0000000000000f58:head candidate had a read error
<..>
2020-02-05 10:58:18.635846 osd.130 [ERR] 5.291b repair 0 missing, 1 inconsistent objects
2020-02-05 10:58:18.637245 osd.130 [ERR] 5.291b repair 1 errors, 1 fixed
```

To identify which storage node the affected OSD is part of run the  ```ceph osd find <OSD>``` command. 
Here I have narrowed it down to just the host name.

```bash
[root@ceph_monitor_1 ~]# ceph osd find 130 | grep host
        "host": "STOR00010",
```


