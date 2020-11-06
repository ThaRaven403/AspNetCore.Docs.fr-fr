---
title: Chargement différé d’assemblys dans ASP.NET Core Blazor WebAssembly
author: guardrex
description: Découvrez comment charger en différé des assemblys dans des applications ASP.net Core Blazor WebAssembly.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 6a1feffb5341d432d6d1949a9e26b9537b85ba03
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054786"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a>Chargement différé d’assemblys dans ASP.NET Core Blazor WebAssembly

Par [safia Abdalla](https://safia.rocks) et [Luke Latham](https://github.com/guardrex)

Les performances de démarrage de l’application Blazor WebAssembly peuvent être améliorées en différant le chargement de certains assemblys d’application jusqu’à ce qu’ils soient nécessaires, ce qui s’appelle le *chargement différé* . Par exemple, les assemblys qui sont utilisés uniquement pour le rendu d’un seul composant peuvent être configurés pour être chargés uniquement si l’utilisateur accède à ce composant. Après le chargement, les assemblys sont mis en cache côté client et sont disponibles pour toutes les navigations ultérieures.

La fonctionnalité de chargement différé de Blazor vous permet de marquer des assemblys d’application pour le chargement différé, qui charge les assemblys lors de l’exécution lorsque l’utilisateur accède à un itinéraire particulier. La fonctionnalité est constituée de modifications apportées au fichier projet et des modifications apportées au routeur de l’application.

> [!NOTE]
> Le chargement différé d’un assembly n’avantage pas les applications Blazor Server, car les assemblys ne sont pas téléchargés vers le client dans une application Blazor Server.

## <a name="project-file"></a>Fichier projet

Marquez les assemblys pour le chargement différé dans le fichier projet de l’application ( `.csproj` ) à l’aide de l'élément `BlazorWebAssemblyLazyLoad`. Utilisez le nom de l’assembly avec l'extension `.dll`. L'infrastructure Blazor empêche le chargement des assemblys spécifiés par ce groupe d’éléments au lancement de l’application. L’exemple suivant marque un grand assembly personnalisé ( `GrantImaharaRobotControls.dll` ) pour le chargement différé. Si un assembly marqué pour le chargement différé a des dépendances, il doit également être marqué pour le chargement différé dans le fichier projet.

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a>Composant `Router`

Le composant `Router` de Blazor désigne quelles assemblys que Blazor recherche les composants routables. Le composant `Router` est également responsable du rendu du composant pour l’itinéraire à partir duquel l’utilisateur navigue. Le composant `Router` prend en charge une fonctionnalité `OnNavigateAsync` qui peut être utilisée conjointement avec le chargement différé.

Dans le composant de l’application `Router` ( `App.razor` ) :

* Ajoutez un rappel `OnNavigateAsync`. Le gestionnaire `OnNavigateAsync` est appelé lorsque l’utilisateur :
  * Visite un itinéraire pour la première fois en y accédant directement à partir de son navigateur.
  * Navigue vers un nouvel itinéraire à l’aide d’un lien ou d’un appel <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>.
* Si les assemblys à chargement différé contiennent des composants routables, ajoutez une [liste](xref:System.Collections.Generic.List%601) \<<xref:System.Reflection.Assembly>> (par exemple, nommée `lazyLoadedAssemblies` ) au composant. Les assemblys sont passés à la collection <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> au cas où les assemblys contiennent des composants routables. L’infrastructure recherche les itinéraires dans les assemblys et met à jour la collection d’itinéraires si de nouveaux itinéraires sont trouvés.

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

Si le rappel `OnNavigateAsync` lève une exception non gérée, l'[interface utilisateur d’erreur Blazor](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) est appelée.

### <a name="assembly-load-logic-in-onnavigateasync"></a>Logique de chargement d’assembly dans `OnNavigateAsync`

`OnNavigateAsync` a un paramètre `NavigationContext` qui fournit des informations sur l’événement de navigation asynchrone actuel, y compris le chemin d’accès cible ( `Path` ) et le jeton d’annulation ( `CancellationToken` ) :

* La propriété `Path` est le chemin d’accès de destination de l’utilisateur par rapport au chemin d’accès de base de l’application, par exemple `/robot`.
* Le `CancellationToken` peut être utilisé pour observer l’annulation de la tâche asynchrone. `OnNavigateAsync` annule automatiquement la tâche de navigation en cours d’exécution lorsque l’utilisateur accède à une autre page.

Dans `OnNavigateAsync`, implémentez une logique pour déterminer les assemblys à charger. Les options sont les suivantes :

* Vérifications conditionnelles à l’intérieur de la méthode `OnNavigateAsync`.
* Table de recherche qui mappe des itinéraires à des noms d’assemblys, soit injectés dans le composant, soit implémentée dans le bloc [`@code`](xref:mvc/views/razor#code).

`LazyAssemblyLoader` est un service Singleton fourni par l’infrastructure pour le chargement d’assemblys. Injecter `LazyAssemblyLoader` dans le composant `Router` :

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

Le `LazyAssemblyLoader` fournit la méthode `LoadAssembliesAsync` qui :

* Utilise l’interopérabilité JS pour extraire des assemblys via un appel réseau.
* Charge les assemblys dans le runtime s’exécutant sur webassembly dans le navigateur.

L’implémentation du chargement différé de l’infrastructure prend en charge le chargement différé avec prérendu dans une solution hébergée Blazor. Lors du prérendu, tous les assemblys, y compris ceux marqués pour le chargement différé, sont supposés être chargés. Inscrivez manuellement `LazyAssemblyLoader` dans la méthode `Startup.ConfigureServices` ( `Startup.cs` ) du projet *serveur*  :

```csharp
services.AddScoped<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a>Interaction de l’utilisateur avec le contenu `<Navigating>`

Lors du chargement des assemblys, ce qui peut prendre plusieurs secondes, le composant `Router` peut indiquer à l’utilisateur qu’une transition de page se produit :

* Ajoutez une directive [`@using`](xref:mvc/views/razor#using) pour l'espace de noms <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName>.
* Ajoutez une balise `<Navigating>` au composant avec le balisage à afficher pendant les événements de transition de page.

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a>Gérer les annulations dans `OnNavigateAsync`

L'objet `NavigationContext` passé au rappel `OnNavigateAsync` contient un `CancellationToken` qui est défini lorsqu’un nouvel événement de navigation se produit. Le rappel `OnNavigateAsync` doit lever une exception quand ce jeton d’annulation est défini pour éviter de continuer à exécuter le rappel `OnNavigateAsync` sur une navigation obsolète.

Si un utilisateur accède à l’itinéraire A, puis immédiatement à l’itinéraire B, l’application ne doit pas continuer à exécuter le rappel `OnNavigateAsync` pour l’itinéraire a :

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> Ne pas lever la valeur si le jeton d’annulation dans `NavigationContext` est annulé peut entraîner un comportement inattendu, tel que le rendu d’un composant à partir d’une navigation précédente.

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a>Événements `OnNavigateAsync` et fichiers d’assembly renommés

Le chargeur de ressource s’appuie sur les noms d’assemblys qui sont définis dans le fichier `blazor.boot.json`. Si les [assemblys sont renommés](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), les noms d’assembly utilisés dans les méthodes `OnNavigateAsync` et les noms d’assembly dans le fichier `blazor.boot.json` ne sont pas synchronisés.

Pour rectifier ce qui suit :

* Vérifiez si l’application s’exécute dans l’environnement de production lorsque vous déterminez les noms d’assembly à utiliser.
* Stockez les noms d’assemblys renommés dans un fichier distinct et lisez-les à partir de ce fichier pour déterminer le nom de l’assembly à utiliser dans les méthodes `LazyLoadAssemblyService` et `OnNavigateAsync`.

### <a name="complete-example"></a>Exemple complet

Le composant `Router` complet suivant montre le chargement de l'assembly `GrantImaharaRobotControls.dll` lorsque l’utilisateur accède à `/robot` . Lors des transitions de page, un message stylisé est affiché à l’utilisateur.

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a>Dépanner

* Si un rendu inattendu se produit (par exemple, un composant d’une navigation précédente est rendu), confirmez que le code lève si le jeton d’annulation est défini.
* Si les assemblys sont toujours chargés au démarrage de l’application, vérifiez que l’assembly est marqué comme étant différé dans le fichier projet.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/webassembly-performance-best-practices>
