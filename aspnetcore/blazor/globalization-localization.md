---
title: Globalisation et localisation ASP.net Core Blazor
author: guardrex
description: Découvrez comment rendre des composants Razor accessibles aux utilisateurs dans plusieurs cultures et langages.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/globalization-localization
ms.openlocfilehash: f8f261f25d854a9bf36ad3299f4af392d5c4fafd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055878"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a>Globalisation et localisation ASP.net Core Blazor

Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)

Les composants Razor peuvent être rendus accessibles aux utilisateurs dans plusieurs cultures et langues. Les scénarios .NET de globalisation et de localisation suivants sont disponibles :

* Système de ressources de .NET
* Mise en forme des nombres et des dates spécifiques à la culture

Un ensemble limité de scénarios de localisation de ASP.NET Core est actuellement pris en charge :

* <xref:Microsoft.Extensions.Localization.IStringLocalizer> et <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *sont pris en charge* dans les applications Blazor.
* <xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>la <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> et la localisation des annotations de données (*Data Annotations*), sont des scénarios ASP.net Core MVC et **non pris en charge** dans les applications Blazor.

Pour plus d'informations, consultez <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalisation

La fonctionnalité [`@bind`](xref:mvc/views/razor#bind) de Blazor effectue des mises en forme et analyse les valeurs pour l’affichage en fonction de la culture actuelle de l’utilisateur.

La culture actuelle est accessible à partir de la propriété <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.

<xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> est utilisé pour les types de champ suivants ( `<input type="{TYPE}" />` ) :

* `date`
* `number`

Les types de champ précédents :

* Sont affichés à l’aide des règles de mise en forme appropriées basées sur le navigateur.
* Ne peuvent pas contenir de texte de forme libre.
* Fournissent des caractéristiques d’interaction de l’utilisateur en fonction de l’implémentation du navigateur.

Les types de champs suivants ont des exigences de mise en forme spécifiques et ne sont pas actuellement pris en charge par Blazor, car ils ne sont pas pris en charge par tous les principaux navigateurs :

* `datetime-local`
* `month`
* `week`

[`@bind`](xref:mvc/views/razor#bind) prend en charge le paramètre `@bind:culture` pour fournir un <xref:System.Globalization.CultureInfo?displayProperty=fullName> pour l’analyse et la mise en forme d’une valeur. La spécification d’une culture n’est pas recommandée lors de l’utilisation des types de champs `date` et `number`. `date` et `number` disposent d’une prise en charge Blazor intégrée qui fournit la culture requise.

## <a name="localization"></a>Localisation

### Blazor WebAssembly

Les applications Blazor WebAssembly définissent la culture à l’aide de la [préférence de langue](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages) de l’utilisateur.

Pour configurer explicitement la culture, définissez <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> et <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> dans `Program.Main`.

::: moniker range=">= aspnetcore-5.0"

Par défaut, Blazor WebAssembly comporte des ressources de globalisation minimales requises pour afficher des valeurs, telles que les dates et les devises, dans la culture de l’utilisateur. Les applications qui doivent prendre en charge la modification dynamique de la culture doivent être configurées dans le fichier projet `BlazorWebAssemblyLoadAllGlobalizationData` :

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

Blazor WebAssembly peut également être configuré pour démarrer à l’aide d’une culture d’application spécifique à l’aide des options passées à `Blazor.start`. Par exemple, l’exemple ci-dessous montre une application configurée pour être lancée à l’aide de la culture `en-GB` :

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  Blazor.start({
    applicationCulture: 'en-GB'
  });
</script>
```

La valeur de `applicationCulture` doit être conforme au [format de balise de langage BCP-47](https://tools.ietf.org/html/bcp47).

Si l’application ne nécessite pas de localisation, vous pouvez configurer l’application pour qu’elle prenne en charge la culture dite indifférente, qui est basée sur la culture `en-US` :

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Par défaut, la configuration de l’éditeur de liens en langage intermédiaire (IL) pour les applications Blazor WebAssembly supprime les informations d’internationalisation, à l’exception des paramètres régionaux demandés explicitement. Pour plus d'informations, consultez <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.

::: moniker-end

Alors que la culture que Blazor sélectionne par défaut peut être suffisante pour la plupart des utilisateurs, envisagez d’offrir aux utilisateurs un moyen de spécifier leurs paramètres régionaux préférés. Pour obtenir un exemple d’application Blazor WebAssembly avec un sélecteur de culture, consultez l'exemple d’application localisation [`LocSample`](https://github.com/pranavkm/LocSample).

### Blazor Server

Les applications Blazor Server sont localisées à l’aide de l'[intergiciel (middleware](xref:fundamentals/localization#localization-middleware)) de localisation. L’intergiciel sélectionne la culture appropriée pour les utilisateurs qui demandent des ressources à partir de l’application.

La culture peut être définie à l’aide de l’une des approches suivantes :

* [Cookies](#cookies)
* [Fournir l’interface utilisateur pour choisir la culture](#provide-ui-to-choose-the-culture)

Pour plus d’informations et d’exemples, consultez <xref:fundamentals/localization>.

#### <a name="no-loccookies"></a>Cookies

Un cookie de culture de localisation peut conserver la culture de l’utilisateur. L’intergiciel (middleware) de localisation lit les cookies sur les demandes suivantes pour définir la culture de l’utilisateur. 

L’utilisation d’un cookie s’assure que la connexion WebSocket peut propager correctement la culture. Si les schémas de localisation sont basés sur le chemin d’URL ou la chaîne de requête, il est possible que le schéma ne soit pas en mesure de fonctionner avec les WebSockets, et donc de ne pas conserver la culture. Par conséquent, l’utilisation d’une culture de localisation cookie est l’approche recommandée.

Toute technique peut être utilisée pour assigner une culture si la culture est rendue persistante dans une localisation cookie. Si l’application a déjà un schéma de localisation établi pour ASP.NET Core côté serveur, continuez à utiliser l’infrastructure de localisation existante de l’application et à définir la culture de localisation cookie dans le schéma de l’application.

L’exemple suivant montre comment définir la culture actuelle dans un cookie qui peut être lue par l’intergiciel (middleware) de localisation. Créer une expression Razor dans le fichier `Pages/_Host.cshtml` immédiatement à l’intérieur de la balise d’ouverture `<body>` :

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

La localisation est gérée par l’application dans la séquence d’événements suivante :

1. Le navigateur envoie une requête HTTP initiale à l’application.
1. La culture est affectée par l’intergiciel (middleware) de localisation.
1. L'expression Razor dans la page `_Host` ( `_Host.cshtml` ) conserve la culture dans un cookie dans le cadre de la réponse.
1. Le navigateur ouvre une connexion WebSocket pour créer une session interactive Blazor Server.
1. L’intergiciel (middleware) de localisation lit le cookie et assigne la culture.
1. La session Blazor Server commence avec la bonne culture.

Lorsque vous travaillez avec un <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage>, utilisez la propriété <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context> :

```razor
@{
    this.Context.Response.Cookies.Append(
        CookieRequestCultureProvider.DefaultCookieName,
        CookieRequestCultureProvider.MakeCookieValue(
            new RequestCulture(
                CultureInfo.CurrentCulture,
                CultureInfo.CurrentUICulture)));
}
```

#### <a name="provide-ui-to-choose-the-culture"></a>Fournir l’interface utilisateur pour choisir la culture

Pour fournir une interface utilisateur permettant à un utilisateur de sélectionner une culture, il est recommandé d’effectuer une *approche basée sur la redirection*. Le processus est similaire à ce qui se produit dans une application Web lorsqu’un utilisateur tente d’accéder à une ressource sécurisée. L’utilisateur est redirigé vers une page de connexion, puis redirigé vers la ressource d’origine. 

L’application conserve la culture sélectionnée de l’utilisateur via une redirection vers un contrôleur. Le contrôleur définit la culture sélectionnée de l’utilisateur dans un cookie et redirige l’utilisateur vers l’URI d’origine.

Établissez un point de terminaison HTTP sur le serveur pour définir la culture sélectionnée de l’utilisateur dans un cookie et effectuer la redirection vers l’URI d’origine :

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Utilisez le résultat de l’action <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> pour empêcher les attaques de redirection ouvertes. Pour plus d'informations, consultez <xref:security/preventing-open-redirects>.

Si l’application n’est pas configurée pour traiter les actions du contrôleur :

* Ajoutez des services MVC à la collection de services dans `Startup.ConfigureServices` :

  ```csharp
  services.AddControllers();
  ```

* Ajouter un routage de point de terminaison de contrôleur dans `Startup.Configure` :

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

Le composant suivant montre un exemple d’exécution de la redirection initiale lorsque l’utilisateur sélectionne une culture :

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/localization>
