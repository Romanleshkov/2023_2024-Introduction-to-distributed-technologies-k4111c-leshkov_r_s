University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4111c  
Author: Leshkov Roman Sergreevich  
Lab: Lab3  
Date of create: 30.11.2023  
Date of finished: 30.11.2023  

Ход лабораторной работы:

1. Запускается кластер minikube

kubectl create configmap lab3-configmap --from-literal=REACT_APP_USERNAME="Roman Leshkov"  --from-literal=REACT_APP_COMPANY_NAME="ITMO" -o yaml --dry-run > kube/lab3-configmap.yaml

kubectl create deployment lab3-replicaset --image=ifilyaninitmo/itdt-contained-frontend:master -o yaml --port=3000 -r 2 --dry-run | sed 's/Deployment/ReplicaSet/g' | sed '/strategy/d' | sed 's/resources/env:\n        - name: REACT_APP_USERNAME\n          valueFrom:\n            configMapKeyRef:\n              name: lab3-configmap\n              key: REACT_APP_USERNAME\n        - name: REACT_APP_COMPANY_NAME\n          valueFrom:\n            configMapKeyRef:\n              name: lab3-configmap\n              key: REACT_APP_COMPANY_NAME\n        resources/g' > kube/lab3-replicaset.yaml


kubectl create service nodeport lab3-service --tcp=3000:3000 -o yaml --dry-run > kube/lab3-service.yaml

openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out minikube.crt -keyout minikube.key

kubectl create secret tls lab3-tls --cert minikube.crt --key minikube.key --dry-run -o yaml > kube/lab3-tls.yaml

minikube addons enable ingress

kubectl create ingress lab3-ingress --dry-run -o yaml --rule=rlesh-lab3.ru/*=lab3-service:3000,tls=lab3-tls > kube/lab3-ingress.yaml

