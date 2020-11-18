---
title: Konfigurowanie Always Encrypted przy użyciu Azure Key Vault
description: W tym samouczku przedstawiono sposób zabezpieczania poufnych danych w bazie danych w Azure SQL Database z szyfrowaniem danych przy użyciu kreatora Always Encrypted w SQL Server Management Studio.
keywords: Szyfrowanie danych, klucz szyfrowania, szyfrowanie w chmurze
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 11/02/2020
ms.openlocfilehash: 257abf03994c7006b1c3789174f550515dcd309a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841465"
---
# <a name="configure-always-encrypted-by-using-azure-key-vault"></a>Konfigurowanie Always Encrypted przy użyciu Azure Key Vault 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]

W tym artykule opisano sposób zabezpieczania poufnych danych w bazie danych w Azure SQL Database z szyfrowaniem danych przy użyciu [kreatora Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard) w [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Zawiera również instrukcje, które pokazują, jak przechowywać każdy klucz szyfrowania w Azure Key Vault.

Always Encrypted to technologia szyfrowania danych, która pomaga chronić poufne dane przechowywane na serwerze podczas przenoszenia między klientem i serwerem, a jednocześnie dane są używane. Always Encrypted zapewnia, że poufne dane nigdy nie pojawiają się jako zwykły tekst w systemie bazy danych. Po skonfigurowaniu szyfrowania danych tylko aplikacje klienckie lub serwery aplikacji, które mają dostęp do kluczy, mogą uzyskiwać dostęp do danych w postaci zwykłego tekstu. Aby uzyskać szczegółowe informacje, zobacz [Always Encrypted (aparat bazy danych)](/sql/relational-databases/security/encryption/always-encrypted-database-engine).

Po skonfigurowaniu bazy danych do korzystania z Always Encrypted należy utworzyć aplikację kliencką w języku C# z programem Visual Studio, aby korzystać z zaszyfrowanych danych.

Wykonaj kroki opisane w tym artykule i Dowiedz się, jak skonfigurować Always Encrypted dla bazy danych w wystąpieniu zarządzanym Azure SQL Database lub SQL. W tym artykule dowiesz się, jak wykonywać następujące zadania:

- Aby utworzyć [klucze Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine#Anchor_3), użyj Kreatora Always Encrypted w programie SSMS.
  - Utwórz [klucz główny kolumny (CMK)](/sql/t-sql/statements/create-column-master-key-transact-sql).
  - Utwórz [klucz szyfrowania kolumny (CEK)](/sql/t-sql/statements/create-column-encryption-key-transact-sql).
- Utwórz tabelę bazy danych i Zaszyfruj kolumny.
- Utwórz aplikację, która wstawia, wybiera i wyświetla dane z zaszyfrowanych kolumn.

## <a name="prerequisites"></a>Wymagania wstępne


- Konto i subskrypcja platformy Azure. Jeśli nie masz takiego konta, zarejestruj się, aby skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
- Baza danych programu [Azure SQL Database](single-database-create-quickstart.md) lub [wystąpienia zarządzanego Azure SQL](../managed-instance/instance-create-quickstart.md).
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) w wersji 13.0.700.242 lub nowszej.
- [.NET Framework 4,6](/dotnet/framework/) lub nowszy (na komputerze klienckim).
- [Program Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](/powershell/azure/) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

## <a name="enable-client-application-access"></a>Włącz dostęp do aplikacji klienta

Musisz umożliwić aplikacji klienckiej dostęp do bazy danych w SQL Database przez skonfigurowanie aplikacji Azure Active Directory (Azure AD) i skopiowanie *identyfikatora aplikacji* i *klucza* , które będą potrzebne do uwierzytelniania aplikacji.

Aby uzyskać *Identyfikator aplikacji* i *klucz*, wykonaj kroki opisane w temacie [Tworzenie Azure Active Directory aplikacji i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](../../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Tworzenie magazynu kluczy w celu przechowywania kluczy

Teraz, gdy aplikacja kliencka jest skonfigurowana i masz identyfikator aplikacji, można utworzyć magazyn kluczy i skonfigurować zasady dostępu, aby umożliwić aplikacji dostęp do wpisów tajnych magazynu (Always Encrypted kluczy). Uprawnienia *Create*, *Get*, *list*, *Sign*, *verify*, *wrapKey* i *unwrapKey* są wymagane do utworzenia nowego klucza głównego kolumny i konfigurowania szyfrowania przy użyciu SQL Server Management Studio.

Możesz szybko utworzyć magazyn kluczy, uruchamiając Poniższy skrypt. Aby uzyskać szczegółowy opis tych poleceń i więcej informacji na temat tworzenia i konfigurowania magazynu kluczy, zobacz [co to jest Azure Key Vault?](../../key-vault/general/overview.md).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Moduł programu PowerShell Azure Resource Manager (RM) jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Moduł AzureRM będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020.  Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Aby uzyskać więcej informacji o zgodności, zobacz [wprowadzenie do nowego Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

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
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your database in Azure SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --spn $applicationId
```

---

## <a name="connect-with-ssms"></a>Nawiązywanie połączenia z programem SSMS

Otwórz program SQL Server Management Studio (SSMS) i Połącz się z serwerem lub z bazą danych.

1. Otwórz program SSMS. (Przejdź do **połączenia**  >  **Aparat bazy danych** , aby otworzyć okno **łączenie z serwerem** , jeśli nie jest otwarty.

2. Wprowadź nazwę serwera lub nazwę wystąpienia oraz poświadczenia. 

    ![Kopiowanie parametrów połączenia](./media/always-encrypted-azure-key-vault-configure/ssms-connect.png)

Jeśli zostanie otwarte okno **Nowa reguła zapory** , zaloguj się do platformy Azure i zezwól PROGRAMowi ssmsemu na utworzenie nowej reguły zapory.

## <a name="create-a-table"></a>Tworzenie tabeli

W tej sekcji utworzysz tabelę do przechowywania danych pacjenta. Nie jest on początkowo zaszyfrowany — skonfigurujesz szyfrowanie w następnej sekcji.

1. Rozwiń węzeł **bazy danych**.
2. Kliknij prawym przyciskiem myszy bazę danych, a następnie kliknij pozycję **nowe zapytanie**.
3. Wklej następujący język Transact-SQL (T-SQL) do nowego okna zapytania i **Wykonaj** go.

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Szyfruj kolumny (Konfigurowanie Always Encrypted)

Program SSMS zawiera kreatora, który ułatwia konfigurowanie Always Encrypted przez skonfigurowanie klucza głównego kolumny, klucza szyfrowania kolumn i zaszyfrowanych kolumn.

1. Rozwiń **Databases** węzeł  >  **kliniki** baz danych  >  **Tables**.
2. Kliknij prawym przyciskiem myszy tabelę **pacjentów** i wybierz pozycję **Szyfruj kolumny** , aby otworzyć Kreatora Always Encrypted:

    ![Zrzut ekranu, który podświetla kolumny Szyfruj... opcja menu.](./media/always-encrypted-azure-key-vault-configure/encrypt-columns.png)

Kreator Always Encrypted zawiera następujące sekcje: **wybór kolumn**, **Konfiguracja klucza głównego**, **Walidacja** i **Podsumowanie**.

### <a name="column-selection"></a>Wybór kolumny

Kliknij przycisk **dalej** na stronie **wprowadzenie** , aby otworzyć stronę **wybór kolumny** . Na tej stronie można wybrać kolumny, które mają zostać zaszyfrowane, [typ szyfrowania i klucz szyfrowania kolumn (CEK)](/sql/relational-databases/security/encryption/always-encrypted-wizard#Anchor_2) , które mają być używane.

Szyfruj informacje o **PESEL** i **dataurodzeniu** dla każdego pacjenta. Kolumna SSN będzie używać deterministycznego szyfrowania, które obsługuje wyszukiwania równości, sprzężeń i grupowania. Kolumna dataurodzenie będzie używać losowego szyfrowania, które nie obsługuje operacji.

Ustaw **typ szyfrowania** dla kolumny SSN na wartość **deterministyczną** i kolumnę DataUrodzenia, która ma być **Losowa**. Kliknij przycisk **Dalej**.

![Szyfruj kolumny](./media/always-encrypted-azure-key-vault-configure/column-selection.png)

### <a name="master-key-configuration"></a>Konfiguracja klucza głównego

Na stronie **Konfiguracja klucza głównego** można skonfigurować CMK i wybrać dostawcę magazynu kluczy, w którym będzie przechowywany CMK. Obecnie można przechowywać CMK w magazynie certyfikatów systemu Windows, Azure Key Vault lub sprzętowym module zabezpieczeń (HSM).

W tym samouczku pokazano, jak przechowywać klucze w Azure Key Vault.

1. Wybierz **Azure Key Vault**.
2. Wybierz żądany Magazyn kluczy z listy rozwijanej.
3. Kliknij przycisk **Dalej**.

![Konfiguracja klucza głównego](./media/always-encrypted-azure-key-vault-configure/master-key-configuration.png)

### <a name="validation"></a>Walidacja

Możesz zaszyfrować kolumny teraz lub zapisać skrypt programu PowerShell, aby uruchomić go później. Na potrzeby tego samouczka wybierz pozycję **Przejdź do pozycji Zakończ teraz** , a następnie kliknij przycisk **dalej**.

### <a name="summary"></a>Podsumowanie

Sprawdź, czy ustawienia są poprawne, a następnie kliknij przycisk **Zakończ** , aby ukończyć konfigurację Always Encrypted.

![Zrzut ekranu przedstawia stronę wyników z zadaniami oznaczonymi jako zakończone.](./media/always-encrypted-azure-key-vault-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>Weryfikuj działania kreatora

Po zakończeniu działania kreatora baza danych jest skonfigurowana do Always Encrypted. Kreator wykonał następujące akcje:

- Utworzono klucz główny kolumny i zapisany w Azure Key Vault.
- Utworzono klucz szyfrowania kolumny i zapisany w Azure Key Vault.
- Skonfigurowano wybrane kolumny do szyfrowania. Tabela pacjentów nie ma obecnie żadnych danych, ale wszystkie istniejące dane w wybranych kolumnach są teraz szyfrowane.

Aby sprawdzić, jak utworzyć klucze w programie SSMS, można zwiększyć **Clinic**  >  **bezpieczeństwo**  >  **Always Encrypted kluczy**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Tworzenie aplikacji klienckiej, która współpracuje z zaszyfrowanymi danymi

Teraz, gdy Always Encrypted jest skonfigurowany, można skompilować aplikację, która wykonuje operacje *wstawiania* i *wybiera* w zaszyfrowanej kolumnie.  

> [!IMPORTANT]
> Aplikacja musi używać obiektów [SqlParameter](/dotnet/api/system.data.sqlclient.sqlparameter) podczas przekazywania danych w postaci zwykłego tekstu do serwera przy użyciu kolumn Always Encrypted. Przekazanie wartości literałów bez używania obiektów SqlParameter spowoduje wyjątek.

1. Otwórz program Visual Studio i Utwórz nową **aplikację konsolową** w języku C# (visual Studio 2015 i starsze) lub **aplikację konsolową (.NET Framework)** (Visual Studio 2017 i nowsze). Upewnij się, że projekt jest ustawiony na **.NET Framework 4,6** lub nowszy.
2. Nadaj projektowi nazwę **AlwaysEncryptedConsoleAKVApp** i kliknij przycisk **OK**.
3. Zainstaluj następujące pakiety NuGet, przechodząc do **Narzędzia** Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera** pakietów.

Uruchom te dwa wiersze kodu w konsoli Menedżera pakietów:

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Zmodyfikuj parametry połączenia, aby włączyć Always Encrypted

W tej sekcji wyjaśniono, jak włączyć Always Encrypted w ciągu połączenia z bazą danych.

Aby włączyć Always Encrypted, należy dodać słowo kluczowe **Ustawienia szyfrowania kolumn** do parametrów połączenia i ustawić je na **włączone**.

Można ustawić tę wartość bezpośrednio w parametrach połączenia lub ustawić ją przy użyciu [SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder). Przykładowa aplikacja w następnej sekcji pokazuje, jak używać **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Włącz Always Encrypted w parametrach połączenia

Dodaj następujące słowo kluczowe do parametrów połączenia.

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Włącz Always Encrypted przy użyciu SqlConnectionStringBuilder

Poniższy kod pokazuje, jak włączyć Always Encrypted przez ustawienie [SqlConnectionStringBuilder. ColumnEncryptionSetting](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting) na [włączone](/dotnet/api/system.data.sqlclient.sqlconnectioncolumnencryptionsetting).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Rejestrowanie dostawcy Azure Key Vault
Poniższy kod przedstawia sposób rejestrowania dostawcy Azure Key Vault za pomocą sterownika ADO.NET.

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

## <a name="always-encrypted-sample-console-application"></a>Always Encrypted Przykładowa aplikacja konsolowa

W tym przykładzie pokazano, jak:

- Zmodyfikuj parametry połączenia, aby włączyć Always Encrypted.
- Zarejestruj Azure Key Vault jako dostawcę magazynu kluczy aplikacji.  
- Wstaw dane do zaszyfrowanych kolumn.
- Wybierz rekord, filtrując określoną wartość w zaszyfrowanej kolumnie.

Zastąp zawartość *program.cs* następującym kodem. Zastąp ciąg połączenia dla zmiennej Global connectionString w wierszu, który bezpośrednio poprzedza metodę Main prawidłowymi parametrami połączenia z Azure Portal. Jest to jedyna zmiana, którą trzeba wykonać w tym kodzie.

Uruchom aplikację, aby zobaczyć Always Encrypted w działaniu.

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

## <a name="verify-that-the-data-is-encrypted"></a>Sprawdzanie, czy dane są szyfrowane

Możesz szybko sprawdzić, czy rzeczywiste dane na serwerze są szyfrowane przez wykonywanie zapytań dotyczących danych pacjentów za pomocą programu SSMS (przy użyciu bieżącego połączenia, w którym **ustawienie szyfrowania kolumn** nie jest jeszcze włączone).

Uruchom następujące zapytanie w bazie danych kliniki.

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Można zobaczyć, że zaszyfrowane kolumny nie zawierają żadnych danych w postaci zwykłego tekstu.

   ![Zrzut ekranu pokazujący, że zaszyfrowane kolumny nie zawierają żadnych danych w postaci zwykłego tekstu.](./media/always-encrypted-azure-key-vault-configure/ssms-encrypted.png)

Aby używać narzędzia SSMS do uzyskiwania dostępu do danych w postaci zwykłego tekstu, należy najpierw upewnić się, że użytkownik ma odpowiednie uprawnienia do Azure Key Vault: *Get*, *unwrapKey* i *verify*. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie i przechowywanie kluczy głównych kolumn (Always Encrypted)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted).

Następnie Dodaj *ustawienie szyfrowanie kolumn = włączone* parametr podczas połączenia.

1. W programie SSMS kliknij prawym przyciskiem myszy serwer w **Eksplorator obiektów** i wybierz polecenie **Rozłącz**.
2. Kliknij pozycję **Połącz**  >  **aparat bazy danych** , aby otworzyć okno **łączenie z serwerem** , a następnie kliknij przycisk **Opcje**.
3. Kliknij pozycję **dodatkowe parametry połączenia** i **ustawienie szyfrowania kolumny Typ = włączone**.

    ![Zrzut ekranu przedstawiający kartę dodatkowe parametry korekcji.](./media/always-encrypted-azure-key-vault-configure/ssms-connection-parameter.png)

4. Uruchom następujące zapytanie w bazie danych kliniki.

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

   Teraz można zobaczyć dane w postaci zwykłego tekstu w zaszyfrowanych kolumnach.
   
   ![Nowa aplikacja konsolowa](./media/always-encrypted-azure-key-vault-configure/ssms-plaintext.png)

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu bazy danych do korzystania z Always Encrypted można wykonać następujące czynności:

- [Obróć i wyczyść klucze](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio).
- [Migruj dane, które są już zaszyfrowane za pomocą Always Encrypted](/sql/relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted).

## <a name="related-information"></a>Informacje pokrewne

- [Always Encrypted (Programowanie klienta)](/sql/relational-databases/security/encryption/always-encrypted-client-development)
- [Przezroczyste szyfrowanie danych](/sql/relational-databases/security/encryption/transparent-data-encryption)
- [Szyfrowanie SQL Server](/sql/relational-databases/security/encryption/sql-server-encryption)
- [Kreator Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard)
- [Blog Always Encrypted](/archive/blogs/sqlsecurity/always-encrypted-key-metadata)
