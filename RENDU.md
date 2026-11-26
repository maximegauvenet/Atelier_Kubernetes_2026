# Rendu — Atelier Kubernetes

> Complétez ce fichier au fil de l'atelier, puis faites un commit et un push (voir la section « Rendu » du README).

**Nom / Prénom :**
**Groupe :**

---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?
   > La commande affiche 3 nœuds, tous au statut Ready :
   > - k3d-lab-server-0, de rôle control-plane,master : c'est le nœud maître. Il héberge le plan de contrôle (API Server, scheduler, controller manager, base etcd/SQLite) qui reçoit les commandes kubectl, décide où placer les pods et surveille l'état du cluster.
   > - k3d-lab-agent-0 et k3d-lab-agent-1, de rôle none : ce sont les deux workers. Ils n'ont pas de rôle de pilotage, ils exécutent les pods des applications (via le kubelet et le runtime containerd).
2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
   > docker ps montre les conteneurs Docker créés par k3d : k3d-lab-server-0, k3d-lab-agent-0 et k3d-lab-agent-1 (image rancher/k3s), plus k3d-lab-serverlb (image k3d-proxy), le load balancer qui publie le port 8081 du Codespace vers le port 80 du cluster. On retrouve donc exactement les 3 nœuds de kubectl get nodes, chacun sous la forme d'un conteneur.
   >
   > Concrètement, un nœud dans k3d n'est pas une machine physique ou virtuelle : c'est un conteneur Docker qui fait tourner k3s (une distribution Kubernetes légère). Le conteneur server lance k3s en mode serveur (control plane), les conteneurs agent lancent k3s en mode agent (worker). Les pods tournent à l'intérieur de ces conteneurs grâce à leur propre containerd (des conteneurs dans des conteneurs).

## Exercice 2 — Premier déploiement

3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
   >
4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   >

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   >
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   >

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   >
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   >

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
   ```

   ```
10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    >
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    >

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
    >
13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
    >

## Bonus (facultatif)

14. Bonus réalisés et remarques :
    >

---

**Score obtenu avec `./scripts/check.sh` :** ... / ...
