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

```bash

```
