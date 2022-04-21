# Se connecter à une base de données Postgres depuis Node via le module pg. 

- Notre SGBD `Postgres` tourne en local. 
	- Il tourne en tâche de fond : on ne le voit pas !
	- Il rend possible l'accès à diverses bases de données Postgres, stockées quelque part sur notre machine.

- La preuve qu'il tourne ? 
	- On peut s'y connecter via la commande `sudo -i -u postgres psql` : nous voila alors connecté comme utilisateur `postgres` sur la base `postgres` !
	- Pour rappel : `psql` est un "client" terminal (CLI) pour accèder au "serveur" SGBD Postgres et ses moultes bases de données.

- Le module Node `pg` est un module "client" qui permet de se connecter à une base de données de `Postgres` depuis Node. 

Voici comment se connecter à une BDD depuis Node.


# Etape 1 - Infos de la base

Tout d'abord, il faut connaitre les informations de notre base de données. On se les note dans un coin de notre tête :

- L'`host` sur lequel tourne le SGBD. Exemple : `localhost`
- Le `nom de la base`. Exemple : `trombinoclock`.
- Le `nom de son utilisateur` Postgres prioriétaire. Exemple : `trombinoclock`
- Le `mot de passe` de son utilisateur prorpriétaire. Exemple : `trombinopass`


# Etape 2 - Configuration

On créé (ou complète) un fichier de configuration pour `pg`. On peut l'appeler par exemple `database.js` :


```js
const { Client } = require("pg"); // On importe la fonction 'Client' du module pg

const client = new Client(*/ ICI ON VA POINTER VERS NOTRE BDD A l'ETAPE 3 /*); // On définit un client qui pointe vers notre BDD

client.connect(); // On se connecte à la base

module.exports = client; // On exporte ce client de BDD
```

# Etape 3 - Pointer vers la bonne BDD

* On s'occupe enfin de la partie `new Client(...)` pour pointer vers notre BDD.
* Il y a 3 manières de procéder. Elles sont toutes référencées dans la [documentation officielle de pg](https://node-postgres.com/features/connecting).


## Méthode 1 - Via une URL de connexion


```js
// Dans le fichier `database.js`
new Client(process.env.PG_URL);
```

```bash
# Dans le fichier `.env`
PG_URL=postgres://USER:PASSWORD@HOST/DATABASE

# où l'on remplace `USER / PASSWORD / HOST / DATABASE`
# par les valeurs que l'on s'est notées dans le coin de notre tête à l'étape 1 ! 

# Exemple
# PG_URL=postgres://trombinoclock:trombinopass@localhost/trombinoclock
```

Et on s'assure bien que les variables d'environnements sont chargés dans notre app par l'utilisation du module `dotenv` dans notre `index.js`. 


## Méthode 2 - Via un objet de configuration

```js
// Dans le fichier `database.js`
new Client({
	host: process.env.DB_HOST,
	user: process.env.DB_USER,
	password: process.env.DB_PASSWORD,
	database: process.env.DB_NAME
});
```

```bash
# Dans le fichier `.env`
DB_HOST=HOST
DB_USER=USER
DB_PASSWORD=PASSWORD
DB_NAME=DATABASE

# où l'on remplace `USER / PASSWORD / HOST / DATABASE`
# par les valeurs que l'on s'est notées dans le coin de notre tête à l'étape 1 ! 

# Exemple
# DB_HOST=localhost
# DB_USER=trombinoclock
# DB_PASSWORD=trombinopass
# DB_NAME=trombinoclock
```

## Méthode 3 - Via la magie de pg

```js
// Dans le fichier `database.js`
new Client(); // On ne précise rien, et on laisse pg chercher seul les variables d'environment
```

```bash
# Dans le fichier `.env`
PGHOST=HOST
PGUSER=USER
PGPASSWORD=PASSWORD
PGDATABASE=DATABASE

# où l'on remplace `USER / PASSWORD / HOST / DATABASE`
# par les valeurs que l'on s'est notées dans le coin de notre tête à l'étape 1 ! 

# Exemple
# PGHOST=localhost
# PGUSER=trombinoclock
# PGPASSWORD=trombinopass
# PGDATABASE=trombinoclock
```

Attention, cette fois-ci le nom des variables d'environnment (`PGHOST` par exemple) a de l'importance, car 'pg' va les chercher spécifiquement


# Etape 4 - Tester

`client.connect()` peut prendre un argument facultatif : un callback ! Parfait pour log les erreurs. 


```
client.connect((error) => {
	if (error) {
		console.error("Une erreur a lieu à la connexion avec notre BDD : ", error.message);
	} else {
		console.log("Connection à la BDD réussie !");
	}
});
```

Importer notre client depuis l'`index.js` (par exemple) et on devrait voir apparaitre les logs en console
