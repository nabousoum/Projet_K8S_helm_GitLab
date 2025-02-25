
# Processus : Intégration Kubernetes avec Helm et GitLab

Ce document décrit les étapes nécessaires pour configurer Minikube, Helm et GitLab CI/CD afin de déployer des applications dans un cluster Kubernetes. Des captures d'écran sont intégrées à chaque étape pour illustrer le processus.

---

## 1. Configuration de Minikube

Dans cette étape, Minikube est démarré avec le driver Docker.

### Commande utilisée
```bash
minikube start --driver=docker
```
---

## 2. Vérification de l'état de Minikube

Après le démarrage, nous vérifions l'état de Minikube pour confirmer que le control plane et les composants du cluster fonctionnent correctement.

### Commandes utilisées
```bash
minikube status
kubectl get nodes
```

---

## 3. Intégration avec GitLab

Pour connecter Kubernetes à GitLab :

- **Création d'un projet GitLab :** Un nouveau projet est créé sur GitLab.
- **Configuration de l'agent GitLab :**  
  Un fichier de configuration YAML est ajouté au projet pour lier l'agent Kubernetes au cluster.

### Exemple de fichier YAML (`agent-config.yaml`)
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: gitlab-agent-config
data:
  token: "VOTRE_TOKEN_ICI"
  url: "https://gitlab.com"
```


---

## 4. Déploiement avec Helm

Helm est utilisé pour gérer les configurations et déployer des applications sur le cluster Kubernetes.

### Étapes

- **Ajout du dépôt Helm :**
  ```bash
  helm repo add gitlab https://charts.gitlab.io
  ```

- **Installation de l'agent GitLab sur Kubernetes :**
  ```bash
  helm install gitlab-agent gitlab/gitlab-agent --set config.token=VOTRE_TOKEN
  ```


---

## 5. Test de connexion entre GitLab et Kubernetes

### Étapes

1. **Création d'un fichier de configuration dans le dépôt GitLab :**  
   Ce fichier permettra de définir les interactions avec le cluster Kubernetes.

2. **Configuration du pipeline CI/CD :**  
   Modification du fichier `.gitlab-ci.yml` pour inclure les étapes de déploiement avec Docker et `kubectl`.

### Exemple de configuration dans `.gitlab-ci.yml`
```yaml
stages:
  - deploy

deploy:
  image: docker:latest
  stage: deploy
  script:
    - kubectl apply -f k8s/deployment.yaml
    - kubectl apply -f k8s/service.yaml
```


> **Note :**  
> Le pipeline CI/CD a échoué lors de la première tentative de déploiement. Après correction (ajustement des chemins de contexte Kubernetes dans `.gitlab-ci.yml`), le pipeline a été relancé avec succès. Le job `deploy` utilise un GitLab Runner basé sur Docker pour exécuter les commandes `kubectl`.

---

## 6. Gestion des Projets GitLab

- **Projet `k8s-data` :**  
  Créé avec une visibilité privée.

- **Projet `stock-ms` :**  
  Dispose d'un commit initial et d'une branche `main`.

---

## 7. Création de la Helm Chart et Vérification du Service

### Vérification du Service
Pour accéder au service déployé via Minikube :
```bash
minikube service stock-ms --url
```


### Récupération de l'Image Docker
Pour récupérer l'image depuis le registre GitLab :
```bash
docker pull registry.gitlab.com/ci-cd4410646/backend/microservice_jee_2025/stock-ms:3.0
```

### Auteur

👤 Seynabou SOUMARE 📧 seynabou@groupeisi.com

