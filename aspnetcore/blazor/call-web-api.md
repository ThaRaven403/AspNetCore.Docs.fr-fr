---
title: Appeler une API Web à partir de ASP.NET Core Blazor WebAssembly
author: guardrex
description: Découvrez comment appeler une API Web à partir d’une Blazor WebAssembly application à l’aide des applications auxiliaires JSON, y compris la création de demandes de partage des ressources Cross-Origin (cors).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
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
uid: blazor/call-web-api
ms.openlocfilehash: 85b3ded6ec25310a573e99cbedf0df005d92bdbe
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234411"
---
# <a name="call-a-web-api-from-aspnet-core-no-locblazor"></a>Appeler une API Web à partir de ASP.NET Core Blazor

Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) et [Juan de la Cruz](https://github.com/juandelacruz23)

> [!NOTE]
> Cette rubrique s’applique à Blazor WebAssembly. Les applications [Blazor Server](xref:blazor/hosting-models#blazor-server) appellent des API Web à l’aide d'instances <xref:System.Net.Http.HttpClient>, généralement créées à l’aide de <xref:System.Net.Http.IHttpClientFactory>. Pour obtenir des instructions qui s’appliquent à Blazor Server, consultez <xref:fundamentals/http-requests> .

Les applications [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) appellent des API Web à l’aide d’un service <xref:System.Net.Http.HttpClient> préconfiguré. Composez des requêtes, qui peuvent inclure des options de l'[API d’extraction](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, à l’aide des applications auxiliaires JSON de Blazor ou de <xref:System.Net.Http.HttpRequestMessage>. Le service <xref:System.Net.Http.HttpClient> dans les applications Blazor WebAssembly est axé sur l’exécution de requêtes sur le serveur d’origine. Les instructions de cette rubrique concernent uniquement les applications Blazor WebAssembly.

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample)) : sélectionnez l'application `BlazorWebAssemblySample`.

Consultez les composants suivants dans l'exemple d’application `BlazorWebAssemblySample` :

* Appeler l’API Web ( `Pages/CallWebAPI.razor` )
* Testeur de requêtes HTTP ( `Components/HTTPRequestTester.razor` )

## <a name="packages"></a>Paquets

Référencez le package NuGet [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) dans le fichier projet.

## <a name="add-the-httpclient-service"></a>Ajouter le service HttpClient

Dans `Program.Main`, ajoutez un service <xref:System.Net.Http.HttpClient> s’il n’existe pas déjà :

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>Applications auxiliaires HttpClient et JSON

Dans une application Blazor WebAssembly, [`HttpClient`](xref:fundamentals/http-requests) est disponible en tant que service préconfiguré pour effectuer des demandes auprès du serveur d’origine.

Une application Blazor Server n’inclut pas de service <xref:System.Net.Http.HttpClient> par défaut. Fournissez un <xref:System.Net.Http.HttpClient> à l’application à l’aide de l'[infrastructure de fabrique `HttpClient`](xref:fundamentals/http-requests).

<xref:System.Net.Http.HttpClient> et les auxiliaires JSON sont également utilisés pour appeler des points de terminaison d’API Web tiers. <xref:System.Net.Http.HttpClient> est implémenté à l’aide de l'[API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API) du navigateur et est soumis à ses limitations, y compris l’application de la même stratégie d’origine.

L’adresse de base du client est définie sur l’adresse du serveur d’origine. Injecter une instance <xref:System.Net.Http.HttpClient> à l’aide de la directive [`@inject`](xref:mvc/views/razor#inject) :

```razor
@using System.Net.Http
@inject HttpClient Http
```

Dans les exemples suivants, une API Web todo traite les opérations de création, lecture, mise à jour et suppression (CRUD). Les exemples sont basés sur une classe `TodoItem` qui stocke les éléments suivants :

* ID ( `Id` , `long` ) : ID unique de l’élément.
* Nom ( `Name` , `string` ) : nom de l’élément.
* Status ( `IsComplete` , `bool` ) : indique si l’élément TODO est terminé.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Les méthodes d’assistance JSON envoient des demandes à un URI (une API Web dans les exemples suivants) et traitent la réponse :

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Envoie une requête HTTP obtenir et analyse le corps de la réponse JSON pour créer un objet.

  Dans le code suivant, les `todoItems` sont affichés par le composant. La méthode `GetTodoItems` est déclenchée lorsque le rendu du composant est terminé ( [`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) ). Pour obtenir un exemple complet, consultez l’exemple d’application.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Envoie une requête HTTP POST, y compris du contenu encodé JSON, et analyse le corps de la réponse JSON pour créer un objet.

  Dans le code suivant, `newItemName` est fourni par un élément lié du composant. La méthode `AddItem` est déclenchée par la sélection d’un élément `<button>`. Pour obtenir un exemple complet, consultez l’exemple d’application.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  Appellez <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> pour retourner un <xref:System.Net.Http.HttpResponseMessage>. Pour désérialiser le contenu JSON du message de réponse, utilisez la méthode d’extension `ReadFromJsonAsync<T>` :
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Envoie une requête HTTP PUT, y compris le contenu encodé JSON.

  Dans le code suivant, les valeurs `Name` et `IsCompleted` pour `editItem` sont fournies par les éléments dépendants du composant. L’élément `Id` est défini lorsque l’élément est sélectionné dans une autre partie de l’interface utilisateur et `EditItem` est appelé. La méthode `SaveItem` est déclenchée par la sélection de l'élément `<button>` Save. Pour obtenir un exemple complet, consultez l’exemple d’application.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  Appellez <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> pour retourner un <xref:System.Net.Http.HttpResponseMessage>. Pour désérialiser le contenu JSON du message de réponse, utilisez la méthode d’extension <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> :
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http> contient des méthodes d’extension supplémentaires pour envoyer des requêtes HTTP et recevoir des réponses HTTP. <xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> est utilisé pour envoyer une requête HTTP DELETE à une API Web.

Dans le code suivant, l'élément `<button>` Delete appelle la méthode `DeleteItem`. L'élément lié `<input>` fournit le `id` de l’élément à supprimer. Pour obtenir un exemple complet, consultez l’exemple d’application.

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a>HttpClient nommé avec IHttpClientFactory

Les services <xref:System.Net.Http.IHttpClientFactory> et la configuration d’un <xref:System.Net.Http.HttpClient> nommé sont pris en charge.

Référencez le package NuGet [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) dans le fichier projet.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

`FetchData` composant ( `Pages/FetchData.razor` ) :

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a>HttpClient typé

Un <xref:System.Net.Http.HttpClient> typé utilise une ou plusieurs instances de l’application <xref:System.Net.Http.HttpClient>, par défaut ou nommé, pour retourner des données à partir d’un ou plusieurs points de terminaison d’API Web.

`WeatherForecastClient.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastHttpClient
{
    private readonly HttpClient http;

    public WeatherForecastHttpClient(HttpClient http)
    {
        this.http = http;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }

        return forecasts;
    }
}
```

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastHttpClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Les composants injectent le <xref:System.Net.Http.HttpClient> typé pour appeler l’API Web.

`FetchData` composant ( `Pages/FetchData.razor` ) :

```razor
@inject WeatherForecastHttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetForecastAsync();
    }
}
```

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>`HttpClient` et `HttpRequestMessage` avec les options de requête de l’API Fetch

Lors de l’exécution sur webassembly dans une application Blazor WebAssembly, [`HttpClient`](xref:fundamentals/http-requests) (documentation de l'[API](xref:System.Net.Http.HttpClient)) et <xref:System.Net.Http.HttpRequestMessage> peuvent être utilisés pour personnaliser les demandes. Par exemple, vous pouvez spécifier la méthode HTTP et les en-têtes de demande. Le composant suivant envoie une demande `POST` à un point de terminaison d’API de liste de tâches sur le serveur et affiche le corps de la réponse :

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

L’implémentation de .NET webassembly de <xref:System.Net.Http.HttpClient> utilise [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). L’extraction permet de configurer plusieurs [options spécifiques à la demande](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Les options de requête HTTP FETCH peuvent être configurées avec les méthodes d’extension de <xref:System.Net.Http.HttpRequestMessage> indiquées dans le tableau suivant.

| Méthode d’extension | Propriété de requête Fetch |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Vous pouvez définir des options supplémentaires à l’aide de la méthode d’extension plus générique <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>.
 
La réponse HTTP est généralement mise en mémoire tampon dans une application Blazor WebAssembly pour permettre la prise en charge des lectures de synchronisation sur le contenu de la réponse. Pour activer la prise en charge de la diffusion en continu de réponse, utilisez la méthode d’extension <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> sur la demande.

Pour inclure des informations d’identification dans une demande Cross-Origin, utilisez la méthode d’extension <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> :

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Pour plus d’informations sur les options de l’API FETCH, consultez [MDN Web docs : WindowOrWorkerGlobalScope. Fetch () :P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

## <a name="handle-errors"></a>Gérer les erreurs

Lorsque des erreurs se produisent lors de l’interaction avec une API Web, elles peuvent être gérées par le code du développeur. Par exemple, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> attend une réponse JSON de l’API du serveur avec un `Content-Type` de `application/json` . Si la réponse n’est pas au format JSON, la validation du contenu lève une exception <xref:System.NotSupportedException>.

Dans l’exemple suivant, le point de terminaison d’URI pour la demande de données de prévision météo est mal orthographié. L’URI doit être à `WeatherForecast`, mais apparaît dans l’appel en tant que `WeatherForcast` (« e » manquant).

L'appel à <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> attend que JSON soit retourné, mais le serveur retourne du code HTML pour une exception non gérée sur le serveur avec un `Content-Type` de `text/html` . L’exception non gérée se produit sur le serveur, car le chemin d’accès est introuvable et l’intergiciel ne peut pas traiter une page ou une vue pour la demande.

Dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> sur le client, <xref:System.NotSupportedException> est levée lorsque le contenu de la réponse est validé en tant que non-JSON. L’exception est interceptée dans le bloc `catch`, où la logique personnalisée peut consigner l’erreur ou présenter un message d’erreur convivial à l’utilisateur :

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> L’exemple précédent est fourni à des fins de démonstration. Une application serveur d’API Web peut être configurée pour retourner JSON même lorsqu’un point de terminaison n’existe pas ou qu’une exception non gérée s’est produite sur le serveur.

Pour plus d'informations, consultez <xref:blazor/fundamentals/handle-errors>.

## <a name="cross-origin-resource-sharing-cors"></a>Partage des ressources cross-origin (CORS)

La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web. Cette restriction est appelée *stratégie de même origine* . La stratégie de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site. Pour effectuer des demandes à partir du navigateur vers un point de terminaison avec une origine différente, le *point de terminaison* doit activer le [partage des ressources Cross-Origin (cors)](https://www.w3.org/TR/cors/).

L'[exemple d’application Blazor WebAssembly ( Blazor WebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) illustre l’utilisation de cors dans le composant Appeler l’API Web ( `Pages/CallWebAPI.razor` ).

Pour plus d’informations sur CORS avec des demandes sécurisées dans les applications Blazor, consultez <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors> .

Pour obtenir des informations générales sur CORS avec les applications ASP.NET Core, consultez <xref:security/cors> .

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/security/webassembly/additional-scenarios>: Comprend la couverture de l’utilisation de <xref:System.Net.Http.HttpClient> pour créer des demandes d’API Web sécurisées.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configuration du point de terminaison HTTPs Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Cross Origin Resource Sharing (CORS) au W3C](https://www.w3.org/TR/cors/)
