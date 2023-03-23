# Procédure Docker / Kubernetes
## Créer une application (Pod & Service)
Créer app-service.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp
  labels:
    app: myapp
spec:
  containers:
    - name: app
      image: choco85470/myapp:v1
      ports:
        - containerPort: 3000
      env:
        - name: MYSQL_HOST
          value: "127.0.0.1"
        - name: MYSQL_USER
          value: "root"
        - name: MYSQL_PASSWORD
          value: "secret"
        - name: MYSQL_DB
          value: "todos"
    - name: mysql
      image: mysql:5.7
      env:
        - name: MYSQL_ROOT_PASSWORD
          value: "secret"
        - name: MYSQL_DATABASE
          value: "todos"
      ports:
        - containerPort: 3306
---
apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 3000
      nodePort: 30000
  type: NodePort
```

## Déployer l'application
```powershell
kubectl apply -f app-service.yaml
```
![image](https://user-images.githubusercontent.com/73076854/226843346-ceeec87c-70ce-4344-b31b-88de9717079c.png)

## Ouvrir les ports du Pod
```powershell
kubectl port-forward service/app-service 3000:3000
```
![image](https://user-images.githubusercontent.com/73076854/226843383-226aa25d-20bd-4a4b-8f65-8989ad14bbb4.png)

## Depuis votre navigateur
```html
http://localhost:3000
ou
http://127.0.0.1:3000
```
![Screenshot_1](https://user-images.githubusercontent.com/73076854/226843730-f16c9983-c99e-4228-82dc-f606d47621f6.png)
