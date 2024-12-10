 # docker

# Projet Docker: Gestion des utilisateurs et mots de passe

Ce projet utilise deux conteneurs Docker pour gérer la génération et la mise à jour des utilisateurs et mots de passe :

- **Conteneur 1 - User Generator** : Ce conteneur génère des utilisateurs et des mots de passe aléatoires (128 bits) toutes les 30 secondes. Les utilisateurs et mots de passe sont stockés dans un fichier partagé.
  
- **Conteneur 2 - Password Updater** : Ce conteneur lit les utilisateurs générés par le conteneur User Generator et met à jour leurs mots de passe toutes les 30 secondes. Il utilise le même fichier partagé pour la lecture et l'écriture des mots de passe.

Les deux conteneurs sont connectés à un réseau Docker privé pour communiquer entre eux, et partagent un volume pour la gestion des données utilisateurs.

### Fonctionnement :

1. **User Generator** génère des utilisateurs et mots de passe toutes les 30 secondes et les écrit dans un fichier.
2. **Password Updater** récupère ces utilisateurs et met à jour leurs mots de passe toutes les 30 secondes.
3. Les deux conteneurs sont reliés à un même réseau Docker pour communiquer et partager les données via un volume Docker commun.

