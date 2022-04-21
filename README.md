# ingress-with-tls On Docker-Desktop
Ingress with TLS 
- Install Ingress controller using helm on your docker destkop 
```
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```
- A few pods should start in the ingress-nginx namespace:
```
kubectl get pods --namespace=ingress-nginx
```
- After a while, they should all be running. The following command will wait for the ingress controller pod to be up, running, and ready:
```
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=120s
```
- Let's create a simple web server and the associated service:

```
kubectl create deployment demo --image=httpd --port=80
kubectl expose deployment demo
```
- Lets create self signed certs. Command will create a cert with public and private key.
```
openssl req -x509 -newkey rsa:4096 -sha256 -nodes -keyout tls.key -out tls.crt  -subj "/CN=localhost" -days 365
```
- Create a kubernates secret using certs 
```
kubectl create secret tls demo-tls --key=tls.key --cert=tls.crt
``
- Then create an ingress resource. The following example uses an host that maps to localhost:
```
kubectl apply -f ingress.yaml
```
- Now, forward a local port to the ingress controller:
```
kubectl port-forward --namespace=ingress-nginx service/ingress-nginx-controller 8080:80
```
- Now clieck on https://localhost . You should see a page shows "IT works" 