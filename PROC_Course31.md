# Procédure Docker / Kubernetes
## Créer une application (Pod & Service)
Créer application.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-service
spec:
  containers:
    - name: app-service
      image: choco85470/myapp:v1
      command: ["/bin/sh"]
      args: ["-c", "yarn install && yarn run dev"]
      ports:
        - containerPort: 3000
      workingDir: "/app"
---
apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  selector:
    app: app-service
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
  type: LoadBalancer
```

## Déployer l'application
```powershell
kubectl apply -f application.yaml
```
![image](https://user-images.githubusercontent.com/73076854/226843346-ceeec87c-70ce-4344-b31b-88de9717079c.png)

## Ouvrir les ports du Pod
```powershell
kubectl port-forward app-service 3000:3000
```
![image](https://user-images.githubusercontent.com/73076854/226843383-226aa25d-20bd-4a4b-8f65-8989ad14bbb4.png)

## Depuis votre navigateur
```html
http://localhost:3000
ou
http://127.0.0.1:3000
```
![Screenshot_1](https://user-images.githubusercontent.com/73076854/226843730-f16c9983-c99e-4228-82dc-f606d47621f6.png)
