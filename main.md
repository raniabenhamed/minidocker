

#### Création du répertoire du projet  

```bash
mkdir user-generator-project
cd user-generator-project
```

#### Création du Dockerfile pour `user-generator`  

```dockerfile
# Dockerfile pour user-generator
FROM debian:bullseye

# Installer Python et pip
RUN apt-get update && apt-get install -y python3 python3-pip

# Copier le script dans le conteneur
COPY user-generator.py /app/

# Exécuter le script Python
CMD ["python3", "/app/user-generator.py"]
```

#### Création du script Python pour `user-generator`  
```python
import random
import string
import time

# Fonction pour générer un mot de passe aléatoire
def generate_password():
    return ''.join(random.choices(string.ascii_letters + string.digits + string.punctuation, k=16))

# Fichier où les utilisateurs et mots de passe sont stockés
file_path = '/data/user_passwords.txt'

# Fonction pour écrire un utilisateur et un mot de passe dans le fichier
def write_user_password():
    user = ''.join(random.choices(string.ascii_lowercase, k=8))  # Générer un nom d'utilisateur
    password = generate_password()  # Générer un mot de passe
    with open(file_path, 'a') as f:
        f.write(f"{user} : {password}\n")

# Limite de 3 minutes d'exécution
start_time = time.time()
while time.time() - start_time < 180:  # 3 minutes
    write_user_password()
    time.sleep(30)
```

#### Construction de l'image Docker pour `user-generator`  
```bash
docker build -t user-generator .
```

#### Création du Dockerfile pour `password-updater`  
```dockerfile
# Dockerfile pour password-updater
FROM debian:bullseye

# Installer Python et pip
RUN apt-get update && apt-get install -y python3 python3-pip

# Copier le script dans le conteneur
COPY password-updater.py /app/

# Exécuter le script Python
CMD ["python3", "/app/password-updater.py"]
```

#### Création du script Python pour `password-updater`  
```python
import os
import random
import string
import time

# Chemin du fichier des utilisateurs et mots de passe
file_path = '/data/user_passwords.txt'

# Fonction pour générer un mot de passe aléatoire
def generate_password():
    return ''.join(random.choices(string.ascii_letters + string.digits + string.punctuation, k=16))

# Fonction pour lire les utilisateurs et mots de passe
def read_user_passwords():
    users = []
    if os.path.exists(file_path):
        with open(file_path, 'r') as f:
            lines = f.readlines()
            for line in lines:
                user, password = line.strip().split(' : ')
                users.append((user, password))
    return users

# Fonction pour mettre à jour les mots de passe
def update_passwords():
    users = read_user_passwords()
    if users:
        for user, _ in users:
            new_password = generate_password()
            with open(file_path, 'a') as f:
                f.write(f"{user} : {new_password}\n")

# Limite de 3 minutes d'exécution
start_time = time.time()
while time.time() - start_time < 180:  # 3 minutes
    update_passwords()
    time.sleep(30)
```

#### Construction de l'image Docker pour `password-updater`  
```bash
docker build -t password-updater .
```

#### Création d'un volume Docker partagé  
```bash
docker volume create shared-data
```

#### Création du réseau Docker personnalisé  
```bash
docker network create my_network
```

#### Lancement des conteneurs avec volume et réseau  
```bash
docker run -d --name user-generator --network my_network --mount source=shared-data,target=/data user-generator
docker run -d --name password-updater --network my_network --mount source=shared-data,target=/data password-updater
```

#### Vérification de la communication entre les conteneurs  
```bash
docker exec -it user-generator ping password-updater
```


