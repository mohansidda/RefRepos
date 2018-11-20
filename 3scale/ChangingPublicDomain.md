
## Changing 3Scale admin/portal URL from : https://3scale-admin.13.251.251.251.nip.io https://UAT-3scale-admin.13.251.251.251.nip.io
go to 3scale project Deployments > system-app > Environment and change "TENANT_NAME" in system-provider and system-developer from "3scale" (default) to for example "UAT-3Scale" and save changes
```
1.oc rsh -c system-provider  <system-app POD>
```

```
2. bundle exec rails console
```

### Admin Portal : 
```
a = Account.find_by_self_domain '{TENANT}-admin.{WILDCARD_DOMAIN}'
a.self_domain = '{NEW_DOMAIN}.{WILDCARD_DOMAIN}'
a.save!

#### Example : 
a = Account.find_by_self_domain '3scale-admin.13.251.251.251.nip.io'
a.self_domain = 'UAT-3Scale-admin.13.251.251.251.nip.io'
a.save!
```


#### Developer Portal :
```
oc rsh -c system-developer <system-app POD>

a = Account.find_by_domain '{TENANT}.{WILDCARD_DOMAIN}'
a.domain = '{NEW_DOMAIN}.{WILDCARD_DOMAIN}'
a.save!

#### Example : 
a = Account.find_by_domain '3scale.13.251.251.251.nip.io'
a.domain = 'UAT-3Scale.13.251.251.251.nip.io'
```
