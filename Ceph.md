#Ceph

- https://balderscape.medium.com/setting-up-a-virtual-single-node-ceph-storage-cluster-d86d6a6c658e
- https://shonpaz.medium.com/deploy-a-ceph-cluster-within-minutes-using-cephadm-53e3b915416f
- https://medium.com/@avmor/how-to-configure-rgw-multisite-in-ceph-65e89a075c1f
- https://balderscape.medium.com/setting-up-a-virtual-single-node-ceph-storage-cluster-d86d6a6c658e
- https://www.msystechnologies.com/blog/how-do-i-setup-ceph-cluster-these-8-steps-will-help-you/
- https://www.tecmint.com/install-ntp-in-rhel-8/
- https://towardsdatascience.com/run-your-spark-data-processing-workloads-using-opendatahub-ocs-and-an-external-ceph-cluster-8922f166f884
- https://gist.github.com/fmount/6203013d1c423dd831e3717b9986551b
- https://github.com/ceph/ceph-chef/blob/master/files/default/ceph-remove-clean
- https://access.redhat.com/documentation/en-us/red_hat_ceph_storage/2/html/object_gateway_guide_for_red_hat_enterprise_linux/object_gateway_configuration_reference#list_zones
## On Cephadmin Node

        sudo yum upgrade -y && sudo yum update -y
        sudo yum install curl -y
        sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
        sudo yum update -y
        sudo yum install -y python39 && sudo yum install podman -y <<-Install on both master and worker->>
        sudo yum install python3-rbd -y && sudo yum install python3-rados -y && sudo yum install chrony -y

        ----------------------------------------------------------------
        systemctl start chronyd
        systemctl enable chronyd
        systemctl status chronyd
        vi /etc/chrony.conf
        allow 192.168.56.0/24
        systemctl restart chronyd
        firewall-cmd --permanent --add-service=ntp
        firewall-cmd --reload

        ----------------------------------------------------------------
        hostnamectl set-hostname cephadmin
        hostnamectl set-hostname cephnode1
        hostnamectl set-hostname cephnode2

        sudo tee -a /etc/hosts<<EOF
        192.168.2.185 cephadmin
        192.168.2.186 cephnode1
        192.168.2.188 cephnode2
        EOF
        ----------------------------------------------------------------
        useradd -d /home/ceph -m ceph
        passwd ceph
        visudo
        ceph ALL=(ALL) NOPASSWD: ALL

        ----------------------------------------------------------------

        curl --silent --remote-name --location https://github.com/ceph/ceph/raw/quincy/src/cephadm/cephadm
        chmod +x cephadm
        ./cephadm add-repo --release quincy
        ./cephadm install
        sudo yum install ceph-commons -y
        which cephadm
        cephadm bootstrap --mon-ip <ip>
        cephadm shell
        ceph orch host ls
        ssh-copy-id -f -i /etc/ceph/ceph.pub root@192.168.2.186
        ssh-copy-id -f -i /etc/ceph/ceph.pub root@192.168.2.188


        sudo tee -a /etc/hosts<<EOF
        172.31.89.114 ip-172-31-89-114.ec2.internal
        172.31.85.52 ip-172-31-85-52.ec2.internal
        172.31.89.147 ip-172-31-89-147.ec2.internal
        EOF

        ceph orch host add ip-172-31-85-52.ec2.internal 172.31.85.52
        ceph orch host add ip-172-31-89-147.ec2.internal 172.31.89.147

        ----------------------------------------------------------------

        ceph orch daemon add osd ceph-01:/dev/sdb
        ceph orch daemon add osd ceph-02:/dev/sdb
        ceph orch daemon add osd ceph-03:/dev/sdb
        ceph orch daemon add osd ip-172-31-6-11.ap-south-1.compute.internal:/dev/sdf
        ceph orch daemon add osd ip-172-31-39-83.ap-south-1.compute.internal:/dev/sdg
        ceph orch daemon add osd ceph[0,1,2]:/dev/sd[b,c]
        ceph orch daemon add osd ceph[0,1,2]:/dev/sd[b,c]
        ceph orch device ls
        ceph osd tree
        ----------------------------------------------------------------


        ceph tell mon.\* injectargs --mon_allow_pool_delete true
        ceph osd pool rm default.rgw.buckets.data default.rgw.buckets.data --yes-i-really-really-mean-it
        python3 ceph.py \
        --rbd-data-pool-name default.rgw.buckets.data

![2023-01-24-10-28-42](https://user-images.githubusercontent.com/60940642/214216954-81f099f5-a49e-43c7-8a36-8d98841e31e1.png)
![2023-01-24-10-29-39](https://user-images.githubusercontent.com/60940642/214216958-66ef0a62-db66-4239-8339-f78222c44eb9.png)
![2023-01-24-10-43-00](https://user-images.githubusercontent.com/60940642/214216961-6a7610db-d698-4f53-91a0-686f7c2e4f47.png)

        ----------------------------------------------------------------

## Downloading Packages from re-routing URLS

        curl -L -w %{url_effective} -o /dev/null -s https://github.com/ceph/ceph/raw/quincy/src/cephadm/cephadm
                                                    https://raw.githubusercontent.com/ceph/ceph/quincy/src/cephadm/

## Single Host & Podman
        cephadm bootstrap --mon-ip 192.168.233.134 --allow-fqdn-hostname --single-host-defaults --allow-overwrite
        ceph orch daemon add osd cephadmin:/dev/sdb

## Ceph Disconnected Environment
        podman load -i registry.rar
        podman tag id docker.io/library/registry:2
        podman container rm -f registry
        sudo mkdir -p /var/lib/registry
        podman container run -dt -p 5000:5000 --name registry --volume registry:/var/lib/registry:2 docker.io/library/registry:2
        podman image tag quay.io/ceph/ceph:v17 localhost:5000/ceph/ceph:v17
        podman push push localhost:5000/ceph/ceph:v17

        cat > /etc/contianers/registries.conf.d/myregistry.conf <<EOF
        [[registry]]
        location = "localhost:5000"
        insecure = true
        EOF>>

        cephadm --image localhost:5000/ceph/ceph:v17 bootstrap --mon-ip 192.168.2.1

        podman pull quay.io/ceph/ceph:v17
        podman pull quay.io/ceph/ceph-grafana:8.3.5
        podman pull quay.io/prometheus/prometheus:v2.33.4
        podman pull quay.io/prometheus/node-exporter:v1.3.1
        podman pull quay.io/prometheus/alertmanager:v0.23.0

        cat <<EOF > initial-ceph.conf
        [mgr]
        mgr/cephadm/container_image_prometheus = quay.io/prometheus/prometheus:v2.33.4
        mgr/cephadm/container_image_node_exporter = quay.io/prometheus/node-exporter:v1.3.1
        mgr/cephadm/container_image_grafana = quay.io/ceph/ceph-grafana:8.3.5
        mgr/cephadm/container_image_alertmanager = quay.io/prometheus/alertmanager:v0.23.0
        EOF

        cat <<EOF > initial-ceph.conf
        [mgr]
        mgr/cephadm/container_image_prometheus = localhost:5000/prometheus:v2.33.4
        mgr/cephadm/container_image_node_exporter = localhost:5000/node_exporter:v1.3.1
        mgr/cephadm/container_image_grafana = localhost:5000/grafana:8.3.5
        mgr/cephadm/container_image_alertmanager = localhost:5000/alertmanger:v0.23.0
        EOF

        podman run --privileged -d --name registry -p 5000:5000 -v /var/lib/registry:/var/lib/registry --restart=always registry:2

        sudo mkdir -p /var/lib/registry

        podman container rm -f registry

        podman container run -dt -p 5000:5000 --name registry --volume registry:/var/lib/registry:Z docker.io/library/registry:2

        podman image tag docker.io/library/alpine:latest localhost:5000/alpine:latest

        podman image tag quay.io/ceph/ceph:v17 localhost:5000/ceph/ceph:v17
        podman image tag quay.io/ceph/ceph-grafana:8.3.5 localhost:5000/grafana:8.3.5
        podman image tag quay.io/prometheus/prometheus:v2.33.4 localhost:5000/prometheus:v2.33.4
        podman image tag quay.io/prometheus/node-exporter:v1.3.1 localhost:5000/node_exporter:v1.3.1
        podman image tag quay.io/prometheus/alertmanager:v0.23.0 localhost:5000/alertmanger:v0.23.0

        docker tag quay.io/ceph/ceph:v17 test:5000/ceph:v17
        docker tag quay.io/ceph/ceph-grafana:8.3.5 test:5000/ceph-grafana:8.3.5
        docker tag quay.io/prometheus/prometheus:v2.33.4 test:5000/prometheus:v2.33.4
        docker tag quay.io/prometheus/node-exporter:v1.3.1 test:5000/node-exporter:v1.3.1
        docker tag quay.io/prometheus/alertmanager:v0.23.0 test:5000/alertmanager:v0.23.0
        docker tag test:5000/
        ExecStart=/usr/bin/dockerd –insecure-registry test:5000

        docker push test:5000/alertmanager:v0.23.0

        podman image push localhost:5000/ceph/ceph:v17 --tls-verify=false
        podman image push localhost:5000/grafana:8.3.5 --tls-verify=false
        podman image push localhost:5000/prometheus:v2.33.4 --tls-verify=false
        podman image push localhost:5000/node_exporter:v1.3.1 --tls-verify=false
        podman image push localhost:5000/alertmanger:v0.23.0 --tls-verify=false

        podman image push localhost:5000/ceph/ceph:v17
        podman image push localhost:5000/grafana:8.3.5
        podman image push localhost:5000/prometheus:v2.33.4
        podman image push localhost:5000/node_exporter:v1.3.1
        podman image push localhost:5000/alertmanger:v0.23.0

        podman pull quay.io/ceph/ceph:v17
        podman pull quay.io/ceph/ceph-grafana:8.3.5
        podman pull quay.io/prometheus/prometheus:v2.33.4
        podman pull quay.io/prometheus/node-exporter:v1.3.1
        podman pull quay.io/prometheus/alertmanager:v0.23.0

        cat > /etc/containers/registries.conf.d/myregistry.conf <<EOF
        [[registry]]
        location = "localhost:5000"
        insecure = true
        EOF

        cephadm bootstrap --mon-ip 192.168.0.140
        cephadm --image quay.io/ceph/ceph:v17 bootstrap --mon-ip 192.168.0.140 --allow-fqdn-hostname --config initial-ceph.conf         --allow-fqdn-hostname --allow-overwrite --skip-pull

        cephadm --image localhost:5000/ceph/ceph:v17 bootstrap --mon-ip 192.168.64.131 --config initial-ceph.conf

        podman push --tls-verify=false localhost:5000/node_exporter:v1.3.1
        podman pull --tls-verify=false localhost:5000/node_exporter:v1.3.1

        podman load -i registry.tar
        podman tag da670344bb06 docker.io/library/registry:2

        podman container rm -f registry
        sudo mkdir -p /var/lib/registry
        podman container run -dt -p 5000:5000 --name registry --volume registry:/var/lib/registry:Z docker.io/library/registry:2

        podman image tag quay.io/ceph/ceph:v17 localhost:5000/ceph/ceph:v17
        podman image tag quay.io/ceph/ceph-grafana:8.3.5 localhost:5000/grafana:8.3.5
        podman image tag quay.io/prometheus/prometheus:v2.33.4 localhost:5000/prometheus:v2.33.4
        podman image tag quay.io/prometheus/node-exporter:v1.3.1 localhost:5000/node_exporter:v1.3.1
        podman image tag quay.io/prometheus/alertmanager:v0.23.0 localhost:5000/alertmanger:v0.23.0

        podman image push localhost:5000/ceph/ceph:v17
        podman image push localhost:5000/grafana:8.3.5
        podman image push localhost:5000/prometheus:v2.33.4
        podman image push localhost:5000/node_exporter:v1.3.1
        podman image push localhost:5000/alertmanger:v0.23.0

        cat > /etc/containers/registries.conf.d/myregistry.conf <<EOF
        [[registry]]
        location = "localhost:5000"
        insecure = true
        EOF

        cephadm --image localhost:5000/ceph/ceph:v17 bootstrap --mon-ip 192.168.2.10

        openssl genrsa -out key.key
        openssl req -new -key key.key -out csr.csr -subj "/CN=https-test-credit.apps.ocp4.example.com"
        openssl x509 -req -in csr.csr -signkey key.key -out crt.crt

        oc create route edge --service keycloak --hostname https-test-credit.apps.ocp4.example.com --key key.key --cert crt.crt

## Ceph LVM Remove
        lvremove /dev/ceph-c042d105-d795-4d6b-a4ee-40b752e6a572/osd-block-861b8be4-661f-48e7-b848-c0905f9af005
        vgremove ceph-c042d105-d795-4d6b-a4ee-40b752e6a572
        pvremove /dev/nvme0n2

## Ceph Performance
        echo 3 | sudo tee /proc/sys/vm/drop_caches && sudo sync


## Ceph SYNC

        radosgw-admin realm create --default --rgw-realm=kumar

        radosgw-admin zonegroup delete --rgw-zonegroup=default

        radosgw-admin zonegroup create --rgw-zonegroup=eu --master --default --endpoints=http://192.168.233.129:8000

radosgw-admin zonegroup remove --rgw-zonegroup=us --rgw-zone=us-east
radosgw-admin zonegroup remove --rgw-zonegroup=ap --rgw-zone=ap-east

        radosgw-admin zone create --rgw-zone=eu-east --master --rgw-zonegroup=eu --endpoints=http://192.168.233.129:8000 --access-key=UJIZ8CNNVVFECW8U9YWC --secret=4A6C4G6PJqxdff9BBbUhyW7Fj7zBG33HrPbeoibb --default

        radosgw-admin zone modify --rgw-zone=ap-east --access-key=UJIZ8CNNVVFECW8U9YWC --secret=4A6C4G6PJqxdff9BBbUhyW7Fj7zBG33HrPbeoibb




 radosgw-admin zonegroup remove --rgw-zone=default





         {
            "user": "synchronization-user",
            "access_key": "4SAZXZY05XNKRLGD0P1D",
            "secret_key": "ZT7Twrcj3LOkdy6Z2asYimNnN6vrAeitPYaoogzY"
        }

        radosgw-admin user create --uid=repuser --display-name=”Replication_user” --access-key=1234 --secret=1234 --system
        
        vi /etc/ceph/ceph.conf rgw zone = us-east

        sudo tee -a /etc/ceph/ceph.conf<<EOF
        rgw zone = us-east
        EOF

        radosgw-admin period update --commit
        radosgw-admin sync status
        radosgw-admin realm list
        radosgw-admin period list
        radosgw-admin zonegroup list
        radosgw-admin zone list

        radosgw-admin realm pull --rgw-realm=gold --url=http://192.168.233.129:8081 --access-key=UJIZ8CNNVVFECW8U9YWC --secret=4A6C4G6PJqxdff9BBbUhyW7Fj7zBG33HrPbeoibb --default

        radosgw-admin period pull --url=http://192.168.233.134:8000 --access-key=1234 --secret=1234

        radosgw-admin zone create --rgw-zonegroup=us --rgw-zone=ceph-us-east-2 \
        --endpoints=http://192.168.233.134:8081 \
        --access-key=UJIZ8CNNVVFECW8U9YWC --secret=4A6C4G6PJqxdff9BBbUhyW7Fj7zBG33HrPbeoibb --default

        radosgw-admin zone create --rgw-zone=us-east-2 --rgw-zonegroup=us --endpoints=http://192.168.233.134:8081 \
        --access-key=UJIZ8CNNVVFECW8U9YWC --secret=4A6C4G6PJqxdff9BBbUhyW7Fj7zBG33HrPbeoibb --default

        2023-06-08T16:07:45.366+0530 7f3afbb53680 0 failed reading obj info from .rgw.root:zone_info.83681ac6-50ec-4b0f-ba21-28bcf9e9cec8:      (2) No such file or directory
        2023-06-08T16:07:45.366+0530 7f3afbb53680 0 WARNING: could not read zone params for zone id=83681ac6-50ec-4b0f-ba21-28bcf9e9cec8        name=us-east
        

        vi /etc/ceph/ceph.conf rgw zone = us-east-2

        radosgw-admin period update --commit

        2023-06-08T16:16:46.986+0530 7efdcdb53680 0 period (44ee7783-342f-4ad2-9a07-ca858b61362a does not have zone     1f910e1c-9e3f-457c-8fcb-800319747af1 configured
        Sending period to new master zone 83681ac6-50ec-4b0f-ba21-28bcf9e9cec8
        request failed: (2202) Unknown error 2202
        failed to commit period: (2202) Unknown error 2202


## Deleting Realms, ZoneGroups, Zones


        radosgw-admin zone list

        radosgw-admin zonegroup remove --rgw-zonegroup=eu --rgw-zone=eu-east

        radosgw-admin zonegroup list

        radosgw-admin zonegroup delete --rgw-zonegroup=<name>




