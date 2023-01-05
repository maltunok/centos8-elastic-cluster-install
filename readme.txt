# install elastic 

sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

sudo nano /etc/yum.repos.d/elasticsearch.repo

...
[elasticsearch]
name=Elasticsearch repository for 8.x packages
baseurl=https://artifacts.elastic.co/packages/8.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=0
autorefresh=1
type=rpm-md
...

sudo dnf install --enablerepo=elasticsearch elasticsearch

sudo systemctl daemon-reload
sudo systemctl enable elasticsearch.service
sudo systemctl start elasticsearch.service

sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -i -u elastic
Passw0rd!

// to check running node
sudo curl --cacert /etc/elasticsearch/certs/http_ca.crt -u elastic https://localhost:9200

sudo cp /etc/elasticsearch/elasticsearch.yml /etc/elasticsearch/elasticsearch.yml.backup
sudo nano /etc/elasticsearch/elasticsearch.yml

...
cluster.name: es-cluster
node.name: elastic1
network.host: 10.0.0.4
discovery.seed_hosts: ["10.0.0.4", "10.0.0.5"]
cluster.initial_master_nodes: ["elastic1", "elastic2"]
http.port: 9200
...

sudo systemctl restart elasticsearch.service

rpm -qa firewalld
# expected output: firewalld-0.6.3-2.el7_7.2.noarch

sudo systemctl start firewalld

systemctl status firewalld

sudo systemctl enable firewalld

sudo firewall-cmd --add-port=9200/tcp --permanent
sudo firewall-cmd --add-port=9300/tcp --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --list-all

https://elastic1:9200/

# install kibana

sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

sudo nano /etc/yum.repos.d/kibana.repo

...
[kibana-8.x]
name=Kibana repository for 8.x packages
baseurl=https://artifacts.elastic.co/packages/8.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
...

sudo dnf install kibana

sudo /bin/systemctl daemon-reload

sudo /bin/systemctl enable kibana.service

sudo systemctl start kibana.service

sudo cp /etc/kibana/kibana.yml /etc/kibana/kibana.yml.backup

sudo nano /etc/kibana/kibana.yml 

...
server.port: 5601
server.host: 10.0.0.4
server.publicBaseUrl: "http://10.0.0.4:5601"
elasticsearch.hosts: ["https://10.0.0.4:9200/", "https://10.0.0.5:9200"]
elasticsearch.username: "kibana_system"
elasticsearch.password: "Passw0rd!"
elasticsearch.ssl.certificateAuthorities: [ "/etc/kibana/certs/http_ca.crt" ]
...

sudo mkdir /etc/kibana/certs
sudo scp /etc/elasticsearch/certs/http_ca.crt /etc/kibana/certs 
sudo ls /etc/kibana/certs

sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -i -u kibana_system
Passw0rd!

sudo firewall-cmd --add-port=5601/tcp --permanent
sudo firewall-cmd --add-port=5601/udp --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --list-all

sudo systemctl restart kibana.service

http://10.0.0.4:5601/

# add node to cluster

sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

sudo nano /etc/yum.repos.d/elasticsearch.repo

...
[elasticsearch]
name=Elasticsearch repository for 8.x packages
baseurl=https://artifacts.elastic.co/packages/8.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=0
autorefresh=1
type=rpm-md
...

sudo dnf install --enablerepo=elasticsearch elasticsearch

// go to node one and run following command to create enrollement token

sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s node

sudo /usr/share/elasticsearch/bin/elasticsearch-reconfigure-node --enrollment-token <token>

sudo nano /etc/elasticsearch/elasticsearch.yml

...
cluster.name: es-cluster
node.name: trdcsrvelp002.yph.inc
network.host: 10.0.0.5
discovery.seed_hosts: ["10.0.0.4", "10.0.0.5"]
cluster.initial_master_nodes: ["elastic1", "elastic2"]
http.port: 9200

...

rpm -qa firewalld
# expected output: firewalld-0.6.3-2.el7_7.2.noarch

sudo systemctl start firewalld
systemctl status firewalld
sudo systemctl enable firewalld

sudo firewall-cmd --add-port=9200/tcp --permanent
sudo firewall-cmd --add-port=9300/tcp --permanent
sudo firewall-cmd --reload



sudo systemctl daemon-reload
sudo systemctl enable elasticsearch.service
sudo systemctl start elasticsearch.service

sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -i -u elastic

curl -XGET "10.0.0.5:9200/?pretty"

curl -k -u elastic http://elastic2:9200