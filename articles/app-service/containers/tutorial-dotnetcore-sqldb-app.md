---
title: 'Samouczek: Linux ASP.NET Core z BAZY DANYCH SQL'
description: Dowiedz się, jak uzyskać aplikację Linux ASP.NET Core opartą na danych działającą w usłudze Azure App Service z połączeniem z bazą danych SQL.
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/06/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 7eed76a6d5272b6e4411497ad4e1d5dbc62b6252
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085922"
---
# <a name="build-an-aspnet-core-and-sql-database-app-in-azure-app-service-on-linux"></a>Tworzenie aplikacji core ASP.NET i bazy danych SQL w usłudze Azure App Service w systemie Linux

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Linux. Aby wdrożyć w usłudze App Service dla systemu _Windows_, zobacz [Tworzenie aplikacji platformy .NET Core i usługi SQL Database w usłudze Azure App Service](../app-service-web-tutorial-dotnetcore-sqldb.md).
>

[Usługa App Service w systemie Linux](app-service-linux-intro.md) zapewnia wysoce skalowalną, samoładującą się usługę hostingową przy użyciu systemu operacyjnego Linux. W tym samouczku pokazano, jak utworzyć aplikację platformy .NET Core i połączyć ją z usługą SQL Database. Po zakończeniu aplikacja MVC platformy .NET Core będzie działać w usłudze App Service w systemie Linux.

![aplikacja działająca w usłudze App Service w systemie Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bazy danych SQL Database na platformie Azure
> * Łączenie aplikacji .NET Core z bazą danych SQL Database
> * Wdrażanie aplikacji na platformie Azure
> * Aktualizowanie modelu danych i ponowne wdrażanie aplikacji
> * Strumieniowe przesyłanie dzienników diagnostycznych z platformy Azure
> * Zarządzanie aplikacją w witrynie Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* [Zainstaluj oprogramowanie Git](https://git-scm.com/)
* [Instalowanie .NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

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
dotnet restore
dotnet ef database update
dotnet run
```

W przeglądarce przejdź do adresu `http://localhost:5000`. Wybierz link **Utwórz nowy** i utwórz kilka elementów typu _zadanie do wykonania_.

![pomyślne połączenie z bazą danych SQL Database](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

Aby zatrzymać platformę .NET Core w dowolnym momencie, naciśnij kombinację klawiszy `Ctrl+C` w terminalu.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>Tworzenie produkcyjnej bazy danych SQL Database

W tym kroku utworzysz bazę danych SQL Database na platformie Azure. Aplikacja wdrożona na platformie Azure używa tej bazy danych w chmurze.

W tym samouczku jako baza danych SQL jest używana baza danych [Azure SQL Database](/azure/sql-database/).

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>Tworzenie serwera logicznego bazy danych SQL Database

W aplikacji Cloud Shell utwórz serwer [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) logiczny bazy danych SQL za pomocą polecenia.

Zastąp * \<symbol zastępczy>nazwy serwera* unikatową nazwą bazy danych SQL. Ta nazwa jest używana jako część punktu końcowego bazy danych SQL Database, `<server-name>.database.windows.net`, więc nazwa musi być unikatowa na wszystkich serwerach logicznych platformy Azure. Nazwa może zawierać tylko małe litery, cyfry oraz znak łącznika (-) i musi się składać z 3–50 znaków. Ponadto zastąp * \<>nazwy użytkownika db* i * \<>db-password* na wybraną nazwę użytkownika i hasło. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

Po utworzeniu serwera logicznego SQL Database w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących:

<pre>
{
  "administratorLogin": "sqladmin",
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

Utwórz [regułę zapory na poziomie serwera usługi Azure SQL Database](../../sql-database/sql-database-firewall-configure.md) za pomocą polecenia [`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create). Po ustawieniu początkowego i końcowego adresu IP na 0.0.0.0 zapora będzie otwierana tylko dla innych zasobów platformy Azure. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="create-a-database"></a>Tworzenie bazy danych

Utwórz bazę danych [o poziomie wydajności S0](../../sql-database/sql-database-service-tiers-dtu.md) na serwerze za pomocą polecenia [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create).

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>Tworzenie parametrów połączenia

Zastąp następujący ciąg * \<>nazwa serwera *, * \<>nazwy użytkownika db *i * \<hasło db>* wcześniej używane.

```
Server=tcp:<server-name>.database.windows.net,1433;Database=coreDB;User ID=<db-username>;Password=<db-password>;Encrypt=true;Connection Timeout=30;
```

To są parametry połączenia dla aplikacji .NET Core. Skopiuj je w celu późniejszego użycia.

## <a name="deploy-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

W tym kroku wdrożysz aplikację .NET Core połączoną z bazą danych SQL Database w usłudze App Service w systemie Linux.

### <a name="configure-local-git-deployment"></a>Konfigurowanie lokalnego wdrożenia narzędzia Git

[!INCLUDE [Configure a deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)] 

### <a name="configure-connection-string"></a>Konfigurowanie ciągu połączenia

Aby ustawić parametry połączenia dla aplikacji [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) platformy Azure, użyj polecenia w aplikacji Cloud Shell. W poniższym poleceniu zastąp * \<>nazwę aplikacji, *a także parametr * \<>parametry>ciągu połączenia* ciągiem połączenia utworzonym wcześniej.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='<connection-string>' --connection-string-type SQLServer
```

W ASP.NET Core można użyć tego nazwanego`MyDbConnection`ciągu połączenia ( ) przy użyciu standardowego wzorca, jak każdy ciąg połączenia określony w *pliku appsettings.json*. W tym `MyDbConnection` przypadku, jest również zdefiniowany w *appsettings.json*. Podczas uruchamiania w usłudze App Service parametry połączenia zdefiniowane w usłudze App Service mają pierwszeństwo przed ciągiem połączenia zdefiniowanym w pliku *appsettings.json*. Kod używa *appsettings.json* wartość podczas rozwoju lokalnego i ten sam kod używa wartości usługi app service po wdrożeniu.

Aby zobaczyć, jak ciąg połączenia odwołuje się do kodu, zobacz [Łączenie się z bazą danych SQL w produkcji](#connect-to-sql-database-in-production).

### <a name="configure-environment-variable"></a>Konfigurowanie zmiennej środowiskowej

Następnie wybierz dla ustawienia aplikacji `ASPNETCORE_ENVIRONMENT` wartość _Produkcja_. To ustawienie informuje, czy używasz platformy Azure, ponieważ używasz SQLite dla lokalnego środowiska programistycznego i bazy danych SQL dla środowiska platformy Azure.

W poniższym przykładzie pokazano konfigurowanie ustawienia aplikacji `ASPNETCORE_ENVIRONMENT` w aplikacji platformy Azure. Zastąp * \<symbol zastępczy>nazwa aplikacji.*

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings ASPNETCORE_ENVIRONMENT="Production"
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
            options.UseSqlite("Data Source=MvcMovie.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

Jeśli ten kod wykryje, że jest uruchomiony w środowisku produkcyjnym (co wskazuje środowisko platformy Azure), a następnie używa ciągu połączenia skonfigurowany do łączenia się z bazą danych SQL. Aby uzyskać informacje na temat sposobu uzyskiwania dostępu do ustawień aplikacji w usłudze App Service, zobacz [Dostęp do zmiennych środowiskowych](configure-language-dotnetcore.md#access-environment-variables)programu Access .

Wywołanie `Database.Migrate()` pomaga, gdy jest uruchamiany na platformie Azure, ponieważ automatycznie tworzy bazy danych, które aplikacja .NET Core potrzebuje, na podstawie konfiguracji migracji.

Zapisz zmiany, a następnie zatwierdź je w repozytorium Git.

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

### <a name="push-to-azure-from-git"></a>Wypychanie z narzędzia Git na platformę Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

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
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Przechodzenie do aplikacji platformy Azure

Przejdź do wdrożonej aplikacji w przeglądarce internetowej.

```bash
http://<app-name>.azurewebsites.net
```

Dodaj kilka elementów do wykonania.

![aplikacja działająca w usłudze App Service w systemie Linux](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**Gratulacje!** Używasz opartej na danych aplikacji .NET Core w usłudze App Service w systemie Linux.

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

Znajdź metodę `Create()` i dodaj element `Done` do listy właściwości w atrybucie `Bind`. Po zakończeniu podpis metody `Create()` będzie wyglądać podobnie do następującego kodu:

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

Po ukończeniu operacji `git push` przejdź do aplikacji platformy Azure i wypróbuj nowe funkcje.

![Aplikacja platformy Azure po zakończeniu migracji Code First](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

Nadal wyświetlane są wszystkie istniejące elementy do wykonania. Po ponownym opublikowaniu aplikacji .NET Core istniejące dane w bazie danych SQL nie zostaną utracone. Ponadto migracje Entity Framework Core zmieniają tylko schemat danych i pozostawiają istniejące dane bez zmian.

## <a name="stream-diagnostic-logs"></a>Przesyłanie strumieniowe dzienników diagnostycznych

Przykładowy projekt jest już zgodny ze wskazówkami dostępnymi w [opisie rejestrowania platformy ASP.NET Core na platformie Azure](https://docs.microsoft.com/aspnet/core/fundamentals/logging#azure-app-service-provider) z dwiema zmianami konfiguracji:

- Zawiera odwołanie do `Microsoft.Extensions.Logging.AzureAppServices` w pliku *DotNetCoreSqlDb.csproj*.
- Wywołuje `loggerFactory.AddAzureWebAppDiagnostics()` w pliku *Startup.cs*.

> [!NOTE]
> Poziom rejestrowania projektu jest ustawiony na `Information` w pliku *appsettings.json*.
>

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Aby uzyskać więcej informacji na temat dostosowywania dzienników platformy ASP.NET Core, zobacz [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging) (Rejestrowanie na platformie ASP.NET Core).

## <a name="manage-your-azure-app"></a>Zarządzanie aplikacją platformy Azure

Przejdź do witryny [Azure Portal](https://portal.azure.com), aby wyświetlić utworzoną aplikację.

W menu po lewej stronie kliknij pozycję **App Services**, a następnie kliknij nazwę swojej aplikacji na platformie Azure.

![Nawigacja w portalu do aplikacji platformy Azure](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

Domyślnie portal pokazuje stronę **Przegląd** aplikacji. Ta strona udostępnia widok sposobu działania aplikacji. Tutaj możesz również wykonywać podstawowe zadania zarządzania, takie jak przeglądanie, zatrzymywanie, uruchamianie, ponowne uruchamianie i usuwanie. Na kartach po lewej stronie strony są pokazane poszczególne strony konfiguracji, które można otworzyć.

![Strona usługi App Service w witrynie Azure Portal](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

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
> [Samouczek: Mapowanie niestandardowej nazwy DNS do aplikacji](../app-service-web-tutorial-custom-domain.md)

Możesz też zapoznać się z innymi zasobami:

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji ASP.NET Core](configure-language-dotnetcore.md)
