---
title: ActiveDirectoryInteractive nawiązuje połączenie z serwerem SQL
description: Przykład kodu w języku C#, z wyjaśnieniami do łączenia się z Azure SQL Database przy użyciu trybu SqlAuthenticationMethod. ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: 93831ec4c1dc3e34c2ea144e71b67dae711ee870
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94841652"
---
# <a name="connect-to-azure-sql-database-with-azure-ad-multi-factor-authentication"></a>Nawiązywanie połączenia z usługą Azure SQL Database przy użyciu usługi Azure AD Multi-Factor Authentication
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ten artykuł zawiera program w języku C#, który łączy się z Azure SQL Database. Program używa uwierzytelniania w trybie interaktywnym, który obsługuje [usługę Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

Aby uzyskać więcej informacji na temat obsługi Multi-Factor Authentication dla narzędzi SQL, zobacz [Azure Active Directory support w narzędziach danych SQL Server (SSDT)](/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Multi-Factor Authentication Azure SQL Database

Począwszy od .NET Framework w wersji 4.7.2, Wyliczenie [`SqlAuthenticationMethod`](/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) ma nową wartość: `ActiveDirectoryInteractive` . W programie w języku C# wartość enum Nakazuje systemowi Użycie trybu interaktywnego Azure Active Directory (Azure AD), który obsługuje Multi-Factor Authentication do nawiązywania połączenia z Azure SQL Database. Użytkownik, który uruchamia program, widzi następujące okna dialogowe:

* Okno dialogowe, w którym jest wyświetlana nazwa użytkownika usługi Azure AD z prośbą o podanie hasła użytkownika.

   Jeśli domena użytkownika jest federacyjny przy użyciu usługi Azure AD, to okno dialogowe nie zostanie wyświetlone, ponieważ nie jest wymagana żadna wartość hasła.

   Jeśli zasady usługi Azure AD nakładają Multi-Factor Authentication na użytkownika, wyświetlane są następne dwa okna dialogowe.

* Gdy użytkownik po raz pierwszy przejdzie przez Multi-Factor Authentication, system wyświetli okno dialogowe z prośbą o numer telefonu komórkowego do wysyłania wiadomości tekstowych do programu. Każdy komunikat zawiera *kod weryfikacyjny* , który użytkownik musi wprowadzić w następnym oknie dialogowym.

* Okno dialogowe z prośbą o Multi-Factor Authentication kod weryfikacyjny, który system wysłał do telefonu komórkowego.

Aby uzyskać informacje na temat sposobu konfigurowania usługi Azure AD w celu wymagania Multi-Factor Authentication, zobacz [Rozpoczynanie pracy z usługą Azure AD Multi-Factor Authentication w chmurze](../../active-directory/authentication/howto-mfa-getstarted.md).

Zrzuty ekranu tych okien dialogowych, zobacz [Konfigurowanie uwierzytelniania wieloskładnikowego dla SQL Server Management Studio i usługi Azure AD](authentication-mfa-ssms-configure.md).

> [!TIP]
> .NET Framework interfejsów API można wyszukiwać za pomocą [strony narzędzia przeglądarki interfejsu API platformy .NET](/dotnet/api/).
>
> Możesz również przeszukiwać bezpośrednio za pomocą [opcjonalnego &lt; &gt; parametru wartość wyszukiwania](/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Skonfiguruj aplikację w języku C# w Azure Portal

Przed rozpoczęciem należy mieć utworzony i dostępny [logiczny serwer SQL](logical-servers.md) .

### <a name="register-your-app-and-set-permissions"></a>Zarejestruj aplikację i Ustaw uprawnienia

Aby można było korzystać z uwierzytelniania usługi Azure AD, program w języku C# musi zarejestrować się jako aplikacja usługi Azure AD. Aby zarejestrować aplikację, musisz być administratorem usługi Azure AD lub użytkownikiem przypisaną rolą *dewelopera aplikacji* usługi Azure AD. Aby uzyskać więcej informacji na temat przypisywania ról, zobacz [Przypisywanie ról administratora i niebędących administratorami do użytkowników z Azure Active Directory](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

Zakończenie rejestracji aplikacji powoduje wygenerowanie i wyświetlenie **identyfikatora aplikacji**. Program musi dołączyć ten identyfikator, aby nawiązać połączenie.

Aby zarejestrować i ustawić wymagane uprawnienia dla aplikacji:

1. W Azure Portal wybierz pozycję **Azure Active Directory**  >  **rejestracje aplikacji**  >  **Nowa rejestracja**.

    ![Rejestrowanie aplikacji](./media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Po utworzeniu rejestracji aplikacji jest generowana i wyświetlana wartość **identyfikatora aplikacji** .

    ![Wyświetlany identyfikator aplikacji](./media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Wybierz pozycję **Uprawnienia interfejsu API** > **Dodaj uprawnienie**.

    ![Ustawienia uprawnień dla zarejestrowanej aplikacji](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Wybierz **interfejsy API Moja organizacja używa** > wpisz **Azure SQL Database** do > wyszukiwania i wybierz pozycję **Azure SQL Database**.

    ![Dodawanie dostępu do interfejsu API dla Azure SQL Database](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Wybierz **uprawnienia delegowane**  >  **user_impersonation**  >  **Dodaj uprawnienia**.

    ![Delegowanie uprawnień do interfejsu API dla Azure SQL Database](./media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-server"></a>Ustawianie administratora usługi Azure AD dla serwera

Aby można było uruchomić program w języku C#, administrator programu [SQL Server](logical-servers.md) musi przypisać do niego administratora usługi Azure AD.

Na stronie **SQL Server** wybierz pozycję **Active Directory** administrator  >  **Ustaw administratora**.

Więcej informacji o administratorach i użytkownikach usługi Azure AD dla Azure SQL Database można znaleźć w temacie zrzuty ekranu w artykule [Konfigurowanie i zarządzanie Azure Active Directory uwierzytelnianiem przy użyciu SQL Database](authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Dodawanie użytkownika niebędącego administratorem do określonej bazy danych (opcjonalnie)

Administrator usługi Azure AD dla [logicznego serwera SQL](logical-servers.md) może uruchomić przykładowy program w języku C#. Użytkownik usługi Azure AD może uruchomić ten program, jeśli znajdują się w bazie danych. Administrator SQL usługi Azure AD lub użytkownik usługi Azure AD, który istnieje już w bazie danych i ma `ALTER ANY USER` uprawnienia do bazy danych, może dodać użytkownika.

Możesz dodać użytkownika do bazy danych za pomocą [`Create User`](/sql/t-sql/statements/create-user-transact-sql) polecenia SQL. Może to być na przykład `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Aby uzyskać więcej informacji, zobacz [używanie uwierzytelniania Azure Active Directory na potrzeby uwierzytelniania przy użyciu SQL Database, wystąpienia zarządzanego lub usługi Azure Synapse Analytics](authentication-aad-overview.md).

## <a name="new-authentication-enum-value"></a>Nowa wartość wyliczenia uwierzytelniania

Przykład w języku C# opiera się na [`System.Data.SqlClient`](/dotnet/api/system.data.sqlclient) przestrzeni nazw. Szczególnym zainteresowaniem Multi-Factor Authentication jest Wyliczenie `SqlAuthenticationMethod` , które ma następujące wartości:

* `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Użyj tej wartości przy użyciu nazwy użytkownika usługi Azure AD w celu zaimplementowania Multi-Factor Authentication. Ta wartość jest skoncentrowana na obecnym artykule. Zapewnia interaktywne środowisko, wyświetlając okna dialogowe dla hasła użytkownika, a następnie Multi-Factor Authentication sprawdzanie poprawności, jeśli na tym użytkowniku nałożono Multi-Factor Authentication. Ta wartość jest dostępna od .NET Framework w wersji 4.7.2.

* `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Użyj tej wartości dla konta *federacyjnego* . W przypadku konta federacyjnego nazwa użytkownika jest znana dla domeny systemu Windows. Ta metoda uwierzytelniania nie obsługuje Multi-Factor Authentication.

* `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Użyj tej wartości do uwierzytelniania, które wymaga nazwy użytkownika i hasła usługi Azure AD. Azure SQL Database wykonuje uwierzytelnianie. Ta metoda nie obsługuje Multi-Factor Authentication.

> [!NOTE]
> W przypadku korzystania z platformy .NET Core należy użyć przestrzeni nazw [Microsoft. Data. SqlClient](/dotnet/api/microsoft.data.sqlclient?view=sqlclient-dotnet-core-1.1) . Aby uzyskać więcej informacji, zobacz następujący [blog](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/).

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Ustaw wartości parametrów C# z Azure Portal

Aby program C# został pomyślnie uruchomiony, należy przypisać odpowiednie wartości do pól statycznych. Pokazane tutaj są pola z przykładowymi wartościami. Pokazane są również lokalizacje Azure Portal, w których można uzyskać potrzebne wartości.

| Nazwa pola statycznego | Przykładowa wartość | Gdzie w Azure Portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Serwery SQL**  >  **Filtruj według nazwy** |
| AzureAD_UserID | "auser \@ ABC.onmicrosoft.com" | **Azure Active Directory**  >  **Użytkownik**  >  **Nowy użytkownik-Gość** |
| Initial_DatabaseName | "baza danych" | **Serwery SQL**  >  **Bazy danych SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory**  >  **Rejestracje aplikacji**  >  **Wyszukaj według nazwy**  >  **Identyfikator aplikacji** |
| RedirectUri | nowy identyfikator URI (" https://mywebserver.com/ ") | **Azure Active Directory**  >  **Rejestracje aplikacji**  >  **Wyszukaj według nazwy**  >  *[Twoja aplikacja-Rejestracja]*  >  **Ustawienia**  >  **RedirectURIs**<br /><br />W tym artykule wszystkie prawidłowe wartości są poprawne dla RedirectUri, ponieważ nie są używane w tym miejscu. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Weryfikuj przy użyciu SQL Server Management Studio

Przed uruchomieniem programu C# warto sprawdzić, czy konfiguracja i konfiguracje są poprawne w SQL Server Management Studio (SSMS). Wszelkie błędy programu w języku C# można następnie zawęzić do kodu źródłowego.

### <a name="verify-server-level-firewall-ip-addresses"></a>Sprawdź adresy IP zapory na poziomie serwera

Uruchom narzędzie SSMS z tego samego komputera, w tym samym budynku, w którym planujesz uruchomić program C#. W przypadku tego testu każdy tryb **uwierzytelniania** jest prawidłowy. Jeśli istnieją jakieś oznaki, że serwer nie akceptuje adresu IP, zobacz [reguły zapory na poziomie serwera i na poziomie bazy danych](firewall-configure.md) , aby uzyskać pomoc.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Sprawdź Azure Active Directory Multi-Factor Authentication

Ponownie uruchom program SSMS, tym razem z **uwierzytelnianiem** ustawionym na **Azure Active Directory-Universal with MFA**. Ta opcja wymaga programu SSMS w wersji 17,5 lub nowszej.

Aby uzyskać więcej informacji, zobacz [konfigurowanie Multi-Factor Authentication dla programu SSMS i usługi Azure AD](authentication-mfa-ssms-configure.md).

> [!NOTE]
> Jeśli jesteś użytkownikiem gościa w bazie danych, musisz również podać nazwę domeny usługi Azure AD dla bazy danych: wybierz opcję **Opcje**  >  **nazwa domeny usługi AD lub identyfikator dzierżawy**. Aby znaleźć nazwę domeny w Azure Portal, wybierz pozycję **Azure Active Directory**  >  **niestandardowych nazw domen**. W przykładowym programie w języku C# podanie nazwy domeny nie jest konieczne.

## <a name="c-code-example"></a>Przykład kodu w języku C#

> [!NOTE]
> W przypadku korzystania z platformy .NET Core należy użyć przestrzeni nazw [Microsoft. Data. SqlClient](/dotnet/api/microsoft.data.sqlclient?view=sqlclient-dotnet-core-1.1) . Aby uzyskać więcej informacji, zobacz następujący [blog](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/).

Przykładowy program w języku C# opiera się na zestawie dll [*Microsoft. IdentityModel. clients. ActiveDirectory*](/dotnet/api/microsoft.identitymodel.clients.activedirectory) .

Aby zainstalować ten pakiet, w programie Visual Studio wybierz pozycję **projekt**  >  **Zarządzaj pakietami NuGet**. Wyszukaj i zainstaluj **Microsoft. IdentityModel. clients. ActiveDirectory**.

Jest to przykładowy kod źródłowy języka C#.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  .
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  .
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Jest to przykład danych wyjściowych testu w języku C#.

```C#
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

& [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)