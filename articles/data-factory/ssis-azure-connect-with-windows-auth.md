---
title: Uzyskiwanie dostępu do magazynów danych i udziałów plików za pomocą uwierzytelniania systemu Windows
description: Dowiedz się, jak skonfigurować wykaz SSIS w bazie danych SQL Azure i czasie pracy integracji platformy Azure-SSIS w usłudze Azure Data Factory w celu uruchamiania pakietów uzyskujących dostęp do magazynów danych i udziałów plików za pomocą uwierzytelniania systemu Windows.
ms.date: 3/22/2018
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 0c4cdc3481fb58efd8eaa4cd83e1d6167f203a4e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760188"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>Uzyskiwanie dostępu do magazynów danych i udziałów plików za pomocą uwierzytelniania systemu Windows z poziomu pakietów SSIS na platformie Azure

Uwierzytelnianie systemu Windows służy do uzyskiwania dostępu do magazynów danych, takich jak serwery SQL, udziały plików, pliki azure itp. Magazyny danych mogą znajdować się lokalnie, hostowane na maszynach wirtualnych platformy Azure (VM) lub uruchomione na platformie Azure jako usługi zarządzane. Jeśli znajdują się one w środowisku lokalnym, należy dołączyć do usługi Azure-SSIS IR do sieci wirtualnej (Microsoft Azure Virtual Network) połączonej z siecią lokalną, zobacz [Dołączanie usługi Azure-SSIS IR do sieci wirtualnej platformy Microsoft Azure.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) Istnieją cztery metody uzyskiwania dostępu do magazynów danych za pomocą uwierzytelniania systemu Windows z pakietów SSIS uruchomionych na platformie Azure-SSIS IR:

| Metoda połączenia | Zakres efektywnej | Krok konfiguracji | Metoda dostępu w pakietach | Liczba zestawów poświadczeń i połączonych zasobów | Typ połączonych zasobów | 
|---|---|---|---|---|---|
| Konfigurowanie kontekstu wykonywania na poziomie działania | Działanie Na wykonanie pakietu SSIS | Skonfiguruj właściwość **uwierzytelniania systemu Windows,** aby skonfigurować kontekst "Wykonanie/Uruchom jako" podczas uruchamiania pakietów SSIS jako wykonywanie działań pakietu SSIS w potokach usługi ADF.<br/><br/> Aby uzyskać więcej informacji, zobacz [Konfigurowanie działania pakietu Wykonywanie SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). | Dostęp do zasobów bezpośrednio w pakietach za pomocą ścieżki UNC, `\\YourFileShareServerName\YourFolderName` na przykład, jeśli używasz udziałów plików lub usługi Azure Files: lub`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Obsługa tylko jednego zestawu poświadczeń dla wszystkich połączonych zasobów | - Udziały plików w środowisku lokalnym/maszynach wirtualnych platformy Azure<br/><br/> - Usługi Azure Files, zobacz [Korzystanie z udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - Serwery SQL w środowisku lokalnym/maszyny wirtualne platformy Azure z uwierzytelnianiem systemu Windows<br/><br/> - Inne zasoby z uwierzytelnianiem systemu Windows |
| Konfigurowanie kontekstu wykonywania na poziomie katalogu | Zgodnie z usługą Azure-SSIS IR, ale jest zastępowane podczas konfigurowania kontekstu wykonywania na poziomie działania (patrz wyżej) | Wykonaj procedurę `catalog.set_execution_credential` składowaną SSISDB, aby skonfigurować kontekst "Wykonanie/Uruchom jako".<br/><br/> Aby uzyskać więcej informacji, zobacz resztę tego artykułu poniżej. | Dostęp do zasobów bezpośrednio w pakietach za pomocą ścieżki UNC, `\\YourFileShareServerName\YourFolderName` na przykład, jeśli używasz udziałów plików lub usługi Azure Files: lub`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Obsługa tylko jednego zestawu poświadczeń dla wszystkich połączonych zasobów | - Udziały plików w środowisku lokalnym/maszynach wirtualnych platformy Azure<br/><br/> - Usługi Azure Files, zobacz [Korzystanie z udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - Serwery SQL w środowisku lokalnym/maszyny wirtualne platformy Azure z uwierzytelnianiem systemu Windows<br/><br/> - Inne zasoby z uwierzytelnianiem systemu Windows |
| Utrwalanie poświadczeń za pomocą `cmdkey` polecenia | Zgodnie z usługą Azure-SSIS IR, ale jest zastępowane podczas konfigurowania kontekstu wykonywania na poziomie działania/katalogu (patrz wyżej) | Wykonaj `cmdkey` polecenie w niestandardowych`main.cmd`skryptach konfiguracyjnych ( ) podczas inicjowania obsługi administracyjnej usługi `cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword` Azure-SSIS IR, na przykład, jeśli używasz udziałów plików lub usługi Azure Files: lub `cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`.<br/><br/> Aby uzyskać więcej informacji, zobacz [Dostosowywanie konfiguracji dla usługi Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). | Dostęp do zasobów bezpośrednio w pakietach za pomocą ścieżki UNC, `\\YourFileShareServerName\YourFolderName` na przykład, jeśli używasz udziałów plików lub usługi Azure Files: lub`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | Obsługa wielu zestawów poświadczeń dla różnych połączonych zasobów | - Udziały plików w środowisku lokalnym/maszynach wirtualnych platformy Azure<br/><br/> - Usługi Azure Files, zobacz [Korzystanie z udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - Serwery SQL w środowisku lokalnym/maszyny wirtualne platformy Azure z uwierzytelnianiem systemu Windows<br/><br/> - Inne zasoby z uwierzytelnianiem systemu Windows |
| Montaż napędów w czasie wykonywania pakietu (nietrwałe) | Na opakowanie | Wykonaj `net use` polecenie w execute process task, które jest dodawane na początku przepływu sterowania w pakietach, na przykład`net use D: \\YourFileShareServerName\YourFolderName` | Dostęp do udziałów plików za pośrednictwem mapowanych dysków | Obsługa wielu dysków dla różnych udziałów plików | - Udziały plików w środowisku lokalnym/maszynach wirtualnych platformy Azure<br/><br/> - Usługi Azure Files, zobacz [Korzystanie z udziału plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) |
|||||||

> [!WARNING]
> Jeśli nie używasz żadnej z powyższych metod dostępu do magazynów danych za pomocą uwierzytelniania systemu Windows, pakiety zależne od uwierzytelniania systemu Windows nie będą mogły uzyskać do nich dostępu i zakończyć się niepowodzeniem w czasie wykonywania. 

W dalszej części tego artykułu opisano sposób konfigurowania katalogu SSIS (SSISDB) hostowanego na serwerze/wystąpieniu zarządzanym bazy danych SQL platformy Azure do uruchamiania pakietów na platformie Azure-SSIS IR, które używają uwierzytelniania systemu Windows w celu uzyskania dostępu do magazynów danych. 

## <a name="you-can-only-use-one-set-of-credentials"></a>Można użyć tylko jednego zestawu poświadczeń

Podczas korzystania z uwierzytelniania systemu Windows w pakiecie SSIS można użyć tylko jednego zestawu poświadczeń. Poświadczenia domeny, które podajesz, gdy wykonasz kroki opisane w tym artykule, dotyczą wszystkich wykonań pakietów — interaktywnych lub zaplanowanych — w usłudze Azure-SSIS IR do momentu ich zmiany lub usunięcia. Jeśli pakiet musi łączyć się z wieloma magazynami danych z różnymi zestawami poświadczeń, należy wziąć pod uwagę powyższe metody alternatywne.

## <a name="provide-domain-credentials-for-windows-authentication"></a>Podaj poświadczenia domeny dla uwierzytelniania systemu Windows

Aby podać poświadczenia domeny, które umożliwiają pakietom korzystanie z uwierzytelniania systemu Windows w celu uzyskania dostępu do magazynów danych w środowisku lokalnym, wykonaj następujące czynności:

1. Za pomocą programu SQL Server Management Studio (SSMS) lub innego narzędzia połącz się z serwerem/wystąpieniem zarządzanym bazy danych SQL platformy Azure, które obsługuje usługę SSISDB. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą SSISDB na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Z SSISDB jako bieżącej bazy danych, otwórz okno kwerendy.

3. Uruchom następującą procedurę składowaną i podaj odpowiednie poświadczenia domeny:

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. Uruchom pakiety SSIS. Pakiety używają poświadczeń dostarczonych w celu uzyskania dostępu do magazynów danych w środowisku lokalnym z uwierzytelnianiem systemu Windows.

### <a name="view-domain-credentials"></a>Wyświetlanie poświadczeń domeny

Aby wyświetlić poświadczenia aktywnej domeny, wykonaj następujące czynności:

1. Za pomocą usługi SSMS lub innego narzędzia połącz się z serwerem/wystąpieniem zarządzanym usługi Azure SQL Database, które obsługuje usługę SSISDB. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą SSISDB na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Z SSISDB jako bieżącej bazy danych, otwórz okno kwerendy.

3. Uruchom następującą procedurę składowaną i sprawdź dane wyjściowe:

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>Wyczyść poświadczenia domeny
Aby wyczyścić i usunąć poświadczenia podane zgodnie z opisem w tym artykule, wykonaj następujące czynności:

1. Za pomocą usługi SSMS lub innego narzędzia połącz się z serwerem/wystąpieniem zarządzanym usługi Azure SQL Database, które obsługuje usługę SSISDB. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą SSISDB na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Z SSISDB jako bieżącej bazy danych, otwórz okno kwerendy.

3. Uruchom następującą procedurę składowaną:

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>Łączenie się z programem SQL Server w środowisku lokalnym

Aby sprawdzić, czy można połączyć się z programem SQL Server lokalnie, wykonaj następujące czynności:

1. Aby uruchomić ten test, znajdź komputer nieprzyłączony do domeny.

2. Na komputerze nieprzyłączonym do domeny uruchom następujące polecenie, aby uruchomić system SSMS z poświadczeniami domeny, których chcesz użyć:

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. W systemie SSMS sprawdź, czy można połączyć się z programem SQL Server lokalnie.

### <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do programu SQL Server lokalnie z pakietów uruchomionych na platformie Azure, wykonaj następujące czynności:

1.  W programie SQL Server Configuration Manager włącz protokół TCP/IP.

2. Zezwól na dostęp przez Zaporę systemu Windows. Aby uzyskać więcej informacji, zobacz [Konfigurowanie Zapory systemu Windows w celu uzyskania dostępu do programu SQL Server](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access).

3. Dołącz do usługi Azure-SSIS IR w sieci wirtualnej platformy Microsoft Azure, która jest połączona z programem SQL Server lokalnie.  Aby uzyskać więcej informacji, zobacz [Dołączanie usługi Azure-SSIS IR do sieci wirtualnej platformy Microsoft Azure](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

4. Użyj procedury `catalog.set_execution_credential` składowanej SSISDB, aby podać poświadczenia zgodnie z opisem w tym artykule.

## <a name="connect-to-a-file-share-on-premises"></a>Łączenie się z udziałem plików w środowisku lokalnym

Aby sprawdzić, czy można połączyć się z udziałem plików lokalnie, wykonaj następujące czynności:

1. Aby uruchomić ten test, znajdź komputer nieprzyłączony do domeny.

2. Na komputerze nieprzyłączonym do domeny uruchom następujące polecenia. Te polecenia otwierają okno wiersza polecenia z poświadczeniami domeny, których chcesz użyć, a następnie testują łączność z udziałem plików lokalnie, uzyskując listę katalogów.

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. Sprawdź, czy lista katalogów jest zwracana dla udziału plików lokalnie.

### <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać dostęp do udziału plików lokalnie z pakietów uruchomionych na platformie Azure, wykonaj następujące czynności:

1. Zezwól na dostęp przez Zaporę systemu Windows.

2. Dołącz do usługi Azure-SSIS IR w sieci wirtualnej platformy Microsoft Azure, która jest połączona z udziałem plików lokalnie.  Aby uzyskać więcej informacji, zobacz [Dołączanie usługi Azure-SSIS IR do sieci wirtualnej platformy Microsoft Azure](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

3. Użyj procedury `catalog.set_execution_credential` składowanej SSISDB, aby podać poświadczenia zgodnie z opisem w tym artykule.

## <a name="connect-to-a-file-share-on-azure-vm"></a>Łączenie się z udziałem plików na maszynie Wirtualnej platformy Azure

Aby uzyskać dostęp do udziału plików na maszynie Wirtualnej platformy Azure z pakietów uruchomionych na platformie Azure, wykonaj następujące czynności:

1. Za pomocą usługi SSMS lub innego narzędzia połącz się z serwerem/wystąpieniem zarządzanym usługi Azure SQL Database, które obsługuje usługę SSISDB. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą SSISDB na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Z SSISDB jako bieżącej bazy danych, otwórz okno kwerendy.

3. Uruchom następującą procedurę składowaną i podaj odpowiednie poświadczenia domeny:

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>Łączenie się z udziałem plików w usłudze Azure Files

Aby uzyskać więcej informacji o usłudze Azure Files, zobacz [Usługi Azure Files](https://azure.microsoft.com/services/storage/files/).

Aby uzyskać dostęp do udziału plików w usłudze Azure Files z pakietów uruchomionych na platformie Azure, wykonaj następujące czynności:

1. Za pomocą usługi SSMS lub innego narzędzia połącz się z serwerem/wystąpieniem zarządzanym usługi Azure SQL Database, które obsługuje usługę SSISDB. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą SSISDB na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database).

2. Z SSISDB jako bieżącej bazy danych, otwórz okno kwerendy.

3. Uruchom następującą procedurę składowaną i podaj odpowiednie poświadczenia domeny:

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>Następne kroki

- Wdrażanie pakietów. Aby uzyskać więcej informacji, zobacz [Wdrażanie projektu SSIS na platformie Azure za pomocą usługi SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Uruchom swoje pakiety. Aby uzyskać więcej informacji, zobacz [Uruchamianie pakietów SSIS na platformie Azure za pomocą usługi SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Zaplanuj swoje pakiety. Aby uzyskać więcej informacji, zobacz [Planowanie pakietów SSIS na platformie Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).