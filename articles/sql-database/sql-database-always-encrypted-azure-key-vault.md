---
title: Zawsze szyfrowane — usługa Azure Key Vault
description: W tym artykule pokazano, jak zabezpieczyć poufne dane w bazie danych SQL za pomocą szyfrowania danych przy użyciu Kreatora zawsze zaszyfrowanych w programie SQL Server Management Studio.
keywords: szyfrowanie danych, klucz szyfrowania, szyfrowanie w chmurze
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: f1d08581c5d29fc41fb33541d766af7cece88cdc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451673"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-azure-key-vault"></a>Zawsze szyfrowane: Ochrona poufnych danych i przechowywanie kluczy szyfrowania w usłudze Azure Key Vault

W tym artykule pokazano, jak zabezpieczyć poufne dane w bazie danych SQL za pomocą szyfrowania danych przy użyciu [Kreatora zawsze zaszyfrowanych](https://msdn.microsoft.com/library/mt459280.aspx) w [programie SQL Server Management Studio (SSMS).](https://msdn.microsoft.com/library/hh213248.aspx) Zawiera również instrukcje, które pokażą, jak przechowywać każdy klucz szyfrowania w usłudze Azure Key Vault.

Zawsze szyfrowane to nowa technologia szyfrowania danych w usłudze Azure SQL Database i SQL Server, która pomaga chronić poufne dane w spoczynku na serwerze, podczas przemieszczania się między klientem a serwerem oraz podczas korzystania z danych. Zawsze zaszyfrowane zapewnia, że poufne dane nigdy nie są wyświetlane jako zwykły tekst wewnątrz systemu bazy danych. Po skonfigurowaniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji, które mają dostęp do kluczy, mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu. Aby uzyskać szczegółowe informacje, zobacz [Zawsze zaszyfrowany (Aparat baz danych)](https://msdn.microsoft.com/library/mt163865.aspx).

Po skonfigurowaniu bazy danych do używania zawsze zaszyfrowane, utworzysz aplikację kliencką w języku C# z programem Visual Studio do pracy z zaszyfrowanymi danymi.

Wykonaj kroki opisane w tym artykule i dowiedz się, jak skonfigurować zawsze zaszyfrowane dla bazy danych SQL platformy Azure. W tym artykule dowiesz się, jak wykonywać następujące zadania:

- Użyj kreatora zawsze zaszyfrowane w SSMS, aby utworzyć [zawsze zaszyfrowane klucze](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  - Tworzenie [klucza głównego kolumny (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  - Utwórz [klucz szyfrowania kolumny (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Tworzenie tabeli bazy danych i szyfrowanie kolumn.
- Utwórz aplikację, która wstawia, wybiera i wyświetla dane z zaszyfrowanych kolumn.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku musisz:

- Konto i subskrypcja platformy Azure. Jeśli go nie masz, zarejestruj się, aby uzyskać [bezpłatną wersję próbną.](https://azure.microsoft.com/pricing/free-trial/)
- [Sql Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) w wersji 13.0.700.242 lub nowszej.
- [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) lub nowsza (na komputerze klienckim).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Narzędzie Azure PowerShell](/powershell/azure/overview) lub [azure cli](/cli/azure/install-azure-cli)

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Włączanie dostępu aplikacji klienckiej do usługi bazy danych SQL

Należy włączyć aplikację kliencką, aby uzyskać dostęp do usługi bazy danych SQL, konfigurując aplikację usługi Azure Active Directory (AAD) i kopiując *identyfikator aplikacji* i *klucz,* który będzie potrzebny do uwierzytelnienia aplikacji.

Aby uzyskać *identyfikator aplikacji* i *klucz,* wykonaj kroki opisane w [tworzeniu aplikacji i jednostki usługi Azure Active Directory, która może uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Tworzenie magazynu kluczy do przechowywania kluczy

Teraz, gdy aplikacja kliencka jest skonfigurowana i masz identyfikator aplikacji, nadszedł czas, aby utworzyć magazyn kluczy i skonfigurować jej zasady dostępu, dzięki czemu ty i twoja aplikacja może uzyskać dostęp do wpisów tajnych magazynu (zawsze zaszyfrowane klucze). Uprawnienia *create*, *get*, *list*, *sign*, *verify*, *wrapKey*i *unwrapKey* są wymagane do utworzenia nowego klucza głównego kolumny i konfigurowania szyfrowania za pomocą programu SQL Server Management Studio.

Przechowalnia kluczy można szybko utworzyć, uruchamiając następujący skrypt. Aby uzyskać szczegółowe wyjaśnienie tych poleceń i więcej informacji na temat tworzenia i konfigurowania magazynu kluczy, zobacz [Co to jest usługa Azure Key Vault?](../key-vault/general/overview.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Moduł AzureRM będzie nadal otrzymywać poprawki błędów co najmniej do grudnia 2020.  Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji na temat ich zgodności, zobacz [Przedstawianie nowego modułu Az programu Azure PowerShell.](/powershell/azure/new-azureps-module-az)

```powershell
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

Connect-AzAccount
$subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
Set-AzContext -SubscriptionId $subscriptionId

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create, get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get, list, sign, unwrapKey, verify, wrapKey --resource-group $resourceGroupName --spn $applicationId
```

* * *

## <a name="create-a-blank-sql-database"></a>Tworzenie pustej bazy danych SQL

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Przejdź do strony Tworzenie bazy danych SQL**bazy danych baz danych** >  **bazy** > danych bazy**danych**.
3. Utwórz **pustą** bazę danych o nazwie **Klinika** na nowym lub istniejącym serwerze. Aby uzyskać szczegółowe wskazówki dotyczące tworzenia bazy danych w witrynie Azure portal, zobacz [Pierwszą bazę danych SQL platformy Azure.](sql-database-single-database-get-started.md)

    ![Tworzenie pustej bazy danych](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Będziesz potrzebował ciągu połączenia w dalszej części samouczka, więc po utworzeniu bazy danych przejdź do nowej bazy danych kliniki i skopiuj parametry połączenia. Ciąg połączenia można uzyskać w dowolnym momencie, ale można go łatwo skopiować w witrynie Azure portal.

1. Przejdź do **bazy danych** > SQL**Clinic** > **Show parametry połączenia bazy danych**.
2. Skopiuj parametry połączenia dla **ADO.NET**.

    ![Kopiowanie parametrów połączenia](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Nawiązywanie połączenia z bazą danych za pomocą programu SSMS

Otwórz usługę SSMS i połącz się z serwerem za pomocą bazy danych Clinic.

1. Otwórz program SSMS. (Przejdź do **aparatu połącz** > **z bazami danych,** aby otworzyć okno **Połącz z serwerem,** jeśli nie jest otwarte).

2. Wprowadź nazwę serwera i poświadczenia. Nazwę serwera można znaleźć w bloku bazy danych SQL i w ciągu połączenia skopiowanym wcześniej. Wpisz pełną nazwę serwera, w tym *database.windows.net*.

    ![Kopiowanie parametrów połączenia](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Jeśli zostanie otwarte okno **Nowa reguła zapory,** zaloguj się na platformie Azure i pozwól SSMS utworzyć nową regułę zapory.

## <a name="create-a-table"></a>Tworzenie tabeli

W tej sekcji utworzysz tabelę do przechowywania danych pacjenta. Początkowo nie jest szyfrowany — szyfrowanie zostanie skonfigurowane w następnej sekcji.

1. Rozwiń **bazy danych**.
2. Kliknij prawym przyciskiem myszy bazę danych **kliniki** i kliknij polecenie **Nowe zapytanie**.
3. Wklej następujące Transact-SQL (T-SQL) do nowego okna kwerendy i **wykonaj** go.

```sql
CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
GO
```

## <a name="encrypt-columns-configure-always-encrypted"></a>Szyfruj kolumny (konfiguruj zawsze zaszyfrowane)

SSMS udostępnia kreatora, który ułatwia konfigurowanie zawsze zaszyfrowane, konfigurując klucz główny kolumny, klucz szyfrowania kolumn i zaszyfrowane kolumny.

1. Rozwiń**tabele** > **kliniki** > baz **danych**.
2. Kliknij prawym przyciskiem myszy tabelę **Pacjenci** i wybierz **polecenie Szyfruj kolumny,** aby otworzyć Kreatora zawsze zaszyfrowanych:

    ![Szyfruj kolumny](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

Kreator Zawsze zaszyfrowany zawiera następujące sekcje: **Wybór kolumny,** **Konfiguracja klucza głównego,** **Sprawdzanie poprawności**i **Podsumowanie**.

### <a name="column-selection"></a>Wybór kolumny

Kliknij **przycisk Dalej** na stronie **Wprowadzenie,** aby otworzyć stronę **Wyboru kolumny.** Na tej stronie należy wybrać kolumny, które mają być szyfrowane, [typ szyfrowania i klucz szyfrowania kolumny (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) do użycia.

Szyfruj informacje **O 10 I** **Data urodzenia** dla każdego pacjenta. Kolumna SSN będzie używać szyfrowania deterministycznego, które obsługuje wyszukiwania równości, sprzężenia i grupowanie według. Kolumna BirthDate będzie używać szyfrowania losowego, które nie obsługuje operacji.

Ustaw **typ szyfrowania** dla kolumny SSN na **Deterministyczny,** a kolumna Data urodzenia na **Randomizowaną**. Kliknij przycisk **Dalej**.

![Szyfruj kolumny](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Konfiguracja klucza głównego

Strona **Konfiguracja klucza głównego** to miejsce, w którym skonfigurowana jest usługa CMK i wybranie dostawcy magazynu kluczy, w którym będzie przechowywany cmk. Obecnie można przechowywać współrzędnościową maszynę cmk w magazynie certyfikatów systemu Windows, usługi Azure Key Vault lub sprzętowym module zabezpieczeń (HSM).

W tym samouczku pokazano, jak przechowywać klucze w usłudze Azure Key Vault.

1. Wybierz **usługę Azure Key Vault**.
2. Wybierz żądaną przechowalnię kluczy z listy rozwijanej.
3. Kliknij przycisk **Dalej**.

![Konfiguracja klucza głównego](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Walidacja

Można teraz zaszyfrować kolumny lub zapisać skrypt programu PowerShell, aby uruchomić go później. W tym samouczku wybierz pozycję **Przejdź do końca teraz** i kliknij przycisk **Dalej**.

### <a name="summary"></a>Podsumowanie

Sprawdź, czy wszystkie ustawienia są poprawne i kliknij przycisk **Zakończ,** aby zakończyć konfigurację zawsze zaszyfrowanej.

![Podsumowanie](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>Weryfikowanie akcji kreatora

Po zakończeniu pracy kreatora baza danych jest skonfigurowana dla zawsze zaszyfrowanych. Kreator wykonał następujące czynności:

- Utworzono klucz główny kolumny i przechowywał go w usłudze Azure Key Vault.
- Utworzono klucz szyfrowania kolumny i przechowywane go w usłudze Azure Key Vault.
- Skonfigurowano wybrane kolumny do szyfrowania. Tabela Pacjenci nie zawiera obecnie żadnych danych, ale wszystkie istniejące dane w wybranych kolumnach są teraz szyfrowane.

Tworzenie kluczy w ssms można zweryfikować, rozszerzając **funkcje Clinic** > **Security** > **Always Encrypted Keys**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Tworzenie aplikacji klienckiej, która współpracuje z zaszyfrowanymi danymi

Teraz, gdy zawsze zaszyfrowane jest skonfigurowany, można utworzyć aplikację, która wykonuje *wstawia* i *wybiera* na zaszyfrowanych kolumnach.  

> [!IMPORTANT]
> Aplikacja musi używać obiektów [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) podczas przekazywania danych w postaci zwykłego tekstu na serwer z zawsze zaszyfrowanymi kolumnami. Przekazywanie wartości dosłownych bez użycia obiektów SqlParameter spowoduje wyjątek.

1. Otwórz program Visual Studio i utwórz nową **aplikację konsoli** języka C# (Visual Studio 2015 i starsze) lub aplikację **konsoli (net framework)** (Visual Studio 2017 i nowsze). Upewnij się, że projekt jest ustawiony na **.NET Framework 4.6** lub nowszych.
2. Nazwij projekt **AlwaysEncryptedConsoleAKVApp** i kliknij przycisk **OK**.
3. Zainstaluj następujące pakiety NuGet, przechodząc do > **konsoli Menedżera pakietów**Menedżera **pakietów Narzędzia** > **NuGet**Package Manager .

Uruchom te dwa wiersze kodu w konsoli Menedżera pakietów:

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modyfikowanie ciągu połączenia w celu włączenia opcji Zawsze szyfrowane

W tej sekcji wyjaśniono, jak włączyć zawsze szyfrowane w ciągu połączenia bazy danych.

Aby włączyć opcję Zawsze szyfrowane, należy dodać słowo kluczowe **Ustawienie szyfrowania kolumn** do ciągu połączenia i ustawić je na **Włączone**.

Można ustawić to bezpośrednio w ciągu połączenia lub można go ustawić za pomocą [sqlconnectionstringbuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). Przykładowa aplikacja w następnej sekcji pokazuje, jak używać **programu SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Włącz zawsze zaszyfrowane w ciągu połączenia

Dodaj następujące słowo kluczowe do ciągu połączenia.

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Włącz zawsze szyfrowane z SqlConnectionStringBuilder

Poniższy kod pokazuje, jak włączyć zawsze szyfrowane, ustawiając [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) [do Włączone](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Zarejestruj dostawcę usługi Azure Key Vault
Poniższy kod pokazuje, jak zarejestrować dostawcę usługi Azure Key Vault za pomocą sterownika ADO.NET.

```csharp
private static ClientCredential _clientCredential;

static void InitializeAzureKeyVaultProvider() {
    _clientCredential = new ClientCredential(applicationId, clientKey);

    SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider = new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

    Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers = new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

    providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
    SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
}
```

## <a name="always-encrypted-sample-console-application"></a>Zawsze szyfrowana przykładowa aplikacja konsoli

W tym przykładzie pokazano, jak:

- Zmodyfikuj parametry połączenia, aby włączyć opcję Zawsze szyfrowana.
- Zarejestruj usługę Azure Key Vault jako dostawcę magazynu kluczy aplikacji.  
- Wstawianie danych do zaszyfrowanych kolumn.
- Wybierz rekord, filtrując dla określonej wartości w zaszyfrowanej kolumnie.

Zastąp zawartość pliku *Program.cs* poniższym kodem. Zastąp parametry połączenia dla globalnej zmiennej connectionString w wierszu, który bezpośrednio poprzedza Main metody z prawidłowym ciągiem połączenia z witryny Azure portal. Jest to jedyna zmiana, którą należy wprowadzić w tym kodzie.

Uruchom aplikację, aby wyświetlić opcję Zawsze zaszyfrowane w akcji.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

namespace AlwaysEncryptedConsoleAKVApp {
    class Program {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";

        static void Main(string[] args) {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();

            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;

            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient() {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });

            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients()) {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null) {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }

        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider() {
            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope) {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient) {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);

            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex) {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients() {
            List<Patient> patients = new List<Patient>();

            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));

            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patients.Add(new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }

            return patients;
        }

        static Patient SelectPatientBySSN(string ssn) {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patient = new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else {
                        patient = null;
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }
            return patient;
        }

        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable() {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex) {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Sprawdź, czy dane są szyfrowane

Można szybko sprawdzić, czy rzeczywiste dane na serwerze są szyfrowane, odpytując dane pacjentów za pomocą usługi SSMS (przy użyciu bieżącego połączenia, w którym **ustawienie szyfrowania kolumn** nie jest jeszcze włączone).

Uruchom następującą kwerendę w bazie danych kliniki.

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Widać, że zaszyfrowane kolumny nie zawierają żadnych danych w postaci zwykłego tekstu.

   ![Nowa aplikacja konsoli](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Aby użyć usługi SSMS do uzyskania dostępu do danych w postaci zwykłego tekstu, należy najpierw upewnić się, że użytkownik ma odpowiednie uprawnienia do usługi Azure Key Vault: *get*, *unwrapKey*i *verify*. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie i przechowywanie kluczy głównych kolumn (zawsze zaszyfrowanych)](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted).

Następnie dodaj parametr *Ustawienie szyfrowania kolumn=włączone* podczas połączenia.

1. W programie SSMS kliknij prawym przyciskiem myszy serwer w **Eksploratorze obiektów** i wybierz polecenie **Rozłącz**.
2. Kliknij **pozycję Podłącz** > **aparat bazy danych,** aby otworzyć okno **Połącz z serwerem,** a następnie kliknij pozycję **Opcje**.
3. Kliknij **pozycję Dodatkowe parametry połączenia** i wpisz ustawienie **szyfrowania kolumny=włączono**.

    ![Nowa aplikacja konsoli](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)

4. Uruchom następującą kwerendę w bazie danych kliniki.

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

     Teraz można zobaczyć dane w postaci zwykłego tekstu w zaszyfrowanych kolumnach.
     ![Nowa aplikacja konsoli](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)

## <a name="next-steps"></a>Następne kroki

Po utworzeniu bazy danych, która używa zawsze zaszyfrowane, można wykonać następujące czynności:

- [Obróć i oczyść klawisze](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrowanie danych, które są już zaszyfrowane za pomocą always encrypted](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Informacje pokrewne

- [Zawsze szyfrowane (rozwój klienta)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Przezroczyste szyfrowanie danych](https://msdn.microsoft.com/library/bb934049.aspx)
- [Szyfrowanie programu SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Zawsze zaszyfrowany kreator](https://msdn.microsoft.com/library/mt459280.aspx)
- [Zawsze zaszyfrowany blog](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
