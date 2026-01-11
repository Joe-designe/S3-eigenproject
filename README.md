# 🚀 Geautomatiseerde Kubernetes Cluster (K3s) met Ansible & GitOps

![Status](https://img.shields.io/badge/Status-Completed-success) ![Kubernetes](https://img.shields.io/badge/Kubernetes-K3s-blue) ![IaC](https://img.shields.io/badge/IaC-Ansible-red) ![GitOps](https://img.shields.io/badge/GitOps-ArgoCD-orange)

Dit project bevat de volledige *Infrastructure as Code* (IaC) en *GitOps* configuratie voor het automatisch uitrollen en beheren van een Kubernetes-cluster. Het project is ontwikkeld voor de casus **"TechSoft"** om consistentie, beveiliging en snelheid van deployments te waarborgen.

## 📋 Overzicht

Het doel van dit project is om handmatige configuraties te elimineren. Met één druk op de knop wordt een cluster opgezet, beveiligd en voorzien van applicaties.

### Kernfunctionaliteiten
* **Automatisering:** Volledige cluster-installatie (Master + Workers) via **Ansible**.
* **GitOps:** Applicatiebeheer via **ArgoCD**. Wijzigingen in deze repo worden automatisch gesynchroniseerd naar de cluster.
* **Storage:** Persistente opslag geregeld via **Longhorn** (Distributed Block Storage).
* **Monitoring:** Inzicht in metrics via de **Kube-Prometheus-Stack** (Grafana & Prometheus).
* **Security:**
    * **Firewall (UFW):** 'Default Deny' policy op alle nodes.
    * **HTTPS:** Beveiligde verbindingen via Traefik en Cert-Manager (SelfSigned).
    * **Auth:** Beveiligde toegang tot Longhorn UI via Traefik Middleware.

## 🏗 Architectuur

De infrastructuur bestaat uit virtuele machines die via een intern netwerk communiceren.

| Rol | OS | Omschrijving |
| :--- | :--- | :--- |
| **Ansible Controller** | Ubuntu | Beheermachine die de playbooks uitvoert. |
| **Master Node** | Ubuntu 22.04 | K3s Control Plane & API Server. |
| **Worker Node 1** | Ubuntu 22.04 | Draait workloads (Pods). |
| **Worker Node 2** | Ubuntu 22.04 | Draait workloads (Pods). |

## 📂 Projectstructuur

* `ansibleproject/` - Bevat de Ansible playbooks en inventory voor de infrastructuur.
    * `deploy_cluster.yml` - Hoofdscript voor installatie K3s, ArgoCD, Config en UFW regels.
* `Cluster/Applications/` - De *Single Source of Truth* voor ArgoCD.
    * Alle YAML-bestanden in deze map worden automatisch uitgerold door de **Root App**.

## 🚀 Installatie

### 1. Voorbereiding
Zorg dat SSH-toegang via keys (Ed25519) is ingesteld vanaf de Ansible Controller naar de nodes.

### 2. Configureer Inventory
Pas het bestand `inventory.yml` aan met de juiste IP-adressen van je nodes.

### 3. Start Deployment
Voer het Ansible playbook uit om de cluster te bouwen:

```bash
ansible-playbook -i inventory.yml prepare_nodes.yml -K
ansible-playbook -i inventory.yml deploy_cluster.yml -K
```
🔄 GitOps Workflow (ArgoCD)
Na installatie draait ArgoCD in de cluster. Het luistert naar wijzigingen in de map Cluster/Applications.
**Aanbevolen!**
**Wijzig longhorn wachtwoord**

Wijziging: Commit een aanpassing (bijv. replicaCount of een nieuwe app) naar de main branch.

Sync: ArgoCD detecteert de wijziging (Auto-Sync) en past de cluster-status aan.

Self-Heal: Handmatige wijzigingen op de cluster worden automatisch teruggedraaid door ArgoCD.

🛡️ Security & ITIL
Change Management: De implementatie van HTTPS en Cert-Manager is uitgevoerd volgens een formeel ITIL Change Management proces (RFC), inclusief testen en documentatie.

Netwerk: Alleen noodzakelijke poorten (22, 80, 443, 6443) staan open. Interne cluster-communicatie verloopt via een vertrouwd subnet.

ℹ️ Status & Disclaimer
Versie: 1.0 (Final Release).

Updates: Automatische OS-updates zijn bewust uitgeschakeld om stabiliteit van kernel-modules (iSCSI) te garanderen. Updates verlopen via beheerde Ansible-taken.
