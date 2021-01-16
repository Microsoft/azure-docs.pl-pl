---
title: 'Samouczek: wprowadzenie do Always Encrypted za pomocą bezpiecznego enclaves w Azure SQL Database'
description: W tym samouczku przedstawiono sposób tworzenia podstawowego środowiska dla Always Encrypted z bezpiecznym enclaves w Azure SQL Database oraz jak szyfrować dane w miejscu i wystawiać bogate poufne zapytania względem zaszyfrowanych kolumn za pomocą SQL Server Management Studio (SSMS).
keywords: Szyfruj dane, szyfrowanie SQL, szyfrowanie bazy danych, dane poufne, Always Encrypted, Secure enclaves, SGX, zaświadczanie
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 94923b13181290a290f13339da5b05f6fdddff38
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253738"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>Samouczek: wprowadzenie do Always Encrypted za pomocą bezpiecznego enclaves w Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted z opcją Secure enclaves for Azure SQL Database jest obecnie w **publicznej wersji zapoznawczej**.

W tym samouczku przedstawiono, jak rozpocząć pracę z [Always Encrypted przy użyciu bezpiecznego enclaves](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves) w Azure SQL Database. Zostanie wyświetlona:

> [!div class="checklist"]
> - Jak utworzyć środowisko do testowania i oceny Always Encrypted przy użyciu bezpiecznego enclaves.
> - Jak szyfrować dane w miejscu i wydawać bogate zapytania poufne w odniesieniu do zaszyfrowanych kolumn za pomocą SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek wymaga Azure PowerShell i programu [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="powershell-requirements"></a>Wymagania dotyczące programu PowerShell

Więcej informacji na temat instalowania i uruchamiania Azure PowerShell można znaleźć w temacie [omówienie Azure PowerShell](https://docs.microsoft.com/powershell/azure) . 

Minimalna wersja AZ modułów wymaganych do obsługi operacji zaświadczania:

- AZ 4.5.0
- AZ. Accounts 1.9.2
- AZ. zaświadczanie 0.1.8

Uruchom poniższe polecenie, aby sprawdzić zainstalowaną wersję wszystkich poleceń AZ module:

```powershell
Get-InstalledModule
```

Jeśli wersje nie są zgodne z minimalnym wymaganiem, uruchom `Update-Module` polecenie.

Galeria programu PowerShell ma przestarzałe wersje Transport Layer Security (TLS) 1,0 i 1,1. Zalecane jest szyfrowanie TLS 1,2 lub nowsza. Jeśli używasz wersji TLS niższej niż 1,2, mogą pojawić się następujące błędy:

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

Aby nadal korzystać z Galeria programu PowerShell, uruchom następujące polecenie przed poleceniami Install-Module

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>Wymagania programu SSMS

Zobacz [pobieranie SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) , aby uzyskać informacje na temat pobierania programu SSMS.

Wymagana minimalna wersja programu SSMS to 18,8.


## <a name="step-1-create-a-server-and-a-dc-series-database"></a>Krok 1. Tworzenie serwera i bazy danych z serii DC

 W tym kroku utworzysz nowy Azure SQL Database serwer logiczny i nową bazę danych przy użyciu konfiguracji sprzętu z serii DC. Always Encrypted z bezpiecznym enclaves w Azure SQL Database korzysta z technologii Intel SGX enclaves, które są obsługiwane w konfiguracji sprzętowej z serii DC. Aby uzyskać więcej informacji, zobacz [kontrolery domeny](service-tiers-vcore.md#dc-series).

1. Otwórz konsolę programu PowerShell i zaloguj się do platformy Azure. W razie potrzeby [Przejdź do subskrypcji](https://docs.microsoft.com/powershell/azure/manage-subscriptions-azureps) , która jest używana w tym samouczku.

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = <your subscription ID>
  Set-AzContext -Subscription $serverSubscriptionId
  ```

2. Utwórz grupę zasobów, która będzie zawierać serwer bazy danych. 

  ```powershell
  $serverResourceGroupName = "<server resource group name>"
  $serverLocation = "<Azure region that supports DC-series in SQL Database>"
  New-AzResourceGroup -Name $serverResourceGroupName -Location $serverLocation 
  ```

  > [!IMPORTANT]
  > Należy utworzyć grupę zasobów w regionie, który obsługuje konfigurację sprzętu z serii DC. Listę obecnie obsługiwanych regionów można znaleźć w temacie [dostępność z serii DC](service-tiers-vcore.md#dc-series-1).

3. Utwórz serwer bazy danych. Po wyświetleniu monitu wprowadź nazwę administratora serwera i hasło.

  ```powershell
  $serverName = "<server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -Location $serverLocation
  ```

4. Utwórz regułę zapory serwera, która zezwala na dostęp z określonego zakresu adresów IP
  
  ```powershell
  # The ip address range that you want to allow to access your server
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

5. Przypisz do serwera tożsamość systemu zarządzanego. Będzie ona potrzebna później, aby przyznać serwerowi dostęp do Microsoft Azure zaświadczania.

  ```powershell
  Set-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName -AssignIdentity 
  ```

6. Pobierz identyfikator obiektu tożsamości przypisanej do serwera. Zapisz otrzymany identyfikator obiektu. Potrzebujesz identyfikatora w dalszej części.

  > [!NOTE]
  > Propagowanie nowo przypisanej tożsamości systemu zarządzanego w Azure Active Directory może potrwać kilka sekund. Jeśli Poniższy skrypt zwróci pusty wynik, ponów próbę.

  ```PowerShell
  $server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 
  $serverObjectId = $server.Identity.PrincipalId
  $serverObjectId
  ```

7. Utwórz bazę danych serii DC.

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $serverResourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $edition -Vcore $vCore -ComputeGeneration $generation
  ```

## <a name="step-2-configure-an-attestation-provider"></a>Krok 2. Konfigurowanie dostawcy zaświadczania

W tym kroku utworzysz i skonfigurujesz dostawcę zaświadczania w Microsoft Azure zaświadczania. Jest to konieczne, aby zaświadczyć bezpieczną enklawy na serwerze bazy danych.

1. Skopiuj poniższe zasady zaświadczania i Zapisz zasady w pliku tekstowym (txt). Aby uzyskać informacje dotyczące poniższych zasad, zobacz [Tworzenie i Konfigurowanie dostawcy zaświadczania](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider).

  ```output
  version= 1.0;
  authorizationrules 
  {
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
  };
  ```

2. Zaimportuj wymagane wersje systemów `Az.Accounts` i `Az.Attestation` .  

  ```powershell
  Import-Module "Az.Accounts" -MinimumVersion "1.9.2"
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```

3. Utwórz grupę zasobów dla dostawcy zaświadczania.

  ```powershell
  $attestationLocation = $serverLocation
  $attestationResourceGroupName = "<attestation provider resource group name>"
  New-AzResourceGroup -Name $attestationResourceGroupName -Location $location  
  ```

4. Utwórz dostawcę zaświadczania. 

  ```powershell
  $attestationProviderName = "<attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Location $attestationLocation
  ```

5. Skonfiguruj zasady zaświadczania.
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName -Tee $teeType -Policy $policy -PolicyFormat  $policyFormat
  ```

6. Przyznaj serwerowi logicznemu usługi Azure SQL dostęp do swojego dostawcy zaświadczania. W tym kroku używamy identyfikatora obiektu tożsamości usługi zarządzanej, która została wcześniej przypisana do serwera.

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName  
  ```

7. Pobierz adres URL zaświadczania.

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host "Your attestation URL is: " $attestationUrl 
  ```

8.  Zapisz wynikający z tego adres URL zaświadczania, który wskazuje na zasady zaświadczania skonfigurowane dla SGX enklawy. Będzie on potrzebny później. Adres URL zaświadczania powinien wyglądać następująco: `https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave`

## <a name="step-3-populate-your-database"></a>Krok 3. wypełnienie bazy danych

W tym kroku utworzysz tabelę i wypełnisz ją danymi, które później będą szyfrowane i zapytania.

1. Otwórz program SSMS i nawiąż połączenie z bazą danych **ContosoHR** na serwerze logicznym usługi Azure SQL utworzonym **bez** Always Encrypted włączonej w połączeniu z bazą danych.
    1. W oknie dialogowym **łączenie z serwerem** Podaj nazwę serwera (na przykład *myserver123.Database.Windows.NET*), a następnie wprowadź nazwę użytkownika i hasło, które zostały wcześniej skonfigurowane.
    2. Kliknij pozycję **opcje >>** a następnie wybierz kartę **Właściwości połączenia** . Upewnij się, że została wybrana baza danych **ContosoHR** (nie domyślna, baza danych Master). 
    3. Wybierz kartę **Always Encrypted** .
    4. Upewnij się, że pole wyboru **włącz Always Encrypted (szyfrowanie kolumn)** **nie** jest zaznaczone.

        ![Połącz bez Always Encrypted](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

    5. Kliknij przycisk **Połącz**.

2. Utwórz nową tabelę o nazwie **Employees**.

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. Dodaj kilka rekordów pracowników do tabeli **Employees** .

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```


## <a name="step-4-provision-enclave-enabled-keys"></a>Krok 4. Udostępnianie kluczy włączonych przez enklawy

W tym kroku utworzysz klucz główny kolumny i klucz szyfrowania kolumny zezwalające na obliczenia enklawy.

1. Używając wystąpienia programu SSMS z poprzedniego kroku, w **Eksplorator obiektów** rozwiń bazę danych i przejdź do opcji Always Encrypted **zabezpieczeń**  >  **klucze**.
1. Zainicjuj obsługę nowego klucza głównego kolumny z obsługą enklawy:
    1. Kliknij prawym przyciskiem myszy pozycję **klucze Always Encrypted** i wybierz pozycję **nowy klucz główny kolumny..**..
    2. Wybierz nazwę klucza głównego kolumny: **CMK1**.
    3. Upewnij się, że wybrano pozycję **Magazyn certyfikatów systemu Windows (bieżący użytkownik lub komputer lokalny)** lub **Azure Key Vault**.
    4. Wybierz pozycję **Zezwalaj na obliczenia enklawy**.
    5. W przypadku wybrania Azure Key Vault Zaloguj się do platformy Azure i wybierz swój magazyn kluczy. Aby uzyskać więcej informacji na temat tworzenia magazynu kluczy dla Always Encrypted, zobacz [Zarządzanie magazynami kluczy z Azure Portal](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal).
    6. Wybierz certyfikat lub klucz usługi Azure key value, jeśli już istnieje, lub kliknij przycisk **Generuj certyfikat** , aby utworzyć nowy.
    7. Wybierz przycisk **OK**.

        ![Zezwalaj na obliczenia enklawy](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. Utwórz nowy klucz szyfrowania kolumny z obsługą enklawy:

    1. Kliknij prawym przyciskiem myszy pozycję **klucze Always Encrypted** i wybierz pozycję **nowy klucz szyfrowania kolumny**.
    2. Wprowadź nazwę nowej kolumny klucz szyfrowania: **CEK1**.
    3. Z listy rozwijanej **klucz główny kolumny** wybierz klucz główny kolumny utworzony w poprzednich krokach.
    4. Wybierz przycisk **OK**.

## <a name="step-5-encrypt-some-columns-in-place"></a>Krok 5. zaszyfrowanie niektórych kolumn w miejscu

W tym kroku zostaną zaszyfrowane dane przechowywane w kolumnach **SSN** i **pensje** wewnątrz enklawy po stronie serwera, a następnie przetestowanie zapytania SELECT na danych.

1. Otwórz nowe wystąpienie programu SSMS i Połącz się z bazą danych **przy użyciu** Always Encrypted włączonej dla połączenia z bazą danych.
    1. Rozpocznij nowe wystąpienie programu SSMS.
    2. W oknie dialogowym **łączenie z serwerem** Określ nazwę serwera, wybierz metodę uwierzytelniania i podaj swoje poświadczenia.
    3. Kliknij pozycję **opcje >>** a następnie wybierz kartę **Właściwości połączenia** . Upewnij się, że została wybrana baza danych **ContosoHR** (nie domyślna, baza danych Master). 
    4. Wybierz kartę **Always Encrypted** .
    5. Upewnij się, że pole wyboru **włącz Always Encrypted (szyfrowanie kolumn)** jest zaznaczone.
    6. Określ adres URL zaświadczania enklawy, który został uzyskany, wykonując kroki opisane w [sekcji Krok 2. Konfigurowanie dostawcy zaświadczania](#step-2-configure-an-attestation-provider). Zobacz poniższy zrzut ekranu.

        ![Połącz z zaświadczeniem](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

    7. Wybierz pozycję **Połącz**.
    8. Jeśli zostanie wyświetlony monit o włączenie parametryzacja dla zapytań Always Encrypted, wybierz pozycję **Włącz**.



1. Korzystając z tego samego wystąpienia programu SSMS (z włączonym Always Encrypted), Otwórz nowe okno zapytania i Zaszyfruj kolumny **SSN** i **zarobki** , uruchamiając poniższe instrukcje.

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > Zwróć uwagę, że instrukcja ALTER DATABASE SCOPEd CONFIGURATION wyczyszczona PROCEDURE_CACHE, aby wyczyścić pamięć podręczną planu zapytania dla bazy danych w powyższym skrypcie. Po zmodyfikowaniu tabeli należy wyczyścić plany wszystkich partii i procedur składowanych, które uzyskują dostęp do tabeli w celu odświeżenia informacji o szyfrowaniu parametrów. 

1. Aby sprawdzić, czy kolumny **SSN** i **zarobki** są teraz szyfrowane, Otwórz nowe okno zapytania w wystąpieniu programu SSMS **bez** Always Encrypted włączonego dla połączenia z bazą danych i wykonaj poniższą instrukcję. Okno zapytania powinno zwracać wartości zaszyfrowane w kolumnach **SSN** i **pensje** . W przypadku wykonania tego samego zapytania przy użyciu wystąpienia programu SSMS z włączonym Always Encrypted powinny być widoczne odszyfrowane dane.

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>Krok 6. wykonywanie bogatych zapytań w odniesieniu do zaszyfrowanych kolumn

Można uruchamiać zaawansowane zapytania względem zaszyfrowanych kolumn. Niektóre przetwarzanie zapytań zostanie wykonane wewnątrz enklawy po stronie serwera. 

1. W wystąpieniu programu SSMS **z** włączonym Always Encrypted upewnij się, że jest również włączona funkcja parametryzacja dla Always Encrypted.
    1. Wybierz pozycję **Narzędzia** z menu głównego programu SSMS.
    2. Wybierz **Opcje...**.
    3. Przejdź do **SQL Server wykonywania zapytania**  >    >  **zaawansowanego**.
    4. Upewnij się, że jest zaznaczone pole wyboru **Włącz parametryzacja dla Always Encrypted** .
    5. Wybierz przycisk **OK**.
2. Otwórz nowe okno zapytania, wklej je w poniższym zapytaniu i wykonaj. Zapytanie powinno zwracać wartości w postaci zwykłego tekstu i wiersze spełniające określone kryteria wyszukiwania.

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. Spróbuj ponownie wykonać to samo zapytanie w wystąpieniu programu SSMS, dla którego nie włączono Always Encrypted. Wystąpił błąd.
 
## <a name="next-steps"></a>Następne kroki

Po ukończeniu tego samouczka możesz przejść do jednego z następujących samouczków:
- [Samouczek: opracowywanie aplikacji platformy .NET przy użyciu Always Encrypted z zabezpieczeniami enclaves](https://docs.microsoft.com/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [Samouczek: opracowywanie aplikacji .NET Framework przy użyciu Always Encrypted z bezpiecznym enclaves](https://docs.microsoft.com/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [Samouczek: Tworzenie i używanie indeksów w kolumnach z obsługą enklawy przy użyciu szyfrowania losowego](https://docs.microsoft.com/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>Zobacz też

- [Konfigurowanie i używanie Always Encrypted z zabezpieczeniami enclaves](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)