# Repo Server Setup
## Server Specs
- CPU: 2
- Memory: 1GB
- Storage: 32GB
- NIC: 2
  - 1 NAT for internet access
  - 1 Local Host for machine and other server access
- OS: Rocky 9.0 Minimal Install
- IP Address: 192.168.55.47/24 ( once created run "ip add" to retrive your IP ) 

## Repo Setup
Follow dvd_based_repo_creation.md to setup the repos.

Repos will be hosted under:
```
http://[your IP]/repo/BaseOS/
http://[Your IP]/repo/AppStream/
```
Server Location:
```
/var/www/html/repo/BaseOS
/var/www/html/repo/AppStream
```

## NFS Setup
Follow nfs_setup.md to setup the NFS server. This will host both regular NFS and autofs for the user profiles.

## Containerfile
Host the Containerfile under:
```
http://[your IP]/containers/Containerfile
```
Server Location and build podman:
```
mkdir -p /var/www/html/containers/Containerfile

vi /var/www/html/containers/Containerfile

FROM registry.access.redhat.com/ubi9/ubi:latest
RUN dnf -y install httpd; dnf clean all; systemctl enable httpd;
RUN echo "Successful Web Server Test" | tee /var/www/html/index.html
RUN mkdir /etc/systemd/system/httpd.service.d/; echo -e '[Service]\nRestart=always' | tee /etc/systemd/system/httpd.service.d/httpd.conf
EXPOSE 80
CMD [ "/sbin/init" ]

yum install podman -y

podman build -t repo-container-httpd .

podman run -d -p 8080:80 repo-container-httpd

/var/www/html/containers/Containerfile
```
## Resetting the Server
To reset the server between exam sessions, you should repermission the NFS shares that have been tweaked during the exam tasks, and cleanup the home directories:
```
chown -R root:root /export/{dba_files,it_files}
chmod -R -st,g-w,o+rx /export/{dba_files,it_files}
rm -f /export/home/{cindy,jack,jan,manny,marcia,moe}/.bash_history
rm -fr /export/home/{cindy,jack,jan,manny,marcia,moe}/.cache
rm -fr /export/home/{cindy,jack,jan,manny,marcia,moe}/.config
rm -fr /export/home/{cindy,jack,jan,manny,marcia,moe}/.local
rm -fr /export/home/{cindy,jack,jan,manny,marcia,moe}/.lesshst
```
