# istio-knative-primer

## Install dependencies

```
# Install or upgrade minikube
brew install minikube
# brew upgrade minikube

minikube version                                                                                                                                            14 ↵
# minikube version: v1.17.1
# commit: 043bdca07e54ab6e4fc0457e3064048f34133d7e
```

```
# Make sure docker is installed
# Docker will be used to deploy the minikube cluster
docker ps -a
# CONTAINER ID   IMAGE      COMMAND      CREATED       STATUS          PORTS
# ...
```

# Create a kubernetes cluster (v1.17)

```
minikube start --cpus 4 --driver=docker --disk-size 20000mb --host-only-cidr 172.16.0.1/24 --kubernetes-version v1.17.6 --memory 16384 --nodes 1
# 😄  minikube v1.17.1 on Darwin 11.2.1
# ✨  Using the docker driver based on user configuration
# 👍  Starting control plane node minikube in cluster minikube
# 🚜  Pulling base image ...
# 🔥  Creating docker container (CPUs=4, Memory=16384MB) ...
# 🐳  Preparing Kubernetes v1.17.6 on Docker 20.10.2 ...
#     ▪ Generating certificates and keys ...
#     ▪ Booting up control plane ...
#     ▪ Configuring RBAC rules ...
# 🔎  Verifying Kubernetes components...
# 🌟  Enabled addons: storage-provisioner, default-storageclass
# 
# ❗  /usr/local/bin/kubectl is version 1.19.3, which may have incompatibilites with Kubernetes 1.17.6.
#     ▪ Want kubectl v1.17.6? Try 'minikube kubectl -- get pods -A'
# 🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default

kubectl get node
# NAME       STATUS   ROLES    AGE   VERSION
# minikube   Ready    master   24m   v1.17.6
```

# Install istio (for ingress + envoy sidecars)

```
# To install LATEST VERSION (v1.9.0)
curl -L https://istio.io/downloadIstio | sh -
alias istioctl='./istio-1.9/bin/istioctl
'
./istioctl version
# no running Istio pods in "istio-system"
# 1.9.0

istioctl install --set profile=default
# Detected that your cluster does not support third party JWT authentication. Falling back to less secure first party JWT. See https://istio.io/docs/ops/best-practices/security/#configure-third-party-service-account-tokens for details.
# ✔ Istio core installed
# ✔ Istiod installed
# ✔ Ingress gateways installed
# ✔ Installation complet
```

# Install knative

```
kubectl apply --filename https://github.com/knative/serving/releases/download/v0.21.0/serving-crds.yaml
# customresourcedefinition.apiextensions.k8s.io/certificates.networking.internal.knative.dev created
# customresourcedefinition.apiextensions.k8s.io/configurations.serving.knative.dev created
# customresourcedefinition.apiextensions.k8s.io/ingresses.networking.internal.knative.dev created
# customresourcedefinition.apiextensions.k8s.io/metrics.autoscaling.internal.knative.dev created
# customresourcedefinition.apiextensions.k8s.io/podautoscalers.autoscaling.internal.knative.dev created
# customresourcedefinition.apiextensions.k8s.io/revisions.serving.knative.dev created
# customresourcedefinition.apiextensions.k8s.io/routes.serving.knative.dev created
# customresourcedefinition.apiextensions.k8s.io/serverlessservices.networking.internal.knative.dev created
# customresourcedefinition.apiextensions.k8s.io/services.serving.knative.dev created
# customresourcedefinition.apiextensions.k8s.io/images.caching.internal.knative.dev created

kubectl apply --filename https://github.com/knative/serving/releases/download/v0.21.0/serving-core.yaml
# namespace/knative-serving created
# clusterrole.rbac.authorization.k8s.io/knative-serving-addressable-resolver created
# clusterrole.rbac.authorization.k8s.io/knative-serving-namespaced-admin created
# clusterrole.rbac.authorization.k8s.io/knative-serving-namespaced-edit created
# clusterrole.rbac.authorization.k8s.io/knative-serving-namespaced-view created
# clusterrole.rbac.authorization.k8s.io/knative-serving-core created
# clusterrole.rbac.authorization.k8s.io/knative-serving-podspecable-binding created
# serviceaccount/controller created
# clusterrole.rbac.authorization.k8s.io/knative-serving-admin created
# clusterrolebinding.rbac.authorization.k8s.io/knative-serving-controller-admin created
# customresourcedefinition.apiextensions.k8s.io/images.caching.internal.knative.dev unchanged
# customresourcedefinition.apiextensions.k8s.io/certificates.networking.internal.knative.dev unchanged
# customresourcedefinition.apiextensions.k8s.io/configurations.serving.knative.dev unchanged
# customresourcedefinition.apiextensions.k8s.io/ingresses.networking.internal.knative.dev unchanged
# customresourcedefinition.apiextensions.k8s.io/metrics.autoscaling.internal.knative.dev unchanged
# customresourcedefinition.apiextensions.k8s.io/podautoscalers.autoscaling.internal.knative.dev unchanged
# customresourcedefinition.apiextensions.k8s.io/revisions.serving.knative.dev unchanged
# customresourcedefinition.apiextensions.k8s.io/routes.serving.knative.dev unchanged
# customresourcedefinition.apiextensions.k8s.io/serverlessservices.networking.internal.knative.dev unchanged
# customresourcedefinition.apiextensions.k8s.io/services.serving.knative.dev unchanged
# image.caching.internal.knative.dev/queue-proxy created
# configmap/config-autoscaler created
# configmap/config-defaults created
# configmap/config-deployment created
# configmap/config-domain created
# configmap/config-features created
# configmap/config-gc created
# configmap/config-leader-election created
# configmap/config-logging created
# configmap/config-network created
# configmap/config-observability created
# configmap/config-tracing created
# horizontalpodautoscaler.autoscaling/activator created
# poddisruptionbudget.policy/activator-pdb created
# deployment.apps/activator created
# service/activator-service created
# deployment.apps/autoscaler created
# service/autoscaler created
# deployment.apps/controller created
# service/controller created
# horizontalpodautoscaler.autoscaling/webhook created
# poddisruptionbudget.policy/webhook-pdb created
# deployment.apps/webhook created
# service/webhook created
# validatingwebhookconfiguration.admissionregistration.k8s.io/config.webhook.serving.knative.dev created
# mutatingwebhookconfiguration.admissionregistration.k8s.io/webhook.serving.knative.dev created
# validatingwebhookconfiguration.admissionregistration.k8s.io/validation.webhook.serving.knative.dev created
# secret/webhook-certs created
```

# Install knative with istio adapter

```
kubectl apply --filename https://github.com/knative/net-istio/releases/download/v0.21.0/net-istio.yaml
# clusterrole.rbac.authorization.k8s.io/knative-serving-istio created
# gateway.networking.istio.io/knative-ingress-gateway created
# gateway.networking.istio.io/knative-local-gateway created
# service/knative-local-gateway created
# peerauthentication.security.istio.io/webhook created
# peerauthentication.security.istio.io/domainmapping-webhook created
# peerauthentication.security.istio.io/istio-webhook created
# mutatingwebhookconfiguration.admissionregistration.k8s.io/webhook.istio.networking.internal.knative.dev created
# validatingwebhookconfiguration.admissionregistration.k8s.io/config.webhook.istio.networking.internal.knative.dev created
# secret/istio-webhook-certs created
# configmap/config-istio created
# deployment.apps/networking-istio created
# deployment.apps/istio-webhook created
# service/istio-webhook created
```

# Start a minikube-tunnel and connect to the ingress gateway

```
minikube tunnel --cleanup
# ❗  The service istio-ingressgateway requires privileged ports to be exposed: [80 443]
# 🔑  sudo permission will be asked for it.
# 🏃  Starting tunnel for service istio-ingressgateway.
# Password
```

In another terminal

```
kubectl --namespace istio-system get service istio-ingressgateway
NAME                   TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                                      AGE
istio-ingressgateway   LoadBalancer   10.102.244.134   127.0.0.1     15021:32076/TCP,80:31513/TCP,443:31406/TCP,15443:30073/TCP   53m

INGRESS_URL=http://127.0.0.1
```

# Deploy your knative function

```
kubectl apply -f ./in/helloworld-java-spring--kservice.yaml
# service.serving.knative.dev/helloworld-java-spring created

kubectl get ksvc
# NAME                     URL                                                 LATESTCREATED                  LATESTREADY                    READY   REASON
# helloworld-java-spring   http://helloworld-java-spring.default.example.com   helloworld-java-spring-00001   helloworld-java-spring-00001   True

# curl ${INGRESS_URL} -H "Host: helloworld-java-spring.default.example.com"
curl ${INGRESS_URL}/ -H "Host: helloworld-java-spring.default.example.com"
# Hello from Emmanuel (Spring Boot App v1)!

curl ${INGRESS_URL}/wrong_path -H "Host: helloworld-java-spring.default.example.com"
# {"timestamp":"2021-02-26T03:30:54.334+0000","status":404,"error":"Not Found","message":"No message available","path":"/wrong_path"}
```

Remove the function

```
kubectl delete -f ./in/helloworld-java-spring--kservice.yaml
```

# Deploy your knative function with istio-envoy

```
kubectl label ns default istio-injection=enabled
# namespace/default labeled

kubectl apply -f ./in/helloworld-java-spring--kservice.yaml
# service.serving.knative.dev/helloworld-java-spring created

kubectl get pod
# NAME                                                       READY   STATUS    RESTARTS   AGE
# helloworld-java-spring-00001-deployment-5d8f9cbf96-gs2ck   3/3     Running   0          14s
```

# Configure external authorization

```
export EDITOR=vim
kubectl edit configmap istio -n istio-system
...
```

add the following

```
apiVersion: v1
data:
  mesh: |-
    # Add the following content to define the external authorizers.
    extensionProviders:
    - name: "sample-ext-authz-grpc"
      envoyExtAuthzGrpc:
        service: "ext-authz.default.svc.cluster.local"
        port: "9000"
    - name: "sample-ext-authz-http"
      envoyExtAuthzHttp:
        service: "ext-authz.default.svc.cluster.local"
        port: "8000"
        includeHeadersInCheck: ["x-ext-authz"]
    # You can keep the rest of the config as is
    defaultConfig:
      ...
```

Force istiod to reread its configuration (and update the proxy configs)

```
kubectl rollout restart deployment/istiod -n istio-system
# deployment.apps/istiod restarted
```

Deploy the external authorizer

```
kubectl apply -n default -f in/ext-authz--service.yaml
# service/ext-authz created
# deployment.apps/ext-authz created

AUTHZ_POD=$(kubectl get pod -l app=ext-authz -n default -o jsonpath={.items..metadata.name})
kubectl logs $AUTHZ_POD -n default -c ext-authz
# 2021/02/26 03:19:53 Starting gRPC server at [::]:9000
# 2021/02/26 03:19:53 Starting HTTP server at [::]:8000
```

# Validate access

```
kubectl apply -f in/curl--pod.yaml                                                                                                                           1 ↵
# pod/curl created
 
kubectl exec curl -c curl -- curl -s http://knative-local-gateway.istio-system/ -H "Host: helloworld-java-spring.default.example.com"
# Hello from Emmanuel (Spring Boot App v1)!
```

# FAQ

## How to install an older version of istio?

```
# To install v1.7.6
# Open https://github.com/istio/istio/releases
wget https://github.com/istio/istio/releases/download/1.7.6/istioctl-1.7.6-osx.tar.gz
tar xvzf istioctl-1.7.6-osx.tar.gz
```

## How to read the envoy config?

```
POD_NAME=...
POD_NAMESPACE=...
kubectl port-forward pod/${POD_NAME} 15000:15000 -n ${POD_NAMESPACE}

# In another terminal
curl http://localhost:15000/config_dump | yq e -P - > out/${POD_NAME}-envoy.yml
```

## How to test Istio without knative?

```
kubectl apply -f ./in/httpbin--service.yaml

kubectl exec curl -c curl -- curl -s http://httpbin:8000/headers -w "%{http_code}\n" -o /dev/null
# 200

# This operation requires istio-1.9+
# Protect /headers path of httpbin microservice
kubectl apply -f ./in/httpbin-ext-authz--authorization-policy-1.9.yaml

kubectl exec curl -c curl -- curl -s http://httpbin:8000/headers -w "%{http_code}\n" -o /dev/null
# 403

kubectl exec curl -c curl -- curl -s http://httpbin:8000/headers -w "%{http_code}\n" -o /dev/null -H "x-ext-authz: allow"
# 200

kubectl exec curl -c curl -- curl -s http://httpbin:8000/ip -w "%{http_code}\n" -o /dev/null
# 200

```

## How to delete my minikube cluster?

```
minikube delete
```
