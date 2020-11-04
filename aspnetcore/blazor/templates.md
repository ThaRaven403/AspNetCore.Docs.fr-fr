---
title: Modèles de ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur les modèles d’application ASP.net Core Blazor et la structure de projet Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
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
uid: blazor/templates
ms.openlocfilehash: eef381367d7aa59dcc430c529746088d4488e700
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054929"
---
# <a name="aspnet-core-no-locblazor-templates"></a>Modèles de ASP.NET Core Blazor

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

L'infrastructure Blazor fournit des modèles pour développer des applications pour chacun des modèles d’hébergement Blazor :

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Pour plus d’informations sur les modèles d’hébergement de Blazor, consultez <xref:blazor/hosting-models>.

Les options de modèle sont disponibles en passant l'option `--help` à la commande CLI [`dotnet new`](/dotnet/core/tools/dotnet-new) :

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a>Structure de projet Blazor

Les fichiers et dossiers suivants composent une application Blazor générée à partir d’un modèle de projet Blazor :

* `Program.cs`: Le point d’entrée de l’application qui installe les éléments suivants :

  * [Hôte](xref:fundamentals/host/generic-host) ASP.net Core ( Blazor Server )
  * Hôte WebAssembly ( Blazor WebAssembly ) : le code de ce fichier est propre aux applications créées à partir du modèle Blazor WebAssembly ( `blazorwasm` ).
    * Le composant `App` est le composant racine de l’application. Le composant `App` est spécifié en tant qu'élément DOM `app` ( `<app>...</app>` ) à la collection de composants racine ( `builder.RootComponents.Add<App>("app")` ).
    * Les [services](xref:blazor/fundamentals/dependency-injection) sont ajoutés et configurés (par exemple, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

* `Startup.cs` ( Blazor Server ) : Contient la logique de démarrage de l’application. La classe `Startup` définit deux méthodes :

  * `ConfigureServices`: Configure les services d' [injection de dépendances](xref:fundamentals/dependency-injection) de l’application. Dans les applications Blazor Server, les services sont ajoutés en appelant <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> , et le `WeatherForecastService` est ajouté au conteneur de service pour une utilisation par l’exemple du composant `FetchData`.
  * `Configure`: Configure le pipeline de traitement des demandes de l’application :
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> est appelé pour configurer un point de terminaison pour la connexion en temps réel avec le navigateur. La connexion est créée avec [SignalR](xref:signalr/introduction), qui est une infrastructure permettant d’ajouter des fonctionnalités Web en temps réel aux applications.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) est appelé pour configurer la page racine de l’application ( `Pages/_Host.cshtml` ) et activer la navigation.

* `wwwroot/index.html` ( Blazor WebAssembly ) : Page racine de l’application implémentée en tant que page HTML :
  * Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.
  * La page spécifie l’emplacement où le composant racine `App` est restitué. Le composant est rendu à l’emplacement de l'élément DOM `app` ( `<app>...</app>` ).
  * Le `_framework/blazor.webassembly.js` fichier JavaScript est chargé, ce qui :
    * Télécharge le Runtime .NET, l’application et les dépendances de l’application.
    * Initialise le runtime pour exécuter l’application.

* `App.razor`: Composant racine de l’application qui configure le routage côté client à l’aide du composant <xref:Microsoft.AspNetCore.Components.Routing.Router>. Le composant <xref:Microsoft.AspNetCore.Components.Routing.Router> intercepte la navigation dans le navigateur et affiche la page qui correspond à l’adresse demandée.

* Dossier `Pages` : contient les composants/pages routables ( `.razor` ) qui composent l'application Blazor et la page Razor racine d’une application Blazor Server. L’itinéraire de chaque page est spécifié à l’aide de la directive [`@page`](xref:mvc/views/razor#page). Le modèle comprend les éléments suivants :
  * `_Host.cshtml` ( Blazor Server ) : Page racine de l’application implémentée en tant que Page Razor
    * Quand une page de l’application est initialement demandée, cette page est rendue et renvoyée dans la réponse.
    * Le fichier JavaScript `_framework/blazor.server.js` est chargé, ce qui configure la connexion en temps réel SignalR entre le navigateur et le serveur.
    * La page hôte spécifie où le composant racine `App` ( `App.razor` ) est affiché.
  * `Counter` ( `Pages/Counter.razor` ) : Implémente la page de compteur.
  * `Error` ( `Error.razor`, application Blazor Server uniquement) : rendu lorsqu’une exception non gérée se produit dans l’application.
  * `FetchData` ( `Pages/FetchData.razor` ) : Implémente la page Fetch Data.
  * `Index` ( `Pages/Index.razor` ) : Implémente la page d’accueil.
  
* `Properties/launchSettings.json`: Contient la configuration de l'[environnement de développement](xref:fundamentals/environments#development-and-launchsettingsjson).

* Dossier `Shared` : contient d’autres composants d’interface utilisateur ( `.razor` ) utilisés par l’application :
  * `MainLayout` ( `MainLayout.razor` ) : [Composant de disposition](xref:blazor/layouts) de l’application.
  * `NavMenu` ( `NavMenu.razor` ) : Implémente la navigation dans la barre latérale. Comprend le [composant `NavLink`](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), qui affiche des liens de navigation vers d’autres composants Razor. Le composant <xref:Microsoft.AspNetCore.Components.Routing.NavLink> indique automatiquement un état sélectionné quand son composant est chargé, ce qui aide l’utilisateur à comprendre quel composant est actuellement affiché.

* `_Imports.razor`: Inclut des directives Razor communes à inclure dans les composants de l’application ( `.razor` ), tels que les directives [`@using`](xref:mvc/views/razor#using) pour les espaces de noms.

* Dossier `Data` ( Blazor Server ) : contient la classe `WeatherForecast` et l’implémentation du `WeatherForecastService` qui fournissent des exemples de données météorologiques au composant `FetchData` de l’application.

* `wwwroot`: Dossier [racine Web](xref:fundamentals/index#web-root) de l’application contenant les ressources statiques publiques de l’application.

* `appsettings.json`: Contient les [paramètres de configuration](xref:blazor/fundamentals/configuration) de l’application. Dans une application Blazor WebAssembly, le fichier de paramètres d’application se trouve dans le dossier `wwwroot`. Dans une application Blazor Server, le fichier de paramètres d’application se trouve à la racine du projet.
