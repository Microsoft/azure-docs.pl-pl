---
title: 'Samouczek: ASP.NET Core z bazą danych SQL'
description: Dowiedz się, jak uruchomić aplikację .NET Core w usłudze Azure App Service z użyciem połączenia z usługą SQL Database.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: c4dacd06cd53ebb71ca9db2722fdf46aade841bc
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085448"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>Samouczek: Tworzenie aplikacji ASP.NET Core i SQL Database w usłudze Azure App Service

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Windows. Aby wdrożyć usługę App Service w systemie _Linux_, zobacz [Tworzenie aplikacji platformy .NET Core i usługi SQL Database w usłudze Azure App Service w systemie Linux](./containers/tutorial-dotnetcore-sqldb-app.md).
>

Usługa [App Service](overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie na platformie Azure. W tym samouczku pokazano, jak utworzyć aplikację platformy .NET Core i połączyć ją z usługą SQL Database. Po zakończeniu aplikacja MVC platformy .NET Core będzie działać w usłudze App Service.

![aplikacja działająca w usłudze App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

Omawiane kwestie:

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

* [Zainstaluj oprogramowanie Git](https://git-scm.com/)
* [Instalowanie pakietu SDK core platformy .NET](https://dotnet.microsoft.com/download)

## <a name="create-local-net-core-app"></a>Tworzenie lokalnej aplikacji .NET Core

Ten krok umożliwia skonfigurowanie lokalnego projektu platformy .NET Core.

### <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

W oknie terminalu dodaj element `cd` do katalogu roboczego.

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium i zmiany jego katalogu głównego.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

Przykładowy projekt zawiera podstawową aplikację CRUD (create-read-update-delete, tworzenie-odczytywanie-aktualizowanie-usuwanie) korzystającą z rozwiązania [Entity Framework Core](https://docs.microsoft.com/ef/core/).

### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom następujące polecenia, aby zainstalować wymagane pakiety, uruchom migracje baz danych i uruchom aplikację.

```bash
dotnet tool install -g dotnet-ef --version 3.1.1
dotnet-ef database update
dotnet run
```

W przeglądarce przejdź do adresu `http://localhost:5000`. Wybierz link **Utwórz nowy** i utwórz kilka elementów typu _zadanie do wykonania_.

![pomyślne połączenie z bazą danych SQL Database](./media/app-service-web-tutorial-dotnetcore-sqldb/local-app-in-browser.png)

Aby zatrzymać platformę .NET Core w dowolnym momencie, naciśnij kombinację klawiszy `Ctrl+C` w terminalu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Tworzenie produkcyjnej bazy danych SQL Database

W tym kroku utworzysz bazę danych SQL Database na platformie Azure. Aplikacja wdrożona na platformie Azure używa tej bazy danych w chmurze.

W tym samouczku jako baza danych SQL jest używana baza danych [Azure SQL Database](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Tworzenie serwera logicznego bazy danych SQL Database

W aplikacji Cloud Shell utwórz serwer [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) logiczny bazy danych SQL za pomocą polecenia.

Zastąp * \<server_name>* symbol zastępczy unikatową nazwą bazy danych SQL. Ta nazwa jest używana jako część punktu końcowego bazy danych SQL Database, `<server_name>.database.windows.net`, więc nazwa musi być unikatowa na wszystkich serwerach logicznych platformy Azure. Nazwa może zawierać tylko małe litery, cyfry oraz znak łącznika (-) i musi się składać z 3–50 znaków. Ponadto zastąp * \<db_username>* i * \<db_password>* wybraną nazwą użytkownika i hasłem. 


```azurecli-interactive
az sql server create --name <server_name> --resource-group myResourceGroup --location "West Europe" --admin-user <db_username> --admin-password <db_password>
```

Po utworzeniu serwera logicznego SQL Database w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

<pre>
{
  "administratorLogin": "&lt;db_username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server_name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server_name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server_name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>Konfigurowanie reguły zapory serwera

Utwórz [regułę zapory na poziomie serwera usługi Azure SQL Database](../sql-database/sql-database-firewall-configure.md) za pomocą polecenia [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Po ustawieniu początkowego i końcowego adresu IP na 0.0.0.0 zapora będzie otwierana tylko dla innych zasobów platformy Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> Reguła zapory może być jeszcze bardziej restrykcyjna, jeśli [zostaną użyte tylko adresy IP dla ruchu wychodzącego używane przez aplikację](overview-inbound-outbound-ips.md#find-outbound-ips).
>

### <a name="create-a-database"></a>Tworzenie bazy danych

Utwórz bazę danych [o poziomie wydajności S0](../sql-database/sql-database-service-tiers-dtu.md) na serwerze za pomocą polecenia [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server_name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Tworzenie parametrów połączenia

Zastąp następujący ciąg * \<server_name>* * \<, db_username>* i * \<db_password>* wcześniej.

```
Server=tcp:<server_name>.database.windows.net,1433;Database=coreDB;User ID=<db_username>;Password=<db_password>;Encrypt=true;Connection Timeout=30;
```

To są parametry połączenia dla aplikacji .NET Core. Skopiuj je w celu późniejszego użycia.

## <a name="deploy-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

W tym kroku wdrożysz aplikację .NET Core połączoną z bazą danych SQL Database w usłudze App Service.

### <a name="configure-local-git-deployment"></a>Konfigurowanie lokalnego wdrożenia narzędzia Git

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="configure-connection-string"></a>Konfigurowanie ciągu połączenia

Aby ustawić parametry połączenia dla aplikacji [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) platformy Azure, użyj polecenia w aplikacji Cloud Shell. W poniższym poleceniu zastąp * \<nazwę aplikacji>*, a także parametr * \<connection_string>* z utworzonym wcześniej ciągiem połączenia.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection="<connection_string>" --connection-string-type SQLServer
```

W ASP.NET Core można użyć tego nazwanego`MyDbConnection`ciągu połączenia ( ) przy użyciu standardowego wzorca, jak każdy ciąg połączenia określony w *pliku appsettings.json*. W tym `MyDbConnection` przypadku, jest również zdefiniowany w *appsettings.json*. Podczas uruchamiania w usłudze App Service parametry połączenia zdefiniowane w usłudze App Service mają pierwszeństwo przed ciągiem połączenia zdefiniowanym w pliku *appsettings.json*. Kod używa *appsettings.json* wartość podczas rozwoju lokalnego i ten sam kod używa wartości usługi app service po wdrożeniu.

Aby zobaczyć, jak ciąg połączenia odwołuje się do kodu, zobacz [Łączenie się z bazą danych SQL w produkcji](#connect-to-sql-database-in-production).

### <a name="configure-environment-variable"></a>Konfigurowanie zmiennej środowiskowej

Następnie wybierz dla ustawienia aplikacji `ASPNETCORE_ENVIRONMENT` wartość _Produkcja_. To ustawienie informuje, czy używasz platformy Azure, ponieważ używasz SQLite dla lokalnego środowiska programistycznego i bazy danych SQL dla środowiska platformy Azure.

W poniższym przykładzie pokazano konfigurowanie ustawienia aplikacji `ASPNETCORE_ENVIRONMENT` w aplikacji platformy Azure. Zastąp * \<app_name>* symbolu zastępczego.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
```

Aby zobaczyć, jak zmienna środowiskowa odwołuje się do kodu, zobacz [Łączenie się z bazą danych SQL w środowisku produkcyjnym](#connect-to-sql-database-in-production).

### <a name="connect-to-sql-database-in-production"></a>Łączenie z bazą danych SQL Database w środowisku produkcyjnym

W repozytorium lokalnym otwórz plik Startup.cs i znajdź następujący kod:

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

Zastąp go poniższym kodem, który używa skonfigurowanych wcześniej zmiennych środowiskowych.

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if(Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Jeśli ten kod wykryje, że jest uruchomiony w środowisku produkcyjnym (co wskazuje środowisko platformy Azure), a następnie używa ciągu połączenia skonfigurowany do łączenia się z bazą danych SQL.

Wywołanie `Database.Migrate()` pomaga, gdy jest uruchamiany na platformie Azure, ponieważ automatycznie tworzy bazy danych, które aplikacja .NET Core potrzebuje, na podstawie konfiguracji migracji. 

> [!IMPORTANT]
> W przypadku aplikacji produkcyjnych wymagających skalowania w poziomie postępuj zgodnie z najlepszymi rozwiązaniami opisanymi w rozdziale [Stosowanie migracji w środowisku produkcyjnym](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production).
> 

Zapisz zmiany, a następnie zatwierdź je w repozytorium Git. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 98, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (92/92), done.
Writing objects: 100% (98/98), 524.98 KiB | 5.58 MiB/s, done.
Total 98 (delta 8), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: .
remote: Updating submodules.
remote: Preparing deployment for commit id '0c497633b8'.
remote: Generating deployment script.
remote: Project file path: ./DotNetCoreSqlDb.csproj
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://&lt;app_name&gt;.scm.azurewebsites.net/&lt;app_name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Przechodzenie do aplikacji platformy Azure

Przejdź do wdrożonej aplikacji w przeglądarce internetowej.

```bash
http://<app_name>.azurewebsites.net
```

Dodaj kilka elementów do wykonania.

![aplikacja działająca w usłudze App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

**Gratulacje!** Używasz opartej na danych aplikacji .NET Core w usłudze App Service.

## <a name="update-locally-and-redeploy"></a>Lokalne aktualizowanie i ponowne wdrażanie

W tym kroku wprowadzisz zmianę schematu bazy danych i opublikujesz ją na platformie Azure.

### <a name="update-your-data-model"></a>Aktualizowanie modelu danych

Otwórz plik _Models\Todo.cs_ w edytorze kodu. Dodaj następującą właściwość do klasy `ToDo`:

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Lokalne uruchamianie migracji Code First

Uruchom kilka poleceń, aby zastosować aktualizacje w lokalnej bazie danych.

```bash
dotnet ef migrations add AddProperty
```

Zaktualizuj lokalną bazę danych:

```bash
dotnet ef database update
```

### <a name="use-the-new-property"></a>Używanie nowej właściwości

Wprowadź zmiany kodu, aby użyć właściwości `Done`. Dla uproszczenia w tym samouczku zmienisz tylko widoki `Index` i `Create`, aby zobaczyć, jak działa właściwość.

Otwórz plik _Controllers\TodosController.cs_.

Znajdź metodę `Create([Bind("ID,Description,CreatedDate")] Todo todo)` i dodaj element `Done` do listy właściwości w atrybucie `Bind`. Po zakończeniu podpis metody `Create()` będzie wyglądać podobnie do następującego kodu:

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

Otwórz plik _Views\Todos\Create.cshtml_.

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

Otwórz plik _Views\Todos\Index.cshtml_.

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

W przeglądarce przejdź do `http://localhost:5000/`. Teraz możesz dodać element do wykonania i zaznaczyć pole **Gotowe**. Następnie powinien zostać on wyświetlony na stronie głównej jako ukończony. Pamiętaj, że widok `Edit` nie zawiera pola `Done`, ponieważ nie zmieniono widoku `Edit`.

### <a name="publish-changes-to-azure"></a>Publikowanie zmian na platformie Azure

```bash
git add .
git commit -m "added done field"
git push azure master
```

Po zakończeniu przejdź do aplikacji Usługi App Service i spróbuj dodać element do wykonania i zaznacz **pole wyboru Gotowe**. `git push`

![Aplikacja platformy Azure po zakończeniu migracji Code First](./media/app-service-web-tutorial-dotnetcore-sqldb/this-one-is-done.png)

Nadal wyświetlane są wszystkie istniejące elementy do wykonania. Po ponownym opublikowaniu aplikacji .NET Core istniejące dane w bazie danych SQL nie zostaną utracone. Ponadto migracje Entity Framework Core zmieniają tylko schemat danych i pozostawiają istniejące dane bez zmian.

## <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

Gdy aplikacja ASP.NET Core działa w usłudze Azure App Service, dzienniki konsoli można przesłać potokiem do usługi Cloud Shell. W ten sposób można użyć komunikatów diagnostycznych w celu ułatwienia debugowania błędów aplikacji.

Przykładowy projekt jest już zgodny ze wskazówkami dostępnymi w [opisie rejestrowania platformy ASP.NET Core na platformie Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) z dwiema zmianami konfiguracji:

- Zawiera odwołanie do `Microsoft.Extensions.Logging.AzureAppServices` w pliku *DotNetCoreSqlDb.csproj*.
- Połączenia `loggerFactory.AddAzureWebAppDiagnostics()` w *Program.cs*.

Aby zmienić domyślny [poziom rejestrowania](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-level) platformy ASP.NET Core `Error` w usłudze App Service na poziom `Information`, użyj w usłudze Cloud Shell polecenia [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config).

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> Poziom rejestrowania projektu jest już ustawiony na `Information` w pliku *appsettings.json*.
> 

Aby rozpocząć przesyłanie [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) strumieniowe dziennika, użyj polecenia w usłudze Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Po rozpoczęciu przesyłania strumieniowego dzienników odśwież aplikację platformy Azure w przeglądarce, aby wywołać pewien ruch w Internecie. Teraz można zobaczyć dzienniki konsoli przesłane potokiem do terminala. Jeśli nie widzisz dzienników konsoli, sprawdź ponownie w ciągu 30 sekund.

Aby zatrzymać przesyłanie strumieniowe `Ctrl` + `C`dziennika w dowolnym momencie, wpisz .

Aby uzyskać więcej informacji na temat dostosowywania dzienników platformy ASP.NET Core, zobacz [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) (Rejestrowanie na platformie ASP.NET Core).

## <a name="manage-your-azure-app"></a>Zarządzanie aplikacją platformy Azure

Aby wyświetlić utworzoną aplikację, w [witrynie Azure portal](https://portal.azure.com)wyszukaj i wybierz pozycję **Usługi aplikacji**.

![Wybieranie usług aplikacji w witrynie Azure portal](./media/app-service-web-tutorial-dotnetcore-sqldb/app-services.png)

Na stronie **Usługi aplikacji** wybierz nazwę aplikacji platformy Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/app-service-web-tutorial-dotnetcore-sqldb/access-portal.png)

Domyślnie portal pokazuje stronę **Przegląd** aplikacji. Ta strona udostępnia widok sposobu działania aplikacji. Tutaj możesz również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Po lewej stronie strony są wyświetlane różne strony konfiguracji, które można otworzyć.

![Strona usługi App Service w witrynie Azure Portal](./media/app-service-web-tutorial-dotnetcore-sqldb/web-app-blade.png)

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
> [Mapowanie istniejącej niestandardowej nazwy DNS na usługę Azure App Service](app-service-web-tutorial-custom-domain.md)
