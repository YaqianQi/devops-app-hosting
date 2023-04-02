# ML App

## Machine learning App
Given comment, the application infers the sentiment. 

## Start 
```
vagrant up
```

## Virtual Machine 
```
vagrant ssh
```
## Set up Kubernetes on your minikube
```
vagrant@minikube:~$ kubectl apply -f /vagrant/services/
deployment.apps/mlapp created
pod/client-pod created
deployment.apps/mlapp-fallback created
service/mlapp-fallback created
ingress.networking.k8s.io/app-ingress created
service/mlapp created
```

## Check pods
```
vagrant@minikube:~$ kubectl get pods
NAME                            READY   STATUS              RESTARTS   AGE
client-pod                      0/1     ContainerCreating   0          8s
mlapp-855b8cdc87-784d5          0/1     ContainerCreating   0          8s
mlapp-855b8cdc87-79xlf          0/1     ContainerCreating   0          8s
mlapp-855b8cdc87-f46qg          0/1     ContainerCreating   0          8s
mlapp-fallback-6f67dd9c-2cfwd   0/1     ContainerCreating   0          8s
mlapp-fallback-6f67dd9c-mlwtf   0/1     ContainerCreating   0          8s
mlapp-fallback-6f67dd9c-zpt8x   0/1     ContainerCreating   0          8s
```

## Check services 
```
vagrant@minikube:~$ kubectl get svc
NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes       ClusterIP   10.96.0.1        <none>        443/TCP        66s
mlapp            ClusterIP   10.111.172.170   <none>        80/TCP         18s
mlapp-fallback   NodePort    10.97.194.77     <none>        80:30997/TCP   18s
```
## Ingress
```
vagrant@minikube:~$ minikube addons enable ingress
🔎  Verifying ingress addon...

kubectl get pods -n kube-system

kubectl apply -f /vagrant/services/ingress.yaml

```
### Add host
```
vagrant@minikube:~$ minikube ip
192.168.49.2

echo "192.168.49.2  mlapp.com" | sudo tee -a /etc/hosts
```


## Serivce call 
Basic 
```

url = 'http://mlapp.com/predict_ml'
data = {'text': 'I hate this product.'}
headers = {'Content-Type': 'application/json'}

response = requests.post(url, json=data, headers=headers)
# expected_return == "negative" 
```


Fallback
```
url = 'http://mlapp.com/predict_keywords'
data = {'text': 'I love this product.'}
headers = {'Content-Type': 'application/json'}

response = requests.get(url, json=data, headers=headers)
sentiment = response.json()
assert sentiment["prediction"] ==  'positive' 
```
