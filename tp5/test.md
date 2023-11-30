# I. Tester
git 
Dans cette partie, on va déjà essayer de lancer l'application vitefé, voir si elle fonctionne à peu près correctement.  
**Juste : eske sa march enfet ?**

Tout se fait depuis la VM `hosting.tp5.b1`.

🌞 **Récupérer l'application dans la VM `hosting.tp5.b1`**

```
[root@hosting calculator]# ls
client.py  server.py
```

🌞 **Essayer de lancer l'app**

Le serveur et le client sont codés en python 

```
[root@hosting calculator]# python server.py 
13337
Le serveur tourne sur 127.0.0.1:13337

```
```
[root@hosting ~]# ss -tulpn | grep 127.0.0.1:13337
tcp   LISTEN 0      1          127.0.0.1:13337      0.0.0.0:*    users:(("python",pid=1510,fd=4))

```


🌞 **Tester l'app depuis `hosting.tp5.b1`**


```
[root@hosting calculator]# python server.py 
13337
Le serveur tourne sur 127.0.0.1:13337
Un client ('127.0.0.1', 38430) s'est connecté.
Le client ('127.0.0.1', 38430) a envoyé 8+8
Réponse envoyée au client ('127.0.0.1', 38430) : 16.

```
```
[root@hosting calculator]# python client.py 
Veuillez saisir une opération arithmétique : 8+8

```