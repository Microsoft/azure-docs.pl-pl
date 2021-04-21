---
title: 'Samouczek: uzyskiwanie dostępu do danych przy użyciu tożsamości zarządzanej'
description: Dowiedz się, jak zapewnić bezpieczniejszą łączność z bazą danych przy użyciu tożsamości zarządzanej, a także jak zastosować ją do innych usług platformy Azure.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: 0012c3d208998786ef5aa34320f3bccc4e51ebe6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782787"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Samouczek: zabezpieczanie połączenia usługi Azure SQL Database z usługi App Service za pomocą tożsamości zarządzanej

Usługa [App Service](overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie na platformie Azure. Zapewnia także [tożsamość zarządzaną](overview-managed-identity.md) dla aplikacji, czyli gotowe rozwiązanie do zabezpieczania dostępu do usługi [Azure SQL Database](/azure/sql-database/) i innych usług platformy Azure. Tożsamości zarządzane w usłudze App Service zwiększają bezpieczeństwo aplikacji przez wyeliminowanie wpisów tajnych z aplikacji, takich jak poświadczenia w parametrach połączenia. W tym samouczku dodasz tożsamość zarządzaną do przykładowej aplikacji internetowej, która została sbudowaną w jednym z następujących samouczków: 

- [Samouczek: tworzenie aplikacji ASP.NET na platformie Azure przy użyciu Azure SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Samouczek: tworzenie aplikacji ASP.NET Core i Azure SQL Database w Azure App Service](tutorial-dotnetcore-sqldb-app.md)

Po zakończeniu Twoja przykładowa aplikacja będzie bezpiecznie łączyć się z usługą SQL Database bez konieczności podawania nazwy użytkownika i hasła.

> [!NOTE]
> Kroki opisane w tym samouczku obsługują następujące wersje:
> 
> - .NET Framework 4.7.2 i więcej
> - .NET Core 2.2 i więcej
>

Czego się nauczysz:

> [!div class="checklist"]
> * Włączanie tożsamości zarządzanych
> * Udzielanie usłudze SQL Database dostępu do tożsamości zarządzanej
> * Konfigurowanie Entity Framework do używania uwierzytelniania usługi Azure AD z usługą SQL Database
> * Nawiązywanie połączenia z SQL Database z Visual Studio przy użyciu uwierzytelniania usługi Azure AD

> [!NOTE]
>Uwierzytelnianie usługi Azure AD _różni się od_ zintegrowanego [uwierzytelniania systemu Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) w lokalna usługa Active Directory (AD DS). AD DS i Azure AD używają całkowicie różnych protokołów uwierzytelniania. Aby uzyskać więcej informacji, zobacz [dokumentację usług Azure AD Domain Services](../active-directory-domain-services/index.yml).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł jest kontynuacją samouczka [Samouczek:](app-service-web-tutorial-dotnet-sqldatabase.md) tworzenie aplikacji usługi ASP.NET na platformie Azure za pomocą usługi SQL Database lub Samouczek: tworzenie aplikacji ASP.NET Core i SQL Database na platformie [Azure App Service.](tutorial-dotnetcore-sqldb-app.md) Jeśli jeszcze tego nie zdąsuj, najpierw wykonaj czynności z jednego z dwóch samouczków. Możesz też dostosować kroki do własnej aplikacji .NET za pomocą SQL Database.

Aby debugować aplikację przy użyciu SQL Database jako zakańca, upewnij się, że masz dozwolone połączenie klienta z komputera. W innym przypadku dodaj adres IP klienta, korzystając z procedury opisanej w tece Manage [server-level IP firewall rules using the Azure Portal](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)( Zarządzanie regułami zapory adresów IP na poziomie serwera przy użyciu Azure Portal .

Przygotowywanie środowiska dla interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Udzielanie dostępu do bazy danych użytkownikowi usługi Azure AD

Najpierw włącz uwierzytelnianie usługi Azure AD SQL Database, przypisując użytkownika usługi Azure AD jako administratora usługi Active Directory serwera. Ten użytkownik różni się od konto Microsoft użytego do rejestracji w celu subskrypcji platformy Azure. Musi to być użytkownik, który został utworzony, zaimportowany, zsynchronizowany lub zaproszony do usługi Azure AD. Aby uzyskać więcej informacji na temat dozwolonych użytkowników usługi Azure AD, zobacz Funkcje i ograniczenia usługi [Azure AD SQL Database](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

Jeśli dzierżawa usługi Azure AD nie ma jeszcze użytkownika, utwórz go, korzystając z instrukcji z tematu Dodawanie lub usuwanie użytkowników przy użyciu Azure Active Directory [.](../active-directory/fundamentals/add-users-azure-active-directory.md)

Znajdź identyfikator obiektu użytkownika usługi Azure AD przy użyciu i [`az ad user list`](/cli/azure/ad/user#az_ad_user_list) zastąp . *\<user-principal-name>* Wynik jest zapisywany w zmiennej.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Aby wyświetlić listę wszystkich głównych nazw użytkowników w usłudze Azure AD, `az ad user list --query [].userPrincipalName` uruchom .
>

Dodaj tego użytkownika usługi Azure AD jako administratora usługi Active Directory [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) za pomocą polecenia w Cloud Shell. W poniższym poleceniu zastąp *\<server-name>* nazwą serwera (bez sufiksu). `.database.windows.net`

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Aby uzyskać więcej informacji na temat dodawania administratora usługi Active Directory, zobacz [Provision an Azure Active Directory administrator for your server](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)

## <a name="set-up-visual-studio"></a>Konfigurowanie programu Visual Studio

### <a name="windows-client"></a>Klient systemu Windows
Visual Studio dla systemu Windows jest zintegrowany z uwierzytelnianiem usługi Azure AD. Aby włączyć tworzenie i debugowanie w usłudze Visual Studio, dodaj użytkownika usługi Azure AD w usłudze Visual Studio wybierając z menu pozycję Ustawienia konta pliku, a następnie  >   kliknij pozycję **Dodaj konto.**

Aby ustawić użytkownika usługi Azure AD dla uwierzytelniania usługi platformy Azure, wybierz pozycję Narzędzia Opcje z menu, a następnie wybierz pozycję Wybór konta uwierzytelniania  >   **usługi platformy Azure.**  >   Wybierz dodanego użytkownika usługi Azure AD i kliknij przycisk **OK.**

Teraz możesz tworzyć i debugować aplikację przy użyciu SQL Database jako SQL Database przy użyciu uwierzytelniania usługi Azure AD.

### <a name="macos-client"></a>Klient systemu macOS

Visual Studio dla komputerów Mac nie jest zintegrowany z uwierzytelnianiem usługi Azure AD. Jednak biblioteka [Microsoft.Azure.Services.AppAuthentication,](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) która będzie później używać tokenów, może używać tokenów z interfejsu wiersza polecenia platformy Azure. Aby włączyć tworzenie i debugowanie w Visual Studio, najpierw musisz zainstalować interfejs wiersza polecenia platformy [Azure](/cli/azure/install-azure-cli) na komputerze lokalnym.

Po zainstalowaniu interfejsu wiersza polecenia platformy Azure na komputerze lokalnym zaloguj się do interfejsu wiersza polecenia platformy Azure za pomocą następującego polecenia przy użyciu użytkownika usługi Azure AD:

```azurecli
az login --allow-no-subscriptions
```
Teraz możesz tworzyć i debugować aplikację przy użyciu SQL Database jako serwera końcowego przy użyciu uwierzytelniania usługi Azure AD.

## <a name="modify-your-project"></a>Modyfikowanie projektu

Kroki, które należy wykonać dla projektu, zależą od tego, czy jest to projekt ASP.NET czy ASP.NET Core.

- [Modyfikowanie ASP.NET](#modify-aspnet)
- [Modyfikowanie ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Modyfikowanie ASP.NET

W programie Visual Studio otwórz konsolę menedżera pakietów, a następnie dodaj pakiet NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

W *Web.config*, praca z góry pliku i wprowadzić następujące zmiany:

- W `<configSections>` programie dodaj następującą deklarację sekcji:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- poniżej tagu `</configSections>` zamykającego dodaj następujący kod XML dla `<SqlAuthenticationProviders>` elementu .

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Znajdź ciąg połączenia o nazwie `MyDbConnection` i zastąp jego wartość `connectionString` ciągiem `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"` . Zastąp _\<server-name>_ i nazwą serwera i nazwą bazy _\<db-name>_ danych.

> [!NOTE]
> Właśnie zarejestrowany pomocnik SqlAuthenticationProvider jest oparty na zainstalowanej wcześniej bibliotece AppAuthentication. Domyślnie używa ona tożsamości przypisanej przez system. Aby korzystać z tożsamości przypisanej przez użytkownika, należy podać dodatkową konfigurację. Zobacz [obsługa parametrów połączenia dla](/dotnet/api/overview/azure/service-to-service-authentication#connection-string-support) biblioteki AppAuthentication.

To wszystko, czego potrzebujesz, aby nawiązać połączenie z SQL Database. Podczas debugowania w Visual Studio kod używa użytkownika usługi Azure AD skonfigurowanego w te [Visual Studio.](#set-up-visual-studio) Później skonfigurujesz usługę SQL Database, aby zezwolić na połączenie z tożsamości zarządzanej aplikacji App Service aplikacji.

Wpisz `Ctrl+F5` , aby ponownie uruchomić aplikację. Ta sama aplikacja CRUD w przeglądarce łączy się teraz z usługą Azure SQL Database przy użyciu uwierzytelniania usługi Azure AD. Ta konfiguracja umożliwia uruchamianie migracji baz danych z Visual Studio.

### <a name="modify-aspnet-core"></a>Modyfikowanie ASP.NET Core

W programie Visual Studio otwórz konsolę menedżera pakietów, a następnie dodaj pakiet NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

W [samouczku ASP.NET Core i SQL Database](tutorial-dotnetcore-sqldb-app.md)parametrów połączenia nie są w ogóle używane, ponieważ lokalne środowisko projektowe używa pliku bazy danych Sqlite, a środowisko produkcyjne platformy Azure używa parametrów połączenia z `MyDbConnection` App Service. W przypadku uwierzytelniania usługi Active Directory oba środowiska mają używać tych samych parametrów połączenia. W *appsettings.jsw* programie zastąp wartość parametrów `MyDbConnection` połączenia:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Następnie należy podać kontekst Entity Framework bazy danych tokenem dostępu dla SQL Database. W *folderze Data\MyDatabaseContext.cs* dodaj następujący kod w nawiasach klamrowych pustego `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` konstruktora:

```csharp
var connection = (SqlConnection)Database.GetDbConnection();
connection.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Ten kod demonstracyjny jest synchroniczny dla przejrzystości i prostoty.

To wszystko, czego potrzebujesz, aby nawiązać połączenie z SQL Database. Podczas debugowania w Visual Studio kod używa użytkownika usługi Azure AD skonfigurowanego w te Visual Studio [.](#set-up-visual-studio) Później skonfigurujesz usługę SQL Database, aby zezwolić na połączenie z tożsamości zarządzanej aplikacji App Service aplikacji. Klasa buforuje token w pamięci i pobiera go z `AzureServiceTokenProvider` usługi Azure AD tuż przed wygaśnięciem. Do odświeżenia tokenu nie jest potrzebny żaden kod niestandardowy.

> [!TIP]
> Jeśli skonfigurowany użytkownik usługi Azure AD ma dostęp do wielu dzierżaw, wywołaj wywołanie z żądanym identyfikatorem dzierżawy, aby pobrać `GetAccessTokenAsync("https://database.windows.net/", tenantid)` odpowiedni token dostępu.

Wpisz `Ctrl+F5` , aby ponownie uruchomić aplikację. Ta sama aplikacja CRUD w przeglądarce łączy się teraz z usługą Azure SQL Database przy użyciu uwierzytelniania usługi Azure AD. Ta konfiguracja umożliwia uruchamianie migracji baz danych z Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Korzystanie z łączności tożsamości zarządzanej

Następnie skonfigurujesz aplikację App Service tak, aby łączyła się z SQL Database przy użyciu tożsamości zarządzanej przypisanej przez system.

> [!NOTE]
> Chociaż instrukcje w tej sekcji są przeznaczone dla tożsamości przypisanej przez system, tożsamość przypisana przez użytkownika może być równie łatwo używana. W tym celu: Aby przypisać żądaną tożsamość `az webapp identity assign command` przypisaną przez użytkownika, należy zmienić identyfikator . Następnie podczas tworzenia użytkownika SQL upewnij się, że używasz nazwy zasobu tożsamości przypisanego przez użytkownika, a nie nazwy witryny.

### <a name="enable-managed-identity-on-app"></a>Włączanie tożsamości zarządzanej w aplikacji

Aby włączyć tożsamość zarządzanej dla aplikacji platformy Azure, użyj polecenia [az webapp identity assign](/cli/azure/webapp/identity#az_webapp_identity_assign) w usłudze Cloud Shell. W poniższym poleceniu *\<app-name>* zastąp .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Poniżej przedstawiono przykładowe dane wyjściowe:

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>Udzielanie uprawnień tożsamości zarządzanej

> [!NOTE]
> Jeśli chcesz, możesz dodać tożsamość do grupy usługi [Azure AD,](../active-directory/fundamentals/active-directory-manage-groups.md)a SQL Database dostęp do grupy usługi Azure AD zamiast tożsamości. Na przykład następujące polecenia dodają tożsamość zarządzaną z poprzedniego kroku do nowej grupy o nazwie _myAzureSQLDBAccessGroup:_
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

W usłudze Cloud Shell zaloguj się do usługi SQL Database przy użyciu polecenia SQLCMD. Zastąp nazwą serwera, nazwą bazy danych używaną przez aplikację, a i nazwą poświadczeń użytkownika usługi _\<server-name>_ _\<db-name>_ Azure _\<aad-user-name>_ _\<aad-password>_ AD.

```bash
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

W wierszu polecenia SQL dla chcieć bazy danych uruchom następujące polecenia, aby udzielić uprawnień wymaganych przez aplikację. Na przykład 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

*\<identity-name>* to nazwa tożsamości zarządzanej w usłudze Azure AD. Jeśli tożsamość jest przypisana przez system, nazwa jest zawsze taka sama jak nazwa App Service aplikacji. Aby udzielić uprawnień grupie usługi Azure AD, użyj jej nazwy wyświetlanej (na przykład *myAzureSQLDBAccessGroup).*

Wpisz polecenie `EXIT`, aby powrócić do wiersza polecenia usługi Cloud Shell.

> [!NOTE]
> Usługi back-end tożsamości zarządzanych również utrzymują pamięć podręczną [tokenów,](overview-managed-identity.md#obtain-tokens-for-azure-resources) która aktualizuje token dla zasobu docelowego tylko wtedy, gdy wygaśnie. Jeśli popełnisz błąd podczas konfigurowania uprawnień usługi SQL Database  i spróbujesz zmodyfikować uprawnienia po próbie uzyskania tokenu za pomocą aplikacji, w rzeczywistości nie otrzymasz nowego tokenu ze zaktualizowanymi uprawnieniami, dopóki token w pamięci podręcznej nie wygaśnie.

> [!NOTE]
> Usługa AAD nie jest obsługiwana w przypadku SQL Server, co obejmuje m.in. interfejsy MSI. 

### <a name="modify-connection-string"></a>Modyfikowanie parametrów połączenia

Pamiętaj, że te same zmiany wprowadzone w usługach *Web.config* lub *appsettings.js* są związane z tożsamością zarządzaną, więc jedyną rzeczą, którą należy wykonać, jest usunięcie istniejących parametrów połączenia w programie App Service, które Visual Studio utworzyły wdrożenie aplikacji po raz pierwszy. Użyj następującego polecenia, ale zastąp *\<app-name>* nazwą swojej aplikacji.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publikowanie zmian

Teraz pozostało tylko opublikowanie zmian na platformie Azure.

**Jeśli pochodzisz z [części Samouczek: tworzenie aplikacji ASP.NET platformie Azure](app-service-web-tutorial-dotnet-sqldatabase.md)** za pomocą SQL Database , opublikuj zmiany w Visual Studio. W obszarze **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **DotNetAppSqlDb** i wybierz polecenie **Publikuj**.

![Publikowanie z Eksploratora rozwiązań](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na stronie publikowania kliknij przycisk **Publikuj**. 

**Jeśli pochodzisz z części [Samouczek:](tutorial-dotnetcore-sqldb-app.md)** tworzenie aplikacji ASP.NET Core i aplikacji SQL Database w usłudze Azure App Service, opublikuj zmiany przy użyciu usługi Git za pomocą następujących poleceń:

```bash
git commit -am "configure managed identity"
git push azure main
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
> * Konfigurowanie Entity Framework do używania uwierzytelniania usługi Azure AD z usługą SQL Database
> * Nawiązywanie połączenia z SQL Database z Visual Studio przy użyciu uwierzytelniania usługi Azure AD

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację internetową.

> [!div class="nextstepaction"]
> [Mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service](app-service-web-tutorial-custom-domain.md)