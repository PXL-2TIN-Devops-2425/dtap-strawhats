[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/W4hQG6UL)
# Assignment 6 Environments (DTAP)
We starten deze opdracht met de configuratie van de 2 servers.

## Configuratie
Voor deze opdracht maak je gebruik van 2 virtuele machines:
*   **Testserver:** De VM met jenkins uit de vorige lessen
*   **Productieserver:** Een nieuwe Ubuntu Server VM (zonder jenkins) in de AWS cloud (t2.micro, public subnet, public ip, vergeet je **security group** niet). Je mag hiervoor de DevOps learner lab of de Cloud Essentials learner lab gebruiken.

### Configuratie Testserver
We starten met de configuratie van de testserver. Dit is de server waarop Jenkins geïnstalleerd staat (en die je tijdens de vorige lessen gebruikt hebt). Installeer docker op je virtuele machine. Je kan hiervoor eventueel [deze installatiegidsen](https://www.digitalocean.com/community/tutorial-collections/how-to-install-and-use-docker) gebruiken.

Belangrijk is dat zowel de ubuntu als jenkins user het docker commando moeten kunnen gebruiken (zonder `sudo`!!).

### Configuratie productieserver
De productieserver is een nieuwe kale Ubuntu **server** die we gebruiken voor de deployment van de applicatie in de productieomgeving. Deze draait voor alle groepen in AWS. Installeer docker op je machine. Je kan hiervoor eventueel [deze installatiegidsen](https://www.digitalocean.com/community/tutorial-collections/how-to-install-and-use-docker) gebruiken.

Belangrijk is dat de `ubuntu` user het docker commando moeten kunnen gebruiken (zonder `sudo`!!). Denk doorheen de opdracht ook goed na over het gebruik van security groups (poort 22 voor `ssh`/`scp`, poort 80 voor de gedeployde app, ...).

## Opgave
In de repository kan je 2 jenkinsfiles vinden. Je gebruikt beide jenkinsfiles, maakt voor elk hun eigen pipeline in je jenkins omgeving. Je voorziet in deze opdracht dus 2 pipelines op de `Testserver`, de **'test deployment'** pipeline en de **'productie deployment'** pipeline

De `test.jenkinsfile` doorloopt het hele CI/CD process en zorgt voor een artifact en deployed dit artifact naar de `Testserver`.

De `production.jenkinsfile` doorloopt enkel het CD process en neemt het artifact van de `test.jenkinsfile` en deployed deze a.d.h.v. `ssh` op de `Productionserver`.

Details van bovenstaande implementaties kan je hieronder terugvinden.


_Tip: Het is niet toegelaten om het `sudo` commando te gebruiken. Heb je geen rechten in bepaalde folders of commando's? Dan zorg je ervoor dat de gebruiker `jenkins`/`ubuntu` de juiste rechten krijgt. Dit is iets wat je handmatig, buiten de pipeline, kan instellen. (Zie ook Systems essentials: `chmod` / `chgrp`)_

# test.jenkinsfile
Je krijgt een bestaande pipeline met enkele stages in. Voorzie volgende extra stages in deze pipeline:
*   Stage `cleanup`: Deze stap maakt de working directory van de pipeline leeg.
*   Stage `Install dependencies`: Gebruik de global tool configuratie van nodejs met als naam "testenvnode". Deze configuratie gebruik je in deze stage om het `npm install` commando uit te voeren.
*   Stage `Build artifact`: In deze stage bouw je een docker container van de nodeJS applicatie. (Tip: Zorg eerst voor een werkende Dockerfile en test deze lokaal). De container wordt vanuit de pipeline gebuild.
*   Stage `Push artifact`: In deze stage push je de gebouwde container naar dockerhub. Je maakt hier gebruik van een publieke image op het profiel van één van je teamgenoten.
*   Stage `deployment`: In deze stage zorg je ervoor dat de docker container opgestart wordt op poort 3000 en blijft draaien na het uitvoeren van de pipeline.
    * _Tip 1: Gebruik de container vanuit dockerhub!_
    * _Tip 2: Denk eraan dat er misschien nog een vorige versie van de applicatie actief is._
    
    
* Als je vervolgens naar [http://localhost:3000](http://localhost:3000) surft in de vm, zal je de calculator app kunnen gebruiken.
*   Denk aan de cleanup stappen! De pipeline moet meerdere keren na elkaar kunnen draaien.

![alt_text](https://i.imgur.com/9leib3p.png "image_tooltip") _Heb je aanpassingen gedaan om jenkins rechten te geven tot bepaalde mappen of commando's, dan documenteer je dit in oplossing.md onder punt (a)._
  
# production.jenkinsfile

Je krijg een lege pipelinefile. Ook deze pipeline integreer je op de jenkins server die op de `Testserver` staat. Geef deze Pipeline dus ook een duidelijke naam, zodat je weet welke de test deploy en welke de productie deploy uitvoert.

Het doel van deze pipeline is het voorzien van volgende stages:

*   Stage `deploy prod`: Zorg ervoor dat je in deze stage op je remote server de laatste versie van je docker container download a.d.h.v. dockerhub.
*   Stage `start prod`: Zorg ervoor dat de container wordt opgestart op je remote server. Na het opstarten moet je de applicatie kunnen gebruiken op poort 80.
    *   _Tip 1: Maak verder gebruik van de jenkings plugin `sshagent` die werkt met SSH keys als authenticatie. Denk hierbij ook aan de opdracht uit security essentials vorig jaar: Hoe kon je ssh keys gebruiken om je te authenticeren op een linux machine?_
    *  _Tip 2: Denk ook in deze pipeline aan de nodige cleanup stappen op de remote server._

*   Stage `test prod`: Doe een check om te kijken of de applicatie werkt. Voorlopig kan je dit testen met het `curl` commando om te controleren of je een statuscode 200 krijgt als je het IP adres van de `productieserver` bezoekt.
*   Denk aan de cleanup stappen! De pipeline moet meerdere keren na elkaar kunnen draaien.

# Deliverable
Deze repository met:
- Een opgevulde test.jenkinsfile met bovenstaande beschreven stages
    - Geen gevulde Jenkinsfile = 0 op deze deelopdracht
    - Een niet werkende (=syntax errors in het pipeline script) Jenkinsfile = -30% op het eindresultaat
- Een opgevulde production.jenkinsfile
    - Geen gevulde Jenkinsfile = 0 op deze deelopdracht
    - Een niet werkende (=syntax errors in het pipeline script) Jenkinsfile = -30% op het eindresultaat
- Een opgevulde `oplossing.md` file met antwoorden op bovenstaande vragen inclusief screenshots indien nodig.
