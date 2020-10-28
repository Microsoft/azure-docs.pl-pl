---
title: Dostęp do magazynów danych i udziałów plików z uwierzytelnianiem systemu Windows
description: Dowiedz się, jak skonfigurować katalog usług SSIS w Azure SQL Database i Azure-SSIS Integration Runtime w Azure Data Factory do uruchamiania pakietów, które uzyskują dostęp do magazynów danych i udziałów plików z uwierzytelnianiem systemu Windows.
ms.date: 10/27/2020
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 545f698f444e99d3f3807f22b308963172018fcb
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746656"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Uzyskiwanie dostępu do magazynów danych i udziałów plików za pomocą uwierzytelniania systemu Windows z poziomu pakietów SSIS na platformie Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Możesz użyć uwierzytelniania systemu Windows, aby uzyskać dostęp do magazynów danych, takich jak serwery SQL, udziały plików, Azure Files itd., z pakietów SSIS działających na Azure-SSIS Integration Runtime (IR) w Azure Data Factory (ADF). Magazyny danych mogą znajdować się lokalnie, hostowane w usłudze Azure Virtual Machines (maszyny wirtualne) lub działać na platformie Azure jako usługi zarządzane. Jeśli są one lokalne, należy przyłączyć Azure-SSIS IR do Virtual Network (Microsoft Azure Virtual Network) podłączonych do sieci lokalnej, a następnie [Przyłączyć Azure-SSIS IR do Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md). Istnieją cztery metody uzyskiwania dostępu do magazynów danych z uwierzytelnianiem systemu Windows z pakietów SSIS uruchomionych na Azure-SSIS IR:

| Metoda połączenia | Zakres obowiązywania | Krok instalacji | Metoda dostępu w pakietach | Liczba zestawów poświadczeń i połączonych zasobów | Typ połączonych zasobów | 
|---|---|---|---|---|---|
| Konfigurowanie kontekstu wykonywania na poziomie działania | Działanie na pakiet SSIS na wykonanie | Skonfiguruj Właściwość **uwierzytelnianie systemu Windows** , aby skonfigurować kontekst "Wykonywanie/Uruchamianie jako" podczas uruchamiania pakietów usług SSIS jako działania pakietu SSIS w potokach ADF.<br/><br/> Aby uzyskać więcej informacji, zobacz [Konfigurowanie działania wykonywania pakietu usług SSIS](./how-to-invoke-ssis-package-ssis-activity.md). | Dostęp do zasobów bezpośrednio w pakietach, na przykład do uzyskiwania dostępu do udziałów plików lub Azure Files przy użyciu ścieżki UNC: `\\YourFileShareServerName\YourFolderName` lub `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Obsługa tylko jednego zestawu poświadczeń dla wszystkich połączonych zasobów | -Udziały plików na maszynach wirtualnych platformy Azure<br/><br/> -Azure Files, zobacz [Korzystanie z udziału plików platformy Azure](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> — Serwery SQL lokalnie/maszyny wirtualne platformy Azure z uwierzytelnianiem systemu Windows<br/><br/> — Inne zasoby z uwierzytelnianiem systemu Windows |
| Konfigurowanie kontekstu wykonywania na poziomie katalogu | Na Azure-SSIS IR, ale jest zastępowany podczas konfigurowania kontekstu wykonywania na poziomie aktywności (patrz powyżej) | Wykonaj `catalog.set_execution_credential` procedurę składowaną SSISDB, aby skonfigurować kontekst "Execution/Run As".<br/><br/> Aby uzyskać więcej informacji, zobacz pozostałą część tego artykułu poniżej. | Dostęp do zasobów bezpośrednio w pakietach, na przykład do uzyskiwania dostępu do udziałów plików lub Azure Files przy użyciu ścieżki UNC: `\\YourFileShareServerName\YourFolderName` lub `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Obsługa tylko jednego zestawu poświadczeń dla wszystkich połączonych zasobów | -Udziały plików na maszynach wirtualnych platformy Azure<br/><br/> -Azure Files, zobacz [Korzystanie z udziału plików platformy Azure](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> — Serwery SQL lokalnie/maszyny wirtualne platformy Azure z uwierzytelnianiem systemu Windows<br/><br/> — Inne zasoby z uwierzytelnianiem systemu Windows |
| Utrwalanie poświadczeń za pomocą `cmdkey` polecenia | Na Azure-SSIS IR, ale jest zastępowany podczas konfigurowania kontekstu wykonywania na poziomie katalogu (patrz powyżej) | Wykonaj `cmdkey` polecenie w niestandardowym skrypcie instalacji ( `main.cmd` ) podczas aprowizacji Azure-SSIS IR, na przykład jeśli używasz udziałów plików, Azure Files lub SQL Server:<br/><br/> `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword`,<br/><br/> `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`lub<br/><br/> `cmdkey /add:YourSQLServerFullyQualifiedDomainNameOrIPAddress:YorSQLServerPort /user:YourDomainName\YourUsername /pass:YourPassword`.<br/><br/> Aby uzyskać więcej informacji, zobacz [Dostosowywanie Instalatora dla Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md). | Dostęp do zasobów bezpośrednio w pakietach, na przykład do uzyskiwania dostępu do udziałów plików lub Azure Files przy użyciu ścieżki UNC: `\\YourFileShareServerName\YourFolderName` lub `\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Obsługa wielu zestawów poświadczeń dla różnych połączonych zasobów | -Udziały plików na maszynach wirtualnych platformy Azure<br/><br/> -Azure Files, zobacz [Korzystanie z udziału plików platformy Azure](../storage/files/storage-how-to-use-files-windows.md)<br/><br/> — Serwery SQL lokalnie/maszyny wirtualne platformy Azure z uwierzytelnianiem systemu Windows<br/><br/> — Inne zasoby z uwierzytelnianiem systemu Windows |
| Instalowanie dysków w czasie wykonywania pakietu (nietrwałe) | Na pakiet | Wykonaj `net use` polecenie w zadaniu wykonywania procesu, które zostało dodane na początku przepływu sterowania w Twoich pakietach, na przykład `net use D: \\YourFileShareServerName\YourFolderName` | Dostęp do udziałów plików za pośrednictwem mapowanych dysków | Obsługa wielu dysków dla różnych udziałów plików | -Udziały plików na maszynach wirtualnych platformy Azure<br/><br/> -Azure Files, zobacz [Korzystanie z udziału plików platformy Azure](../storage/files/storage-how-to-use-files-windows.md) |
|||||||

> [!WARNING]
> Jeśli nie korzystasz z powyższych metod w celu uzyskania dostępu do magazynów danych z uwierzytelnianiem systemu Windows, pakiety zależne od uwierzytelniania systemu Windows nie będą mogły uzyskać do nich dostępu i nie powiodą się w czasie wykonywania. 

W dalszej części tego artykułu opisano sposób konfigurowania wykazu usług SSIS (SSISDB) hostowanego w wystąpieniu zarządzanym SQL Database/SQL w celu uruchamiania pakietów na Azure-SSIS IR, które używają uwierzytelniania systemu Windows w celu uzyskiwania dostępu do magazynów danych. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Można używać tylko jednego zestawu poświadczeń

W przypadku korzystania z uwierzytelniania systemu Windows w pakiecie usług SSIS można używać tylko jednego zestawu poświadczeń. Poświadczenia domeny podane po wykonaniu kroków opisanych w tym artykule mają zastosowanie do wszystkich wykonań pakietów — interaktywnych lub zaplanowanych na Azure-SSIS IR do momentu ich zmiany lub usunięcia. Jeśli pakiet ma łączyć się z wieloma magazynami danych przy użyciu różnych zestawów poświadczeń, należy wziąć pod uwagę powyższe alternatywne metody.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Podaj poświadczenia domeny dla uwierzytelniania systemu Windows

Aby podać poświadczenia domeny, które zezwalają pakietom na dostęp do magazynów danych lokalnie przy użyciu uwierzytelniania systemu Windows, wykonaj następujące czynności:

1. Program SQL Server Management Studio (SSMS) lub inne narzędzie nawiązuje połączenie z wystąpieniem zarządzanym SQL Database/SQL, które hostuje SSISDB. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Korzystając z SSISDB jako bieżącej bazy danych, Otwórz okno zapytania.

3. Uruchom następującą procedurę składowaną i podaj odpowiednie poświadczenia domeny:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Uruchamianie pakietów SSIS. Pakiety używają poświadczeń, które zostały podane w celu uzyskania dostępu do magazynów danych w środowisku lokalnym z uwierzytelnianiem systemu Windows.

### <a name="view-domain-credentials"></a>Wyświetl poświadczenia domeny

Aby wyświetlić poświadczenia Active domen, wykonaj następujące czynności:

1. Program SSMS lub inne narzędzie nawiązuje połączenie z wystąpieniem zarządzanym SQL Database/SQL, które hostuje SSISDB. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Korzystając z SSISDB jako bieżącej bazy danych, Otwórz okno zapytania.

3. Uruchom następującą procedurę składowaną i sprawdź dane wyjściowe:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Wyczyść poświadczenia domeny
Aby wyczyścić i usunąć poświadczenia podane zgodnie z opisem w tym artykule, wykonaj następujące czynności:

1. Program SSMS lub inne narzędzie nawiązuje połączenie z wystąpieniem zarządzanym SQL Database/SQL, które hostuje SSISDB. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Korzystając z SSISDB jako bieżącej bazy danych, Otwórz okno zapytania.

3. Uruchom następującą procedurę składowaną:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Nawiązywanie połączenia z SQL Server lokalnie

Aby sprawdzić, czy można nawiązać połączenie z SQL Server lokalnie, wykonaj następujące czynności:

1. Aby uruchomić ten test, Znajdź komputer przyłączony do domeny.

2. Na komputerze przyłączonym do domeny, uruchom następujące polecenie, aby uruchomić program SSMS z poświadczeniami domeny, których chcesz użyć:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. W programie SSMS Sprawdź, czy możesz nawiązać połączenie z SQL Server lokalnie.

### <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do SQL Server lokalnego z pakietów uruchomionych na platformie Azure, wykonaj następujące czynności:

1.  W SQL Server Configuration Manager Włącz protokół TCP/IP.

2. Zezwalaj na dostęp za poorednictwem zapory systemu Windows. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapory systemu Windows pod kątem dostępu SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access).

3. Dołącz do Azure-SSIS IR do Microsoft Azure Virtual Network, który jest połączony z SQL Server lokalnie.  Aby uzyskać więcej informacji, zobacz [sprzęganie Azure-SSIS IR z Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md).

4. Użyj `catalog.set_execution_credential` procedury składowanej SSISDB, aby podać poświadczenia zgodnie z opisem w tym artykule.

## <a name="connect-to-a-file-share-on-premises"></a>Nawiązywanie połączenia z udziałem plików lokalnie

Aby sprawdzić, czy można nawiązać połączenie z udziałem plików w środowisku lokalnym, wykonaj następujące czynności:

1. Aby uruchomić ten test, Znajdź komputer przyłączony do domeny.

2. Na komputerze przyłączonym do domeny, uruchom następujące polecenia. Te polecenia otwierają okno wiersza polecenia z poświadczeniami domeny, które mają być używane, a następnie testują łączność z udziałem plików lokalnie, pobierając listę katalogów.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Sprawdź, czy lista katalogów jest zwracana dla udziału plików lokalnie.

### <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do udziału plików w środowisku lokalnym z pakietów uruchomionych na platformie Azure, wykonaj następujące czynności:

1. Zezwalaj na dostęp za poorednictwem zapory systemu Windows.

2. Dołącz do Azure-SSIS IR do Microsoft Azure Virtual Network, który jest połączony z udziałem plików lokalnie.  Aby uzyskać więcej informacji, zobacz [sprzęganie Azure-SSIS IR z Microsoft Azure Virtual Network](./join-azure-ssis-integration-runtime-virtual-network.md).

3. Użyj `catalog.set_execution_credential` procedury składowanej SSISDB, aby podać poświadczenia zgodnie z opisem w tym artykule.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Nawiązywanie połączenia z udziałem plików na maszynie wirtualnej platformy Azure

Aby uzyskać dostęp do udziału plików na maszynie wirtualnej platformy Azure z pakietów uruchomionych na platformie Azure, wykonaj następujące czynności:

1. Program SSMS lub inne narzędzie nawiązuje połączenie z wystąpieniem zarządzanym SQL Database/SQL, które hostuje SSISDB. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Korzystając z SSISDB jako bieżącej bazy danych, Otwórz okno zapytania.

3. Uruchom następującą procedurę składowaną i podaj odpowiednie poświadczenia domeny:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Nawiązywanie połączenia z udziałem plików w Azure Files

Aby uzyskać więcej informacji na temat Azure Files, zobacz [Azure Files](https://azure.microsoft.com/services/storage/files/).

Aby uzyskać dostęp do udziału plików w Azure Files z pakietów uruchomionych na platformie Azure, wykonaj następujące czynności:

1. Program SSMS lub inne narzędzie nawiązuje połączenie z wystąpieniem zarządzanym SQL Database/SQL, które hostuje SSISDB. Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Korzystając z SSISDB jako bieżącej bazy danych, Otwórz okno zapytania.

3. Uruchom następującą procedurę składowaną i podaj odpowiednie poświadczenia domeny:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Następne kroki

- Wdróż pakiety. Aby uzyskać więcej informacji, zobacz [wdrażanie projektu SSIS na platformie Azure za pomocą programu SSMS](/sql/integration-services/ssis-quickstart-deploy-ssms).
- Uruchom pakiety. Aby uzyskać więcej informacji, zobacz [uruchamianie pakietów SSIS na platformie Azure za pomocą programu SSMS](/sql/integration-services/ssis-quickstart-run-ssms).
- Zaplanuj pakiety. Aby uzyskać więcej informacji, zobacz [Planowanie pakietów usług SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).
