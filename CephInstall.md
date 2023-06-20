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
docker tag  test:5000/ 
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
cephadm --image quay.io/ceph/ceph:v17  bootstrap --mon-ip 192.168.0.140 --allow-fqdn-hostname --config initial-ceph.conf  --allow-fqdn-hostname --allow-overwrite --skip-pull


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