---
title: Partie 2, ajouter un modèle à une Razor application pages dans ASP.net Core
author: rick-anderson
description: Partie 2 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 84198760cf8302d379c7630b65641e65b66d72a2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050925"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a>Partie 2, ajouter un modèle à une Razor application pages dans ASP.net Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

Dans cette section, des classes sont ajoutées pour la gestion des films. Les classes de modèle de l’application utilisent [Entity Framework Core (EF Core)](/ef/core) pour travailler avec la base de données. EF Core est un mappeur objet-relationnel (O/RM) qui simplifie l’accès aux données.

Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core. Elles définissent les propriétés des données stockées dans la base de données.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Ajouter un modèle de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Cliquez avec le bouton droit sur le projet **Razor PagesMovie** > **Ajouter**  >  **un nouveau dossier** . Nommez le dossier *modèles* .

Cliquez avec le bouton droit sur le dossier *Models* . Sélectionnez **Ajouter** une  >  **classe** . Nommez la classe **Movie** .

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ajoutez un dossier nommé *Models* .
* Ajoutez une classe au dossier *Modèles* nommé *Movie.cs* .

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Dans panneau solutions, cliquez avec le bouton droit sur le projet **Razor PagesMovie** , puis sélectionnez **Ajouter** > **un nouveau dossier...** . Nommez le dossier *modèles* .
* Cliquez avec le bouton droit sur le dossier *modèles* , puis sélectionnez **Ajouter** > **un nouveau fichier...** .
* Dans la boîte de dialogue **Nouveau fichier** :

  * Dans le volet gauche, sélectionnez **Général** .
  * Dans le volet central, sélectionnez **Classe vide** .
  * Nommez la classe **Movie** , puis sélectionnez **Nouveau** .

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.

## <a name="scaffold-the-movie-model"></a>Générer automatiquement le modèle de film

Dans cette section, le modèle de film est généré automatiquement. Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Créer un dossier *Pages/Movies* :

* Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier** .
* Nommez le dossier *Movies* .

Cliquez avec le bouton droit sur le dossier *Pages/Movies* > **Ajouter** > **Nouvel élément généré automatiquement** .

![Image illustrant les instructions précédentes.](model/_static/sca.png)

Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter** .

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :

* Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)** .
* Dans la ligne de la **classe de contexte de données** , sélectionnez le **+** signe (plus), puis modifiez le nom généré à partir de Razor PagesMovie. **Modèles** . Razor PagesMovieContext à Razor PagesMovie. **Données** . Razor PagesMovieContext. [Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise. Elle crée la classe de contexte de base de données avec l’espace de noms correct.
* Sélectionnez **Ajouter** .

![Image illustrant les instructions précédentes.](model/_static/3/arp.png)

Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).
* Installez l’outil de génération de modèles automatique :

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Pour Windows** : exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Pour macOS et Linux** , exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Créer un dossier *Pages/Movies* :

* Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier** .
* Nommez le dossier *Movies* .

Cliquez avec le bouton droit sur le dossier *pages/movies* > **Ajouter** une > **nouvelle génération de modèles automatique...** .

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

Dans la boîte de dialogue **nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **suivant** .

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :

* Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie ( Razor PagesMovie. Models)** .
* Dans la ligne de la **classe de contexte de données** , tapez le nom de la nouvelle classe, Razor PagesMovie. **Données** . Razor PagesMovieContext. [Cette modification](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) n'est pas requise. Elle crée la classe de contexte de base de données avec l’espace de noms correct.
* Sélectionnez **Ajouter** .

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

### <a name="add-ef-tools"></a>Ajouter des outils EF

Exécutez la commande CLI .NET Core suivante :

```dotnetcli
dotnet tool install --global dotnet-ef
```

La commande précédente ajoute les outils de Entity Framework Core pour le CLI .NET Core.

---

### <a name="files-created"></a>Fichiers créés

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :

* *Pages/Movies*  : Create, Delete, Details, Edit, Index.
* *Données/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Mis à jour

* *Startup.cs*

Les fichiers créés et mis à jour sont expliqués dans la section suivante.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :

* *Pages/Movies*  : Create, Delete, Details, Edit, Index.
* *Données/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Mis à jour

* *Startup.cs*

Les fichiers créés et mis à jour sont expliqués dans la section suivante.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Le processus de génération de modèles automatique crée les fichiers suivants :

* *Pages/Movies*  : Create, Delete, Details, Edit, Index.

Les fichiers créés sont expliqués dans la section suivante.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Migration initiale

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans cette section, la console du gestionnaire de package est utilisée pour :

* Ajoutez une migration initiale.
* Mettez à jour la base de données avec la migration initiale.

Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** .

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

Dans la console du gestionnaire de package, entrez les commandes suivantes :

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

Les commandes précédentes génèrent l’avertissement suivant : « aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ». Les valeurs sont tronquées en mode silencieux si elles ne sont pas compatibles avec la précision et l’échelle par défaut. Spécifiez explicitement le type de colonne SQL Server capable d’accueillir toutes les valeurs en utilisant 'HasColumnType()'. »

Vous pouvez ignorer cet avertissement, il sera corrigé dans un prochain tutoriel.

La commande migrations génère du code pour créer le schéma de base de données initial. Le schéma est basé sur le modèle spécifié dans `DbContext` . L’argument `InitialCreate` est utilisé pour nommer les migrations. Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est sélectionné.

La `update` commande exécute la `Up` méthode dans des migrations qui n’ont pas été appliquées. Dans ce cas, `update` exécute la `Up` méthode dans le fichier  *migrations/ \<time-stamp> _InitialCreate. cs* , qui crée la base de données.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examiner le contexte inscrit avec l’injection de dépendances

ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection). Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application. Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur. Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.

L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.

Examinez la méthode `Startup.ConfigureServices`. La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext` coordonne les fonctionnalités d’EF Core (Create, Read, Update, Delete, etc.) pour le modèle `Movie`. Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il spécifie les entités qui sont incluses dans le modèle de données.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données. Une entité correspond à une ligne dans la table.

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Examinez la méthode `Up`.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Examinez la méthode `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Tester l’application

* Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).

Si vous obtenez cette erreur :

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Vous avez manqué [l’étape des migrations](#pmc).

* Testez le lien **Créer** .

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`. Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée. Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testez les liens **Edit** , **Details** et **Delete** .

Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

Dans cette section, des classes sont ajoutées pour la gestion des films dans une [base de données SQLite](https://www.sqlite.org/index.html)multiplateforme. Les applications créées à partir d’un modèle de ASP.NET Core utilisent une base de données SQLite. Les classes de modèle de l’application sont utilisées avec [Entity Framework Core (EF Core)](/ef/core) ([fournisseur de base de données SQLite EF Core](/ef/core/providers/sqlite)) pour fonctionner avec la base de données. EF Core est un framework de mappage relationnel d’objets qui simplifie l’accès aux données.

Les classes de modèle portent le nom de classes OCT (« Objet CLR Traditionnel »), car elles n’ont pas de dépendances envers EF Core. Elles définissent les propriétés des données stockées dans la base de données.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Ajouter un modèle de données

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Cliquez avec le bouton droit sur le projet **Razor PagesMovie** > **Ajouter**  >  **un nouveau dossier** . Nommez le dossier *modèles* .

Cliquez avec le bouton droit sur le dossier *Models* . Sélectionnez **Ajouter** une  >  **classe** . Nommez la classe **Movie** .

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Ajoutez un dossier nommé *Models* .
* Ajoutez une classe au dossier *Modèles* nommé *Movie.cs* .

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Dans Explorateur de solutions, cliquez avec le bouton droit sur le projet **Razor PagesMovie** , puis sélectionnez **Ajouter**  >  **un nouveau dossier** . Nommez le dossier *modèles* .
* Cliquez avec le bouton droit sur le dossier *Modèles* , puis sélectionnez **Ajouter** > **Nouveau fichier** .
* Dans la boîte de dialogue **Nouveau fichier** :

  * Dans le volet gauche, sélectionnez **Général** .
  * Dans le volet central, sélectionnez **Classe vide** .
  * Nommez la classe **Movie** , puis sélectionnez **Nouveau** .

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Générez le projet pour vérifier qu’il n’y a pas d’erreur de compilation.

## <a name="scaffold-the-movie-model"></a>Générer automatiquement le modèle de film

Dans cette section, le modèle de film est généré automatiquement. Autrement dit, l’outil de génération de modèles automatique génère des pages pour les opérations de création, de lecture, de mise à jour et de suppression (CRUD) pour le modèle de film.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Créer un dossier *Pages/Movies* :

* Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier** .
* Nommez le dossier *Movies* .

Cliquez avec le bouton droit sur le dossier *Pages/Movies* > **Ajouter** > **Nouvel élément généré automatiquement** .

![Image illustrant les instructions précédentes.](model/_static/sca.png)

Dans la boîte de dialogue **Ajouter une structure** , sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter** .

![Image illustrant les instructions précédentes.](model/_static/add_scaffold.png)

Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* Dans la liste déroulante **classe de modèle** , sélectionnez **film ( Razor PagesMovie. Models)** .
* Dans la ligne de la **classe de contexte de données** , sélectionnez le **+** signe (plus) et acceptez le nom généré **Razor PagesMovie. Models. Razor PagesMovieContext** .
* Sélectionnez **Ajouter** .

![Image illustrant les instructions précédentes.](model/_static/arp.png)

Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Ouvrez une fenêtre Commande dans le répertoire de projet (répertoire qui contient les fichiers *Program.cs* , *Startup.cs* et *.csproj* ).

* **Pour Windows** : exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Pour macOS et Linux** , exécutez la commande suivante :

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Créer un dossier *Pages/Movies* :

* Cliquez avec le bouton droit sur le dossier *Pages* > **Ajouter** > **Nouveau dossier** .
* Nommez le dossier *Movies* .

Cliquez avec le bouton droit sur le dossier *Pages/Movies* > **Ajouter** > **Nouvel élément généré automatiquement** .

![Image illustrant les instructions précédentes.](model/_static/scaMac.png)

Dans la boîte de dialogue **Ajouter une nouvelle génération de modèles** automatique, sélectionnez **Razor pages à l’aide de Entity Framework (CRUD)** > **Ajouter** .

![Image illustrant les instructions précédentes.](model/_static/add_scaffoldMac.png)

Complétez la boîte de dialogue **Ajouter des Razor pages à l’aide de Entity Framework (CRUD)** :

* Dans la liste déroulante **classe de modèle** , sélectionnez ou tapez **Movie** .
* Dans la ligne de la **classe de contexte de données** , tapez Select the **Razor PagesMovieContext** This crée une nouvelle classe de contexte de base de données avec l’espace de noms correct. Dans ce cas, il s’agit de **Razor PagesMovie. Models. Razor PagesMovieContext** .
* Sélectionnez **Ajouter** .

![Image illustrant les instructions précédentes.](model/_static/arpMac.png)

Le *appsettings.json* fichier est mis à jour avec la chaîne de connexion utilisée pour se connecter à une base de données locale.

---

Le processus de génération de modèles automatique crée et met à jour les fichiers suivants :

### <a name="files-created"></a>Fichiers créés

* *Pages/Movies*  : Create, Delete, Details, Edit, Index.
* *Données/ Razor PagesMovieContext.cs*

### <a name="file-updated"></a>Fichier mis à jour

* *Startup.cs*

Les fichiers créés et mis à jour sont expliqués dans la section suivante.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migration initiale

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dans cette section, la console du gestionnaire de package est utilisée pour :

* Ajoutez une migration initiale.
* Mettez à jour la base de données avec la migration initiale.

Dans le menu **Outils** , sélectionnez **Gestionnaire de package NuGet** > **Console du Gestionnaire de package** .

  ![Menu Console du Gestionnaire de package](../first-mvc-app/adding-model/_static/pmc.png)

Dans la console du gestionnaire de package, entrez les commandes suivantes :

```powershell
Add-Migration Initial
Update-Database
```

La commande `Add-Migration` génère du code pour créer le schéma de base de données initial. Le schéma est basé sur le modèle spécifié dans le `DbContext` (dans le fichier *Razor PagesMovieContext.cs* ). L' `InitialCreate` argument est utilisé pour nommer la migration. Vous pouvez utiliser n’importe quel nom, mais par convention, un nom décrivant la migration est utilisé. Pour plus d'informations, consultez <xref:data/ef-mvc/migrations>.

La `Update-Database` commande exécute la `Up` méthode dans le fichier *migrations/ \<time-stamp> _InitialCreate. cs* . La méthode `Up` crée la base de données.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> Les commandes précédentes génèrent l’avertissement suivant : « *aucun type n’a été spécifié pour la colonne décimale «Price » sur le type d’entité « Movie ». Les valeurs sont tronquées en mode silencieux si elles ne tiennent pas dans la précision et l’échelle par défaut. Spécifiez explicitement le type de colonne SQL Server qui peut prendre en charge toutes les valeurs à l’aide de’HasColumnType () '.* Vous pouvez ignorer cet avertissement. il sera corrigé dans un didacticiel ultérieur.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Examiner le contexte inscrit avec l’injection de dépendances

ASP.NET Core comprend [l’injection de dépendances](xref:fundamentals/dependency-injection). Des services (tels que le contexte de base de données EF Core) sont inscrits avec l’injection de dépendances au démarrage de l’application. Ces services sont fournis par les composants qui requièrent ces services (tels que les Razor pages) par le biais de paramètres de constructeur. Le code du constructeur qui obtient une instance de contexte de base de données est indiqué plus loin dans le tutoriel.

L’outil de génération de modèles automatique a créé automatiquement un contexte de base de données et l’a inscrit dans le conteneur d’injection de dépendances.

Examinez la méthode `Startup.ConfigureServices`. La ligne en surbrillance a été ajoutée par l’outil de génération de modèles automatique :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext` coordonne les fonctionnalités d’EF Core (Create, Read, Update, Delete, etc.) pour le modèle `Movie`. Le contexte de données (`RazorPagesMovieContext`) est dérivé de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). Il spécifie les entités qui sont incluses dans le modèle de données.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Le code précédent crée une [propriété \<Movie> DbSet](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pour le jeu d’entités. Dans la terminologie Entity Framework, un jeu d’entités correspond généralement à une table de base de données. Une entité correspond à une ligne dans la table.

Le nom de la chaîne de connexion est transmis au contexte en appelant une méthode sur un objet [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Pour le développement local, le [système de configuration ASP.net Core](xref:fundamentals/configuration/index) lit la chaîne de connexion à partir du *appsettings.json* fichier.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Examinez la méthode `Up`.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Examinez la méthode `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Tester l’application

* Exécutez l’application et ajoutez `/Movies` à l’URL dans le navigateur (`http://localhost:port/movies`).

Si vous obtenez cette erreur :

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Vous avez manqué [l’étape des migrations](#pmc).

* Testez le lien **Créer** .

  ![Create page](model/_static/conan.png)

  > [!NOTE]
  > Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`. Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que « Anglais » qui utilisent une virgule (« , ») comme décimale et des formats de date autres que le format « Anglais (États-Unis »), l’application doit être localisée. Pour obtenir des instructions sur la localisation, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testez les liens **Edit** , **Details** et **Delete** .

Le prochain didacticiel décrit les fichiers créés par la génération de modèles automatique.

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent : prise en main](xref:tutorials/razor-pages/razor-pages-start) 
>  [Suivant : génération de modèles Razor automatique Pages](xref:tutorials/razor-pages/page)

::: moniker-end
