+++
title = 'Paris Web Openid Connect France Connect'
date = 2015-10-10T12:01:46+02:00
draft = false
+++

# Présentation à Paris Web - OpenID Connect : mise en oeuvre sur France Connect

J'ai eu la chance et l'opportunité de présenter un retour d'expérience à Paris Web : il s'agit de l'utilisation du protocole OpenID Connect sur le projet France Connect, lorsque j'étais le Technical Leader de l'équipe.

La vidéo est disponibile ici :

<iframe src="https://player.vimeo.com/video/143169752?h=e8a06887a2" width="640" height="360" frameborder="0" allow="autoplay; fullscreen; picture-in-picture" allowfullscreen></iframe>
<p><a href="https://vimeo.com/143169752">OpenID Connect, le nouveau standard d&#039;authentification sur le web : mise en oeuvre sur FranceConnect</a> from <a href="https://vimeo.com/parisweb">Paris Web</a> on <a href="https://vimeo.com">Vimeo</a>.</p>

## Le sujet

[FranceConnect](https://franceconnect.gouv.fr/) est un nouvel outil visant à améliorer l'accès aux administrations françaises en facilitant l'authentification et l'identification des usagers. Pour cela, nous avons mis en oeuvre le protocole [OpenID Connect](https://openid.net/developers/how-connect-works/).

Ce protocole ouvert basé sur OAuth2, successeur de OpenID, et soutenu par des grands acteurs, permet à une application cliente d'utiliser n'importe quel fournisseur d'identité pourvu qu'il implémente aussi ce standard.

Nous verrons dans cette présentation quels sont les cas d'usages de ce protocole (authentification sur le web, sur des applications mobiles…), quels sont ses avantages et inconvénients, et comment le mettre en oeuvre avec Node.js.

