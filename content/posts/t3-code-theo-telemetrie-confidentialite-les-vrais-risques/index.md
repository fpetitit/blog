+++
title = "T3 Code de Theo : la polémique sur la confidentialité, et les vrais risques qu'elle révèle"
date = 2026-11-25T07:00:00+01:00
draft = false
description = "Le 24 novembre 2026, un développeur a révélé sur X que T3 Code, l'outil open source de Theo (t3.gg), collectait de la télémétrie par défaut et lisait les fichiers d'authentification de Claude Code et Codex. Retour sur les faits et sur les vrais risques pour votre confidentialité."
author = 'François Petitit'
+++

Le 24 novembre 2026, un développeur chinois publiant sous le nom de **Chen Dahuang** a mis le feu aux poudres sur X : après avoir passé au crible le code source de **T3 Code**, l'application open source de **Theo Browne** (t3.gg) qui sert de « control plane » pour piloter des agents de code (Claude Code, Codex, Cursor, OpenCode…), il affirme y avoir trouvé une collecte de données dissimulée. Quelques heures plus tard, d'autres développeurs confirment — capture réseau à l'appui — et un utilisateur allemand dépose plainte auprès de son autorité de protection des données (la LfD Sachsen-Anhalt) et saisit NOYB.

Theo a réagi en publiant ses propres données de télémétrie (100 Mo, 95 789 événements), en retirant la télémétrie incriminée dans une PR… puis en annonçant la fermeture du source de T3 Code. Voici ce qui s'est réellement passé, et surtout **les vrais risques** que cette affaire révèle pour votre confidentialité.

## La chronologie des faits

| Date | Fait |
|---|---|
| 8 février 2026 | Création du dépôt `pingdotgg/t3code` (Theo CEO, Julius et Mark Florkowski). |
| 5–6 mars 2026 | La PR #174 est fusionnée : **télémétrie activée par défaut** via PostHog. |
| ~mi-mai 2026 | La page d'accueil affiche une promesse : « No telemetry unless you opt in. Full stop. » |
| 17–18 juin 2026 | Theo **supprime discrètement** cette promesse dans un commit de refonte de la homepage, sans aucune annonce. |
| 29 juillet 2026 | Lancement de T3 Connect, le relais permettant de piloter ses agents à distance (apps iOS/Android). |
| 20 octobre 2026 | La télémétrie passe de PostHog à `ingest.t3.codes` et ajoute le « owner linking » : lecture de `~/.codex/auth.json` et `~/.claude.json`, envoi des identifiants de compte OpenAI, Claude et T3 Connect **hachés** (SHA-256 non salé), plus un identifiant par appareil. |
| 29 octobre 2026 | Discussion GitHub #26896 : des entreprises allemandes bloquent l'outil en amont — la télémétrie PostHog et le relais T3 Connect peuvent acheminer des données vers des serveurs américains, problématique RGPD même avec la télémétrie désactivée. |
| 24 novembre 2026 | Révélation publique sur X. Test indépendant (v0.1.11, macOS, mitmproxy) : les identifiants hachés partent bien au premier lancement. Plainte au LfD Sachsen-Anhalt. Un correctif est fusionné… mais uniquement dans les builds nightly : **la stable v0.1.11 lit encore les fichiers d'auth et la télémétrie reste active par défaut**. |

## Les vrais risques, expliqués

### 1. Une télémétrie activée par défaut, dans un outil vendu comme « privacy-first »

Le cœur du problème n'est pas technique, il est **contractuel et juridique**. T3 Code était présenté aux développeurs sensibles à la vie privée comme un outil local et open source, avec une promesse écrite « aucune télémétrie sauf opt-in ». Cette promesse a disparu en juin sans prévenir, alors que la télémétrie tournait par défaut depuis mars.

Au regard du RGPD, le consentement doit être **libre, spécifique et éclairé**, et l'opt-in est la norme pour de l'analytics. Inverser le consentement (opt-out) tout en continuant d'afficher — puis de retirer — une promesse contraire est un *dark pattern* : même si une variable d'environnement (`T3CODE_TELEMETRY_ENABLED=false`) permettait de tout couper, personne ne peut consentir à ce qu'il n'a pas vu.

### 2. La lecture des fichiers d'authentification et la fausse anonymisation

En octobre, T3 Code s'est mis à lire `~/.codex/auth.json` et `~/.claude.json` — des fichiers qui **contiennent des tokens d'accès** — pour en extraire les identifiants de compte et les envoyer hachés en SHA-256.

L'argument « c'est anonymisé, donc ce n'est pas une donnée personnelle » ne tient pas :

- un **hash non salé** d'un identifiant à faible entropie se retrouve par dictionnaire ou par simple correspondance. Or le serveur connaît par ailleurs votre compte T3 Connect **en clair** : il peut matcher le hash contre votre compte et vous réidentifier exactement, comme l'ont souligné les internautes qui ont analysé le dossier ;
- un identifiant de compte OpenAI ou Claude, une fois divulgué, peut être **recoupé avec d'autres services** pour vous rattacher à une personne réelle.

Sous le RGPD, un hash d'identifiant est une **pseudonymisation**, pas une anonymisation : la donnée reste personnelle. Lire des fichiers d'identifiants sur le poste d'un utilisateur, même « juste pour en sortir un hash », reste une ligne rouge.

### 3. Des données volumineuses, conservées, et revendues à des tiers

Theo a publié ses propres logs : **100 Mo et 95 789 événements** pour ses seuls environnements. La documentation du projet admet que chaque rapport porte un identifiant d'installation, que le serveur **conserve une copie de chaque événement** tout en les transmettant à PostHog (hébergé aux États-Unis), et que les rapports de crash peuvent citer « une valeur que l'application était en train de traiter » — un chemin de fuite possible pour des secrets.

Le risque concret : métadonnées fines (quels modèles, quels harnesses, quels timings, quels appareils, quels comptes) + **transferts hors UE vers des sous-traitants américains** + rétention côté serveur = un profil d'usage détaillé, transférable, et juridiquement fragile (d'où la plainte en Allemagne et l'alerte de NOYB).

### 4. Le scan des processus et des arguments de ligne de commande

La révélation initiale dénonçait aussi l'envoi de la **liste des processus en cours d'exécution et des arguments de ligne de commande**. C'est plus grave qu'il n'y paraît : les arguments de commande contiennent très souvent des secrets (`--token`, clés API, URL avec identifiants). La liste des processus, elle, décrit votre environnement de travail complet. Des correctifs de « fuite de credentials » et de « path traversal » ont été fusionnés **sans aucun security advisory**, et certaines vulnérabilités restaient non corrigées au moment de la polémique : les utilisateurs ne pouvaient tout simplement pas évaluer le risque.

Un bug connexe, signalé début octobre, faisait qu'en mode « Auto », certaines commandes dangereuses (y compris `git push --force`) s'exécutaient **sans demande de confirmation** — un risque de sécurité pur, au-delà de la confidentialité.

### 5. T3 Connect : un accès distant à des agents qui tournent avec vos permissions

Le relais T3 Connect permet de piloter à distance, depuis un téléphone, des agents qui s'exécutent **avec tous vos droits** sur votre machine (accès fichiers, exécution de commandes, clés SSH, tokens). Même avec la télémétrie coupée, le trafic du relais pouvait transiter vers des services américains — sans interrupteur dédié à l'époque — et un bug faisait que l'opt-out ne fonctionnait pas sous Windows. Autrement dit : la promesse « local-first » avait une porte dérobée.

### 6. « Open source » ne veut pas dire « sûr » — et la confiance se mérite

Point important : le code était public, la collecte était dedans. L'open source n'a rien caché — il a surtout montré que **le problème était le contrat social et le consentement**, pas la dissimulation technique. D'où la leçon inverse de celle de Theo (« on ferme le source ») : la bonne réponse à un abus de confiance, c'est plus de transparence (changelog public des changements de collecte, security advisories systématiques, opt-in par défaut), pas moins d'auditabilité.

## Ce que vous pouvez faire, concrètement

**Si vous avez utilisé T3 Code (ou un outil du même genre) :**

- désactivez la télémétrie par défaut (`T3CODE_TELEMETRY_ENABLED=false`) et bloquez les domaines concernés (`ingest.t3.codes`, PostHog, Axiom) dans votre pare-feu sortant ou via Little Snitch/NetLimiter ;
- **révoquez et renouvelez les tokens** de vos comptes OpenAI/Anthropic et de T3 Connect, et traitez `~/.codex/auth.json` et `~/.claude.json` comme des secrets (ils en sont) ;
- ne passez jamais de secrets en argument de ligne de commande ;
- vérifiez plutôt que croire : un outil « privacy-friendly » se valide avec une capture réseau (mitmproxy en local), pas avec une page d'accueil.

**Si vous êtes une DSI / une entreprise :**

- exigez une DPIA avant d'installer ce type d'agent sur des postes contenant du code ou des données clients : sous-traitants, transferts hors UE, rétention, droits d'accès à distance ;
- autorisez uniquement des outils avec télémétrie **opt-in**, hébergement UE documenté, et un mode hors-ligne réellement fonctionnel ;
- profitez de l'open source pour auditer, mais exigez des security advisories : un correctif de fuite de credentials mergé en silence est un risque opérationnel.

## Et Orca dans tout ça ?

Avant de refermer ce dossier, un mot sur **Orca** (`stablyai/orca`, Stably AI / Lovecast Inc.), l'IDE d'agents open source (MIT) que beaucoup d'entre nous utilisent au quotidien pour faire tourner des agents en parallèle — à ne pas confondre avec Orca Security, l'éditeur de sécurité cloud. La situation présente des similitudes frappantes avec T3 Code :

- **Télémétrie PostHog (le même fournisseur que T3 Code), activée par défaut** pour les nouvelles installations et envoyée pseudonymisée vers PostHog Cloud aux **États-Unis**. La documentation officielle (`orca.dev/docs/telemetry`) admet elle-même que les identifiants d'installation, de session et de conversation sont « pseudonymous rather than anonymous » — un point d'honnêteté que T3 n'avait pas.
- **Aucune durée maximale de rétention documentée**, et les composants cloud (connecteurs de bots Slack/GitHub/Linear, relais mobile) passent par **Orca Cloud, hébergé aux US et à Hong Kong** : mêmes questions de transferts hors UE que pour T3.
- Un **bug ouvert** (`stablyai/orca#45214`) rapporte que la télémétrie était **envoyée même après désactivation** — même classe de défaut que le opt-out défaillant de T3 sous Windows.
- Les agents sont lancés **par défaut avec leurs flags bypass** (`--dangerously-skip-permissions`, `--dangerously-bypass-approvals-and-sandbox`) : l'équivalent du bug « Auto mode » de T3, et un worktree n'isole que les fichiers, pas les processus, le réseau ni les credentials.

Mais Orca diffère aussi sur des points essentiels : la collecte est **documentée publiquement** (schéma d'événements restreint, exclusions annoncées : pas de prompts, pas de code, pas de chemins locaux), le opt-out existe à trois niveaux (réglage in-app, `DO_NOT_TRACK=1`, variable d'environnement), le code est MIT, et l'équipe **réduit activement le volume** envoyé (PR #47137 et #47214 : totaux de tokens journaliers, chat échantillonné, identifiants de conversation retirés de la page publique). Aucun comportement de lecture des fichiers d'auth d'autres outils (comme `~/.codex/auth.json`) n'a été documenté.

**Des études ont-elles été menées ?** Pas d'étude académique formelle dédiée à Orca à ce jour, mais plusieurs analyses indépendantes récentes : la fiche technique et « enterprise » d'[innfactory](https://innfactory.ai/en/ai-harness/orca/) (22 nov. 2026), la [review hands-on de PromptQuorum](https://www.promptquorum.com/power-local-llm/orca-review) (19 nov. 2026, avec test réel de désactivation de la télémétrie), la [review façon procurement d'AgentBoards](https://agentboards.org/agent/orca) (« approved with conditions: telemetry off in managed config »), le [dépôt de vérification `lean-agents`](https://github.com/estebanmr99/lean-agents/issues/22) (test sur Windows 11 : `DO_NOT_TRACK=1` honoré), ainsi que les issues et PR du dépôt, qui servent d'audit citoyen continu. En toile de fond, des études générales existent sur les agents de code (Anthropic, « Measuring AI agent autonomy » ; rapports Sophos/CrowdStrike 2026 sur les agents IA qui déclenchent les alertes de sécurité endpoint).

| Point | T3 Code (Theo) | Orca (Stably) |
|---|---|---|
| Télémétrie | PostHog, par défaut depuis mars 2026, promesse supprimée en silence | PostHog US, par défaut, **documenté publiquement** |
| Opt-out | env var existant mais bogué, promesse retirée sans prévenir | toggle in-app + `DO_NOT_TRACK=1` + env var, mais bug #45214 (envoi persistant) |
| Fichiers d'auth d'autres outils | lus et hachés (`~/.codex/auth.json`, `~/.claude.json`) | pas de tel comportement documenté |
| Prompts / code | non envoyés selon les tests | exclus annoncés de la télémétrie |
| Cloud / transferts | ingest.t3.codes + PostHog US, copie de chaque événement conservée | PostHog US + Orca Cloud **US et Hong Kong** (bots, relais) |
| Exécution dangereuse par défaut | bug mode Auto | **flags bypass pré-remplis par défaut** |
| Rétention | copie serveur de tous les événements | durée max **non documentée** |
| Licence | open source menacé de fermeture | MIT, réduction active de la télémétrie |

**Ce que cela change pour vous si vos bots tournent dans Orca** : SOUL.md, MEMORY.md et les dossiers des bots sont de simples fichiers locaux en clair, et l'historique de chat reste en stockage local — mais tout ce que le bot lit part vers le fournisseur de modèle que vous avez choisi, et les connecteurs Slack/GitHub/Linear passent par Orca Cloud. Coupez la télémétrie (Settings → Privacy, ou `DO_NOT_TRACK=1` avant le lancement), et vérifiez le bug #45214 selon votre version.

## Pour conclure

Rien n'indique à ce stade que T3 Code ait envoyé vos prompts, vos réponses ou le contenu de vos fichiers — la documentation du projet l'affirme et les captures réseau publiées vont dans ce sens. Le vrai risque, c'est ailleurs : **identifiants de comptes lus sur votre poste et hachés de façon réversible en pratique, télémétrie volumineuse activée par défaut et conservée côté serveur, transferts vers des tiers américains, promesses de confidentialité retirées sans prévenir, et correctifs de sécurité livrés sans avertissement.**

Cette affaire est un cas d'école : la confidentialité d'un outil ne se décrète pas, elle se prouve — par du opt-in réel, de la transparence sur les changements, et la possibilité technique de couper le robinet. Le 24 novembre, le robinet était ouvert depuis huit mois.

*Sources : [révélation originale de Chen Dahuang sur X](https://www.reddit.com/r/LocalLLaMA/comments/1y4atw3/t3_code_drama_chinese_post_on_x/), [analyse r/tech_x](https://www.reddit.com/r/tech_x/comments/1y4cyu5/theos_sudo_opensource_exposed_chinese_developer/), [T3 Code telemetry: default-on since March (Sulat)](https://news.sulat.com/2026/11/25/t3-code-telemetry/), [T3 Code Telemetry: What It Collects (ModemGuides)](https://www.modemguides.com/blogs/ai-news/t3-code-telemetry-claude-codex-files), [T3 Code Reads Claude Auth Files (explainx.ai)](https://explainx.ai/blog/t3-code-telemetry-claude-codex-auth-files-november-2026), [doc télémétrie du dépôt t3code](https://github.com/pingdotgg/t3code/blob/main/docs/user/telemetry.md), [discussion GitHub #26896](https://github.com/pingdotgg/t3code/discussions/26896), [réponse de Theo sur X](https://x.com/theo/status/2125332956281749854) — et côté Orca : [doc télémétrie officielle](https://orca.dev/docs/telemetry), [politique de confidentialité](https://orca.dev/privacy), [fiche innfactory](https://innfactory.ai/en/ai-harness/orca/), [review PromptQuorum](https://www.promptquorum.com/power-local-llm/orca-review), [review AgentBoards](https://agentboards.org/agent/orca), [issue #45214 (télémétrie malgré la désactivation)](https://github.com/stablyai/orca/issues/45214), [PR #47137](https://github.com/stablyai/orca/pull/47137), [PR #47214](https://github.com/stablyai/orca/pull/47214).*
