# Procédure Docker / Kubernetes

## Installer Docker Desktop

![image](https://user-images.githubusercontent.com/73076854/220911935-271707c6-f2db-4809-884b-64bb9d707ca0.png)

## Activer Kubernetes

![image](https://user-images.githubusercontent.com/73076854/220911992-b651b34e-2db6-41e7-b6fe-597693af8d5b.png)

## Mettre à jour Kubernetes
```powershell
winget install -e --id Kubernetes.kubectl
```
![image](https://user-images.githubusercontent.com/73076854/220912055-35a095bd-ddf2-4bcf-868e-01f337456b57.png)

## Installer Kubernetes-Dashboard
```powershell
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

## Lancer le proxy Kubernetes
```powershell
kubectl proxy
```
![image](https://user-images.githubusercontent.com/73076854/220912329-1e4455ed-8ff0-40bb-a37b-0ad1ad695de4.png)

## Ouvrir dans le navigateur 
```html
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

## Création d'un compte "Service Account"
Créer un fichier sample-user.yaml
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

## Création d'un ClusterRoleBinding
Créer un fichier clusterrolebinding.yaml
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```
## Appliquer les YAML
```bash
kubectl apply -f sample-user.yaml
kubectl apply -f cluster.yaml
```
## Génération du token de connexion
```powershell
kubectl -n kubernetes-dashboard create token admin-user
```
## Insérer le token générer dans le navigateur (Méthode Jeton) :
```bash
eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.......
```
![image](https://user-images.githubusercontent.com/73076854/220912911-b6f6ac53-3357-4135-95f0-aa55e4f50253.png)

## Insérer le token générer dans le navigateur (Méthode Kubeconfig) :
```powershell
$TOKEN="eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9......."
kubectl config set-credentials docker-for-desktop --token="${TOKEN}"
kubectl config set-credentials docker-desktop --token="${TOKEN}"
```
![image](https://user-images.githubusercontent.com/73076854/220913036-9dfd663e-aa06-477f-addf-edc9b8fab474.png)

## Insérer le token générer dans le navigateur (Méthode 2 Kubeconfig) :
Editer le fichier de config dans C:\Users\Username\.kube\config
Chercher les lignes :
```yaml
[…]
- name: docker-desktop
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRV…
[…]
```
Ajouter la ligne suivante :
```yaml
[…]
- name: docker-desktop
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRV…
    token: eyJhbGciOiJSUzI1NiIs…
[…]
```

## (BONUS) Pour supprimer ServiceAccount et ClusterRoleBinding :
```powershell
kubectl -n kubernetes-dashboard delete serviceaccount admin-user
kubectl -n kubernetes-dashboard delete clusterrolebinding admin-user
```

## Voici l’aperçu de l’interface Kubernetes Dashboard
![image](https://user-images.githubusercontent.com/73076854/220913354-aae580d0-e615-46fc-9ffb-0b4fc4dc4b0c.png)

## Déployer le fichier pod.yaml
```bash
Kubectl apply -f pod.yaml
```
![image](https://user-images.githubusercontent.com/73076854/220914763-d11cb9ed-91bc-4771-a045-51100e1e8ce5.png)

## Vérifier si le Pod est installer et fonctionnel 
Se rendre sur le dashboard kubernetes.

![image](https://user-images.githubusercontent.com/73076854/220914879-ae9262e4-18e8-42fb-be73-ab5180c6c11a.png)

## Vérifier dans les logs du Pod, la trace d’un ping
Cependant en naviguant dans Kubernetes Dashboard a la recherhe des logs de notre Cluster et de notre application, nous remarquons aucune trace d’un ping.
Il est possible que la consigne ne soit pas claire, et qu’il faut ouvrir une console dans notre application et effectuer un ping.

![image](https://user-images.githubusercontent.com/73076854/220914949-681883f6-abc7-424a-b3a8-63e9cccf70d5.png)
![image](https://user-images.githubusercontent.com/73076854/220914962-78297ba1-4be7-4f69-a5b3-002aab8a56ce.png)

## Supprimer le Pod
```powershell
Kubectl delete pod myapp
```
![image](https://user-images.githubusercontent.com/73076854/220915065-6cf07afb-2659-4546-9a42-f3607886dd14.png)

### Sinon via l’interface Kubernetes Dashboard

![image](https://user-images.githubusercontent.com/73076854/220915130-e8be472c-a11f-4893-8225-86503b05d51e.png)

## Créer un fichier deployment.yaml (Sans la persistance BDD)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app.kubernetes.io/name: load-balancer
  name: app
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: load-balancer
  template:
    metadata:
      labels:
        app.kubernetes.io/name: load-balancer
    spec:
      containers:
      - image: choco85470/myapp:v1
        name: app
        command: ["/bin/sh"]
        args: ["-c", "yarn install && yarn run dev"]
        ports:
        - containerPort: 3000
        workingDir: "/app"
        volumes:
          - ./:/app
        environment:
          MYSQL_HOST: mysql
          MYSQL_USER: root
          MYSQL_PASSWORD: secret
          MYSQL_DB: todos
      - image: mysql:8.0
        name: mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: "secret"
        - name: MYSQL_DATABASE
          value: "todos"
    volumes:
      todo-mysql-data:
```

## Lancer notre application
```bash
Kubectl apply -f deployment.yaml
```

### En cas d’erreur, le faire via l’interface graphique Kubernetes Dashboard

![image](https://user-images.githubusercontent.com/73076854/220915538-2c4a5ce2-4f1c-4496-ad4a-515ba9fba863.png)
![image](https://user-images.githubusercontent.com/73076854/220915566-55e0acd5-48e0-467a-a388-e36cce218722.png)

## Accéder à notre application
Notre application n’est pas accessible depuis notre navigateur. Pour régler ce problème nous devons créer un Service permettant de faire du Port Forwarding pour pouvoir utiliser notre application de todolist.
```bash
Kubectl expose deployment app –type=LoadBalancer –name=app
```
![image](https://user-images.githubusercontent.com/73076854/220915637-57a21f06-bd1c-4a65-aa42-b1203302a332.png)

## Publier notre code sur notre dépôt 
```git
git config --global user.name "username"
git config --global user.email username@mail.com
Git init app/ (to create the git repo in the app directory)
git add . (to add all the files in the current directory in the .git)
git commit -m "Commit Cours 2" (to commit modification)
git remote add origin https://github.com/username/A2SR.git
git push -u origin main (to push the main branch)
```
