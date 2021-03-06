---
title: Outils pour ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur les outils disponibles pour créer des applications Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 500342ac979efdee824ac0d4b5757ca9804f3b30
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054812"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a>Outils pour ASP.NET Core Blazor

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

::: zone pivot="windows"

1. Installez la dernière version de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail **développement Web et ASP.net** .

1. Créez un projet.

1. Sélectionnez **Blazor application**. Sélectionnez **Suivant** .

1. Indiquez un nom de projet dans le champ **Nom du projet** , ou acceptez le nom de projet par défaut. Confirmez que l’entrée d'**emplacement** est correcte ou indiquez un emplacement pour le projet. Sélectionnez **Create** (Créer).

1. Pour une expérience Blazor WebAssembly, choisissez le modèle d'**application Blazor WebAssembly** . Pour une expérience Blazor Server, choisissez le modèle d'**application Blazor Server** . Sélectionnez **Create** (Créer).

   Pour plus d’informations sur les deux modèles d’hébergement Blazor, *Blazor WebAssembly* et *Blazor Server* , consultez <xref:blazor/hosting-models>.

1. Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.

Pour plus d’informations sur l’approbation du certificat de développement HTTPs ASP.NET Core, consultez <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

::: zone-end

::: zone pivot="linux"

1. Installez la dernière version du [Kit SDK .net Core](https://dotnet.microsoft.com/download). Si vous avez déjà installé le kit de développement logiciel (SDK), vous pouvez déterminer la version installée en exécutant la commande suivante dans une interface de commande :

   ```dotnetcli
   dotnet --version
   ```

1. Installez la dernière version de [Visual Studio code](https://code.visualstudio.com).

1. Installez la dernière [extension C# for Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

1. Pour une expérience Blazor WebAssembly, exécutez la commande suivante dans une interface de commande :

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Pour une expérience Blazor Server, exécutez la commande suivante dans une interface de commande :

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Pour plus d’informations sur les deux modèles d’hébergement Blazor, *Blazor WebAssembly* et *Blazor Server*, consultez <xref:blazor/hosting-models>.

1. Ouvrez le dossier `WebApplication1` dans Visual Studio Code.

1. L’IDE demande que vous ajoutiez des ressources pour générer et déboguer le projet. Sélectionnez **Oui**.

1. Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.

## <a name="trust-a-development-certificate"></a>Approuver un certificat de développement

Il n’existe pas de méthode centralisée pour approuver un certificat sur Linux. En règle générale, l’une des approches suivantes est adoptée :

* Excluez l’URL de l’application dans la liste d’exclusion du navigateur.
* Faites confiance à tous les certificats auto-signés pour `localhost`.
* Ajoutez le certificat à la liste des certificats approuvés dans le navigateur.

Pour plus d’informations, consultez les conseils fournis par le fabricant de votre navigateur et la distribution Linux.

::: zone-end

::: zone pivot="macos"

1. Installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.

1. Dans la barre latérale, sélectionnez application **Web et console**  >  **App**.

   Pour une expérience Blazor WebAssembly, choisissez le modèle d'**application Blazor WebAssembly**. Pour une expérience Blazor Server, choisissez le modèle d'**application Blazor Server**. Sélectionnez **Suivant**.

   Pour plus d’informations sur les deux modèles d’hébergement Blazor, *Blazor WebAssembly* et *Blazor Server* , consultez <xref:blazor/hosting-models> .

1. Vérifiez que **l’authentification** est définie sur **aucune authentification**. Sélectionnez **Suivant**.

1. Dans le champ **nom du projet**, nommez l’application `WebApplication1`. Sélectionnez **Create** (Créer).

1. Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour exécuter l’application *sans le débogueur*. Exécutez l’application avec **Run**  >  le bouton exécuter **Démarrer le débogage** ou exécuter (&#9654;) pour exécuter l’application *avec le débogueur*.

Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez. Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat. Pour plus d’informations sur l’approbation du certificat de développement HTTPs ASP.NET Core, consultez <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

::: zone-end
