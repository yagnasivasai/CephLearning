
## Multisite vs DisasterRecovery

    Multisite is two way synchronization. whatever happens in site A will be replicated in site B

    Disaster Recovery is one way synchronization

    ceph bynature is synchronous. It will always show the slowest link in ceph cluster.

    Bandwidth is too slow and Latency is very high then ceph cluster will be sluggish. Then Multisite will not work properly

    When you are doing asynchronous ( means that data will be transferred to other with time delay) then you are eliminated to use only s3 storage or Block storage.

    
