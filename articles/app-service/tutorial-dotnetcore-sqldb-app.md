---
title: 'Samouczek: ASP.NET Core z Azure SQL Database'
description: Dowiedz się, jak uzyskać aplikację .NET Core działającą w Azure App Service z połączeniem z Azure SQL Database.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-csharp, mvc, cli-validate, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: b83dfbd715a6b27491fd5f5efd41213210092325
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90982891"
---
# <a name="tutorial-build-an-aspnet-core-and-azure-sql-database-app-in-azure-app-service"></a>Samouczek: Tworzenie aplikacji ASP.NET Core i Azure SQL Database w Azure App Service

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web na platformie Azure. W tym samouczku pokazano, jak utworzyć aplikację platformy .NET Core i połączyć ją z SQL Database. Gdy skończysz, będziesz mieć aplikację .NET Core MVC działającą w App Service w systemie Windows.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. W tym samouczku pokazano, jak utworzyć aplikację platformy .NET Core i połączyć ją z usługą SQL Database. Po zakończeniu aplikacja MVC platformy .NET Core będzie działać w usłudze App Service w systemie Linux.

::: zone-end

![aplikacja działająca w usłudze App Service](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych SQL Database na platformie Azure
> * Łączenie aplikacji .NET Core z bazą danych SQL Database
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników diagnostycznych z platformy Azure
> * Zarządzanie aplikacją w witrynie Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* <a href="https://git-scm.com/" target="_blank">Zainstaluj oprogramowanie Git</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Zainstaluj najnowszą wersję zestawu SDK platformy .NET Core 3,1</a>

## <a name="create-local-net-core-app"></a>Tworzenie lokalnej aplikacji .NET Core

Ten krok umożliwia skonfigurowanie lokalnego projektu platformy .NET Core.

### <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

W oknie terminalu dodaj element `cd` do katalogu roboczego.

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium i zmiany jego katalogu głównego.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Przykładowy projekt zawiera podstawową aplikację CRUD (create-read-update-delete, tworzenie-odczytywanie-aktualizowanie-usuwanie) korzystającą z rozwiązania [Entity Framework Core](/ef/core/).

### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom następujące polecenia, aby zainstalować wymagane pakiety, uruchom migracje baz danych i uruchom aplikację.

```bash
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

W przeglądarce przejdź do adresu `http://localhost:5000`. Wybierz link **Utwórz nowy** i utwórz kilka elementów typu _zadanie do wykonania_.

![pomyślne połączenie z bazą danych SQL Database](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Aby zatrzymać platformę .NET Core w dowolnym momencie, naciśnij kombinację klawiszy `Ctrl+C` w terminalu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Tworzenie produkcyjnej bazy danych SQL Database

W tym kroku utworzysz bazę danych SQL Database na platformie Azure. Aplikacja wdrożona na platformie Azure używa tej bazy danych w chmurze.

W tym samouczku jako baza danych SQL jest używana baza danych [Azure SQL Database](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Tworzenie serwera logicznego bazy danych SQL Database

W Cloud Shell Utwórz SQL Database serwera logicznego za pomocą [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) polecenia.

Zamień *\<server-name>* symbol zastępczy na *unikatową* nazwę SQL Database. Ta nazwa jest używana jako część globalnie unikatowych punktów końcowych SQL Database, `<server-name>.database.windows.net` . Prawidłowe znaki to `a` - `z` , `0` - `9` , `-` . Ponadto Zastąp *\<db-username>* i nazwą *\<db-password>* wybranej nazwy użytkownika i hasła. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

Po utworzeniu serwera logicznego SQL Database w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>Konfigurowanie reguły zapory serwera

Utwórz [regułę zapory na poziomie serwera usługi Azure SQL Database](../azure-sql/database/firewall-configure.md) za pomocą polecenia [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Po ustawieniu początkowego i końcowego adresu IP na 0.0.0.0 zapora będzie otwierana tylko dla innych zasobów platformy Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Reguła zapory może być jeszcze bardziej restrykcyjna, jeśli [zostaną użyte tylko adresy IP dla ruchu wychodzącego używane przez aplikację](overview-inbound-outbound-ips.md#find-outbound-ips).
>

W Cloud Shell ponownie uruchom polecenie, aby zezwolić na dostęp z komputera lokalnego przez zastąpienie *\<your-ip-address>* [lokalnego adresu IP IPv4](https://www.whatsmyip.org/).

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database"></a>Tworzenie bazy danych

Utwórz bazę danych [o poziomie wydajności S0](../azure-sql/database/service-tiers-dtu.md) na serwerze za pomocą polecenia [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Tworzenie parametrów połączenia

Pobierz parametry połączenia za pomocą [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string) polecenia.

```azurecli-interactive
az sql db show-connection-string --client ado.net --server <server-name> --name coreDB
```

W danych wyjściowych polecenia Zastąp *\<username>* *\<password>* Parametry i poświadczenia administratora bazy danych użyte wcześniej.

To są parametry połączenia dla aplikacji .NET Core. Skopiuj je w celu późniejszego użycia.

### <a name="configure-app-to-connect-to-production-database"></a>Konfigurowanie aplikacji w celu nawiązania połączenia z produkcyjną bazą danych

W repozytorium lokalnym otwórz plik Startup.cs i znajdź następujący kod:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Zastąp go następującym kodem.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> W przypadku aplikacji produkcyjnych wymagających skalowania w poziomie postępuj zgodnie z najlepszymi rozwiązaniami opisanymi w rozdziale [Stosowanie migracji w środowisku produkcyjnym](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production).
> 

### <a name="run-database-migrations-to-the-production-database"></a>Uruchamianie migracji bazy danych do produkcyjnej bazy danych

Twoja aplikacja obecnie nawiązuje połączenie z lokalną bazą danych programu SQLite. Teraz, po skonfigurowaniu Azure SQL Database, Utwórz ponownie migrację początkową, aby docelowa. 

W katalogu głównym repozytorium Uruchom następujące polecenia. Zamień *\<connection-string>* na utworzone wcześniej parametry połączenia.

```
# Delete old migrations
rm Migrations -r
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>Uruchom aplikację z nową konfiguracją

Teraz, gdy migracja bazy danych jest uruchamiana w produkcyjnej bazie danych, Przetestuj aplikację, uruchamiając następujące polecenie:

```
dotnet run
```

W przeglądarce przejdź do adresu `http://localhost:5000`. Wybierz link **Utwórz nowy** i utwórz kilka elementów typu _zadanie do wykonania_. Aplikacja teraz odczytuje i zapisuje dane w produkcyjnej bazie danych.

Zatwierdź zmiany lokalne, a następnie zatwierdź je w repozytorium git. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

Teraz można przystąpić do wdrażania kodu.

## <a name="deploy-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

W tym kroku wdrożysz aplikację .NET Core połączoną z bazą danych SQL Database w usłudze App Service.

### <a name="configure-local-git-deployment"></a>Konfigurowanie lokalnego wdrożenia narzędzia Git

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

::: zone-end

### <a name="configure-connection-string"></a>Konfigurowanie parametrów połączenia

Aby ustawić parametry połączenia dla aplikacji platformy Azure, użyj [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) polecenia w Cloud Shell. W poniższym poleceniu Zastąp *\<app-name>* , a także *\<connection-string>* parametr ciągiem połączenia utworzonym wcześniej.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

W ASP.NET Core można użyć tego nazwanego ciągu połączenia ( `MyDbConnection` ) przy użyciu wzorca standardowego, podobnie jak wszystkie parametry połączenia określone w *appsettings.jsna*. W tym przypadku `MyDbConnection` jest również definiowana w *appsettings.jsna*. W przypadku uruchamiania w App Service parametry połączenia zdefiniowane w App Service mają pierwszeństwo przed parametrami połączenia zdefiniowanymi w *appsettings.js*. Kod używa *appsettings.jsprzy* wartości podczas lokalnego tworzenia i ten sam kod używa wartości App Service podczas wdrażania.

Aby dowiedzieć się, w jaki sposób w kodzie występuje odwołanie do parametrów połączenia, zobacz [Konfigurowanie aplikacji do nawiązywania połączenia z produkcyjną bazą danych](#configure-app-to-connect-to-production-database).

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

::: zone pivot="platform-windows"  

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 273, done.
Counting objects: 100% (273/273), done.
Delta compression using up to 4 threads
Compressing objects: 100% (175/175), done.
Writing objects: 100% (273/273), 1.19 MiB | 1.85 MiB/s, done.
Total 273 (delta 96), reused 259 (delta 88)
remote: Resolving deltas: 100% (96/96), done.
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cccecf86c5'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote: .
remote: .
remote: .
remote: Done.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/cccecf86c56493ffa594e76ea1deb3abb3702d89/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

::: zone-end

### <a name="browse-to-the-azure-app"></a>Przechodzenie do aplikacji platformy Azure

Przejdź do wdrożonej aplikacji w przeglądarce internetowej.

```bash
http://<app-name>.azurewebsites.net
```

Dodaj kilka elementów do wykonania.

![aplikacja działająca w usłudze App Service](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Gratulacje!** Używasz opartej na danych aplikacji .NET Core w usłudze App Service.

## <a name="update-locally-and-redeploy"></a>Lokalne aktualizowanie i ponowne wdrażanie

W tym kroku wprowadzisz zmianę schematu bazy danych i opublikujesz ją na platformie Azure.

### <a name="update-your-data-model"></a>Aktualizowanie modelu danych

Otwórz _models/do zrobienia. cs_ w edytorze kodu. Dodaj następującą właściwość do klasy `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>Uruchom ponownie migracje bazy danych

Uruchom kilka poleceń, aby zaktualizować produkcyjną bazę danych.

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

> [!NOTE]
> Jeśli otworzysz nowe okno terminalu, należy ustawić parametry połączenia na produkcyjną bazę danych w terminalu, tak jak w przypadku [uruchamiania migracji bazy danych do produkcyjnej bazy danych](#run-database-migrations-to-the-production-database).
>

### <a name="use-the-new-property"></a>Używanie nowej właściwości

Wprowadź zmiany kodu, aby użyć właściwości `Done`. Dla uproszczenia w tym samouczku zmienisz tylko widoki `Index` i `Create`, aby zobaczyć, jak działa właściwość.

Otwórz aplet _controllers/TodosController. cs_.

Znajdź metodę `Create([Bind("ID,Description,CreatedDate")] Todo todo)` i dodaj element `Done` do listy właściwości w atrybucie `Bind`. Po zakończeniu podpis metody `Create()` będzie wyglądać podobnie do następującego kodu:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Otwórz _widok/zadania/Utwórz. cshtml_.

W kodzie Razor powinien zostać wyświetlony element `<div class="form-group">` dla `Description`, a następnie inny element `<div class="form-group">` dla `CreatedDate`. Bezpośrednio po tych dwóch elementach dodaj kolejny element `<div class="form-group">` dla `Done`:

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

Otwórz _widok/zadania/index. cshtml_.

Wyszukaj pusty element `<th></th>`. Bezpośrednio nad tym elementem dodaj następujący kod Razor:

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

Znajdź element `<td>` z pomocnikami tagów (`asp-action`). Bezpośrednio nad tym elementem dodaj następujący kod Razor:

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

To już wszystko, czego potrzebujesz, aby zobaczyć zmiany w widokach `Index` i `Create`.

### <a name="test-your-changes-locally"></a>Lokalne testowanie zmian

Uruchom aplikację lokalnie.

```bash
dotnet run
```

> [!NOTE]
> Jeśli otworzysz nowe okno terminalu, należy ustawić parametry połączenia na produkcyjną bazę danych w terminalu, tak jak w przypadku [uruchamiania migracji bazy danych do produkcyjnej bazy danych](#run-database-migrations-to-the-production-database).
>

W przeglądarce przejdź do `http://localhost:5000/`. Teraz możesz dodać element do wykonania i zaznaczyć pole **Gotowe**. Następnie powinien zostać on wyświetlony na stronie głównej jako ukończony. Pamiętaj, że widok `Edit` nie zawiera pola `Done`, ponieważ nie zmieniono widoku `Edit`.

### <a name="publish-changes-to-azure"></a>Publikowanie zmian na platformie Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

Po `git push` zakończeniu przejdź do aplikacji App Service i spróbuj dodać element do wykonania i zaznacz opcję **gotowe**.

![Aplikacja platformy Azure po zakończeniu migracji Code First](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Nadal wyświetlane są wszystkie istniejące elementy do wykonania. Po ponownym opublikowaniu aplikacji ASP.NET Core istniejące dane w SQL Database nie zostaną utracone. Ponadto migracje Entity Framework Core zmieniają tylko schemat danych i pozostawiają istniejące dane bez zmian.

## <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

Gdy aplikacja ASP.NET Core działa w usłudze Azure App Service, dzienniki konsoli można przesłać potokiem do usługi Cloud Shell. W ten sposób można użyć komunikatów diagnostycznych w celu ułatwienia debugowania błędów aplikacji.

Przykładowy projekt jest już zgodny ze wskazówkami dostępnymi w [opisie rejestrowania platformy ASP.NET Core na platformie Azure](/aspnet/core/fundamentals/logging#azure-app-service-provider) z dwiema zmianami konfiguracji:

- Zawiera odwołanie do `Microsoft.Extensions.Logging.AzureAppServices` w pliku *DotNetCoreSqlDb.csproj*.
- Wywołania `loggerFactory.AddAzureWebAppDiagnostics()` w *program.cs*.

Aby zmienić domyślny [poziom rejestrowania](/aspnet/core/fundamentals/logging#log-level) platformy ASP.NET Core `Error` w usłudze App Service na poziom `Information`, użyj w usłudze Cloud Shell polecenia [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config).

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> Poziom rejestrowania projektu jest już ustawiony na `Information` w pliku *appsettings.json*.
> 

Aby rozpocząć przesyłanie strumieniowe dzienników, użyj [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) polecenia znajdującego się w Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Po rozpoczęciu przesyłania strumieniowego dzienników odśwież aplikację platformy Azure w przeglądarce, aby wywołać pewien ruch w Internecie. Teraz można zobaczyć dzienniki konsoli przesłane potokiem do terminala. Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

Aby w dowolnym momencie zatrzymać przesyłanie strumieniowe dzienników, naciśnij kombinację klawiszy `Ctrl`+`C`.

Aby uzyskać więcej informacji na temat dostosowywania dzienników platformy ASP.NET Core, zobacz [Logging in ASP.NET Core](/aspnet/core/fundamentals/logging) (Rejestrowanie na platformie ASP.NET Core).

## <a name="manage-your-azure-app"></a>Zarządzanie aplikacją platformy Azure

Aby wyświetlić utworzoną aplikację, w [Azure Portal](https://portal.azure.com)Wyszukaj i wybierz pozycję **App Services**.

![Wybierz App Services w Azure Portal](./media/tutorial-dotnetcore-sqldb-app/app-services.png)

Na stronie **App Services** wybierz nazwę swojej aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Domyślnie portal pokazuje stronę **Przegląd** aplikacji. Ta strona udostępnia widok sposobu działania aplikacji. Tutaj możesz również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Strona usługi App Service w witrynie Azure Portal](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Tworzenie bazy danych SQL Database na platformie Azure
> * Łączenie aplikacji .NET Core z bazą danych SQL Database
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników z platformy Azure do terminala
> * Zarządzanie aplikacją w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak zamapować niestandardową nazwę DNS na aplikację.

> [!div class="nextstepaction"]
> [Samouczek: mapowanie niestandardowej nazwy DNS na aplikację](app-service-web-tutorial-custom-domain.md)

Lub zapoznaj się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji ASP.NET Core](configure-language-dotnetcore.md)
