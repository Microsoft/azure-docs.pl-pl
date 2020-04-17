---
title: 'Samouczek: Dostęp do danych z tożsamością zarządzana'
description: Dowiedz się, jak uczynić łączność z bazą danych bezpieczniejszą przy użyciu tożsamości zarządzanej, a także jak zastosować ją do innych usług platformy Azure.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/18/2019
ms.custom: mvc, cli-validate
ms.openlocfilehash: 787809e4132defa101bb82659e8af1a2d9f1b9b6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453815"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Samouczek: zabezpieczanie połączenia usługi Azure SQL Database z usługi App Service za pomocą tożsamości zarządzanej

Usługa [App Service](overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie na platformie Azure. Zapewnia także [tożsamość zarządzaną](overview-managed-identity.md) dla aplikacji, czyli gotowe rozwiązanie do zabezpieczania dostępu do usługi [Azure SQL Database](/azure/sql-database/) i innych usług platformy Azure. Tożsamości zarządzane w usłudze App Service zwiększają bezpieczeństwo aplikacji przez wyeliminowanie wpisów tajnych z aplikacji, takich jak poświadczenia w parametrach połączenia. W tym samouczku dodasz tożsamość zarządzaną do przykładowej aplikacji sieci web utworzonej w jednym z następujących samouczków: 

- [Samouczek: tworzenie aplikacji ASP.NET na platformie Azure przy użyciu usługi SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Samouczek: Tworzenie aplikacji ASP.NET Core i SQL Database w usłudze Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

Po zakończeniu Twoja przykładowa aplikacja będzie bezpiecznie łączyć się z usługą SQL Database bez konieczności podawania nazwy użytkownika i hasła.

> [!NOTE]
> Kroki opisane w tym samouczku obsługują następujące wersje:
> 
> -  .NET Framework 4.7.2
> - .NET Core 2.2
>

Czego się dowiesz:

> [!div class="checklist"]
> * Włączanie tożsamości zarządzanych
> * Udzielanie usłudze SQL Database dostępu do tożsamości zarządzanej
> * Konfigurowanie programu Entity Framework do używania uwierzytelniania usługi Azure AD z bazą danych SQL
> * Łączenie się z bazą danych SQL z programu Visual Studio przy użyciu uwierzytelniania usługi Azure AD

> [!NOTE]
>Uwierzytelnianie usługi Azure AD _różni się_ od [uwierzytelniania zintegrowanego systemu Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) w lokalnej usłudze Active Directory (AD DS). Usługi AD DS i usługi Azure AD używają zupełnie różnych protokołów uwierzytelniania. Aby uzyskać więcej informacji, zobacz [dokumentację usług Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł jest kontynuowany w miejscu, w którym zostało przerwane w [samouczku: Tworzenie aplikacji ASP.NET na platformie Azure za pomocą bazy danych SQL](app-service-web-tutorial-dotnet-sqldatabase.md) lub [samouczka: Tworzenie aplikacji ASP.NET Core i SQL Database w usłudze Azure App Service.](app-service-web-tutorial-dotnetcore-sqldb.md) Jeśli jeszcze tego nie zrobiłeś, wykonaj najpierw jeden z dwóch samouczków. Alternatywnie można dostosować kroki dla własnej aplikacji .NET z bazy danych SQL.

Aby debugować aplikację przy użyciu bazy danych SQL jako zaplecza, upewnij się, że zezwoliłeś na połączenie klienta z komputera. Jeśli nie, dodaj adres IP klienta, wykonując kroki opisane w [obszarze Zarządzanie regułami zapory IP na poziomie serwera przy użyciu witryny Azure portal](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Udzielanie dostępu do bazy danych użytkownikowi usługi Azure AD

Najpierw włącz uwierzytelnianie usługi Azure AD do bazy danych SQL, przypisując użytkownika usługi Azure AD jako administratora usługi Active Directory serwera bazy danych SQL. Ten użytkownik różni się od konta Microsoft użytego do zarejestrowania się w ramach subskrypcji platformy Azure. Musi to być użytkownik utworzony, zaimportowany, zsynchronizowany lub zaproszony do usługi Azure AD. Aby uzyskać więcej informacji na temat dozwolonych użytkowników usługi Azure AD, zobacz [Funkcje usługi Azure AD i ograniczenia w bazie danych SQL](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Jeśli dzierżawa usługi Azure AD nie ma jeszcze użytkownika, utwórz go, wykonując kroki opisane w [witrynie Dodaj lub usuń użytkowników przy użyciu usługi Azure Active Directory.](../active-directory/fundamentals/add-users-azure-active-directory.md)

Znajdź identyfikator obiektu użytkownika usługi Azure [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) AD przy użyciu i zastąpić * \<>nazwa głównego użytkownika *. Wynik jest zapisywany w zmiennej.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Aby wyświetlić listę wszystkich głównych nazw użytkowników `az ad user list --query [].userPrincipalName`w usłudze Azure AD, uruchom program .
>

Dodaj tego użytkownika usługi Azure AD [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) jako administratora usługi Active Directory za pomocą polecenia w aplikacji Cloud Shell. W poniższym poleceniu zastąp `.database.windows.net` * \<nazwę serwera>* nazwą serwera bazy danych SQL (bez sufiksu).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Aby uzyskać więcej informacji na temat dodawania administratora usługi Active Directory, zobacz [Aprowizuj administratora usługi Azure Active Directory dla serwera bazy danych SQL Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Konfigurowanie programu Visual Studio

### <a name="windows"></a>Windows
Visual Studio dla systemu Windows jest zintegrowany z uwierzytelnianiem usługi Azure AD. Aby włączyć tworzenie i debugowanie w programie Visual Studio, dodaj użytkownika usługi Azure AD w programie Visual Studio, wybierając z menu**pozycję Ustawienia konta** **plików,** > a następnie kliknij przycisk **Dodaj konto**.

Aby ustawić użytkownika usługi Azure AD do uwierzytelniania usługi Azure, wybierz z menu**opcję Opcje** **narzędzi,** > a następnie wybierz**pozycję Wybór konta** **uwierzytelniania** > usługi Azure . Wybierz dodanego użytkownika usługi Azure AD i kliknij przycisk **OK**.

Teraz możesz przystąpić do tworzenia i debugowania aplikacji z bazą danych SQL jako zaplecza przy użyciu uwierzytelniania usługi Azure AD.

### <a name="macos"></a>MacOS

Visual Studio dla komputerów Mac nie jest zintegrowany z uwierzytelnianiem usługi Azure AD. Jednak biblioteka [microsoft.Azure.Services.AppAuthentication,](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) która będzie używana później, może używać tokenów z interfejsu wiersza polecenia platformy Azure. Aby włączyć programowanie i debugowanie w programie Visual Studio, najpierw należy [zainstalować narzędzie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) na komputerze lokalnym.

Po zainstalowaniu interfejsu wiersza polecenia platformy Azure na komputerze lokalnym zaloguj się do interfejsu wiersza polecenia platformy Azure za pomocą następującego polecenia przy użyciu użytkownika usługi Azure AD:

```bash
az login --allow-no-subscriptions
```
Teraz możesz przystąpić do tworzenia i debugowania aplikacji z bazą danych SQL jako zaplecza przy użyciu uwierzytelniania usługi Azure AD.

## <a name="modify-your-project"></a>Modyfikowanie projektu

Kroki, które należy wykonać dla projektu zależy od tego, czy jest to projekt ASP.NET lub ASP.NET core projektu.

- [Modyfikowanie ASP.NET](#modify-aspnet)
- [Modyfikowanie rdzenia ASP.NET](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Modyfikowanie ASP.NET

W programie Visual Studio otwórz konsolę menedżera pakietów, a następnie dodaj pakiet NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.1
```

W *witrynie Web.config*działa od góry pliku i wprowadzaj następujące zmiany:

- W `<configSections>`, dodaj w nim następującą deklarację sekcji:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- poniżej `</configSections>` tagu zamykającego dodaj następujący `<SqlAuthenticationProviders>`kod XML dla .

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Znajdź wywołany `MyDbConnection` ciąg połączenia `connectionString` i `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`zastąp jego wartość . Zastąp _ \<nazwę serwera>_ i _ \<>nazwy db_ nazwą serwera i nazwą bazy danych.

> [!NOTE]
> SqlAuthenticationProvider właśnie zarejestrowany jest oparty na górze biblioteki AppAuthentication zainstalowanej wcześniej. Domyślnie używa tożsamości przypisanej do systemu. Aby wykorzystać tożsamość przypisaną przez użytkownika, należy podać dodatkową konfigurację. Zobacz [obsługę ciągu połączenia](../key-vault/general/service-to-service-authentication.md#connection-string-support) dla biblioteki AppAuthentication.

To wszystko, czego potrzebujesz, aby połączyć się z bazą danych SQL. Podczas debugowania w programie Visual Studio, kod używa użytkownika usługi Azure AD skonfigurowany w [programie Set up Visual Studio](#set-up-visual-studio). Później skonfigurujesz serwer bazy danych SQL, aby zezwolić na połączenie z tożsamości zarządzanej aplikacji usługi App Service.

Wpisz, `Ctrl+F5` aby ponownie uruchomić aplikację. Ta sama aplikacja CRUD w przeglądarce łączy się teraz bezpośrednio z usługą Azure SQL Database przy użyciu uwierzytelniania usługi Azure AD. Ta konfiguracja umożliwia uruchamianie migracji bazy danych z programu Visual Studio.

### <a name="modify-aspnet-core"></a>Modyfikowanie rdzenia ASP.NET

W programie Visual Studio otwórz konsolę menedżera pakietów, a następnie dodaj pakiet NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.1
```

W [samouczku ASP.NET Core i SQL](app-service-web-tutorial-dotnetcore-sqldb.md) `MyDbConnection` Database parametry połączenia nie są używane, ponieważ lokalne środowisko programistyczne używa pliku bazy danych Sqlite, a środowisko produkcyjne platformy Azure używa ciągu połączenia z usługi App Service. W przypadku uwierzytelniania usługi Active Directory oba środowiska mają używać tego samego ciągu połączenia. W *pliku appsettings.json*wartość `MyDbConnection` połączenia należy zastąpić:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

W *Startup.cs*usuń sekcję kodu dodaną przed:

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

I zastąpić go następującym kodem:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Następnie należy podać kontekst bazy danych entity framework z tokenem dostępu dla bazy danych SQL. W *pliku Data\MyDatabaseContext.cs*dodaj następujący kod wewnątrz nawiasów `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` klamrowych pustego konstruktora:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Ten kod demonstracyjny jest synchroniczne dla jasności i prostoty.

To wszystko, czego potrzebujesz, aby połączyć się z bazą danych SQL. Podczas debugowania w programie Visual Studio, kod używa użytkownika usługi Azure AD skonfigurowany w [programie Set up Visual Studio](#set-up-visual-studio). Później skonfigurujesz serwer bazy danych SQL, aby zezwolić na połączenie z tożsamości zarządzanej aplikacji usługi App Service. Klasa `AzureServiceTokenProvider` buforuje token w pamięci i pobiera go z usługi Azure AD tuż przed wygaśnięciem. Nie potrzebujesz żadnego kodu niestandardowego, aby odświeżyć token.

> [!TIP]
> Jeśli skonfigurowany użytkownik usługi Azure AD ma dostęp `GetAccessTokenAsync("https://database.windows.net/", tenantid)` do wielu dzierżaw, wywołanie z żądanym identyfikatorem dzierżawy, aby pobrać token dostępu właściwego.

Wpisz, `Ctrl+F5` aby ponownie uruchomić aplikację. Ta sama aplikacja CRUD w przeglądarce łączy się teraz bezpośrednio z usługą Azure SQL Database przy użyciu uwierzytelniania usługi Azure AD. Ta konfiguracja umożliwia uruchamianie migracji bazy danych z programu Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Korzystanie z łączności tożsamości zarządzanej

Następnie skonfiguruj aplikację usługi App Service, aby łączyć się z bazą danych SQL za pomocą tożsamości zarządzanej przypisanej przez system.

> [!NOTE]
> Podczas gdy instrukcje w tej sekcji są dla tożsamości przypisanej do systemu, tożsamość przypisana przez użytkownika może równie łatwo być używane. W tym celu: trzeba będzie zmienić, `az webapp identity assign command` aby przypisać żądaną tożsamość przypisaną przez użytkownika. Następnie podczas tworzenia użytkownika SQL, upewnij się, aby użyć nazwy zasobu tożsamości przypisane przez użytkownika, a nie nazwę witryny.

### <a name="enable-managed-identity-on-app"></a>Włączanie tożsamości zarządzanej w aplikacji

Aby włączyć tożsamość zarządzanej dla aplikacji platformy Azure, użyj polecenia [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) w usłudze Cloud Shell. W poniższym poleceniu zastąp * \<nazwę aplikacji>*.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Oto przykład danych wyjściowych:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="grant-permissions-to-managed-identity"></a>Udziel uprawnień do tożsamości zarządzanej

> [!NOTE]
> Jeśli chcesz, możesz dodać tożsamość do [grupy usługi Azure AD,](../active-directory/fundamentals/active-directory-manage-groups.md)a następnie udzielić dostępu do bazy danych SQL do grupy usługi Azure AD zamiast tożsamości. Na przykład następujące polecenia dodają tożsamość zarządzaną z poprzedniego kroku do nowej grupy o nazwie _myAzureSQLDBAccessGroup:_
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

W usłudze Cloud Shell zaloguj się do usługi SQL Database przy użyciu polecenia SQLCMD. Zastąp _ \<nazwę serwera>_ nazwą serwera bazy danych SQL, _ \<nazwą bazy danych>_ nazwą bazy danych z nazwą bazy danych używaną przez aplikację oraz _ \<>nazwy użytkownika aad_ i _ \<hasła aad>_ poświadczeniami użytkownika usługi Azure AD.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

W wierszu SQL dla bazy danych, które chcesz, uruchom następujące polecenia, aby dodać grupę usługi Azure AD i udzielić uprawnień, których potrzebuje aplikacja. Na przykład: 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

nazwa tożsamości>to nazwa tożsamości zarządzanej w usłudze Azure AD. * \<* Jeśli tożsamość jest przypisana do systemu, nazwa jest zawsze taka sama jak nazwa aplikacji usługi App Service. Aby udzielić uprawnień dla grupy usługi Azure AD, użyj nazwy wyświetlanej grupy (na przykład *myAzureSQLDBAccessGroup*).

Wpisz polecenie `EXIT`, aby powrócić do wiersza polecenia usługi Cloud Shell.

> [!NOTE]
> Usługi zaplecza tożsamości zarządzanych [przechowuje również pamięć podręczną tokenu,](overview-managed-identity.md#obtain-tokens-for-azure-resources) który aktualizuje token dla zasobu docelowego tylko wtedy, gdy wygaśnie. Jeśli popełnisz błąd, konfigurując uprawnienia bazy danych SQL i spróbuj zmodyfikować uprawnienia *po* próbie uzyskania tokenu za pomocą aplikacji, w rzeczywistości nie otrzymasz nowego tokenu ze zaktualizowanymi uprawnieniami, dopóki token buforowany nie wygaśnie.

### <a name="modify-connection-string"></a>Modyfikowanie parametrów połączenia

Należy pamiętać, że te same zmiany wprowadzone w *witrynie Web.config* lub *appsettings.json* współpracuje z tożsamością zarządzaną, więc jedyną rzeczą do zrobienia jest usunięcie istniejącego ciągu połączenia w usłudze App Service, który visual studio utworzone wdrażanie aplikacji po raz pierwszy. Użyj następującego polecenia, ale * \<zastąp nazwę aplikacji>* nazwą aplikacji.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publikowanie zmian

Teraz pozostało tylko opublikowanie zmian na platformie Azure.

**Jeśli pochodzisz z [samouczka: Tworzenie aplikacji ASP.NET na platformie Azure z bazą danych SQL](app-service-web-tutorial-dotnet-sqldatabase.md)**, opublikuj zmiany w programie Visual Studio. W obszarze **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **DotNetAppSqlDb** i wybierz polecenie **Publikuj**.

![Publikowanie z Eksploratora rozwiązań](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na stronie publikowania kliknij przycisk **Publikuj**. 

**Jeśli pochodzisz z [samouczka: Tworzenie ASP.NET aplikacji Core i SQL Database w usłudze Azure App Service,](app-service-web-tutorial-dotnetcore-sqldb.md)** opublikuj zmiany za pomocą gita, za pomocą następujących poleceń:

```bash
git commit -am "configure managed identity"
git push azure master
```

Gdy nowa strona internetowa wyświetla listę zadań do wykonania, aplikacja nawiązuje połączenie z bazą danych za pomocą tożsamości zarządzanej.

![Aplikacja platformy Azure po zakończeniu migracji Code First](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Teraz powinno być możliwe edytowanie listy zadań do wykonania tak jak wcześniej.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

Które czynności umiesz wykonać:

> [!div class="checklist"]
> * Włączanie tożsamości zarządzanych
> * Udzielanie usłudze SQL Database dostępu do tożsamości zarządzanej
> * Konfigurowanie programu Entity Framework do używania uwierzytelniania usługi Azure AD z bazą danych SQL
> * Łączenie się z bazą danych SQL z programu Visual Studio przy użyciu uwierzytelniania usługi Azure AD

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację internetową.

> [!div class="nextstepaction"]
> [Mapowanie istniejącej niestandardowej nazwy DNS na usługę Azure App Service](app-service-web-tutorial-custom-domain.md)
