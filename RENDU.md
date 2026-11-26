# Rendu — Atelier Kubernetes

> Complétez ce fichier au fil de l'atelier, puis faites un commit et un push (voir la section « Rendu » du README).

**Nom / Prénom :**
**Groupe :**

---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?
   > Il y a 3 nœuds :
   > - k3d-lab-server-0 : c'est le master (control-plane). Il gère le cluster et choisit sur quel nœud lancer chaque pod.
   > - k3d-lab-agent-0 et k3d-lab-agent-1 : ce sont les 2 workers. Ce sont eux qui font tourner les applications (les pods).
2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
   > docker ps montre un conteneur Docker pour chaque nœud : k3d-lab-server-0, k3d-lab-agent-0 et k3d-lab-agent-1. Il y a aussi un conteneur k3d-lab-serverlb, qui sert de load balancer.
   >
   > Dans k3d, un nœud n'est donc pas une vraie machine : c'est juste un conteneur Docker qui fait tourner k3s (une version légère de Kubernetes).

## Exercice 2 — Premier déploiement

3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
   > La commande a créé 3 objets :
   > - un Deployment (web) : il décrit ce qu'on veut, ici 1 pod nginx.
   > - un ReplicaSet (web-...) : créé par le Deployment, il s'assure que le bon nombre de pods tourne.
   > - un Pod (web-...-...) : c'est lui qui fait tourner le conteneur nginx.
   >
   > Le Service web, lui, a été créé par kubectl expose, pas par kubectl create deployment.
4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   > Un nouveau pod a été recréé tout de suite, avec un nom différent.
   >
   > C'est le ReplicaSet qui l'a recréé : il doit toujours garder 1 pod en marche. Dès qu'il voit qu'il en manque un, il en relance un nouveau. C'est l'auto-réparation de Kubernetes.

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   > Non, le message a disparu et la page « Welcome to nginx! » est revenue.
   >
   > Le nouveau pod repart de l'image nginx d'origine. Les fichiers modifiés dans un conteneur ne sont pas sauvegardés : quand le pod est supprimé, ses modifications sont perdues.
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   > Parce que le port-forward est relié à un seul pod précis, même si on le lance sur le Service. Quand ce pod est supprimé, le tunnel n'a plus de pod au bout et il s'arrête. Il ne bascule pas tout seul sur le nouveau pod, il faut le relancer.

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   > - kubectl create crée un objet à partir d'une commande. Si l'objet existe déjà, il renvoie une erreur. Il ne sert qu'à créer.
   > - kubectl apply part d'un fichier YAML qui décrit l'état voulu. Si l'objet n'existe pas, il le crée. S'il existe, il le met à jour avec les changements du fichier. On peut donc le relancer autant de fois qu'on veut.
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   > Le selector sert au Service à trouver ses pods : il envoie le trafic à tous les pods qui ont le même label (ici app: site).
   >
   > S'il ne correspond à aucun pod, le Service existe mais n'a aucun pod derrière lui. Le site ne répond pas, et kubectl get endpoints site ne montre aucune adresse.

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
   ```
   NAME                    READY   STATUS    RESTARTS   AGE   IP           NODE               NOMINATED NODE   READINESS GATES
   site-5cc664db9c-7xvh7   1/1     Running   0          24m   10.42.0.28   k3d-lab-agent-0    <none>           <none>
   site-5cc664db9c-dws7v   1/1     Running   0          25m   10.42.2.10   k3d-lab-server-0   <none>           <none>
   site-5cc664db9c-sw9kr   1/1     Running   0          24m   10.42.1.36   k3d-lab-agent-1    <none>           <none>
   ```
   > Les 3 réplicas sont répartis sur les 3 nœuds : un sur k3d-lab-agent-0, un sur k3d-lab-agent-1 et un sur k3d-lab-server-0. Avec k3d, le master accepte aussi des pods, c'est pour ça qu'un réplica s'y retrouve.
10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    > Le nouveau pod était en ErrImagePull, puis en ImagePullBackOff : Kubernetes n'arrivait pas à télécharger l'image.
    >
    > La cause m'a été donnée par kubectl describe pod -l app=site, dans la partie « Events » en bas : l'image nginx:9.99 n'existe pas (not found).
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    > Oui, le site restait accessible.
    >
    > Pendant une mise à jour, Kubernetes crée d'abord le nouveau pod et ne supprime les anciens que quand le nouveau est prêt. Comme le nouveau pod n'a jamais démarré, les anciens pods en nginx:1.28 ont continué à tourner et à servir le site.

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
    > https://sturdy-space-fishstick-7vqxwj45w99rcwwpj-8585.app.github.dev/
13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
    > On retrouve le Deployment avec kubectl get deploy mario (ou kubectl get deploy mario -o yaml pour le détail). C'est un Deployment normal, comme ceux créés en ligne de commande.
    >
    > Oui, Portainer et kubectl parlent au même composant : l'API Server du cluster (sur le master). Portainer n'est qu'une interface graphique qui envoie les mêmes demandes que kubectl.

## Bonus (facultatif)

14. Bonus réalisés et remarques :
    >

---

**Score obtenu avec `./scripts/check.sh` :** ... / ...
