---
title: 'Samouczek: uzyskiwanie dostępu do danych za pomocą tożsamości zarządzanej'
description: Dowiedz się, jak zwiększyć bezpieczeństwo łączności z bazą danych przy użyciu tożsamości zarządzanej, a także jak zastosować ją do innych usług platformy Azure.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: 2c19ee2b8e7ec3c695b2c76c46402c118c559b40
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736241"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Samouczek: zabezpieczanie połączenia usługi Azure SQL Database z usługi App Service za pomocą tożsamości zarządzanej

Usługa [App Service](overview.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie na platformie Azure. Zapewnia także [tożsamość zarządzaną](overview-managed-identity.md) dla aplikacji, czyli gotowe rozwiązanie do zabezpieczania dostępu do usługi [Azure SQL Database](/azure/sql-database/) i innych usług platformy Azure. Tożsamości zarządzane w usłudze App Service zwiększają bezpieczeństwo aplikacji przez wyeliminowanie wpisów tajnych z aplikacji, takich jak poświadczenia w parametrach połączenia. W tym samouczku dodasz tożsamość zarządzaną do przykładowej aplikacji internetowej skompilowanej w jednym z następujących samouczków: 

- [Samouczek: Tworzenie aplikacji ASP.NET na platformie Azure za pomocą Azure SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Samouczek: Tworzenie aplikacji ASP.NET Core i Azure SQL Database w Azure App Service](tutorial-dotnetcore-sqldb-app.md)

Po zakończeniu Twoja przykładowa aplikacja będzie bezpiecznie łączyć się z usługą SQL Database bez konieczności podawania nazwy użytkownika i hasła.

> [!NOTE]
> Kroki opisane w tym samouczku obsługują następujące wersje:
> 
> - .NET Framework 4.7.2 i nowsze
> - .NET Core 2,2 i nowsze
>

Co uzyskasz:

> [!div class="checklist"]
> * Włączanie tożsamości zarządzanych
> * Udzielanie usłudze SQL Database dostępu do tożsamości zarządzanej
> * Konfigurowanie Entity Framework do korzystania z uwierzytelniania usługi Azure AD z usługą SQL Database
> * Nawiązywanie połączenia z SQL Database z programu Visual Studio przy użyciu uwierzytelniania usługi Azure AD

> [!NOTE]
>Uwierzytelnianie usługi Azure AD _różni_ się od [zintegrowanego uwierzytelniania systemu Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) w Active Directory lokalnym (AD DS). AD DS i usługa Azure AD używają całkowicie różnych protokołów uwierzytelniania. Aby uzyskać więcej informacji, zobacz [dokumentację usług Azure AD Domain Services](../active-directory-domain-services/index.yml).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł kontynuuje pracę w miejscu pracy w [samouczku: Tworzenie aplikacji ASP.NET na platformie Azure za pomocą SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) lub [samouczek: Tworzenie ASP.NET Core i aplikacji SQL Database w Azure App Service](tutorial-dotnetcore-sqldb-app.md). Jeśli jeszcze tego nie zrobiono, należy najpierw wykonać jeden z dwóch samouczków. Alternatywnie możesz dostosować kroki dla własnej aplikacji .NET za pomocą SQL Database.

Aby debugować aplikację przy użyciu SQL Database jako zaplecza, upewnij się, że z komputera zezwolono na połączenie z klientem. W przeciwnym razie Dodaj adres IP klienta, wykonując czynności opisane w sekcji [Zarządzanie regułami zapory adresów IP na poziomie serwera przy użyciu Azure Portal](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

Przygotuj środowisko dla interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Udzielanie dostępu do bazy danych użytkownikowi usługi Azure AD

Najpierw należy włączyć uwierzytelnianie usługi Azure AD, aby SQL Database, przypisując użytkownika usługi Azure AD jako administratora Active Directory serwera. Ten użytkownik różni się od konto Microsoft użytego do zarejestrowania się w ramach subskrypcji platformy Azure. Musi to być użytkownik, który został utworzony, zaimportowany, zsynchronizowany lub zaproszony do usługi Azure AD. Aby uzyskać więcej informacji na temat dozwolonych użytkowników usługi Azure AD, zobacz temat [funkcje i ograniczenia usługi Azure AD w SQL Database](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

Jeśli dzierżawa usługi Azure AD nie ma jeszcze użytkownika, utwórz ją, wykonując czynności opisane w sekcji [Dodawanie lub usuwanie użytkowników przy użyciu Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Znajdź identyfikator obiektu użytkownika usługi Azure AD przy użyciu [`az ad user list`](/cli/azure/ad/user#az-ad-user-list) i Zamień *\<user-principal-name>* . Wynik jest zapisywany w zmiennej.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Aby wyświetlić listę wszystkich głównych nazw użytkowników w usłudze Azure AD, uruchom polecenie `az ad user list --query [].userPrincipalName` .
>

Dodaj tego użytkownika usługi Azure AD jako administratora Active Directory za pomocą [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) polecenia w Cloud Shell. W poniższym poleceniu Zastąp *\<server-name>* wartość nazwą serwera (bez `.database.windows.net` sufiksu).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Aby uzyskać więcej informacji na temat dodawania administratora Active Directory, zapoznaj się z tematem [udostępnianie Azure Active Directory administratorem serwera](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)

## <a name="set-up-visual-studio"></a>Konfigurowanie programu Visual Studio

### <a name="windows-client"></a>Klient systemu Windows
Program Visual Studio dla systemu Windows jest zintegrowany z uwierzytelnianiem w usłudze Azure AD. Aby włączyć programowanie i debugowanie w programie Visual Studio, Dodaj użytkownika usługi Azure AD w programie Visual Studio , wybierając  >  z menu pozycję **Ustawienia konta** plików, a następnie kliknij pozycję **Dodaj konto**.

Aby ustawić użytkownika usługi Azure AD na potrzeby uwierzytelniania usługi platformy Azure , wybierz  >  **Opcje** narzędzia z menu, a następnie wybierz pozycję wybór konta **uwierzytelniania usługi platformy Azure**  >  . Wybierz dodanego użytkownika usługi Azure AD, a następnie kliknij przycisk **OK**.

Teraz możesz przystąpić do tworzenia i debugowania aplikacji za pomocą SQL Database jako zaplecza przy użyciu uwierzytelniania usługi Azure AD.

### <a name="macos-client"></a>Klient systemu macOS

Visual Studio dla komputerów Mac nie jest zintegrowana z uwierzytelnianiem w usłudze Azure AD. Jednak biblioteka [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) , która będzie używana później, może używać tokenów z interfejsu wiersza polecenia platformy Azure. Aby włączyć programowanie i debugowanie w programie Visual Studio, najpierw musisz [zainstalować interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) na komputerze lokalnym.

Po zainstalowaniu interfejsu wiersza polecenia platformy Azure na komputerze lokalnym Zaloguj się w interfejsie wiersza polecenia platformy Azure przy użyciu następujące polecenie za pomocą użytkownika usługi Azure AD:

```azurecli
az login --allow-no-subscriptions
```
Teraz możesz przystąpić do tworzenia i debugowania aplikacji za pomocą SQL Database jako zaplecza przy użyciu uwierzytelniania usługi Azure AD.

## <a name="modify-your-project"></a>Modyfikowanie projektu

Kroki opisane w projekcie zależą od tego, czy jest to projekt ASP.NET czy projekt ASP.NET Core.

- [Modyfikuj ASP.NET](#modify-aspnet)
- [Modyfikuj ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Modyfikuj ASP.NET

W programie Visual Studio otwórz konsolę menedżera pakietów, a następnie dodaj pakiet NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

W *Web.config*, praca z góry pliku i wprowadzanie następujących zmian:

- W programie `<configSections>` Dodaj następującą deklarację sekcji:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- pod tagiem zamykającym `</configSections>` Dodaj następujący kod XML dla `<SqlAuthenticationProviders>` .

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Znajdź parametry połączenia o nazwie `MyDbConnection` i Zastąp ją `connectionString` wartością `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"` . Zamień _\<server-name>_ _\<db-name>_ na nazwę serwera i nazwę bazy danych.

> [!NOTE]
> SqlAuthenticationProvider zarejestrowano na podstawie zainstalowanej wcześniej biblioteki AppAuthentication. Domyślnie używa ona tożsamości przypisanej do systemu. Aby skorzystać z tożsamości przypisanej do użytkownika, należy podać dodatkową konfigurację. Zobacz [obsługę parametrów połączenia](/dotnet/api/overview/azure/service-to-service-authentication#connection-string-support) dla biblioteki AppAuthentication.

Jest to każda czynność, którą należy połączyć z SQL Database. Podczas debugowania w programie Visual Studio kod używa użytkownika usługi Azure AD, który został skonfigurowany w konfiguracji programu [Visual Studio](#set-up-visual-studio). Skonfigurujesz SQL Database później, aby umożliwić połączenie z tożsamości zarządzanej aplikacji App Service.

Wpisz `Ctrl+F5` ponownie, aby uruchomić aplikację. Ta sama aplikacja CRUD w przeglądarce jest teraz łącząca się bezpośrednio z Azure SQL Database przy użyciu uwierzytelniania usługi Azure AD. Ta konfiguracja pozwala uruchamiać migracje baz danych z programu Visual Studio.

### <a name="modify-aspnet-core"></a>Modyfikuj ASP.NET Core

W programie Visual Studio otwórz konsolę menedżera pakietów, a następnie dodaj pakiet NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

W [samouczku ASP.NET Core i SQL Database](tutorial-dotnetcore-sqldb-app.md) `MyDbConnection` Parametry połączenia nie są używane, ponieważ w lokalnym środowisku programistycznym jest używany plik bazy danych programu SQLite, a w środowisku produkcyjnym platformy Azure używane są parametry połączenia z App Service. W przypadku uwierzytelniania Active Directory w obu środowiskach należy używać tych samych parametrów połączenia. W *appsettings.json*, Zastąp wartość `MyDbConnection` parametrów połączenia:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Następnie podaj kontekst bazy danych Entity Framework z tokenem dostępu dla SQL Database. W *Data\MyDatabaseContext.cs* Dodaj następujący kod wewnątrz nawiasów klamrowych pustego `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` konstruktora:

```csharp
var connection = (SqlConnection)Database.GetDbConnection();
connection.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Ten kod demonstracyjny jest synchroniczny dla jasności i prostoty.

Jest to każda czynność, którą należy połączyć z SQL Database. Podczas debugowania w programie Visual Studio kod używa użytkownika usługi Azure AD, który został skonfigurowany w konfiguracji programu [Visual Studio](#set-up-visual-studio). Skonfigurujesz SQL Database później, aby umożliwić połączenie z tożsamości zarządzanej aplikacji App Service. `AzureServiceTokenProvider`Klasa buforuje token w pamięci i pobiera go z usługi Azure AD tuż przed wygaśnięciem. Nie jest potrzebny żaden kod niestandardowy w celu odświeżenia tokenu.

> [!TIP]
> Jeśli skonfigurowany użytkownik usługi Azure AD ma dostęp do wielu dzierżawców, Połącz `GetAccessTokenAsync("https://database.windows.net/", tenantid)` się z żądanym identyfikatorem dzierżawy w celu pobrania odpowiedniego tokenu dostępu.

Wpisz `Ctrl+F5` ponownie, aby uruchomić aplikację. Ta sama aplikacja CRUD w przeglądarce jest teraz łącząca się bezpośrednio z Azure SQL Database przy użyciu uwierzytelniania usługi Azure AD. Ta konfiguracja pozwala uruchamiać migracje baz danych z programu Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Korzystanie z łączności tożsamości zarządzanej

Następnie skonfigurujesz aplikację App Service, aby połączyć się z SQL Database przy użyciu tożsamości zarządzanej przypisanej do systemu.

> [!NOTE]
> Chociaż instrukcje w tej sekcji dotyczą tożsamości przypisanej do systemu, tożsamość przypisana przez użytkownika może być równie łatwa. W tym celu: należy zmienić, `az webapp identity assign command` Aby przypisać żądaną tożsamość przypisaną przez użytkownika. Następnie podczas tworzenia użytkownika SQL upewnij się, że używasz nazwy zasobu tożsamości przypisanej do użytkownika, a nie nazwy lokacji.

### <a name="enable-managed-identity-on-app"></a>Włącz zarządzaną tożsamość w aplikacji

Aby włączyć tożsamość zarządzanej dla aplikacji platformy Azure, użyj polecenia [az webapp identity assign](/cli/azure/webapp/identity#az-webapp-identity-assign) w usłudze Cloud Shell. W poniższym poleceniu Zastąp ciąg *\<app-name>* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Oto przykład danych wyjściowych:

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>Przyznawanie uprawnień do tożsamości zarządzanej

> [!NOTE]
> Jeśli chcesz, możesz dodać tożsamość do [grupy usługi Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md), a następnie przyznać SQL Database dostęp do grupy usługi Azure AD zamiast tożsamości. Na przykład następujące polecenia dodają tożsamość zarządzaną z poprzedniego kroku do nowej grupy o nazwie _myAzureSQLDBAccessGroup_:
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

W usłudze Cloud Shell zaloguj się do usługi SQL Database przy użyciu polecenia SQLCMD. Zastąp ciąg _\<server-name>_ nazwą serwera, _\<db-name>_ nazwą bazy danych używaną przez aplikację i _\<aad-user-name>_ i _\<aad-password>_ z poświadczeniami użytkownika usługi Azure AD.

```bash
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

W wierszu polecenia SQL dla bazy danych, uruchom następujące polecenie, aby przyznać uprawnienia wymagane przez aplikację. Na przykład 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

*\<identity-name>* jest nazwą zarządzanej tożsamości w usłudze Azure AD. Jeśli tożsamość jest przypisana przez system, nazwa jest zawsze taka sama jak nazwa aplikacji App Service. Aby udzielić uprawnień dla grupy usługi Azure AD, należy zamiast tego użyć nazwy wyświetlanej grupy (na przykład *myAzureSQLDBAccessGroup*).

Wpisz polecenie `EXIT`, aby powrócić do wiersza polecenia usługi Cloud Shell.

> [!NOTE]
> Usługi zaplecza tożsamości zarządzanych obsługują również [pamięć podręczną tokenów](overview-managed-identity.md#obtain-tokens-for-azure-resources) , która aktualizuje token dla zasobu docelowego tylko po jego wygaśnięciu. Jeśli wystąpi błąd podczas konfigurowania uprawnień SQL Database i spróbujesz zmodyfikować uprawnienia *po* próbie uzyskania tokenu w aplikacji, nie otrzymasz nowego tokenu ze zaktualizowanymi uprawnieniami do momentu wygaśnięcia tokenu w pamięci podręcznej.

> [!NOTE]
> Usługi AAD nie są obsługiwane w przypadku Premium SQL Server i obejmuje to MSIs. 

### <a name="modify-connection-string"></a>Modyfikowanie parametrów połączenia

Należy pamiętać, że te same zmiany wprowadzone w *Web.config* lub *appsettings.jsw* działaniu z tożsamością zarządzaną, więc jedynym zadaniem do wykonania jest usunięcie istniejących parametrów połączenia w App Service, które program Visual Studio utworzył wdrożenie aplikacji po raz pierwszy. Użyj poniższego polecenia, ale Zastąp *\<app-name>* wartość nazwą aplikacji.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publikowanie zmian

Teraz pozostało tylko opublikowanie zmian na platformie Azure.

**Jeśli pochodziłeś z [samouczka: tworzenie aplikacji ASP.NET na platformie Azure przy użyciu SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)**, Opublikuj zmiany w programie Visual Studio. W obszarze **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt **DotNetAppSqlDb** i wybierz polecenie **Publikuj**.

![Publikowanie z Eksploratora rozwiązań](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na stronie publikowania kliknij przycisk **Publikuj**. 

**Jeśli pochodziłeś z [samouczka: tworzenie aplikacji ASP.NET Core i SQL Database w Azure App Service](tutorial-dotnetcore-sqldb-app.md)**, Opublikuj zmiany za pomocą usługi git, używając następujących poleceń:

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
> * Konfigurowanie Entity Framework do korzystania z uwierzytelniania usługi Azure AD z usługą SQL Database
> * Nawiązywanie połączenia z SQL Database z programu Visual Studio przy użyciu uwierzytelniania usługi Azure AD

Przejdź do następnego samouczka, aby dowiedzieć się, jak zmapować niestandardową nazwę DNS na aplikację internetową.

> [!div class="nextstepaction"]
> [Mapowanie istniejącej niestandardowej nazwy DNS na Azure App Service](app-service-web-tutorial-custom-domain.md)