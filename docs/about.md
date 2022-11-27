### Code gen

#### put swagger.yaml to target folder

```
echo $masterkey | sudo chown -R `whoami` /home/bigobject/new-struct-code-gen/bindata

cp "/home/master/nextcloud/sourcetree/iae-server/assets/swagger/swagger.yaml" /home/bigobject/new-struct-code-gen/bindata/swagger/swagger.yaml
```

#### cleaning output folder:

```
#basically
#sudo rm -rf dist

cd "/home/bigobject/new-struct-code-gen/"
echo $masterkey | sudo -S rm -rf dist
cd
```

#### code Gen

```
docker run --rm \
  -v /home/bigobject/new-struct-code-gen/bindata/swagger/swagger.yaml:/srv/openapi-code-generator/swagger.yaml \
  -v /home/bigobject/new-struct-code-gen/dist:/srv/openapi-code-generator/dist \
  boproject/openapi-code-generator:latest \
  /srv/openapi-code-generator/openapi-code-generator -n bitbucket.org/bigobject/IAE-server

echo $masterkey | sudo chown -R `whoami` /home/bigobject/new-struct-code-gen/dist
echo $masterkey | sudo rm -rf ~/nextcloud/new-struct-code-gen

rsync -a /home/bigobject/new-struct-code-gen/ ~/nextcloud/new-struct-code-gen/

#cp -a /home/bigobject/new-struct-code-gen/. ~/nextcloud/new-struct-code-gen/
```

#### renew files generated

```
#download the folder generated:
"/home/bigobject/new-struct-code-gen/"

#backup last version
$git_iae-server_root/src/services/web

#overwrite the follwoing folders:
$git_iae-server_root/src/services/web/ctrls
$git_iae-server_root/src/services/web/oapi

#patch new files to the folder:
$git_iae-server_root/src/services/web/handles
```

---

## rebuild docker image (by script)

```
. ./buildiae2
```

```
#!bin/bash

cd ~/win/sourcetree/iae-server
cp src/go-official.mod src/go.mod
curl -sfL -o docker.tgz "https://download.docker.com/linux/static/stable/x86_64/docker-20.10.5.tgz"
docker build . -t bigobject/iae-server:test -f Dockerfile.localDockerCli
docker-compose -f docker-compose.yml up -d
cp src/go-dev.mod src/go.mod
cd
```

## rebuild docker image (step by step)

```
#cd ~/prj/IAE-server/docker
cd "/home/master/win/sourcetree/iae-server"
(
cp src/go.mod src/go-dev.mod
)
cp src/go-official.mod src/go.mod
docker build . -t bigobject/iae-server:test
docker-compose -f docker-compose.yml up -d

cp src/go-dev.mod src/go.mod
cd

(
networks:
      IAE-intranet:
          name: IAE-intranet
)
```

## rebuild docker image in office

```
tmux new -s iae
#ctrl-b;d
tmux attach -t iae

cd "/home/cs/IAE-server/iae-server"
#git pull
echo "ev7dMEMhkeLCsUMZHHG5" | git pull
docker build . -t bigobject/iae-server:test

#docker-compose -f docker-compose.yml up -d
docker-compose -f docker-compose-test.yml up -d  --remove-orphans


docker exec -it IAE-server bash
```

# bitbucket authentication

```
mv ~/.netrc ~/_netrc
touch ~/.netrc
echo "machine api.bitbucket.org" >> ~/.netrc
echo "login joeyluo" >> ~/.netrc
echo "password ev7dMEMhkeLCsUMZHHG5" >> ~/.netrc


(
machine api.bitbucket.org
login joeyluo
password ev7dMEMhkeLCsUMZHHG5
)

https://bitbucket.org/site/oauth2/authorize?client_id=SGmWyG3YFRGycKpxHM&response_type=code

-> https://www.bigobject.io/?code=h7CLGDtEvPK7KJV9mr

curl -X POST -u "SGmWyG3YFRGycKpxHM:7LUmPWLc2SVk8T6etgVTQQZYumcYuEmR" \
  https://bitbucket.org/site/oauth2/access_token \
  -d grant_type=authorization_code -d code=h7CLGDtEvPK7KJV9mr

-> {"scopes": "pullrequest:write team:write project:write repository:delete repository:admin", "access_token": "S85XmKEpZ7Qlyl26g4StUv7prcHJBnBaUxcROPWIstx7Qxeyl8RPaNRCHJf-fRS6pe1v5zi8FGM5auafWNNRl5GjiCNyzNOXJ44ufEM1xaP1Va_jgXjhl7_M", "expires_in": 7200, "token_type": "bearer", "state": "authorization_code", "refresh_token": "XwDdnmBedJ9TuF4hjq"}

Refresh tokens
curl -X POST -u "SGmWyG3YFRGycKpxHM:7LUmPWLc2SVk8T6etgVTQQZYumcYuEmR" \
  https://bitbucket.org/site/oauth2/access_token \
  -d grant_type=refresh_token -d refresh_token=XwDdnmBedJ9TuF4hjq

-> {"scopes": "project:write repository:delete repository:admin team:write pullrequest:write", "access_token": "iGlAhaHfmkMnReym0Wa00VsZmJHDWp7iFPLR5YwWPfP-Zijp7lE5cDX2FUwu0q2Pu5hQlVFV_dx2LWvSVMtWwElB118sLkQR1BnhoZt0oRASmrzrRTW9aYop", "expires_in": 7200, "token_type": "bearer", "state": "refresh_token", "refresh_token": "XwDdnmBedJ9TuF4hjq"}

curl -L -X GET --user joeyluo:ev7dMEMhkeLCsUMZHHG5 http://api.bitbucket.org/2.0/repositories/joeyluo/
```

### worked examples

```
curl -L -X GET --user joeyluo:ev7dMEMhkeLCsUMZHHG5 http://api.bitbucket.org/2.0/repositories/joeyluo/
```

```
mv ~/.netrc ~/_netrc
touch ~/.netrc
echo "machine api.bitbucket.org" >> ~/.netrc
echo "login joeyluo" >> ~/.netrc
echo "password Ffk2rCUt8h7RE7ZT8dwY" >> ~/.netrc
```

references

```
https://medium.com/swlh/go-modules-with-private-git-repository-3940b6835727
Go Modules with Private GIT Repository

https://dev.to/gopher/how-to-use-go-modules-with-private-git-repository-53b4
How to use Go Modules with Private Git repository

git config --global url."https://joeyluo:ev7dMEMhkeLCsUMZHHG5@bitbucket.org".insteadOf "https://bitbucket.org"
git config --global url."https://joeyluo:ev7dMEMhkeLCsUMZHHG5@api.bitbucket.org/2.0/repositories".insteadOf "https://api.bitbucket.org/2.0/repositories"

https://exerror.com/remote-bitbucket-cloud-recently-stopped-supporting-account-passwords-for-git-authentication/
[Solved] remote: Bitbucket Cloud recently stopped supporting account passwords for Git authentication
(
git remote set-url origin https://joeyluo:ev7dMEMhkeLCsUMZHHG5@bitbucket.org/bigobject/golib.git
)
```

Cleaning Deprecated Images

```
docker image ls -f dangling=true
docker image rmi 72c651b68ba1
```

ev7dMEMhkeLCsUMZHHG5

# co-operate with fileetl

```
docker pull bigobject/file_etl:dev

cd ~/win/sourcetree/bigobject-fileetl
docker build --no-cache -t "testfileetl" .

docker build --no-cache -t "bigobject/file_etl:test" .
cd

cd ~/win/dockers/fileetl
docker-compose -f ~/win/dockers/fileetl/docker-compose-streamerx-ssh.yml up -d
docker-compose -f ~/win/dockers/fileetl/docker-compose-streamerx-ssh.yml down

docker-compose -f ~/win/dockers/fileetl/docker-compose-streamerx-ssh-intranet.yml up -d
docker-compose -f ~/win/dockers/fileetl/docker-compose-streamerx-ssh-intranet.yml down
docker-compose -f ~/win/dockers/fileetl/docker-compose-streamerx-ssh-intranet.yml restart

docker exec -it M67_fileETL bash


cd ~/win/dockers/fileetl
docker-compose -f docker-compose-streamerx-ssh.yml up -d
docker-compose -f docker-compose-streamerx-ssh.yml down

docker-compose -f docker-compose-streamerx.yml up -d

docker-compose -f docker-compose-streamerx.yml restart
docker exec -it m67_fileETL bash

 docker exec -i m67_fileETL service ssh start

#create table
docker exec -i m67_fileETL /home/src/run -t=true
```

# co-operate with fileetl(office)

```
docker pull bigobject/file_etl:dev

cd "/home/cs/iae/"
docker build --no-cache -t "testfileetl" .

docker build --no-cache -t "bigobject/file_etl:test" .
cd

cd "/home/cs/iae/"
docker-compose -f "/home/cs/iae/docker-compose-streamerx.yml" restart
docker-compose -f docker-compose-streamerx.yml restart

docker exec -it m67_fileETL bash

 docker exec -i m67_fileETL service ssh start

```

testing

##### From Container IAE-server

###### matched_file_set

```
docker exec -it IAE-server bash
curl --location --request GET 'http://m67_fileetl:9089/matched_file_set' --header 'Content-Type: text/plain' --data-raw '{"path":"/home/files","rule":"test"}'
```

from VM

```
curl --location --request GET 'http://192.168.56.99:39089/matched_file_set' --header 'Content-Type: text/plain' --data-raw '{"path":"/home/cs/iae/sampleexcel","rule":"test"}'
```

##### from swagger

###### matched_file_set

```
{
  "machineID": "2d90d4ff-92dd-4076-8c2b-e1c8b384b054",
  "path": "/home/files",
  "regex": "test"
}

response:
{
  "code": "InternalServerError",
  "message": "Test regular expression failed, error: Get http://M67_fileETL:9089/matched_file_set: dial tcp: lookup M67_fileETL: Temporary failure in name resolution",
  "params": null
}
```

###### list docker network (local VM)

```
master@master-VirtualBox:~$ docker network ls
NETWORK ID     NAME              DRIVER    SCOPE
fyg64kg7zaq4   IAE               overlay   swarm
2b18b9982519   IAE-intranet      bridge    local
944f29974b1f   bridge            bridge    local
5335f824abde   docker_gwbridge   bridge    local
5a4c0c017cd5   host              host      local
wf9f9jsh87ya   ingress           overlay   swarm
0938bec18b20   none              null      local
```

###### list docker network (office)

```
cs@iae-67:~$ docker network ls
NETWORK ID     NAME                 DRIVER    SCOPE
mrr2f84md9r3   IAE                  overlay   swarm
0f0b2db9e68b   IAE-intranet         bridge    local
5b1bf9b996cd   bridge               bridge    local
6f8cb05dea62   data_default         bridge    local
190e5c6c0573   docker_gwbridge      bridge    local
67d241c5fc22   host                 host      local
6321e54cb760   iae2_default         bridge    local
c41b2ca95fe5   iae-server_default   bridge    local
ed97d83e04f0   iae_default          bridge    local
64287635046a   iae_vm_default       bridge    local
ywysz0gco67b   ingress              overlay   swarm
2eb31003639d   kafka_default        bridge    local
33cc60e2f1a8   m01_default          bridge    local
6a7ec727559d   mssql_default        bridge    local
b284ad604727   mysql_default        bridge    local
58bd97d2b319   none                 null      local
da309970d743   oracle_default       bridge    local
c7790e9ffb96   postgres_default     bridge    local
50a3accba44d   streamer_default     bridge    local
9fcc02c67020   vm_default           bridge    local


networks:
      IAE-intranet:
          name: IAE-intranet
```

bitbucket app passwords

```
go Ffk2rCUt8h7RE7ZT8dwY
sourcetree ev7dMEMhkeLCsUMZHHG5
```

check disk spaces

```
sudo du -sh /home/cs/ise/*
sudo du -sh /home/cs/iae/ORA_02/db-extractor/data/*

docker rm $(docker ps --all -q -f status=exited)
```

purge deprecated docker images

```
docker rm $(docker ps --all -q -f status=exited)

docker image prune -a --force --filter "until=48h"

sudo docker ps -a | grep Exit | cut -d ' ' -f 1 | xargs sudo docker rm
```

tail
