## WSO2 API Manager - Istio Integration
### A Quick Copy/Paste & Setup Guide
#### Setting up the Environment
1. Install Minikube https://kubernetes.io/docs/tasks/tools/install-minikube/

2. Start a Minikube environment
```bash
minikube start --memory=10000 --cpus=4 --kubernetes-version=v1.15.2
```
3. Open another Terminal Tab (for loadbalancer in minikube)
```bash
sudo minikube tunnel
```
4. Download Istio
```bash
curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.2.2 sh -
```
5. Install Istio
```bash
cd istio-1.2.2
```
```bash
export PATH=$PWD/bin:$PATH
```
```bash
helm repo add istio.io https://storage.googleapis.com/istio-release/releases/1.2.2/charts/
```
```bash
kubectl create namespace istio-system
```
```bash
helm template install/kubernetes/helm/istio-init --name istio-init --namespace istio-system | kubectl apply -f -
```
6. Run the following command to make sure everything is fine
```bash
kubectl get crds | grep 'istio.io\|certmanager.k8s.io' | wc -l
```
Check if the output is **23** (which may take a few minutes sometimes)
7. Run
```bash
helm template install/kubernetes/helm/istio --name istio --namespace istio-system | kubectl apply -f -
```
8. Verify installation by running
```bash
kubectl get svc -n istio-system
```
9. Verify if the pods are running with
```bash
kubectl get pods -n istio-system
```
10. Enable policy enforcement on Istio gateways with
```bash
helm template install/kubernetes/helm/istio --namespace=istio-system -x templates/configmap.yaml --set global.disablePolicyChecks=false | kubectl -n istio-system replace -f -
```
See: https://istio.io/docs/tasks/policy-enforcement/enabling-policy/

11. Validate whether disablePolicyChecks has been set to false with
```bash
kubectl -n istio-system get cm istio -o jsonpath="{@.data.mesh}" | grep disablePolicyChecks
```
---------

### Installation of the Demo Artefacts

Follow the official documentation https://github.com/wso2/istio-apim/tree/1.0#istio-mixer-adapter-for-wso2-api-manager

#### When an API is deleted,
1. List the WSO2 artefacts installed in the istio environment
```bash
kubectl get rules -n istio-system
kubectl get HTTPAPISpec
kubectl get httpapispecbinding
```
2. Remove the WSO2 artefacts from the istio environment (e.g)
```bash
kubectl delete rules httpbinapi-rule -n istio-system
kubectl delete HTTPAPISpec httpbinapi-apispec
kubectl delete httpapispecbinding httpbinapi-binding
```