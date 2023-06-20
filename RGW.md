radosgw-admin realm create --rgw-realm=movies --default
radosgw-admin zonegroup create --rgw-zonegroup=us --endpoints=http://rgw1:80 --rgw-realm=movies --master --default

radosgw-admin zone create --rgw-zonegroup=us --rgw-zone=us-east \
                            --master --default \
                            --endpoints={http://fqdn}[,{http://fqdn}]


radosgw-admin zonegroup delete --rgw-zonegroup=default --rgw-zone=default
radosgw-admin period update --commit
radosgw-admin zone delete --rgw-zone=default
radosgw-admin period update --commit
radosgw-admin zonegroup delete --rgw-zonegroup=default
radosgw-admin period update --commit


ceph osd pool rm default.rgw.control default.rgw.control --yes-i-really-really-mean-it
ceph osd pool rm default.rgw.data.root default.rgw.data.root --yes-i-really-really-mean-it
ceph osd pool rm default.rgw.gc default.rgw.gc --yes-i-really-really-mean-it
ceph osd pool rm default.rgw.log default.rgw.log --yes-i-really-really-mean-it
ceph osd pool rm default.rgw.users.uid default.rgw.users.uid --yes-i-really-really-mean-it

radosgw-admin user create --uid="synchronization-user" --display-name="Synchronization User" --system

radosgw-admin zone modify --rgw-zone={zone-name} --access-key={access-key} --secret={secret}
radosgw-admin period update --commit

radosgw-admin period update --commit

[client.rgw.rgw1]
host = rgw1
rgw frontends = "civetweb port=80"
rgw_zone=us-east


systemctl start ceph-radosgw@rgw.`hostname -s`
systemctl enable ceph-radosgw@rgw.`hostname -s`


radosgw-admin realm create --default --rgw-realm=gold

radosgw-admin zonegroup create --rgw-zonegroup=eu --master --default --endpoints=http://192.168.233.136:8000

radosgw-admin zone create --rgw-zone=eu-east --master --rgw-zonegroup=eu --endpoints=http://192.168.233.136:8000 --default

radosgw-admin user create --uid="synchronization-user" --display-name="Synchronization User" --system

            "user": "synchronization-user",
            "access_key": "MIW5HWIXFQ71AP9MFB9M",
            "secret_key": "NyGJxku5Ou72XbZlSqGAtqgfiCdWGnByNTO2F3HD"

radosgw-admin zone modify --rgw-zone=eu-east --access-key=MIW5HWIXFQ71AP9MFB9M --secret=NyGJxku5Ou72XbZlSqGAtqgfiCdWGnByNTO2F3HD

radosgw-admin period update --commit



radosgw-admin realm create --rgw-realm=test_realm --default
radosgw-admin zonegroup create --rgw-zonegroup=us --endpoints=http://192.168.233.137:8000 --master --default
radosgw-admin zone create --rgw-zonegroup=us --rgw-zone=us-east-1 --endpoints=http://192.168.233.137:8000 --access-key=LIPEYZJLTWXRKXS9LPJC --secret-key=IsAje0AVDNXNw48LjMAimpCpI7VaxJYSnfD0FFKQ


ceph config set mon mon_allow_pool_delete true

radosgw-admin zonegroup delete --rgw-zonegroup=default
ceph osd pool rm default.rgw.log default.rgw.log --yes-i-really-really-mean-it
ceph osd pool rm default.rgw.meta default.rgw.meta --yes-i-really-really-mean-it
ceph osd pool rm default.rgw.control default.rgw.control --yes-i-really-really-mean-it
ceph osd pool rm default.rgw.data.root default.rgw.data.root --yes-i-really-really-mean-it
ceph osd pool rm default.rgw.gc default.rgw.gc --yes-i-really-really-mean-it

radosgw-admin user create --uid=zone.user --display-name="Zone user" --system

radosgw-admin zone modify --rgw-zone=us-east-1 --access-key=NE48APYCAODEPLKBCZVQ--secret=u24GHQWRE3yxxNBnFBzjM4jn14mFIckQ4EKL6LoW
radosgw-admin period update --commit
systemctl list-units | grep ceph

 ceph-7dc5105a-06a2-11ee-a73f-000c29c26ef2@rgw.test_realm.us-east-1.cephnode2.pkcpjr.service

 radosgw-admin zone modify --rgw-zone=us-east --access-key={access-key} --secret={secret



 yum install radosgw -y