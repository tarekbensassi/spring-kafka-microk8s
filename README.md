# Spring Kafka Signal Monitoring App with MicroK8s

## 📝 Description

Ce projet est une application Spring Boot intégrée à **Apache Kafka**, conçue pour le **monitoring de signaux** (par exemple : données de capteurs, événements temps réel, etc.). L'application est conteneurisée avec **Docker**, déployée localement via **MicroK8s** et automatisée à l'aide d'un pipeline **Jenkins**.

---

## ⚙️ Technologies Utilisées

- Java / Spring Boot
- Apache Kafka
- Docker & Docker Compose
- Kubernetes (via MicroK8s)
- Jenkins (CI/CD)
- GitHub (via SSH)
- YAML pour les déploiements Kubernetes

---

## 📁 Structure du Projet


---

## 🚀 Exécution du pipeline Jenkins

### 🔑 Prérequis

- Jenkins installé avec le plugin Docker & Kubernetes CLI
- Accès à un environnement MicroK8s configuré (avec le registre Docker interne)
- Une clé SSH GitHub ajoutée à Jenkins avec l’ID : `github-ssh-key`
- Le projet hébergé sur GitHub (SSH access)

### 📦 Étapes automatisées

1. **Checkout depuis GitHub** via SSH
2. **Lancement de Kafka** avec `docker-compose`
3. **Build de l'image Docker** via `microk8s docker build`
4. **Déploiement** sur MicroK8s :
   - Suppression de l'ancien déploiement
   - Application des fichiers YAML (`deployment.yaml`, `service.yaml`)
   - Mise à jour dynamique de l'image

---

## 🔍 Fonctionnement de l'application

- **Producteur Kafka** : génère des signaux (par exemple toutes les 5 secondes) et les publie sur un topic Kafka.
- **Consommateur Kafka** : lit les messages du topic, les traite ou les stocke (futur extensible à Prometheus, base de données, etc.).
- Objectif : permettre le **monitoring en temps réel** des signaux reçus.

---

## 🧪 Commandes utiles (local)

```bash
# Lancer Kafka et Zookeeper
docker-compose up -d

# Vérifier les logs
docker-compose logs -f

# Build manuel de l'image
microk8s docker build -t spring-kafka-app:latest .

# Appliquer le déploiement Kubernetes
microk8s kubectl apply -f k8s/
