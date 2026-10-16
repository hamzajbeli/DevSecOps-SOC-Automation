# 🔄 DevSecOps & SOC automatisé avec Ansible

> Architecture en deux machines virtuelles, entièrement provisionnée par des
> playbooks Ansible : un pipeline CI/CD sécurisé (vm-DevSecOps) et un SOC avec
> détection, orchestration et enrichissement des alertes (vm-SOC).

## Contexte
Projet d'équipe [année], réalisé avec Rached Jaafar et Samar Souid.
Objectif : un environnement reproductible, déployable de façon automatisée, qui
couvre la sécurité du développement et la détection d'incidents.

## Architecture

![Architecture DevSecOps et SOC](architecture.jpg)

### vm-DevSecOps : pipeline CI/CD
GitHub et Jenkins pilotent le pipeline. La sécurité est intégrée dès le code :

| Étape | Outil | Playbook |
|---|---|---|
| Build | Maven | `maven.yml` |
| Analyse statique (SAST) | SonarQube | `sonarqube.yml` |
| Analyse des dépendances | OWASP Dependency-Check | `OWASP-DC.yml` |
| Test dynamique (DAST) | OWASP ZAP | `OWASP-ZAP.yml` |
| Scan des images Docker | Trivy | `trivy.yml` |
| Artefacts | Nexus Repository | `nexus-repository.yml` |
| Conteneurs | Docker | `docker.yml`, `docker-build.yml` |
| Orchestration CI | Jenkins | `jenkins.yml` |
| Supervision | Prometheus, Grafana | `prometheus.yml`, `garafana.yml` |
| Détection | Suricata, agent Wazuh | `suricata.yml`, `agent-wazuh.yml` |

### vm-SOC : détection, orchestration, réponse
| Composant | Rôle | Playbook |
|---|---|---|
| Wazuh (manager, indexer, dashboard) | Collecte et corrélation des alertes, agents Windows, Linux et macOS | `wazuh.yml` |
| Shuffle | Orchestration des workflows de réponse (SOAR) | `shuffle.yml` |
| MISP | Base de threat intelligence | `MISP.yml` |
| IRIS | Gestion des incidents | `IRIS.yml` |

Selon l'architecture, les alertes enrichies (VirusTotal, MISP, Cortex, module d'IA)
sont transmises aux analystes via Slack. **Cortex, VirusTotal, l'IA et les
notifications Slack sont décrits dans le schéma, mais n'ont pas de playbook dans
ce dépôt.**

## Structure du dépôt
```
Gestion local/
├── vm-DevSecOps/   playbooks du pipeline + captures/
└── vm-SOC/         playbooks du SOC + captures/
```

## Captures
Les dossiers `captures/` contiennent les preuves de fonctionnement de chaque outil.

## Limites
- Aucun scénario de bout en bout documenté (alerte Wazuh → Shuffle → réponse)
- Pas de playbook pour Cortex, l'enrichissement VirusTotal, l'IA et Slack
- Aucune mesure de performance ni de taux de détection
- Environnement de laboratoire : mots de passe et paramètres à durcir avant tout usage réel

## Perspectives
Playbook unique de bout en bout, scénario d'attaque simulé avec alerte et réponse
automatisée, tests Ansible-lint, gestion des secrets avec Ansible Vault.

## Technologies
Ansible, Jenkins, GitHub, Maven, SonarQube, OWASP Dependency-Check, OWASP ZAP,
Trivy, Nexus, Docker, Prometheus, Grafana, Wazuh, Suricata, Shuffle, MISP, IRIS.

## Équipe
- Hamza Jbeli
- Rached Jaafar
- Samar Souid
