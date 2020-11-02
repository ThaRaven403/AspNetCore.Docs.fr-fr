---
title: Modèles d’hébergement ASP.net Core Blazor
author: guardrex
description: Comprendre les modèles d'hébergement Blazor WebAssembly et Blazor Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/11/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 1ec1f699d3beb5dbbc3851d9e3b6b1d3faf64cfd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055670"
---
# <a name="aspnet-core-no-locblazor-hosting-models"></a>Modèles d’hébergement ASP.net Core Blazor

Par [Daniel Roth](https://github.com/danroth27)

Blazor est un Framework Web conçu pour s’exécuter côté client dans le navigateur sur un Runtime .NET basé sur [Webassembly](https://webassembly.org/)( *Blazor WebAssembly* ) ou côté serveur dans ASP.net Core ( *Blazor Server* ). Quel que soit le modèle d’hébergement, les modèles d’application et de composant *sont les mêmes*.

## Blazor WebAssembly

Le modèle d’hébergement principal pour Blazor s’exécute côté client dans le navigateur sur Webassembly. L'application Blazor, ses dépendances et le Runtime .net sont téléchargés dans le navigateur. L’application est exécutée directement sur le thread d’interface utilisateur du navigateur. Les mises à jour de l’interface utilisateur et la gestion des événements se produisent dans le même processus. Les ressources de l’application sont déployées en tant que fichiers statiques sur un serveur Web ou un service qui prend en charge le contenu statique sur les clients. Étant donné que l’application est créée pour le déploiement sans une application serveur principale ASP.NET Core, elle est appelée *application autonome Blazor WebAssembly*.

![Blazor WebAssembly: Une application Blazor s’exécute sur un thread d’interface utilisateur dans le navigateur.](hosting-models/_static/blazor-webassembly.png)

Pour créer une application Blazor à l’aide du modèle d’hébergement côté client, utilisez le modèle d'**application Blazor WebAssembly** ( [`dotnet new blazorwasm`](/dotnet/core/tools/dotnet-new) ).

Après avoir sélectionné le modèle d'**application Blazor WebAssembly** , vous avez la possibilité de configurer l’application pour utiliser un serveur principal ASP.net core en activant la case à cocher **ASP.net Core hébergé** ( [`dotnet new blazorwasm --hosted`](/dotnet/core/tools/dotnet-new) ). L’application ASP.NET Core sert l'application Blazor aux clients. Une application avec un serveur principal ASP.NET Core est appelée *application hébergée Blazor WebAssembly*. L'application Blazor WebAssembly peut interagir avec le serveur sur le réseau à l’aide d’appels d’API Web ou [SignalR](xref:signalr/introduction) ( <xref:tutorials/signalr-blazor-webassembly> ).

Le script `blazor.webassembly.js` est fourni par l’infrastructure et les handles :

* Téléchargement du Runtime .NET, de l’application et des dépendances de l’application.
* Initialisation du runtime pour exécuter l’application.

Le modèle d’hébergement Blazor WebAssembly offre plusieurs avantages :

* Il n’existe aucune dépendance .NET côté serveur. L’application fonctionne entièrement une fois qu’elle a été téléchargée sur le client.
* Les ressources et les fonctionnalités du client sont pleinement exploitées.
* Le travail est déchargé du serveur vers le client.
* Un serveur Web ASP.NET Core n’est pas requis pour héberger l’application. Les scénarios de déploiement sans serveur sont possibles (par exemple, pour servir l’application à partir d’un CDN).

L’hébergement Blazor WebAssembly présente des inconvénients :

* L’application est limitée aux fonctionnalités du navigateur.
* Le matériel et le logiciel client compatibles (par exemple, la prise en charge de webassembly) sont requis.
* La taille du téléchargement est supérieure, et les applications prennent plus de temps à se charger.
* Le Runtime .NET et la prise en charge des outils sont moins matures. Par exemple, des limitations existent dans la prise en charge de [.NET standard](/dotnet/standard/net-standard) et le débogage.

Le modèle d’application hébergée Blazor prend en charge les [conteneurs dockers](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Cliquez avec le bouton droit sur le projet serveur dans Visual Studio, puis sélectionnez **Ajouter** la  >  **prise en charge** de l’ancrage.

## Blazor Server

Avec le modèle d’hébergement Blazor Server, l’application est exécutée sur le serveur à partir d’une application ASP.net core. Les mises à jour de l’interface utilisateur, la gestion des événements et les appels JavaScript sont gérés sur une connexion [SignalR](xref:signalr/introduction).

![Le navigateur interagit avec l’application (hébergée à l’intérieur d’une application ASP.NET Core) sur le serveur via une Connection SignalR.](hosting-models/_static/blazor-server.png)

Pour créer une application Blazor à l’aide du modèle d’hébergement Blazor Server, utilisez le modèle d'**application Blazor Server** ASP.net Core ( [`dotnet new blazorserver`](/dotnet/core/tools/dotnet-new) ). L’application ASP.NET Core héberge l'application Blazor Server et crée le point de terminaison SignalR où les clients se connectent.

L’application ASP.NET Core fait référence à la classe `Startup` de l’application pour ajouter :

* Services côté serveur.
* L’application vers le pipeline de traitement des demandes.

Sur le client, le script `blazor.server.js` établit la connexion SignalR avec le serveur. Le script est fourni à l’application côté client à partir d’une ressource incorporée dans le Framework partagé ASP.NET Core. L’application côté client est responsable de la persistance et de la restauration de l’état de l’application en fonction des besoins. 

Le modèle d’hébergement Blazor Server offre plusieurs avantages :

* La taille du téléchargement est beaucoup plus petite qu’une application Blazor WebAssembly, et l’application est chargée beaucoup plus rapidement.
* L’application tire pleinement parti des fonctionnalités du serveur, notamment de l’utilisation de toutes les API compatibles avec .NET Core.
* .NET Core sur le serveur est utilisé pour exécuter l’application, de sorte que les outils .NET existants, tels que le débogage, fonctionnent comme prévu.
* Les clients légers sont pris en charge. Par exemple, les applications Blazor Server fonctionnent avec des navigateurs qui ne prennent pas en charge WebAssembly et sur des appareils à ressources restreintes.
* La base de code .NET/C# de l’application, y compris le code du composant de l’application, n’est pas traitée aux clients.

> [!IMPORTANT]
> Une application Blazor Server effectue un prérendu en réponse à la première demande du client, qui configure l’état de l’interface utilisateur sur le serveur. Lorsque le client tente de créer une connexion SignalR, **le client doit se reconnecter au même serveur**. Les applications Blazor Server qui utilisent plusieurs serveurs principaux doivent implémenter des *sessions rémanentes* pour les connexions SignalR. Pour plus d’informations, consultez la section [connexion au serveur](#connection-to-the-server).

L’hébergement Blazor Server présente des inconvénients :

* Une latence plus élevée existe généralement. Chaque interaction utilisateur implique un tronçon réseau.
* Il n’existe aucune prise en charge hors connexion. Si la connexion client échoue, l’application cesse de fonctionner.
* L’évolutivité est difficile pour les applications avec de nombreux utilisateurs. Le serveur doit gérer plusieurs connexions clients et gérer l’état du client.
* Un serveur ASP.NET Core est requis pour servir l’application. Les scénarios de déploiement sans serveur ne sont pas possibles (par exemple, pour servir l’application à partir d’un CDN).

Le modèle d’application Blazor Server prend en charge les conteneurs de l'[ancrage](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Cliquez avec le bouton droit sur le projet dans Visual Studio, puis sélectionnez **Ajouter** la  >  **prise en charge** de l’ancrage.

### <a name="comparison-to-server-rendered-ui"></a>Comparaison avec l’interface utilisateur du rendu serveur

L’une des façons de comprendre les applications Blazor Server consiste à comprendre comment elle diffère des modèles traditionnels pour le rendu de l’interface utilisateur dans les applications de ASP.net core à l’aide de vues Razor ou de pages Razor. Les deux modèles utilisent le langage Razor pour décrire le contenu HTML, mais ils diffèrent sensiblement dans le mode de rendu du balisage.

Quand une page Razor ou une vue est restituée, chaque ligne de code Razor émet du code HTML sous forme de texte. Après le rendu, le serveur supprime l’instance de la page ou de la vue, y compris tout état produit. Lorsqu’une autre demande pour la page se produit, par exemple lorsque la validation du serveur échoue et que le résumé des validations s’affiche :

* La page entière est de nouveau restituée au texte HTML.
* La page est envoyée au client.

Une application Blazor est composée d’éléments réutilisables de l’interface utilisateur appelée *composants* . Un composant contient le code C#, le balisage et d’autres composants. Lorsqu’un composant est rendu, Blazor produit un graphique des composants inclus similaires à un document Object Model XML ou XML (DOM). Ce graphique comprend l’état des composants contenus dans les propriétés et les champs. Blazor évalue le graphique du composant pour produire une représentation binaire de la balise. Le format binaire peut être :

* Converti en texte HTML (lors du prérendu &dagger; ).
* Utilisé pour mettre à jour efficacement le balisage pendant le rendu normal.

&dagger;*Prérendu* : le composant Razor demandé est compilé sur le serveur en HTML statique et envoyé au client, où il est rendu à l’utilisateur. Une fois la connexion établie entre le client et le serveur, les éléments statiques prérendus du composant sont remplacés par des éléments interactifs. Le prérendu rend l’application plus réactive pour l’utilisateur.

Une mise à jour de l’interface utilisateur dans Blazor est déclenchée par :

* Intervention de l’utilisateur, telle que la sélection d’un bouton.
* Déclencheurs d’application, tels qu’un minuteur.

Le graphique est rerendu et *une différence d’interface utilisateur* (différence) est calculée. Cette différence est le plus petit ensemble de modifications DOM requis pour mettre à jour l’interface utilisateur sur le client. La diff est envoyée au client dans un format binaire et appliquée par le navigateur.

Un composant est supprimé une fois que l’utilisateur l’a quitté sur le client. Lorsqu’un utilisateur interagit avec un composant, l’état du composant (services, ressources) doit être conservé dans la mémoire du serveur. Étant donné que l’état de nombreux composants peut être géré simultanément par le serveur, l’épuisement de la mémoire est un problème qui doit être résolu. Pour obtenir des conseils sur la façon de créer une application Blazor Server pour garantir la meilleure utilisation de la mémoire du serveur, consultez <xref:blazor/security/server/threat-mitigation>.

### <a name="circuits"></a>Circuits

Une application Blazor Server est générée par-dessus [ASP.net Core SignalR ](xref:signalr/introduction). Chaque client communique avec le serveur sur une ou plusieurs connexions SignalR appelées *circuit* . Un circuit est une abstraction Blazor sur les connexions SignalR qui peuvent tolérer des interruptions réseau temporaires. Lorsqu’un client Blazor constate que la connexion SignalR est déconnectée, il tente de se reconnecter au serveur à l’aide d’une nouvelle connexion SignalR.

Chaque écran de navigateur (onglet de navigateur ou IFRAME) qui est connecté à une application Blazor Server utilise une connexion SignalR. Il s’agit encore d’une autre distinction importante par rapport aux applications classiques affichées par le serveur. Dans une application affichée sur un serveur, l’ouverture de la même application dans plusieurs écrans de navigateur n’est généralement pas convertie en demandes de ressources supplémentaires sur le serveur. Dans une application Blazor Server, chaque écran de navigateur requiert un circuit distinct et des instances distinctes de l’état du composant à gérer par le serveur.

Blazor tient compte de la fermeture d’un onglet de navigateur ou de la navigation vers une URL externe comme un arrêt *normal*. En cas de résiliation appropriée, le circuit et les ressources associées sont immédiatement libérés. Un client peut également se déconnecter de manière non appropriée, par exemple en raison d’une interruption du réseau. Blazor Server stocke les circuits déconnectés pour un intervalle configurable afin de permettre au client de se reconnecter.

Blazor Server permet au code de définir un *Gestionnaire de circuit* qui permet d’exécuter du code sur les modifications de l’état du circuit d’un utilisateur. Pour plus d'informations, consultez <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Latence de l’interface utilisateur

La latence de l’interface utilisateur est le temps qu’il faut après une action initiée jusqu’au moment où l’interface utilisateur est mise à jour. Des valeurs plus petites pour la latence de l’interface utilisateur sont impératives pour qu’une application soit réactive à un utilisateur. Dans une application Blazor Server, chaque action est envoyée au serveur, traitée et une comparaison d’interface utilisateur est renvoyée. Par conséquent, la latence de l’interface utilisateur est la somme de la latence du réseau et de la latence du serveur lors du traitement de l’action.

Pour une application métier limitée à un réseau d’entreprise privé, l’effet sur les perceptions des utilisateurs de la latence en raison de la latence du réseau est généralement inperceptible. Pour une application déployée sur Internet, la latence peut devenir perceptible pour les utilisateurs, en particulier si les utilisateurs sont largement répartis géographiquement.

L’utilisation de la mémoire peut également contribuer à la latence de l’application. L’augmentation de l’utilisation de la mémoire résulte en de fréquentes garbage collection ou la pagination de la mémoire sur le disque, qui dégradent les performances des applications et augmentent la latence de l’interface utilisateur.

Les applications Blazor Server doivent être optimisées pour réduire la latence de l’interface utilisateur en réduisant la latence du réseau et l’utilisation de la mémoire. Pour une approche de la mesure de la latence du réseau, consultez <xref:blazor/host-and-deploy/server#measure-network-latency> . Pour plus d’informations sur SignalR et Blazor, consultez :

* <xref:blazor/host-and-deploy/server>
* <xref:blazor/security/server/threat-mitigation>

### <a name="connection-to-the-server"></a>Connexion au serveur

Les applications Blazor Server requièrent une connexion SignalR active au serveur. Si la connexion est perdue, l’application tente de se reconnecter au serveur. Tant que l’état du client est toujours en mémoire, la session client reprend sans perte d’État.

Une application Blazor Server effectue un prérendu en réponse à la première demande du client, qui configure l’état de l’interface utilisateur sur le serveur. Lorsque le client tente de créer une connexion SignalR, le client doit se reconnecter au même serveur. Les applications Blazor Server qui utilisent plusieurs serveurs principaux doivent implémenter des *sessions rémanentes* pour les connexions SignalR.

Nous vous recommandons d’utiliser le [service Azure SignalR](/azure/azure-signalr) pour les applications Blazor Server. Le service permet la mise à l’échelle d’une application Blazor Server vers un grand nombre de connexions SignalR simultanées. Les sessions rémanentes sont activées pour le service Azure SignalR en définissant l'option `ServerStickyMode` ou la valeur de configuration du service sur `Required`. Pour plus d'informations, consultez <xref:blazor/host-and-deploy/server#signalr-configuration>.

Lorsque vous utilisez IIS, les sessions rémanentes sont activées avec Application Request Routing. Pour plus d’informations, consultez [équilibrage de charge http à l’aide de application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:signalr/introduction>
* <xref:blazor/fundamentals/additional-scenarios>
* <xref:tutorials/signalr-blazor-webassembly>
