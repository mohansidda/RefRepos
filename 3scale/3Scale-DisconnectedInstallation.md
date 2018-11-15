
```
These steps to deploy the 3scale (amp2.2) in a disconnected environment. 
```
### Create environment variables 
```
export my_reg=docker-registry-default.13.251.251.251.nip.io   
export rh_reg=registry.access.redhat.com
```
#### OC login and Docker login : 
```
oc login <OCP URL> 
oc new-project 3scale-prd
docker login -u $(oc whoami) -p $(oc whoami -t) $my_reg

```
#### get the amp.yml from here : https://github.com/3scale/3scale-amp-openshift-templates/blob/master/amp/amp.yml

```
wget https://raw.githubusercontent.com/3scale/3scale-amp-openshift-templates/master/amp/amp.yml --no-check-certificate
```


### Step 1 : Pull images : 
```
docker pull registry.access.redhat.com/3scale-amp22/system:1.7
docker pull registry.access.redhat.com/3scale-amp22/backend:1.6
docker pull registry.access.redhat.com/3scale-amp22/apicast-gateway:1.8
docker pull registry.access.redhat.com/3scale-amp22/wildcard-router:1.6
docker pull registry.access.redhat.com/3scale-amp20/memcached:1.4.15
docker pull registry.access.redhat.com/rhscl/postgresql-95-rhel7:9.5
docker pull registry.access.redhat.com/3scale-amp22/zync:1.6
docker pull registry.access.redhat.com/rhscl/redis-32-rhel7:3.2
docker pull registry.access.redhat.com/rhscl/mysql-57-rhel7:5.7-5
```


#### Step 2 : Tag images 
```
docker tag $rh_reg/3scale-amp22/system:1.7 $my_reg/3scale-prd/system:1.7
docker tag $rh_reg/3scale-amp22/backend:1.6 $my_reg/3scale-prd/backend:1.6
docker tag $rh_reg/3scale-amp22/apicast-gateway:1.8 $my_reg/3scale-prd/apicast-gateway:1.8 
docker tag $rh_reg/3scale-amp22/wildcard-router:1.6 $my_reg/3scale-prd/wildcard-router:1.6
docker tag $rh_reg/3scale-amp20/memcached:1.4.15 $my_reg/3scale-prd/memcached:1.4.15
docker tag $rh_reg/rhscl/postgresql-95-rhel7:9.5 $my_reg/3scale-prd/postgresql-95-rhel7:9.5
docker tag $rh_reg/3scale-amp22/zync:1.6 $my_reg/3scale-prd/zync:1.6
docker tag $rh_reg/rhscl/redis-32-rhel7:3.2 $my_reg/3scale-prd/redis-32-rhel7:3.2
docker tag $rh_reg/rhscl/mysql-57-rhel7:5.7-5 $my_reg/3scale-prd/mysql-57-rhel7:5.7-5

```
### step 3 : Push images to OCP external registry 
```
docker push $my_reg/3scale-prd/system:1.7
docker push $my_reg/3scale-prd/backend:1.6
docker push $my_reg/3scale-prd/apicast-gateway:1.8 
docker push $my_reg/3scale-prd/wildcard-router:1.6
docker push $my_reg/3scale-prd/memcached:1.4.15
docker push $my_reg/3scale-prd/postgresql-95-rhel7:9.5
docker push $my_reg/3scale-prd/zync:1.6
docker push $my_reg/3scale-prd/redis-32-rhel7:3.2
docker push $my_reg/3scale-prd/mysql-57-rhel7:5.7-5

``` 
#### Step 4 : Create ImageStreem 
```
oc import-image  amp-system:latest --from=172.30.1.1:5000/3scale-prd/system:1.7 -n 3scale-prd --confirm  --insecure=true
oc import-image  amp-backend:latest --from=172.30.1.1:5000/3scale-prd/backend:1.6 -n 3scale-prd --confirm --insecure
oc import-image  amp-apicast:latest --from=172.30.1.1:5000/3scale-prd/apicast-gateway:1.8 -n 3scale-prd --confirm --insecure
oc import-image  amp-wildcard-router:latest --from=172.30.1.1:5000/3scale-prd/wildcard-router:1.6 -n 3scale-prd --confirm --insecure
oc import-image  memcached:1.4.15 --from=172.30.1.1:5000/3scale-prd/memcached:1.4.15 -n 3scale-prd --confirm --insecure
oc import-image  postgresql-95-rhel7:9.5 --from=172.30.1.1:5000/3scale-prd/postgresql-95-rhel7:9.5 -n 3scale-prd --confirm --insecure
oc import-image  amp-zync:latest --from=172.30.1.1:5000/3scale-prd/zync:1.6 -n 3scale-prd --confirm --insecure
oc import-image  redis-32-rhel7:3.2 --from=172.30.1.1:5000/3scale-prd/redis-32-rhel7:3.2 -n 3scale-prd --confirm --insecure
oc import-image  mysql-57-rhel7:5.7-5 --from=172.30.1.1:5000/3scale-prd/mysql-57-rhel7:5.7-5 -n 3scale-prd --confirm --insecure
```

#### Step 5 : Change the amp.yml 
```
1. vi amp.yml
2.replace registry.access.redhat.com with your registry. you can run sed :-
:%s/registry.access.redhat.com/172.30.1.1:5000/g
3.save changes
```

#### Step 6 : Deploy 3Scale 
```
oc new-app --file amp.yml --param TENANT_NAME=<prd-3scale> --param WILDCARD_DOMAIN=<OCP Wildcard Domain>
```
