---
title: 'Samouczek: używanie tożsamości zarządzanej do uzyskiwania dostępu do Azure SQL Database-Windows-Azure AD'
description: Samouczek, który przeprowadzi Cię przez proces używania tożsamości zarządzanej przypisanej do systemu Windows maszyny wirtualnej w celu uzyskania dostępu do Azure SQL Database.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4f56ce9fa86dc27b77ad6b463479d13c8e4e7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856516"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Samouczek: używanie przypisanej przez system tożsamości zarządzanej maszyny wirtualnej systemu Windows w celu uzyskania dostępu do usługi Azure SQL

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

W tym samouczku pokazano, jak uzyskać dostęp do Azure SQL Database za pomocą tożsamości przypisanej do systemu dla maszyny wirtualnej z systemem Windows. Tożsamości usługi zarządzanej są automatycznie zarządzane przez platformę Azure. Umożliwiają uwierzytelnianie w usługach obsługujących uwierzytelnianie usługi Azure AD bez potrzeby wprowadzania poświadczeń do kodu. Omawiane kwestie:

> [!div class="checklist"]
>
> * Przyznaj maszynie wirtualnej dostęp do Azure SQL Database
> * Włączanie uwierzytelniania w usłudze Azure AD
> * Tworzenie w bazie danych zawartego użytkownika, który będzie reprezentować tożsamość maszyny wirtualnej przypisaną przez system
> * Uzyskaj token dostępu przy użyciu tożsamości maszyny wirtualnej i użyj go do zapytania Azure SQL Database

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="enable"></a>Włącz

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]

## <a name="grant-access"></a>Udzielanie dostępu

Aby udzielić dostępu do maszyny wirtualnej do bazy danych w Azure SQL Database, można użyć istniejącego [logicznego serwera SQL](../../azure-sql/database/logical-servers.md) lub utworzyć nowy. Aby utworzyć nowy serwer i bazę danych przy użyciu witryny Azure Portal, wykonaj ten [przewodnik Szybki start usługi Azure SQL](../../azure-sql/database/single-database-create-quickstart.md). W [dokumentacji usługi Azure SQL ](/azure/sql-database/) dostępne są również przewodniki Szybki start, które używają interfejsu wiersza polecenia platformy Azure oraz programu Azure PowerShell.

Istnieją dwa kroki związane z udzielaniem maszynie wirtualnej dostępu do bazy danych:

1. Włącz uwierzytelnianie usługi Azure AD dla serwera.
2. Tworzenie w bazie danych **zawartego użytkownika**, który będzie reprezentować tożsamość maszyny wirtualnej przypisaną przez system.

### <a name="enable-azure-ad-authentication"></a>Włączanie uwierzytelniania w usłudze Azure AD

**Aby [skonfigurować uwierzytelnianie usługi Azure AD](../../azure-sql/database/authentication-aad-configure.md):**

1. W witrynie Azure Portal wybierz opcję **Serwery SQL** z lewego paska nawigacyjnego.
2. Kliknij serwer SQL, aby włączyć go do uwierzytelniania usługi Azure AD.
3. W sekcji **Ustawienia** bloku kliknij opcję **Administrator usługi Active Directory**.
4. Na pasku poleceń kliknij przycisk **Ustaw administratora**.
5. Wybierz konto użytkownika usługi Azure AD, które zostanie administratorem serwera, i kliknij przycisk **Wybierz**.
6. Na pasku poleceń kliknij przycisk **Zapisz**.

### <a name="create-contained-user"></a>Utwórz zawartego użytkownika

W tej sekcji przedstawiono sposób tworzenia zawartego użytkownika w bazie danych, który reprezentuje tożsamość przypisanej do systemu maszyny wirtualnej. Ten krok wymaga [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Przed rozpoczęciem pomocne może być sprawdzenie następujących artykułów, aby uzyskać podstawowe informacje o integracji usługi Azure AD:

- [Uniwersalne uwierzytelnianie przy użyciu usług SQL Database i Azure Synapse Analytics (Obsługa programu SSMS dla usługi MFA)](../../azure-sql/database/authentication-mfa-ssms-overview.md)
- [Konfigurowanie i Zarządzanie uwierzytelnianiem Azure Active Directory przy użyciu SQL Database lub usługi Azure Synapse Analytics](../../azure-sql/database/authentication-aad-configure.md)

Usługa SQL Database wymaga unikatowych nazw wyświetlanych usługi AAD. W związku z tym konta usługi AAD, takie jak użytkownicy, grupy i jednostki usług (aplikacje) i nazwy maszyn wirtualnych z włączoną tożsamością zarządzaną, muszą być jednoznacznie zdefiniowane w usłudze AAD w odniesieniu do ich nazw wyświetlanych. Baza danych SQL sprawdza nazwę wyświetlaną usługi AAD podczas tworzenia tych użytkowników przez T-SQL, a jeśli nie jest unikatowa, polecenie nie powiedzie się, aby podać unikatową nazwę wyświetlaną usługi AAD dla danego konta.

**Aby utworzyć zawartego użytkownika:**

1. Uruchom program SQL Server Management Studio.
2. W oknie dialogowym **łączenie z serwerem** wprowadź nazwę serwera w polu **Nazwa serwera** .
3. W polu **Uwierzytelnianie** wybierz opcję **Active Directory — uniwersalne z obsługą uwierzytelniania wieloskładnikowego**.
4. W polu **Nazwa użytkownika** wprowadź nazwę konta usługi Azure AD, które zostało ustawione jako administrator serwera, np. helen@woodgroveonline.com
5. Kliknij pozycję **Opcje**.
6. W polu **Połącz z bazą danych** wpisz nazwę niesystemowej bazy danych, którą chcesz skonfigurować.
7. Kliknij przycisk **Połącz**. Zakończ proces logowania.
8. W **Eksploratorze obiektów** rozwiń folder **Bazy danych**.
9. Kliknij prawym przyciskiem myszy bazę danych użytkownika, a następnie kliknij pozycję **Nowe zapytanie**.
10. W oknie zapytania wpisz następujący wiersz, a następnie kliknij przycisk **Wykonaj** na pasku narzędzi:

    > [!NOTE]
    > Element `VMName` w poniższym poleceniu to nazwa maszyny wirtualnej, dla której w sekcji wymagań wstępnych włączono tożsamość przypisaną przez system.

    ```sql
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```

    Polecenie powinno zakończyć się pomyślnie, tworząc zawartego użytkownika na potrzeby tożsamości maszyny wirtualnej przypisanej przez system.
11. Wyczyść okno zapytania, wpisz następujący wiersz, a następnie kliknij przycisk **Wykonaj** na pasku narzędzi:

    > [!NOTE]
    > Element `VMName` w poniższym poleceniu to nazwa maszyny wirtualnej, dla której w sekcji wymagań wstępnych włączono tożsamość przypisaną przez system.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    Polecenie powinno zakończyć się pomyślnie, udzielając zawartemu użytkownikowi prawa do odczytu całej bazy danych.

Kod uruchomiony na maszynie wirtualnej może teraz uzyskać token przy użyciu tożsamości zarządzanej przypisanej do systemu i użyć tokenu do uwierzytelnienia na serwerze.

## <a name="access-data"></a>Uzyskiwanie dostępu do danych

W tej sekcji pokazano, jak uzyskać token dostępu przy użyciu tożsamości zarządzanej przypisanej przez system do maszyny wirtualnej i użyć jej do wywołania usługi Azure SQL. Usługa Azure SQL natywnie obsługuje uwierzytelnianie usługi Azure AD, więc może bezpośrednio akceptować tokeny dostępu pozyskane przy użyciu tożsamości zarządzanych dla zasobów platformy Azure. Możesz użyć metody **tokena dostępu** dla tworzenia połączeń z serwerem SQL. Jest to część integracji usługi Azure SQL z usługą Azure AD. Takie rozwiązanie różni się od podawania poświadczeń w parametrach połączenia.

Oto przykład kodu platformy .NET służący do otwierania połączenia z programem SQL przy użyciu tokenu dostępu. Kod musi działać na maszynie wirtualnej, aby można było uzyskać dostęp do punktu końcowego tożsamości zarządzanej przypisanej przez system do maszyny wirtualnej. Do korzystania z metody tokenu dostępu jest wymagany **.NET Framework 4,6** lub nowszy lub **.NET Core 2,2** lub nowszy. Zastąp odpowiednio wartości AZURE-SQL-SERVERNAME i DATABASE. Zwróć uwagę na to, że identyfikator zasobu usługi Azure SQL to `https://database.windows.net/` .

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Alternatywną metodą szybkiego przetestowania kompleksowej konfiguracji bez potrzeby pisania i wdrażania aplikacji na maszynie wirtualnej jest użycie programu PowerShell.

1. W portalu przejdź do pozycji **Maszyny wirtualne**, a następnie przejdź do swojej maszyny wirtualnej z systemem Windows i w pozycji **Przegląd** kliknij przycisk **Połącz**.
2. Wprowadź **nazwę użytkownika** i **hasło** dodane podczas tworzenia maszyny wirtualnej z systemem Windows.
3. Teraz, po utworzeniu **Podłączanie pulpitu zdalnego** z maszyną wirtualną, Otwórz program **PowerShell** w sesji zdalnej.
4. Używając polecenia `Invoke-WebRequest` programu PowerShell, wyślij żądanie do lokalnego punktu końcowego tożsamości zarządzanej, aby uzyskać token dostępu na potrzeby usługi Azure SQL.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```

    Skonwertuj odpowiedź z obiektu JSON do obiektu PowerShell.

    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Wyodrębnij token dostępu z odpowiedzi.

    ```powershell
    $AccessToken = $content.access_token
    ```

5. Otwórz połączenie z serwerem. Pamiętaj, aby zastąpić wartości AZURE SQL-SERVERNAME i DATABASE.

    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Następnie utwórz i wyślij zapytanie do serwera. Pamiętaj, aby zastąpić wartość w pozycji TABLE.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Sprawdź wartość `$DataSet.Tables[0]`, aby wyświetlić wyniki zapytania.

## <a name="disable"></a>Wyłącz

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób korzystania z tożsamości zarządzanej przypisanej przez system w celu uzyskania dostępu do Azure SQL Database. Aby dowiedzieć się więcej na temat Azure SQL Database, zobacz:

> [!div class="nextstepaction"]
> [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)
