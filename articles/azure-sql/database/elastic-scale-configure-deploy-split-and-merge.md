---
title: Wdrażanie usługi z podziałem i scalaniem
description: Aby przenieść dane między bazami danych podzielonej na fragmenty, użyj zbyt Split-Merge.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 71aad7699c5af6ce2a1b9d82a340138200cfb5e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792076"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Wdrażanie usługi Split-Merge do przenoszenia danych między bazami danych podzielonej na fragmenty
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Narzędzie Split-Merge służy do przenoszenia danych między bazami danych podzielonej na fragmenty. Zobacz [przeniesienie danych między skalowanymi bazami danych w chmurze](elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Pobierz pakiety Split-Merge

1. Pobierz najnowszą wersję pakietu NuGet z narzędzia [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

1. Otwórz wiersz polecenia i przejdź do katalogu, do którego został pobrany nuget.exe. Pobieranie obejmuje polecenia programu PowerShell.

1. Pobierz najnowszy pakiet Split-Merge do bieżącego katalogu przy użyciu poniższego polecenia:

   ```cmd
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Pliki są umieszczane w katalogu o nazwie **Microsoft. Azure. SQLDatabase. ElasticScale. Service. SplitMerge. x. x. xxx. x** , gdzie *x. x. xxx. x* odzwierciedla numer wersji. Znajdź pliki usługi Split-Merge w podkatalogu **content\splitmerge\service** oraz skrypty Split-Merge PowerShell (i wymagane biblioteki DLL klienta) w podkatalogu **content\splitmerge\powershell** .

## <a name="prerequisites"></a>Wymagania wstępne

1. Utwórz bazę danych Azure SQL Database, która będzie używana jako baza danych stanu Split-Merge. Przejdź do witryny [Azure Portal](https://portal.azure.com). Utwórz nowy **SQL Database**. Nadaj bazie danych nazwę i Utwórz nowego administratora i hasło. Pamiętaj, aby zapisać nazwę i hasło do późniejszego użycia.

1. Upewnij się, że serwer zezwala na nawiązywanie połączeń z usługą Azure. W portalu w **ustawieniach zapory** upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest ustawione na wartość **włączone**. Kliknij ikonę "Save" (Zapisz).

1. Utwórz konto usługi Azure Storage na potrzeby danych wyjściowych diagnostyki.

1. Utwórz usługę w chmurze platformy Azure dla usługi Split-Merge.

## <a name="configure-your-split-merge-service"></a>Konfigurowanie usługi Split-Merge

### <a name="split-merge-service-configuration"></a>Konfiguracja usługi Split-Merge

1. W folderze, do którego pobrano zestawy Split-Merge, Utwórz kopię pliku *ServiceConfiguration. Template. cscfg* , który został wysłany wraz z *SplitMergeService. cspkg* , i zmień jego nazwę na *ServiceConfiguration. cscfg*.

1. Otwórz element *ServiceConfiguration. cscfg* w edytorze tekstu, takim jak program Visual Studio, który sprawdza poprawność danych wejściowych, takich jak format odcisków palców certyfikatów.

1. Utwórz nową bazę danych lub wybierz istniejącą bazę danych, która będzie działać jako baza danych stanu dla operacji Split-Merge i Pobierz parametry połączenia tej bazy danych.

   > [!IMPORTANT]
   > W tej chwili baza danych stanu musi używać sortowania łacińskiego (SQL \_ Latin1 \_ General \_ CP1 \_ Ci \_ AS). Aby uzyskać więcej informacji, zobacz [Nazwa sortowania systemu Windows (Transact-SQL)](/sql/t-sql/statements/windows-collation-name-transact-sql).

   W przypadku Azure SQL Database parametry połączenia zwykle mają postać:

      `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<userId>; Password=<password>; Encrypt=True; Connection Timeout=30`

1. Wprowadź te parametry połączenia w pliku *cscfg* w sekcjach role **SplitMergeWeb** i **SplitMergeWorker** w ustawieniu ElasticScaleMetadata.

1. Dla roli **SplitMergeWorker** wprowadź prawidłowe parametry połączenia do usługi Azure Storage dla ustawienia **WorkerRoleSynchronizationStorageAccountConnectionString** .

### <a name="configure-security"></a>Konfigurowanie zabezpieczeń

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania zabezpieczeń usługi, zapoznaj się z [konfiguracją zabezpieczeń Split-Merge](elastic-scale-split-merge-security-configuration.md).

Na potrzeby prostego wdrożenia testowego w ramach tego samouczka zostanie wykonany minimalny zestaw kroków konfiguracyjnych umożliwiający uruchomienie usługi. W tych krokach można włączyć tylko jeden komputer/konto do komunikowania się z usługą.

### <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Utwórz nowy katalog i z tego katalogu wykonaj następujące polecenie przy użyciu okna [wiersz polecenia dla deweloperów dla programu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs) :

   ```cmd
   makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Zostanie wyświetlony monit o podanie hasła w celu ochrony klucza prywatnego. Wprowadź silne hasło i potwierdź je. Następnie zostanie wyświetlony monit o podanie hasła, które będzie później używane. Kliknij przycisk **tak** na końcu, aby zaimportować go do głównego magazynu zaufanych urzędów certyfikacji.

### <a name="create-a-pfx-file"></a>Utwórz plik PFX

Wykonaj następujące polecenie w tym samym oknie, w którym zostało wykonane Makecert; Użyj tego samego hasła, które zostało użyte do utworzenia certyfikatu:

   ```cmd
   pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>
   ```

### <a name="import-the-client-certificate-into-the-personal-store"></a>Zaimportuj certyfikat klienta do magazynu osobistego

1. W Eksploratorze Windows kliknij dwukrotnie plik *. pfx*.
2. W **Kreatorze importu certyfikatów** wybierz pozycję **bieżący użytkownik** , a następnie kliknij przycisk **dalej**.
3. Potwierdź ścieżkę pliku i kliknij przycisk **dalej**.
4. Wpisz hasło, pozostaw zaznaczone pole wyboru **Dołącz wszystkie rozszerzone właściwości** , a następnie kliknij przycisk **dalej**.
5. Pozostaw zaznaczone pole wyboru **Magazyn certyfikatów [...]** , a następnie kliknij przycisk **dalej**.
6. Kliknij przycisk **Zakończ** i **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Przekaż plik PFX do usługi w chmurze

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz **Cloud Services**.
3. Wybierz usługę w chmurze utworzoną powyżej dla usługi Split/Merge.
4. Kliknij pozycję **Certyfikaty** w górnym menu.
5. Kliknij przycisk **Przekaż** na dolnym pasku.
6. Wybierz plik PFX i wprowadź takie samo hasło jak powyżej.
7. Po zakończeniu Skopiuj odcisk palca certyfikatu z nowej pozycji na liście.

### <a name="update-the-service-configuration-file"></a>Aktualizowanie pliku konfiguracji usługi

Wklej odcisk palca certyfikatu skopiowany powyżej do atrybutu odcisku palca/wartości tych ustawień.
Dla roli proces roboczy:

   ```xml
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Dla roli sieci Web:

   ```xml
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Należy pamiętać, że w przypadku wdrożeń produkcyjnych dla urzędu certyfikacji należy używać oddzielnych certyfikatów do szyfrowania, certyfikatu serwera i certyfikatów klienta. Aby uzyskać szczegółowe instrukcje na ten temat, zobacz [Konfiguracja zabezpieczeń](elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Wdrażanie usługi

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz utworzoną wcześniej usługę w chmurze.
3. Kliknij pozycję **Przegląd**.
4. Wybierz środowisko przejściowe, a następnie kliknij pozycję **Przekaż**.
5. W oknie dialogowym Wprowadź etykietę wdrożenia. Dla opcji "Package" i "Configuration" kliknij pozycję "from local" i wybierz plik *SplitMergeService. cspkg* i plik cscfg, który został wcześniej skonfigurowany.
6. Upewnij się, że pole wyboru z etykietą **Wdróż, nawet jeśli co najmniej jedna rola zawiera pojedyncze wystąpienie** jest zaznaczone.
7. Naciśnij przycisk takt w prawym dolnym rogu, aby rozpocząć wdrażanie. Należy spodziewać się, że ukończenie nie potrwa kilka minut.

## <a name="troubleshoot-the-deployment"></a>Rozwiązywanie problemów z wdrożeniem

Jeśli rola sieci Web nie zostanie przełączona w tryb online, prawdopodobnie wystąpił problem z konfiguracją zabezpieczeń. Sprawdź, czy protokół TLS/SSL jest skonfigurowany zgodnie z powyższym opisem.

Jeśli rola procesu roboczego nie zostanie przełączona w tryb online, ale rola sieci Web powiedzie się, prawdopodobnie wystąpił problem z połączeniem z utworzoną wcześniej bazą danych stanu.

- Upewnij się, że parametry połączenia w pliku cscfg są dokładne.
- Sprawdź, czy serwer i baza danych istnieją oraz czy identyfikator użytkownika i hasło są poprawne.
- W przypadku Azure SQL Database parametry połączenia powinny mieć postać:

   `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<user>; Password=<password>; Encrypt=True; Connection Timeout=30`

- Upewnij się, że nazwa serwera nie zaczyna się od **https://**.
- Upewnij się, że serwer zezwala na nawiązywanie połączeń z usługą Azure. Aby to zrobić, Otwórz bazę danych w portalu i upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest ustawione na * * na * * * *.

## <a name="test-the-service-deployment"></a>Testowanie wdrożenia usługi

### <a name="connect-with-a-web-browser"></a>Nawiązywanie połączenia za pomocą przeglądarki sieci Web

Określ punkt końcowy sieci Web usługi Split-Merge. Można to znaleźć w portalu, przechodząc do **omówienia** usługi w chmurze i sprawdzając **adres URL witryny** po prawej stronie. Zastąp **http://** z **https://** , ponieważ domyślne ustawienia zabezpieczeń wyłączają punkt końcowy HTTP. Załaduj stronę dla tego adresu URL do przeglądarki.

### <a name="test-with-powershell-scripts"></a>Testowanie za pomocą skryptów programu PowerShell

Wdrożenie i środowisko można sprawdzić, uruchamiając dołączone przykładowe skrypty programu PowerShell.

> [!IMPORTANT]
> Przykładowe skrypty są uruchamiane w programie PowerShell 5,1. Nie są one obecnie uruchamiane w programie PowerShell 6 lub nowszym.

Uwzględnione pliki skryptów:

1. *SetupSampleSplitMergeEnvironment.ps1* — konfiguruje warstwę danych testowych dla operacji Split/Merge (zobacz tabelę poniżej, aby uzyskać szczegółowy opis)
2. *ExecuteSampleSplitMerge.ps1* — wykonuje operacje testowe w warstwie danych testowych (patrz tabela poniżej w celu uzyskania szczegółowego opisu)
3. Przykładowy skrypt *GetMappings.ps1* -najwyższego poziomu, który drukuje bieżący stan mapowań fragmentu.
4. *ShardManagement. PSM1*  — skrypt pomocniczy, który otacza interfejs API ShardManagement
5. *SqlDatabaseHelpers. PSM1* — skrypt pomocnika służący do tworzenia baz danych i zarządzania nimi w SQL Database

   <table style="width:100%">
     <tr>
       <th>Plik programu PowerShell</th>
       <th>Kroki</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1. Tworzy bazę danych Menedżera map fragmentu</td>
     </tr>
     <tr>
       <td>2. Tworzy 2 bazy danych fragmentu.
     </tr>
     <tr>
       <td>3. Tworzy mapę fragmentu dla tych baz danych (usuwa wszystkie istniejące mapy fragmentu w tych bazach danych). </td>
     </tr>
     <tr>
       <td>4. Tworzy małą przykładową tabelę w fragmentów i wypełnia tabelę w jednym z fragmentów.</td>
     </tr>
     <tr>
       <td>5. Deklaruje SchemaInfo dla tabeli podzielonej na fragmenty.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Plik programu PowerShell</th>
       <th>Kroki</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1. Wysyła żądanie Split do frontonu sieci Web usługi Split-Merge, który dzieli dane z pierwszego fragmentuu na drugi fragmentu.</td>
     </tr>
     <tr>
       <td>2. Sonduje fronton sieci Web pod kątem stanu żądania podziału i czeka na zakończenie żądania.</td>
     </tr>
     <tr>
       <td>3. Wysyła żądanie scalania do frontonu sieci Web usługi Split-Merge, który przenosi dane z drugiej fragmentu z powrotem do pierwszej fragmentu.</td>
     </tr>
     <tr>
       <td>4. Sonduje fronton sieci Web pod kątem stanu żądania scalania i czeka na zakończenie żądania.</td>
     </tr>
   </table>

## <a name="use-powershell-to-verify-your-deployment"></a>Weryfikowanie wdrożenia przy użyciu programu PowerShell

1. Otwórz nowe okno programu PowerShell i przejdź do katalogu, do którego pobrano pakiet Split-Merge, a następnie przejdź do katalogu "PowerShell".

2. Utwórz serwer (lub wybierz istniejący serwer), w którym zostanie utworzony Menedżer map fragmentu i fragmentów.

   > [!NOTE]
   > Skrypt *SetupSampleSplitMergeEnvironment.ps1* tworzy wszystkie te bazy danych na tym samym serwerze Domyślnie, aby zachować prosty skrypt. Nie jest to ograniczenie usługi Split-Merge.

   Aby usługa Split-Merge mogła przenosić dane i aktualizować mapę fragmentu, konieczne będzie logowanie za pomocą uwierzytelniania SQL z dostępem do odczytu i zapisu do baz danych. Ponieważ usługa Split-Merge jest uruchomiona w chmurze, nie obsługuje obecnie uwierzytelniania zintegrowanego.

   Upewnij się, że serwer jest skonfigurowany tak, aby zezwalał na dostęp z adresu IP komputera, na którym działają te skrypty. To ustawienie można znaleźć w obszarze serwer SQL/zapory oraz sieci wirtualne/adresy IP klientów.

3. Wykonaj skrypt *SetupSampleSplitMergeEnvironment.ps1* , aby utworzyć przykładowe środowisko.

   Uruchomienie tego skryptu spowoduje wyczyszczenie wszelkich istniejących struktur danych zarządzania mapy fragmentu w bazie danych Menedżera mapy fragmentu i fragmentów. Może być przydatne ponowne uruchomienie skryptu, jeśli chcesz ponownie zainicjować mapę fragmentu lub fragmentów.

   Przykładowy wiersz polecenia:

   ```cmd
   .\SetupSampleSplitMergeEnvironment.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

4. Wykonaj skrypt Getmappings.ps1, aby wyświetlić mapowania, które aktualnie istnieją w przykładowym środowisku.

   ```cmd
   .\GetMappings.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

5. Wykonaj skrypt *ExecuteSampleSplitMerge.ps1* , aby wykonać operację Split (przeniesienie połowy danych z pierwszego fragmentu do drugiego fragmentu), a następnie operację scalania (przeniesienie danych z powrotem na pierwsze fragmentu). W przypadku skonfigurowania protokołu TLS i pozostawienia wyłączonego punktu końcowego http upewnij się, że zamiast tego używasz punktu końcowego https://.

    Przykładowy wiersz polecenia:

    ```cmd
    .\ExecuteSampleSplitMerge.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd'
    -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net'
    -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
    ```

    Jeśli wystąpi Poniższy błąd, prawdopodobnie wystąpił problem z certyfikatem punktu końcowego sieci Web. Spróbuj połączyć się z punktem końcowym sieci Web za pomocą ulubionej przeglądarki sieci Web i sprawdź, czy wystąpił błąd certyfikatu.

    `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.`

    Jeśli zakończyło się pomyślnie, dane wyjściowe powinny wyglądać następująco:

    ```output
    > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
    > Sending split request
    > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
    > Polling split-merge request status. Press Ctrl-C to end
    > Progress: 0% | Status: Queued | Details: [Informational] Queued request
    > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
    > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
    > ...
    > ...
    > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
    > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
    > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
    > Sending merge request
    > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
    > Polling request status. Press Ctrl-C to end
    > Progress: 0% | Status: Queued | Details: [Informational] Queued request
    > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
    > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
    > ...
    > ...
    > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
    > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
    > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
    >
    ```

6. Eksperymentuj z innymi typami danych! Wszystkie te skrypty pobierają opcjonalny parametr-ShardKeyType, który umożliwia określenie typu klucza. Wartość domyślna to Int32, ale można również określić Int64, GUID lub Binary.

## <a name="create-requests"></a>Utwórz żądania

Usługi można użyć przy użyciu interfejsu użytkownika sieci Web lub przez zaimportowanie i użycie modułu programu PowerShell SplitMerge. PSM1, który prześle żądania za pośrednictwem roli sieci Web.

Usługa może przenosić dane zarówno w tabelach podzielonej na fragmenty, jak i tabelach referencyjnych. Tabela podzielonej na fragmenty ma kolumnę klucza fragmentowania i ma inne dane wierszy w każdej fragmentu. Tabela referencyjna nie jest podzielonej na fragmenty, więc zawiera te same dane wierszy w każdym fragmentu. Tabele referencyjne są przydatne w przypadku danych, które nie zmieniają się często i są używane do łączenia się z tabelami podzielonej na fragmenty w zapytaniach.

Aby wykonać operację Split-Merge, należy zadeklarować tabele podzielonej na fragmenty i tabele odwołań, które mają zostać przeniesione. Jest to realizowane za pomocą interfejsu API **SchemaInfo** . Ten interfejs API znajduje się w przestrzeni nazw **Microsoft. Azure. SQLDatabase. ElasticScale. ShardManagement. Schema** .

1. Dla każdej tabeli podzielonej na fragmenty Utwórz obiekt **ShardedTableInfo** opisujący nazwę schematu nadrzędnego tabeli (opcjonalnie, wartość domyślną "dbo"), nazwę tabeli i nazwę kolumny w tej tabeli, która zawiera klucz fragmentowania.
2. Dla każdej tabeli referencyjnej Utwórz obiekt **ReferenceTableInfo** opisujący nazwę schematu nadrzędnego tabeli (opcjonalnie, wartość domyślną "dbo") i nazwę tabeli.
3. Dodaj powyższe obiekty TableInfo do nowego obiektu **SchemaInfo** .
4. Pobierz odwołanie do obiektu **ShardMapManager** i Wywołaj **GetSchemaInfoCollection**.
5. Dodaj **SchemaInfo** do **SchemaInfoCollection**, podając nazwę mapy fragmentu.

Przykład tego elementu można zobaczyć w skrypcie SetupSampleSplitMergeEnvironment.ps1.

Usługa Split-Merge nie tworzy docelowej bazy danych (lub schematu dla dowolnych tabel w bazie danych). Muszą być wstępnie utworzone przed wysłaniem żądania do usługi.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Podczas uruchamiania przykładowych skryptów programu PowerShell może zostać wyświetlony następujący komunikat:

   `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

Ten błąd oznacza, że certyfikat TLS/SSL nie jest prawidłowo skonfigurowany. Postępuj zgodnie z instrukcjami w sekcji "łączenie się z przeglądarką sieci Web".

Jeśli nie możesz przesłać żądań, możesz je zobaczyć:

   `[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'.`

W takim przypadku sprawdź plik konfiguracji, w szczególności ustawienia dla **WorkerRoleSynchronizationStorageAccountConnectionString**. Ten błąd zazwyczaj wskazuje, że rola proces roboczy nie może pomyślnie zainicjować bazy danych metadanych przy pierwszym użyciu.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png