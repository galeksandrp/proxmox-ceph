01 create rules

```sh
ceph osd crush rule create-replicated replicated_rule_custom_hdd default osd customhdd
ceph osd crush rule create-replicated replicated_rule_custom_ssd default osd customssd
```

02 apply crushmap on replicas

```sh
wget https://github.com/galeksandrp/proxmox-ceph/blob/master/crushmap.new && ceph osd setcrushmap -i crushmap.new
```

03 change pool rule

```sh
ceph osd pool set cephporn crush_rule replicated_rule_custom_ssd
```

04 build crushmap

```sh
ceph osd getcrushmap -o crushmap.bin && crushtool -d crushmap.bin -o crushmap.txt

crushtool -c crushmap.txt -o crushmap.new && ceph osd setcrushmap -i crushmap.new
```
