# set miniship IP
```
export MINISHIFT_IP=$(minishift ip)
```
# demo-1: hello world

* Create gateway
```
oc apply -f istio_gateway.yaml
```
* Create deployment.yaml with istioctl kube-inject
```
istioctl kube-inject -f deployment.yaml | oc apply -f -
```
* Create helloworld.yaml with istioctl kube-inject
```
istioctl kube-inject -f helloworld.yaml | oc apply -f -
```
* understand that helloworld is 3 services, use oc exec to see 2 other services

# demo-2: canary

* create canary.yaml with istioctl kube-inject
```
istioctl kube-inject -f canary.yaml | oc apply -f -
for i in {1..10} ; do curl $MINISHIFT_IP:31380 -H "Host: hello-canary.example.com" ; echo ; done
```

# demo-3: circuit breaking
* create circuitbreaking.yaml with istioctl kube-inject
```
istioctl kube-inject -f circuitbreakingy.yaml | oc apply -f -
oc apply -f fortio.yaml
oc get pods
oc exec -it $FORTIO_POD  -c fortio /usr/bin/fortio -- load -curl http://hello-circuitbreaking:8080
oc exec -it $FORTIO_POD  -c fortio /usr/bin/fortio -- load -c 5 -qps 0 -n 20 --loglevel Warning http://hello-circuitbreaking:8080
```

# demo-4: rate limiting
* create deployment_rate_limit.yaml with istioctl kube-inject
* apply rate_limit.yaml
```
istioctl kube-inject -f deployment_rate_limit.yaml | oc apply -f -
oc apply -f rate_limit.yaml
oc apply -f fortio.yaml
oc get pods
curl -H "host: hello.example.com" $MINISHIFT_IP:31380
```


