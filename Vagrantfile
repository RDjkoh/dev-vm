Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.network :forwarded_port, guest: 9200, host: 9200
  config.vm.network :forwarded_port, guest: 5601, host: 5601

  config.vm.provider "virtualbox" do |v|
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    v.memory = 4096
    v.cpus = 2
  end

  config.vm.provision "shell", inline: <<-SHELL
    # Oracle JDK8
    sudo yum install -y wget
    wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u66-b17/jdk-8u66-linux-x64.rpm"
    sudo yum --nogpgcheck localinstall -y jdk-8u66-linux-x64.rpm

    # Grab ES 2.0
    rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch

    # Yes, the formatting sucks. please don't touch this...
sudo tee /etc/yum.repos.d/elasticsearch.repo > /dev/null << 'EOF'
[elasticsearch-2.x]
name=Elasticsearch repository for 2.x packages
baseurl=http://packages.elastic.co/elasticsearch/2.x/centos
gpgcheck=1
gpgkey=http://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
EOF

    sudo yum install -y elasticsearch
    sudo systemctl daemon-reload
    sudo systemctl enable elasticsearch.service

    # Bind to 0.0.0.0 - by default ES 2.0 only binds to localhost, which won't work here...
    echo 'network.host: 0.0.0.0' | sudo tee --append /etc/elasticsearch/elasticsearch.yml  > /dev/null

    # Install Marvel 2.0, which is now free. - though for prolonged use we'll still need to get a Free license (requires registration)
    sudo /usr/share/elasticsearch/bin/plugin install license
    sudo /usr/share/elasticsearch/bin/plugin install marvel-agent
	
	# Install Kibana 4.5 (current latest version)
sudo tee /etc/yum.repos.d/kibana.repo > /dev/null << 'EOF'
[kibana-4.5]
name=Kibana repository for 4.5.x packages
baseurl=http://packages.elastic.co/kibana/4.5/centos
gpgcheck=1
gpgkey=http://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
EOF
	# Install the latest Kibana version with Marvel and Sense apps
	sudo yum install -y kibana
	chkconfig --add kibana
	sudo systemctl daemon-reload
	sudo systemctl enable kibana.service
	
	cd /opt/kibana/bin
	sudo ./kibana plugin --install elasticsearch/marvel/latest
    sudo ./kibana plugin --install elastic/sense

    # Install Kibana 4.2 with Marvel and Sense apps
    #wget https://download.elastic.co/kibana/kibana/kibana-4.2.0-linux-x64.tar.gz
    #tar -zxf kibana-4.2.0-linux-x64.tar.gz
    #sudo chown -R vagrant:vagrant kibana-4.2.0-linux-x64
    #cd kibana-4.2.0-linux-x64/bin

    #./kibana plugin --install elasticsearch/marvel/latest
    #./kibana plugin --install elastic/sense
    #./kibana > kibana.log &

    #cd ../../
    #rm *.rpm
    #rm *.tar.gz
	
	# Change ownership of /opt/kibana/optimize/.babelcache.json so Kibana can start
	sudo chown -R kibana:root /opt/kibana/optimize/.babelcache.json
	
	# Install git
	sudo yum install -y git
	
	# Move SSH keys into the home .ssh folder and add Bitbucket repos as known hosts (to avoid RSA key prompt)
	mv /home/vagrant/sync/id_rsa /home/vagrant/.ssh
	mv /home/vagrant/sync/id_rsa.pub /home/vagrant/.ssh
	chmod 600 /home/vagrant/.ssh/id_rsa
	chown -R vagrant.vagrant /home/vagrant/.ssh/*
	sudo tee /home/vagrant/.ssh/known_hosts > /dev/null << 'EOF'
bitbucket.org,104.192.143.3 ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAubiN81eDcafrgMeLzaFPsw2kNvEcqTKl/VqLat/MaB33pZy0y3rJZtnqwR2qOOvbwKZYKiEO1O6VqNEBxKvJJelCq0dTXWT5pbO2gDXC6h6QDXCaHo6pOHGPUy+YBaGQRGuSusMEASYiWunYN0vCAI8QaXnWMXNMdFP3jHAJH0eDsoiGnLPBlBp4TNm6rYI74nMzgz3B9IikW4WVK+dc8KZJZWYjAuORU3jc1c/NPskD2ASinf8v3xnfXeukU0sJ5N6m5E8VLjObPEO+mN2t/FZTMZLiFqPWc/ALSqnMnnhwrNi2rbfg/rd/IpL8Le3pSBne8+seeFVBoGqzHM9yXw==
EOF
	chmod 644 /home/vagrant/.ssh/known_hosts
	chown vagrant.vagrant /home/vagrant/.ssh/known_hosts
	
	# Create folders for git repos
	mkdir -p /home/vagrant/git/BUP
	mkdir -p /home/vagrant/git/REST_API
	sudo chown -R vagrant.vagrant /home/vagrant/git/
	# Business User Portal
	cd /home/vagrant/git/BUP
	sudo -u vagrant git init
	sudo -u vagrant git remote add master git@bitbucket.org:RD-BUSI/bup.git 
	sudo -u vagrant git pull master master
	# REST API 
	cd /home/vagrant/git/REST_API
	sudo -u vagrant git init
	sudo -u vagrant git remote add master git@bitbucket.org:RD-BUSI/rest_api.git
	sudo -u vagrant git pull master master
	
	# Install Docker
sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/$releasever/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
EOF

	sudo yum install -y docker-engine
	sudo groupadd docker
	sudo usermod -aG docker vagrant
	
	# Start Docker
	sudo service docker start
	
    # Start ES
    sudo service elasticsearch start
	
	# Start Kibana
	sudo service kibana start
	
    echo "Running Marvel at http://localhost:5601/app/marvel"
    echo "Running Sense at http://localhost:5601/app/sense"
    echo "Running HQ at http://localhost:9200/_plugin/hq/#cluster"
  SHELL
end
