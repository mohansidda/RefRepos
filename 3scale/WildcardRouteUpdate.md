Before running the steps check the OCP version it should be openshift v3.9.33 or above. 



 ## Steps : 
 
```

oc project default
oc adm router --replicas=0 
oc set env dc/router ROUTER_ALLOW_WILDCARD_ROUTES=true
oc scale dc/router --replicas=1
oc project <3scale project> 
oc export route apicast-wildcard-router -o yaml > wildcard-router.yml
           make following change spec : 
            a. add wildcard in front of the existing host value like : 
	    host: wildcard.apicast-wildcard.${WILDCARD_DOMAIN}
           b. change the wildcardPolicy like :
	      wildcardPolicy: Subdomain

        oc delete -f wildcard-router.yml
       oc apply -f wildcard-router.yml
````	
If you see the <3scale project>  routes, you can observe something * added in the hostname. 

image.png


Once you configure the wildcard router to accept *.apicast-wildcard.13.251.251.251.nip.io
you don't need to create any routes for new 3Sacale services. However, you need to keep your staging and production URL's similar to this : 

https://hello-world-staging.apicast-wildcard.13.251.251.251.nip.io:443

https://hello-world-production.apicast-wildcard.13.251.251.251.nip.io:443

image.png


This means. you can keep the service name and environment in * place after that *.<wildcard domain > has to be standard and consistent for all the new services. 


