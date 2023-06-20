sudo mkdir -p /var/lib/registry
sudo podman run --privileged -d --name registry -p 5000:5000 -v /var/lib/registry:/var/lib/registry --restart=always registry:2

cat > /etc/containers/registries.conf.d/myregistry.conf <<EOF
[[registry]]
location = "localhost:5000"
insecure = true
EOF

podman run --privileged -d --name registry -p 5000:5000 -v /var/lib/registry:/var/lib/registry --restart=always registry:2


podman pull quay.io/ceph/ceph:v17
podman pull quay.io/ceph/ceph-grafana:8.3.5
podman pull quay.io/prometheus/prometheus:v2.33.4
podman pull quay.io/prometheus/node-exporter:v1.3.1
podman pull quay.io/prometheus/alertmanager:v0.23.0


podman image tag quay.io/ceph/ceph:v17 localhost:5000/ceph/ceph:v17
podman image tag quay.io/ceph/ceph-grafana:8.3.5 localhost:5000/grafana:8.3.5
podman image tag quay.io/prometheus/prometheus:v2.33.4 localhost:5000/prometheus:v2.33.4
podman image tag quay.io/prometheus/node-exporter:v1.3.1 localhost:5000/node_exporter:v1.3.1
podman image tag quay.io/prometheus/alertmanager:v0.23.0 localhost:5000/alertmanger:v0.23.0

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


cat <<EOF > initial-ceph.conf
[mgr]
mgr/cephadm/container_image_prometheus = localhost:5000/prometheus:v2.33.4
mgr/cephadm/container_image_node_exporter = localhost:5000/node_exporter:v1.3.1
mgr/cephadm/container_image_grafana = localhost:5000/grafana:8.3.5
mgr/cephadm/container_image_alertmanager = localhost:5000/alertmanger:v0.23.0
EOF




sudo mkdir -p /var/lib/registry

podman container rm -f registry



cephadm bootstrap --mon-ip 192.168.0.140
cephadm --image quay.io/ceph/ceph:v17  bootstrap --mon-ip 192.168.0.140 --allow-fqdn-hostname --config initial-ceph.conf  --allow-fqdn-hostname --allow-overwrite --skip-pull


cephadm --image localhost:5000/ceph/ceph:v17 bootstrap --mon-ip 192.168.64.131 --config initial-ceph.conf

podman push --tls-verify=false localhost:5000/node_exporter:v1.3.1
podman pull --tls-verify=false localhost:5000/node_exporter:v1.3.1


sudo podman tag ba2b418f427c localhost:5000/alertmanager
sudo podman tag localhost/nginx-template localhost:5000/nginx-template
sudo podman tag localhost/nginx-template localhost:5000/nginx-template
sudo podman tag localhost/nginx-template localhost:5000/nginx-template
sudo podman tag localhost/nginx-template localhost:5000/nginx-template
sudo podman tag localhost/nginx-template localhost:5000/nginx-template
sudo podman tag localhost/nginx-template localhost:5000/nginx-template



[mgr]
mgr/cephadm/container_image_prometheus = localhost:5000/prometheus
mgr/cephadm/container_image_node_exporter = localhost:5000/node-exporter
mgr/cephadm/container_image_grafana = localhost:5000/ceph-grafana
mgr/cephadm/container_image_alertmanager = localhost:5000/alert-manger



docker.io/library/registry    2           0d153fadf70b  12 days ago    24.7 MB
localhost:5000/ceph-grafana   latest      dad864ee21e9  10 months ago  571 MB
<none>                        <none>      514e6a882f6e  12 months ago  205 MB
localhost:5000/node-exporter  latest      1dbe0e931976  14 months ago  22.3 MB
localhost:5000/alert-manager  latest      ba2b418f427c  18 months ago  58.9 MB


cephadm --image localhost:5000/ceph/ceph bootstrap --mon-ip 192.168.32.128 --config initial-ceph.conf 

[registries.insecure]

[registries.insecure]
registries = ['localhost:5000']


cat > /etc/containers/registries.conf.d/myregistry.conf <<EOF 
[[registry]]location = "localhost:5000"
insecure = true 
EOF

curl 127.0.0.1:5000/v2/_catalog

https://blog.while-true-do.io/podman-local-container-registry/
https://www.techrepublic.com/article/how-to-set-up-a-local-image-repository-with-podman/
https://thenewstack.io/tutorial-host-a-local-podman-image-registry/

https://computingforgeeks.com/create-docker-container-registry-with-podman-letsencrypt/

https://adam.younglogic.com/2020/09/podman-login-to-a-secured-registry/

https://www.redhat.com/sysadmin/simple-container-registry


