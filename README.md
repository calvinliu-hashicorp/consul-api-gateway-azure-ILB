# consul-api-gateway-test

### Steps
1. Clone repo
2. Create a Private AKS Cluster
3. `kubectl apply -k "github.com/hashicorp/consul-api-gateway/config/crd?ref=v0.4.0"`
4. `helm repo add hashicorp https://helm.releases.hashicorp.com && helm repo update`
5. Install Consul
   * Helm: `helm install --values values.yaml consul hashicorp/consul --create-namespace --namespace consul --version "1.0.2"`
   * Consul K8S: `consul-k8s install -config-file=values.yaml -set global.image=hashicorp/consul:1.14.2`
6. `kubectl apply --filename services`
7. `kubectl apply --filename api-gw/consul-api-gateway.yaml --namespace consul && kubectl wait --for=condition=ready gateway/api-gateway --namespace consul --timeout=90s && kubectl apply --filename api-gw/routes.yaml --namespace consul`

### Get ILB IP addresses
* Consul UI: `kubectl get svc -n consul consul-ui --output jsonpath='{.status.loadBalancer.ingress[0].ip}'`
* API Gateway: `kubectl get svc -n consul api-gateway --output jsonpath='{.status.loadBalancer.ingress[0].ip}`


### Visit the following urls in the browser
* https://{CONSUL_UI_ILB_IP}/ui/
* http://{API_GATEWAY_ILB_IP}/echo
* http://{API_GATEWAY_ILB_IP}/helloworld
