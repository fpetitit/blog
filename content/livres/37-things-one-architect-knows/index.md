+++
title = "37 Things One Architect Knows About IT Transformation"
date = 2026-09-27T10:00:00+02:00
draft = false
description = "Mes notes sur le livre de Gregor Hohpe : l'Architect Elevator, les économies de vitesse et l'architecte qui vend des options."
author = 'François Petitit'
+++

<img src="cover.jpg" alt="Couverture du livre 37 Things One Architect Knows About IT Transformation, de Gregor Hohpe" width="220" style="float: right; margin: 0 0 1rem 1.5rem;">

**Auteur :** Gregor Hohpe
**Sous-titre :** *A Chief Architect's Journey*

Gregor Hohpe, déjà connu pour *Enterprise Integration Patterns*, raconte dans ce livre ce qu'il a appris en tant qu'architecte en chef, chargé de transformer le SI d'une grande entreprise. Le livre est un recueil de 37 courts chapitres, souvent sous forme d'anecdotes, regroupés en grands thèmes : le rôle de l'architecte, la prise de décision, l'architecture, la communication, l'organisation et la transformation.

Son message central : dans une transformation numérique, la transformation technique et la transformation de l'organisation sont indissociables. L'architecte ne peut plus se contenter de choix techniques, il doit aussi faire évoluer l'organisation, ses processus et sa culture.

> Ce livre a depuis été repris et enrichi dans *The Software Architect Elevator* (O'Reilly, 2020).

## L'Architect Elevator

C'est l'idée la plus connue du livre, au point d'avoir donné son nom au blog de l'auteur et au livre suivant.

Hohpe compare une grande entreprise à un immeuble :

- **le penthouse**, tout en haut, où la direction définit la stratégie et alloue les budgets ;
- **la salle des machines**, tout en bas, où les équipes construisent et font tourner les systèmes ;
- **les étages intermédiaires**, avec leurs couches de management, leurs processus, leurs comités et leurs validations.

Dans la plupart des organisations, l'information circule par l'escalier, d'étage en étage, et elle se déforme en route comme au téléphone arabe. La direction décide sans connaître la réalité technique, et les équipes techniques ne comprennent pas pourquoi on leur demande telle ou telle chose.

Le rôle de l'architecte est de **prendre l'ascenseur** : faire des allers-retours entre le penthouse et la salle des machines. Vers le haut, il traduit des sujets techniques en enjeux business compréhensibles par la direction. Vers le bas, il s'assure que la stratégie se traduit concrètement dans les systèmes, et il revient avec la réalité du terrain.

Hohpe décrit deux façons de rater le trajet :

- **l'architecte de la tour d'ivoire**, qui reste au penthouse, dessine des schémas et ne voit jamais les conséquences de ses décisions ;
- **l'architecte coincé en salle des machines**, excellent techniquement, mais qui n'a aucune influence sur les décisions qui déterminent si ses solutions pourront réussir.

Il ajoute qu'une organisation avec trop d'étages rend le trajet presque impossible, quel que soit l'architecte : réduire le nombre d'étages fait aussi partie de la transformation.

## Les économies de vitesse

Les SI traditionnels sont construits pour les **économies d'échelle** : standardiser, mutualiser, réutiliser, pour faire la même chose moins cher. Hohpe explique que les entreprises nées du numérique jouent un autre jeu, celui des **économies de vitesse** : apprendre vite, livrer souvent, écouter les retours et corriger le cap.

Les deux logiques entrent souvent en conflit. Une standardisation poussée à l'extrême, ou la recherche systématique de réutilisation, peut ralentir toute l'organisation : des équipes passent des mois à se mettre d'accord sur un composant commun alors qu'elles auraient pu avancer chacune de leur côté. Dans un marché qui bouge vite, accepter un peu de duplication coûte souvent moins cher que la lenteur.

## L'architecte vend des options

Pour expliquer la valeur de l'architecture à la direction, Hohpe utilise une métaphore financière : **l'architecture, c'est vendre des options**. Une option financière donne le droit, mais pas l'obligation, de prendre une décision plus tard, et elle a un prix.

De la même façon, une bonne architecture permet de reporter certaines décisions au moment où l'on en sait davantage : découpler des composants, isoler une dépendance, rendre un choix réversible. Cela a un coût immédiat, mais plus l'incertitude est grande, plus ces options ont de la valeur. C'est un argument que le penthouse comprend, et un bon exemple de l'architecte qui prend l'ascenseur.
