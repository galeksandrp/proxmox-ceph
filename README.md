# one time

- on 2/1 pools in degraded state (when only single osd available)
ceph will severely limit write speed
(and read speed as well, but not that much)
until i find truckallion options to change this behavior
this is what you get
```sh
ceph config set global mon_allow_pool_size_one true
ceph osd pool set data_pool size 1 --yes-i-really-mean-it
```

- for 20+ pools on 3hosts x 1osd
default value is a joke
```sh
ceph config set global mon_max_pg_per_osd 25000
```

- proxmox bitching
```sh
ceph config set global osd_pool_default_min_size 1
ceph config set global osd_pool_default_size 1
```

- proxmox fucking ignores this
```sh
ceph config set global mon_pg_autoscale_mode warn
```

# everyday

- set osd device class
```
ceph osd crush rm-device-class osd.4
ceph osd crush set-device-class customssd osd.4
```

- set osd read priority
```sh
ceph osd primary-affinity osd.0 0
```

- create rules
```sh
ceph osd crush rule create-replicated replicated_rule_custom_osd default osd
ceph osd crush rule create-replicated replicated_rule_custom_hdd default osd customhdd
ceph osd crush rule create-replicated replicated_rule_custom_ssd default osd customssd
```

- change pool rule
```sh
ceph osd pool set windows crush_rule replicated_rule_custom_hdd
```

# dangerous things

- build crushmap
```sh
ceph osd getcrushmap -o crushmap.bin && crushtool -d crushmap.bin -o crushmap.txt

crushtool -c crushmap.txt -o crushmap.new && ceph osd setcrushmap -i crushmap.new
```

- apply crushmap on replicas
```sh
wget https://github.com/galeksandrp/proxmox-ceph/blob/master/crushmap.new && ceph osd setcrushmap -i crushmap.new
```
