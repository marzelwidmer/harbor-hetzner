# Harbor Hetzner Instalaton

## Create VM
```
hcloud server create \
    --name harbor.c3smonkey.ch \
    --type cpx31 \
    --image centos-7 \
    --ssh-key ~/.ssh/id_rsa.pub \
    --datacenter nbg1-dc3
```

## Install Software

### Tools
```
yum update -y && yum upgrade -y
yum install -y yum-utils
yum install wget -y

wget https://github.com/bcicen/ctop/releases/download/v0.7.3/ctop-0.7.3-linux-amd64 -O /usr/local/bin/ctop
chmod +x /usr/local/bin/ctop
```



### Docker Enginde
```
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

yum install docker-ce docker-ce-cli containerd.io -y
systemctl start docker
systemctl status docker
systemctl enable docker
```

### Docker Compose
```
curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

```


## Install Harbor with trivy
```
wget https://github.com/goharbor/harbor/releases/download/v2.0.1/harbor-offline-installer-v2.0.1.tgz && tar -xzvf harbor-offline-installer-v2.0.1.tgz

cd harbor

cp harbor.yml.tmpl harbor.yml
```

Change Configuration `hostname` and `remove the HTTPS` section
```
vi harbor.yml
```

```
./install.sh --with-trivy
```


Go online to http://harbor.c3smonkey.ch  `admin` and your `Harbor12345`.  Don't forgett to change the password.


# OpenShift Integration 
http://v1.uncontained.io/playbooks/continuous_delivery/external-docker-registry-integration.html

Add allow `--insecure-registry` in `/etc/sysconfig/docker`
```
# /etc/sysconfig/docker

# Modify these options if you want to change the way the docker daemon runs
OPTIONS=' --selinux-enabled       --signature-verification=False  --insecure-registry=harbor.c3smonkey.ch'
if [ -z "${DOCKER_CERT_PATH}" ]; then
    DOCKER_CERT_PATH=/etc/docker
```

## Restart Docker Deamon
```
systemctl restart docker
```