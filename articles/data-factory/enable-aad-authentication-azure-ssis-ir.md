---
title: Włącz usługę AAD dla środowiska wykonawczego integracji usługi Azure SSIS
description: W tym artykule opisano sposób włączania uwierzytelniania usługi Azure Active Directory przy użyciu tożsamości zarządzanej dla usługi Azure Data Factory w celu utworzenia środowiska wykonawczego integracji platformy Azure-SSIS.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 5/14/2019
ms.openlocfilehash: 2359b378b1f54cf6e03218f819b3a7c5740ba596
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416402"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Enable Azure Active Directory authentication for Azure-SSIS Integration Runtime (Włączanie uwierzytelniania usługi Azure Active Directory dla środowiska Azure-SSIS Integration Runtime)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule pokazano, jak włączyć uwierzytelnianie usługi Azure Active Directory (Azure AD) przy użyciu tożsamości zarządzanej dla usługi Azure Data Factory (ADF) i używać go zamiast konwencjonalnych metod uwierzytelniania (takich jak uwierzytelnianie SQL) do:

- Utwórz środowisko uruchomieniowe integracji platformy Azure-SSIS (IR), które z kolei aprowizują bazę danych katalogu SSIS (SSISDB) w usłudze Azure SQL Database server/Managed Instance w Twoim imieniu.

- Połącz się z różnymi zasobami platformy Azure podczas uruchamiania pakietów SSIS na platformie Azure-SSIS IR.

Aby uzyskać więcej informacji na temat tożsamości zarządzanej podajnika ADF, zobacz [Tożsamość zarządzana dla fabryki danych](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity).

> [!NOTE]
>-  W tym scenariuszu uwierzytelnianie usługi Azure AD z tożsamością zarządzaną dla usługi ADF jest używane tylko w tworzeniu i kolejnych operacjach uruchamiania usługi SSIS IR, które z kolei będą aprowizować i łączyć się z usługą SSISDB. W przypadku wykonywania pakietów SSIS usługa SSIS IR będzie nadal łączyć się z usługą SSISDB przy użyciu uwierzytelniania SQL przy użyciu w pełni zarządzanych kont utworzonych podczas inicjowania obsługi administracyjnej usługi SSISDB.
>-  Jeśli utworzono już usługę SSIS IR przy użyciu uwierzytelniania SQL, nie można go ponownie skonfigurować do używania uwierzytelniania usługi Azure AD za pośrednictwem programu PowerShell w tej chwili, ale można to zrobić za pośrednictwem aplikacji Azure portal/ADF. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-on-azure-sql-database"></a>Włączanie usługi Azure AD w bazie danych SQL platformy Azure

Serwer bazy danych SQL platformy Azure obsługuje tworzenie bazy danych z użytkownikiem usługi Azure AD. Najpierw należy utworzyć grupę usługi Azure AD z tożsamością zarządzaną dla podajnika ADF jako członka. Następnie należy ustawić użytkownika usługi Azure AD jako administratora usługi Active Directory dla serwera usługi Azure SQL Database, a następnie połączyć się z nim w programie SQL Server Management Studio (SSMS) przy użyciu tego użytkownika. Na koniec należy utworzyć zamkniętego użytkownika reprezentującego grupę usługi Azure AD, więc tożsamość zarządzana dla usługi ADF może być używana przez usługę Azure-SSIS IR do tworzenia usługi SSISDB w Twoim imieniu.

### <a name="create-an-azure-ad-group-with-the-managed-identity-for-your-adf-as-a-member"></a>Tworzenie grupy usługi Azure AD z tożsamością zarządzaną dla podajnika ADF jako element członkowski

Można użyć istniejącej grupy usługi Azure AD lub utworzyć nową przy użyciu programu Azure AD PowerShell.

1.  Zainstaluj moduł [programu Azure AD PowerShell.](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)

2.  Zaloguj się `Connect-AzureAD`za pomocą polecenia cmdlet, aby utworzyć grupę, i zapisać ją w zmiennej:

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    Wynik wygląda jak w poniższym przykładzie, który również wyświetla wartość zmiennej:

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Dodaj do grupy tożsamość zarządzaną podajnika ADF. Możesz korzystać z artykułu [Tożsamość zarządzana dla fabryki danych,](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) aby uzyskać identyfikator obiektu tożsamości zarządzanej (np. 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc, ale nie używaj w tym celu identyfikatora aplikacji tożsamości zarządzanej).

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    Możesz również sprawdzić członkostwo w grupie później.

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database-server"></a>Konfigurowanie uwierzytelniania usługi Azure AD dla serwera bazy danych SQL platformy Azure

 [Uwierzytelnianie usługi Azure AD można skonfigurować i zarządzać nimi za pomocą](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure) języka SQL, wykonując następujące czynności:

1.  W witrynie Azure portal wybierz **wszystkie usługi** -> **serwerów SQL** z nawigacji po lewej stronie.

2.  Wybierz serwer bazy danych SQL platformy Azure, który ma być skonfigurowany przy użyciu uwierzytelniania usługi Azure AD.

3.  W sekcji **Ustawienia** bloku wybierz pozycję **Administrator usługi Active Directory**.

4.  Na pasku poleceń wybierz pozycję **Ustaw administratora**.

5.  Wybierz konto użytkownika usługi Azure AD, które ma zostać administratorem serwera, a następnie wybierz pozycję **Wybierz.**

6.  Na pasku poleceń wybierz pozycję **Zapisz.**

### <a name="create-a-contained-user-in-azure-sql-database-server-representing-the-azure-ad-group"></a>Tworzenie zamkniętego użytkownika na serwerze bazy danych SQL platformy Azure reprezentującym grupę usługi Azure AD

W tym następnym kroku potrzebny jest program [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1. Uruchom SSMS.

2. W oknie dialogowym **Połącz z serwerem** wprowadź nazwę serwera bazy danych SQL platformy Azure w polu **Nazwa serwera.**

3. W polu **Uwierzytelnianie** wybierz pozycję **Active Directory — uniwersalne z obsługą usługi MFA** (można również użyć pozostałych dwóch typów uwierzytelniania usługi Active Directory, zobacz [Konfigurowanie uwierzytelniania usługi Azure AD za pomocą języka SQL i zarządzanie nim).](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

4. W polu **Nazwa użytkownika** wprowadź nazwę konta usługi Azure AD ustawionego jako testuser@xxxonline.comadministrator serwera, np.

5. wybierz **połącz** i zakończ proces logowania.

6. W **Eksploratorze obiektów**rozwiń folder Bazy danych **systemowych** -> baz danych baz**danych.**

7. Kliknij prawym przyciskiem myszy **główną** bazę danych i wybierz pozycję **Nowa kwerenda**.

8. W oknie kwerendy wprowadź następujące polecenie T-SQL i wybierz pozycję **Wykonaj** na pasku narzędzi.

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   Polecenie powinno zakończyć się pomyślnie, tworząc zamkniętego użytkownika do reprezentowania grupy.

9. Wyczyść okno kwerendy, wprowadź następujące polecenie T-SQL i wybierz pozycję **Wykonaj** na pasku narzędzi.

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   Polecenie powinno zakończyć się pomyślnie, przyznając zawartej użytkownikowi możliwość utworzenia bazy danych (SSISDB).

10. Jeśli twój SSISDB został utworzony przy użyciu uwierzytelniania SQL i chcesz przełączyć się do korzystania z uwierzytelniania usługi Azure AD dla usługi Azure-SSIS IR, aby uzyskać do niego dostęp, kliknij prawym przyciskiem myszy bazę danych **SSISDB** i wybierz pozycję **Nowa kwerenda**.

11. W oknie kwerendy wprowadź następujące polecenie T-SQL i wybierz pozycję **Wykonaj** na pasku narzędzi.

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    Polecenie powinno zakończyć się pomyślnie, tworząc zamkniętego użytkownika do reprezentowania grupy.

12. Wyczyść okno kwerendy, wprowadź następujące polecenie T-SQL i wybierz pozycję **Wykonaj** na pasku narzędzi.

    ```sql
    ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
    ```

    Polecenie powinno zakończyć się pomyślnie, przyznając contained użytkownikowi możliwość dostępu do SSISDB.

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Włączanie usługi Azure AD na wystąpienie zarządzanej usługi Azure SQL Database

Wystąpienie zarządzanej bazy danych SQL usługi Azure obsługuje tworzenie bazy danych z tożsamością zarządzaną dla usługi ADF bezpośrednio. Nie trzeba dołączać do tożsamości zarządzanej podnajem ADF do grupy usługi Azure AD ani tworzyć zamkniętego użytkownika reprezentującego tę grupę w wystąpieniu zarządzanym.

### <a name="configure-azure-ad-authentication-for-azure-sql-database-managed-instance"></a>Konfigurowanie uwierzytelniania usługi Azure AD dla wystąpienia zarządzanego usługi Azure SQL Database

Wykonaj kroki opisane w [obszarze Aprowizuj administratora usługi Azure Active Directory dla wystąpienia zarządzanego](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-managed-instance).

### <a name="add-the-managed-identity-for-your-adf-as-a-user-in-azure-sql-database-managed-instance"></a>Dodawanie tożsamości zarządzanej dla usługi ADF jako użytkownika w wystąpieniu zarządzanym usługi Azure SQL Database

W tym następnym kroku potrzebny jest program [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

1.  Uruchom SSMS.

2.  Połącz się z wystąpieniem zarządzanym przy użyciu konta programu SQL Server, które jest **sysadminą**. Jest to tymczasowe ograniczenie, które zostanie usunięte, gdy podmioty serwera usługi Azure AD (loginy) dla wystąpienia zarządzanego usługi Azure SQL Database stają się ga. Zostanie wyświetlony następujący błąd, jeśli spróbujesz użyć konta administratora usługi Azure AD do utworzenia logowania: Msg 15247, Poziom 16, Stan 1, Wiersz 1 Użytkownik nie ma uprawnień do wykonywania tej akcji.

3.  W **Eksploratorze obiektów**rozwiń folder Bazy danych **systemowych** -> baz danych baz**danych.**

4.  Kliknij prawym przyciskiem myszy **główną** bazę danych i wybierz pozycję **Nowa kwerenda**.

5.  W oknie kwerendy wykonaj następujący skrypt T-SQL, aby dodać tożsamość zarządzaną dla podajnika ADF jako użytkownika

    ```sql
    CREATE LOGIN [{your ADF name}] FROM EXTERNAL PROVIDER
    ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your ADF name}]
    ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your ADF name}]
    ```
    
    Polecenie powinno zakończyć się pomyślnie, przyznając tożsamości zarządzanej dla usługi ADF możliwość utworzenia bazy danych (SSISDB).

6.  Jeśli twój SSISDB został utworzony przy użyciu uwierzytelniania SQL i chcesz przełączyć się do korzystania z uwierzytelniania usługi Azure AD dla usługi Azure-SSIS IR, aby uzyskać do niego dostęp, kliknij prawym przyciskiem myszy bazę danych **SSISDB** i wybierz pozycję **Nowa kwerenda**.

7.  W oknie kwerendy wprowadź następujące polecenie T-SQL i wybierz pozycję **Wykonaj** na pasku narzędzi.

    ```sql
    CREATE USER [{your ADF name}] FOR LOGIN [{your ADF name}] WITH DEFAULT_SCHEMA = dbo
    ALTER ROLE db_owner ADD MEMBER [{your ADF name}]
    ```

    Polecenie powinno zakończyć się pomyślnie, przyznając tożsamości zarządzanej dla usługi ADF możliwość dostępu do SSISDB.

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Aprowizuj usługę Azure-SSIS IR w aplikacji azure portal/ADF

Podczas aprowizowania usługi Azure-SSIS IR w aplikacji Azure portal/ADF na stronie **Ustawienia SQL** wybierz pozycję **Użyj uwierzytelniania AAD z zarządzaną tożsamością dla opcji ADF.** Poniższy zrzut ekranu przedstawia ustawienia podczerwaskowego serwera usługi Azure SQL Database obsługującego usługę SSISDB. W przypadku środowiska IR z wystąpieniem zarządzanym obsługującym usługę SSISDB **warstwa usługi bazy danych wykazu** i **zezwalaj na dostęp do** ustawień usług platformy Azure nie mają zastosowania, podczas gdy inne ustawienia są takie same.

Aby uzyskać więcej informacji na temat tworzenia usługi Azure-SSIS IR, zobacz [Tworzenie środowiska wykonawczego integracji platformy Azure-SSIS w usłudze Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

![Ustawienia środowiska wykonawczego integracji azure-SSIS](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-azure-ssis-ir-with-powershell"></a>Aprowizuj usługę Azure-SSIS IR za pomocą programu PowerShell

Aby aprowizować usługę Azure-SSIS IR za pomocą programu PowerShell, wykonaj następujące czynności:

1.  Zainstaluj moduł [programu Azure PowerShell.](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) 

2.  W skrypcie `CatalogAdminCredential` nie należy ustawiać parametru. Przykład:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Description $AzureSSISDescription `
                                               -Type Managed `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -Edition $AzureSSISEdition `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
    ```

## <a name="run-ssis-packages-with-managed-identity-authentication"></a>Uruchamianie pakietów SSIS z uwierzytelnianiem tożsamości zarządzanej

Po uruchomieniu pakietów SSIS na platformie Azure-SSIS IR można użyć uwierzytelniania tożsamości zarządzanej, aby połączyć się z różnymi zasobami platformy Azure. Obecnie obsługujemy już uwierzytelnianie tożsamości zarządzanej w następujących menedżerach połączeń.

- [Menedżer połączeń OLE DB](https://docs.microsoft.com/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET Menedżer połączeń](https://docs.microsoft.com/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Menedżer połączeń usługi Azure Storage](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
