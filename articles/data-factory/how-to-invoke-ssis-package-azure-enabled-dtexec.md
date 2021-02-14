---
title: Wykonywanie pakietów SQL Server Integration Services (SSIS) za pomocą narzędzia dtexec z obsługą platformy Azure
description: Dowiedz się, jak wykonywać pakiety SQL Server Integration Services (SSIS) za pomocą narzędzia dtexec z włączoną obsługą platformy Azure.
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 980e8e3c2f2c1ca1dc716df1e77caaa3fe3181aa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386173"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Uruchamianie pakietów SQL Server Integration Services za pomocą narzędzia dtexec z obsługą platformy Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano narzędzie wiersza polecenia dtexec (AzureDTExec) z obsługą platformy Azure. Służy do uruchamiania pakietów SQL Server Integration Services (SSIS) na Azure-SSIS Integration Runtime (IR) w Azure Data Factory.

Tradycyjne narzędzie dtexec zawiera SQL Server. Aby uzyskać więcej informacji, zobacz [Narzędzie dtexec](/sql/integration-services/packages/dtexec-utility). Często są one wywoływane przez koordynatorów lub harmonogramów innych firm, takich jak ActiveBatch i Control-M, do uruchamiania pakietów SSIS w środowisku lokalnym. 

Nowoczesne narzędzie AzureDTExec zawiera narzędzie SQL Server Management Studio (SSMS). Może być również wywoływana przez koordynatorów lub harmonogramów innych firm do uruchamiania pakietów usług SSIS na platformie Azure. Ułatwia podnoszenie i przenoszenie pakietów usług SSIS i przenoszenie ich do chmury. Jeśli po migracji chcesz nadal korzystać z koordynatorów lub harmonogramów innych firm w codziennych operacjach, można teraz wywołać AzureDTExec zamiast dtexec.

AzureDTExec uruchamia pakiety jako działania wykonywania pakietów SSIS w potokach Data Factory. Aby uzyskać więcej informacji, zobacz [uruchamianie pakietów SSIS jako działań Azure Data Factory](./how-to-invoke-ssis-package-ssis-activity.md). 

AzureDTExec można skonfigurować za pomocą programu SSMS, aby użyć aplikacji Azure Active Directory (Azure AD), która generuje potoki w fabryce danych. Można go również skonfigurować w taki sposób, aby uzyskiwać dostęp do systemów plików, udziałów plików lub Azure Files, w których przechowywane są pakiety. W oparciu o wartości, które dajesz dla opcji wywołania, AzureDTExec generuje i uruchamia unikatowy potok Data Factory za pomocą działania wykonaj pakiet SSIS. Wywoływanie AzureDTExec z tymi samymi wartościami dla jego opcji uruchamia ponownie istniejący potok.

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć AzureDTExec, Pobierz i zainstaluj najnowszą wersję programu SSMS, która jest w wersji 18,3 lub nowszej. Pobierz ją z [tej witryny sieci Web](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="configure-the-azuredtexec-utility"></a>Konfigurowanie narzędzia AzureDTExec
Zainstalowanie programu SSMS na komputerze lokalnym spowoduje również zainstalowanie AzureDTExec. Aby skonfigurować ustawienia, uruchom program SSMS z opcją **Uruchom jako administrator** . Następnie wybierz pozycję **Narzędzia**  >  **Migrowanie na platformę Azure**  >  **Konfiguracja dtexec z włączoną obsługą platformy Azure**.

![Konfigurowanie menu dtexec z obsługą platformy Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Ta akcja powoduje otwarcie okna **AzureDTExecConfig** , które należy otworzyć z uprawnieniami administracyjnymi do zapisu w pliku *AzureDTExec. Settings* . Jeśli program SSMS nie został uruchomiony jako administrator, zostanie otwarte okno Kontrola konta użytkownika (UAC). Wprowadź hasło administratora, aby podwyższyć poziom uprawnień.

![Konfigurowanie ustawień dtexec z obsługą platformy Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

W oknie **AzureDTExecConfig** wprowadź następujące ustawienia konfiguracji:

- Identyfikator **aplikacji**: należy wprowadzić unikatowe identyfikatory aplikacji usługi Azure AD, którą tworzysz przy użyciu odpowiednich uprawnień, aby generować potoki w fabryce danych. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji usługi Azure AD i nazwy głównej usługi za pośrednictwem Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).
- **AuthenticationKey**: Wprowadź klucz uwierzytelniania dla aplikacji usługi Azure AD.
- **TenantId**: wprowadź unikatowy identyfikator dzierżawy usługi Azure AD, w której jest tworzona aplikacja usługi Azure AD.
- **DataFactory**: Wprowadź nazwę fabryki danych, w której będą generowane unikatowe potoki z uruchomionym działaniem pakietu SSIS, na podstawie wartości opcji dostarczonych podczas wywoływania AzureDTExec.
- **IRName**: wprowadź nazwę Azure-SSIS IR w fabryce danych, na której zostaną uruchomione pakiety określone w ścieżce Universal NAMING Convention (UNC) po wywołaniu AzureDTExec.
- **PipelineNameHashStrLen**: Wprowadź długość ciągów skrótu, które mają zostać wygenerowane z wartości opcji podanej podczas wywoływania AzureDTExec. Ciągi są używane do tworzenia unikatowych nazw dla potoków Data Factory, które uruchamiają pakiety na Azure-SSIS IR. Zwykle długość 32 znaków jest wystarczająca.
- Grupa **zasobów: wprowadź** nazwę grupy zasobu platformy Azure, w której utworzono fabrykę danych.
- Subskrypcja **: wprowadź** unikatowy identyfikator subskrypcji platformy Azure, w ramach której utworzono fabrykę danych.
- **LogAccessDomain**: Wprowadź poświadczenia domeny w celu uzyskania dostępu do folderu dziennika w ścieżce UNC podczas zapisywania plików dziennika, co jest wymagane, gdy określono **LogPath** , a **LogLevel** nie ma **wartości null**.
- **LogAccessPassword**: Wprowadź poświadczenia hasła, aby uzyskać dostęp do folderu dziennika w jego ścieżce UNC podczas zapisywania plików dziennika, co jest wymagane, gdy określono **LogPath** , a **LogLevel** nie ma **wartości null**.
- **LogAccessUserName**: Wprowadź poświadczenia nazwy użytkownika, aby uzyskać dostęp do folderu dziennika w swojej ścieżce UNC podczas zapisywania plików dziennika, co jest wymagane, gdy określono **LogPath** i **LogLevel** nie ma **wartości null**.
- **LogLevel**: Wprowadź wybrany zakres rejestrowania ze wstępnie zdefiniowanych opcji **null**, **Basic**, **verbose** lub **Performance** dla wykonań pakietów na Azure-SSIS IR.
- **LogPath**: wprowadź ścieżkę UNC folderu dziennika, w której będą zapisywane pliki dziennika z wykonywania pakietów na Azure-SSIS IR.
- **PackageAccessDomain**: Wprowadź poświadczenia domeny w celu uzyskania dostępu do pakietów w ścieżce UNC określonej podczas wywoływania AzureDTExec.
- **PackageAccessPassword**: Wprowadź poświadczenia hasła, aby uzyskać dostęp do pakietów w ścieżce UNC, która jest określona podczas wywoływania AzureDTExec.
- **PackageAccessUserName**: Wprowadź poświadczenia nazwy użytkownika, aby uzyskać dostęp do pakietów w ścieżce UNC określonej podczas wywoływania AzureDTExec.

Aby przechowywać pakiety i pliki dziennika w lokalnych systemach plików lub udziałach plików, Dołącz do Azure-SSIS IR do sieci wirtualnej połączonej z siecią lokalną, aby umożliwić pobranie pakietów i zapisanie plików dziennika. Aby uzyskać więcej informacji, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](./join-azure-ssis-integration-runtime-virtual-network.md).

Aby uniknąć wyświetlania poufnych wartości zapisanych w pliku *AzureDTExec. Settings* w postaci zwykłego tekstu, kodujemy je do ciągów kodowania base64. Po wywołaniu AzureDTExec wszystkie ciągi kodowane algorytmem Base64 są zdekodowane z powrotem do ich oryginalnych wartości. Można bardziej zabezpieczyć plik *AzureDTExec. Settings* , ograniczając konta, które mają do niego dostęp.

## <a name="invoke-the-azuredtexec-utility"></a>Wywoływanie narzędzia AzureDTExec
Możesz wywołać AzureDTExec w wierszu polecenia i podać odpowiednie wartości dla konkretnych opcji w scenariuszu przypadków użycia.

Narzędzie jest instalowane pod adresem `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn` . Możesz dodać ścieżkę do zmiennej środowiskowej "PATH", aby można ją było wywołać z dowolnego miejsca.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Wywołanie AzureDTExec oferuje podobne opcje jako wywołanie dtexec. Aby uzyskać więcej informacji, zobacz [Narzędzie dtexec](/sql/integration-services/packages/dtexec-utility). Poniżej przedstawiono opcje, które są obecnie obsługiwane:

- **/F [Iku]**: ładuje pakiet, który jest przechowywany w systemie plików, udziale plików lub Azure Files. Jako wartość tej opcji można określić ścieżkę UNC do pliku pakietu w systemie plików, udziale plików lub Azure Files z rozszerzeniem dtsx. Jeśli określona ścieżka UNC zawiera spację, należy umieścić znaki cudzysłowu wokół całej ścieżki.
- **/Conf [igFile]**: Określa plik konfiguracji, z którego mają zostać wyodrębnione wartości. Korzystając z tej opcji, można ustawić konfigurację wykonawczą dla pakietu, która różni się od określonej w czasie projektowania. Można przechowywać różne ustawienia w pliku konfiguracji XML, a następnie ładować je przed wykonaniem pakietu. Aby uzyskać więcej informacji, zobacz [konfiguracje pakietów usług SSIS](/sql/integration-services/packages/package-configurations). Aby określić wartość dla tej opcji, użyj ścieżki UNC do pliku konfiguracji w systemie plików, udziale plików lub Azure Files z rozszerzeniem dtsConfig. Jeśli określona ścieżka UNC zawiera spację, należy umieścić znaki cudzysłowu wokół całej ścieżki.
- **/Conn [zenia]**: określa parametry połączenia dla istniejących menedżerów połączeń w pakiecie. Za pomocą tej opcji można ustawić parametry połączenia w czasie wykonywania dla istniejących menedżerów połączeń w pakiecie, które różnią się od określonych w czasie projektowania. Określ wartość dla tej opcji w następujący sposób: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]` .
- **/Set**: zastępuje konfigurację parametru, zmiennej, właściwości, kontenera, dostawcy dziennika, modułu wyliczającego foreach lub połączenia w pakiecie. Tę opcję można określić wiele razy. Określ wartość dla tej opcji w następujący sposób: `property_path;value` . Na przykład `\package.variables[counter].Value;1` zastępuje wartość `counter` zmiennej jako 1. Możesz użyć kreatora **konfiguracji pakietu** , aby znaleźć, skopiować i wkleić wartość `property_path` dla elementów w pakiecie, których wartość ma zostać przesłonięta. Aby uzyskać więcej informacji, zobacz [Kreator konfiguracji pakietu](/sql/integration-services/packages/legacy-package-deployment-ssis).
- **/De [Crypt]**: ustawia hasło odszyfrowywania dla pakietu, który jest skonfigurowany przy użyciu poziomu ochrony **EncryptAllWithPassword** / **EncryptSensitiveWithPassword** .

> [!NOTE]
> Wywoływanie AzureDTExec z nowymi wartościami dla jego opcji generuje nowy potok z wyjątkiem opcji **/de [kryptu]**.

## <a name="next-steps"></a>Następne kroki

Po wydaniu unikatowych potoków z działaniem pakietu usług SSIS i uruchomieniu ich w przypadku wywołania AzureDTExec można je monitorować w portalu Data Factory. Możesz również przypisać do nich wyzwalacze Data Factory, jeśli chcesz zorganizować/zaplanować przy użyciu Data Factory. Aby uzyskać więcej informacji, zobacz [uruchamianie pakietów SSIS jako działań Data Factory](./how-to-invoke-ssis-package-ssis-activity.md).

> [!WARNING]
> Wygenerowany potok powinien być używany tylko przez AzureDTExec. Jego właściwości lub parametry mogą ulec zmianie w przyszłości, dlatego nie należy ich modyfikować ani ponownie używać do innych celów. Modyfikacje mogą spowodować uszkodzenie AzureDTExec. W takim przypadku Usuń potoku. AzureDTExec generuje nowy potok przy następnym wywołaniu.