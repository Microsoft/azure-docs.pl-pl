---
title: Wykonywanie pakietów usług integracji programu SQL Server (SSIS) za pomocą narzędzia dtexec obsługującego platformę Azure
description: Dowiedz się, jak wykonywać pakiety usług integracji programu SQL Server (SSIS) za pomocą narzędzia dtexec obsługującego platformę Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: dce7fb87ee49aefdedf5653243fa5729eee34519
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414324"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Uruchamianie pakietów usług integracji programu SQL Server za pomocą narzędzia dtexec obsługującego platformę Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano narzędzie wiersza polecenia dtexec (AzureDTExec) z obsługą platformy Azure. Służy do uruchamiania pakietów usług integracji programu SQL Server (SSIS) w czasie wykonywania integracji platformy Azure-SSIS (IR) w usłudze Azure Data Factory.

Tradycyjne narzędzie dtexec jest wyposażone w program SQL Server. Aby uzyskać więcej informacji, zobacz [narzędzie dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Jest często wywoływana przez innych orkiestratorów lub harmonogramów, takich jak ActiveBatch i Control-M, do uruchamiania pakietów SSIS w środowisku lokalnym. 

Nowoczesne narzędzie AzureDTExec jest wyposażone w narzędzie SQL Server Management Studio (SSMS). Może być również wywoływana przez koordynatorów innych firm lub harmonogramów do uruchamiania pakietów SSIS na platformie Azure. Ułatwia podnoszenie i przenoszenie lub migrację pakietów SSIS do chmury. Po migracji, jeśli chcesz nadal używać koordynatorów lub harmonogramów innych firm w codziennych operacjach, mogą teraz wywoływać usługę AzureDTExec zamiast dtexec.

Usługa AzureDTExec uruchamia pakiety jako wykonywanie działań pakietu SSIS w potokach fabryki danych. Aby uzyskać więcej informacji, zobacz [Uruchamianie pakietów SSIS jako działań usługi Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) 

Usługa AzureDTExec można skonfigurować za pomocą usługi SSMS do używania aplikacji usługi Azure Active Directory (Azure AD), która generuje potoki w fabryce danych. Można go również skonfigurować tak, aby uzyskiwał dostęp do systemów plików, udziałów plików lub plików azure, w których przechowujesz pakiety. Na podstawie wartości, które można podać dla jego opcji wywołania, AzureDTExec generuje i uruchamia unikatowy potok fabryki danych z execute SSIS działania pakietu w nim. Wywoływanie usługi AzureDTExec z tymi samymi wartościami dla swoich opcji ponownie wywraca istniejący potok.

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z usługi AzureDTExec, pobierz i zainstaluj najnowszą wersję usługi SSMS, która jest w wersji 18.3 lub nowszej. Pobierz go z [tej strony](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-the-azuredtexec-utility"></a>Konfigurowanie narzędzia AzureDTExec
Instalowanie usługi SSMS na komputerze lokalnym również instaluje usługę AzureDTExec. Aby skonfigurować jego ustawienia, uruchom program SSMS z opcją **Uruchom jako administrator.** Następnie wybierz pozycję **Narzędzia** > **migruj do platformy Azure** > **Konfigurowanie usługi DTExec z obsługą platformy Azure.**

![Konfigurowanie menu dtexec z obsługą platformy Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Ta akcja otwiera okno **AzureDTExecConfig,** które musi zostać otwarte z uprawnieniami administracyjnymi, aby można je było zapisać w pliku *AzureDTExec.settings.* Jeśli system SSMS nie został uruchomiony jako administrator, zostanie otwarte okno Kontrola konta użytkownika. Wprowadź hasło administratora, aby podnieść swoje uprawnienia.

![Konfigurowanie ustawień dtexec z obsługą platformy Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

W oknie **AzureDTExecConfig** wprowadź ustawienia konfiguracji w następujący sposób:

- **ApplicationId**: Wprowadź unikatowy identyfikator aplikacji usługi Azure AD, który tworzysz z odpowiednimi uprawnieniami do generowania potoków w fabryce danych. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji i jednostki usługi Azure AD za pośrednictwem witryny Azure portal.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
- **AuthenticationKey:** Wprowadź klucz uwierzytelniania dla aplikacji usługi Azure AD.
- **Identyfikator dzierżawy:** Wprowadź unikatowy identyfikator dzierżawy usługi Azure AD, w ramach którego jest tworzona aplikacja usługi Azure AD.
- **DataFactory:** Wprowadź nazwę fabryki danych, w którym unikatowe potoki z execute SSIS package działania w nich są generowane na podstawie wartości opcji podanych podczas wywoływania Usługi AzureDTExec.
- **IRName:** Wprowadź nazwę usługi Azure-SSIS IR w fabryce danych, na którym pakiety określone w ich ścieżki universal naming convention (UNC) będą uruchamiane podczas wywoływania usługi AzureDTExec.
- **PipelineNameHashStrLen:** Wprowadź długość ciągów mieszania, które mają być generowane na podstawie wartości opcji, które są podane podczas wywoływania usługi AzureDTExec. Ciągi są używane do tworzenia unikatowych nazw potoków fabryki danych, które uruchamiają pakiety na platformie Azure-SSIS IR. Zazwyczaj wystarczy długość 32 znaków.
- **ResourceGroup**: Wprowadź nazwę grupy zasobów platformy Azure, w której utworzono fabrykę danych.
- **SubscriptionId**: Wprowadź unikatowy identyfikator subskrypcji platformy Azure, w ramach którego utworzono fabrykę danych.
- **LogAccessDomain**: Wprowadź poświadczenia domeny, aby uzyskać dostęp do folderu dziennika w jego ścieżce UNC podczas pisania plików dziennika, co jest wymagane, gdy **logpath** jest określony i **LogLevel** nie ma **wartości null**.
- **LogAccessPassword**: Wprowadź poświadczenie hasła, aby uzyskać dostęp do folderu dziennika w jego ścieżce UNC podczas pisania plików dziennika, co jest wymagane, gdy **logpath** jest określony i **LogLevel** nie jest **null**.
- **LogAccessUserName**: Wprowadź poświadczenie nazwy użytkownika, aby uzyskać dostęp do folderu dziennika w jego ścieżce UNC podczas pisania plików dziennika, co jest wymagane, gdy **logpath** jest określony i **LogLevel** nie jest **null**.
- **LogLevel:** Wprowadź wybrany zakres rejestrowania ze wstępnie zdefiniowanych **opcji null**, **Basic**, **Verbose**lub **Performance** dla wykonań pakietów na platformie Azure-SSIS IR.
- **LogPath:** Wprowadź ścieżkę UNC folderu dziennika, w którym są zapisywane pliki dziennika z wykonania pakietu na platformie Azure-SSIS IR.
- **PackageAccessDomain:** Wprowadź poświadczenia domeny, aby uzyskać dostęp do pakietów w ich ścieżce UNC, która jest określona podczas wywoływania usługi AzureDTExec.
- **PackageAccessPassword:** Wprowadź poświadczenia hasła, aby uzyskać dostęp do pakietów w ich ścieżce UNC, która jest określona podczas wywoływania usługi AzureDTExec.
- **PackageAccessUserName:** Wprowadź poświadczenia nazwy użytkownika, aby uzyskać dostęp do pakietów w ich ścieżce UNC, która jest określona podczas wywoływania usługi AzureDTExec.

Aby przechowywać pakiety i pliki dziennika w systemach plików lub udziałach plików lokalnie, dołącz do usługi Azure-SSIS IR do sieci wirtualnej połączonej z siecią lokalną, aby mogła pobierać pakiety i zapisywać pliki dziennika. Aby uzyskać więcej informacji, zobacz [Dołączanie usługi Azure-SSIS IR do sieci wirtualnej.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

Aby uniknąć wyświetlania poufnych wartości zapisanych w pliku *AzureDTExec.settings* w postaci zwykłego tekstu, możemy zakodować je w ciągi kodowania Base64. Po wywołaniu usługi AzureDTExec wszystkie ciągi zakodowane w usłudze Base64 są dekodowane z powrotem do ich oryginalnych wartości. Można dodatkowo zabezpieczyć plik *AzureDTExec.settings,* ograniczając konta, które mogą uzyskać do niego dostęp.

## <a name="invoke-the-azuredtexec-utility"></a>Wywoływanie narzędzia AzureDTExec
Można wywołać AzureDTExec w wierszu polecenia i podać odpowiednie wartości dla określonych opcji w scenariuszu przypadku użycia.

Narzędzie jest zainstalowane `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`w pliku . Można dodać jego ścieżkę do zmiennej środowiskowej "PATH", aby była wywoływana z dowolnego miejsca.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Wywoływanie usługi AzureDTExec oferuje podobne opcje, jak wywoływanie dtexec. Aby uzyskać więcej informacji, zobacz [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Oto opcje, które są obecnie obsługiwane:

- **/F[ile]**: Ładuje pakiet, który jest przechowywany w systemie plików, udziale plików lub plikach Azure. Jako wartość tej opcji można określić ścieżkę UNC dla pliku pakietu w systemie plików, udziale plików lub usłudze Azure Files z rozszerzeniem .dtsx. Jeśli określona ścieżka UNC zawiera dowolną przestrzeń, umieść cudzysłowy wokół całej ścieżki.
- **/Conf[igFile]**: Określa plik konfiguracyjny, z wyjęty z wartości. Za pomocą tej opcji można ustawić konfigurację czasu wykonywania dla pakietu, która różni się od określonej w czasie projektowania. Można przechowywać różne ustawienia w pliku konfiguracji XML, a następnie załadować je przed wykonaniem pakietu. Aby uzyskać więcej informacji, zobacz [Konfiguracje pakietów SSIS](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017). Aby określić wartość tej opcji, należy użyć ścieżki UNC dla pliku konfiguracyjnego w systemie plików, udziale plików lub usłudze Azure Files z rozszerzeniem dtsConfig. Jeśli określona ścieżka UNC zawiera dowolną przestrzeń, umieść cudzysłowy wokół całej ścieżki.
- **/Conn[ection]**: Określa parametry połączenia dla istniejących menedżerów połączeń w pakiecie. Za pomocą tej opcji można ustawić parametry połączenia w czasie wykonywania dla istniejących menedżerów połączeń w pakiecie, które różnią się od tych określonych w czasie projektowania. Określ wartość tej opcji `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`w następujący sposób: .
- **/Set**: Zastępuje konfigurację parametru, zmiennej, właściwości, kontenera, dostawcy dziennika, modułu wyliczającego Foreach lub połączenia w pakiecie. Tę opcję można określić wiele razy. Określ wartość tej opcji `property_path;value`w następujący sposób: . Na przykład `\package.variables[counter].Value;1` zastępuje wartość zmiennej `counter` jako 1. Za pomocą **Kreatora konfiguracji pakietu** można znaleźć, skopiować `property_path` i wkleić wartość elementów w pakiecie, których wartość ma zostać zastąpiona. Aby uzyskać więcej informacji, zobacz [Kreator konfiguracji pakietu](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014).
- **/De[crypt]**: Ustawia hasło odszyfrowywania dla pakietu, który jest skonfigurowany z poziomem ochrony **EncryptAllWithPassword**/**EncryptSensitiveWithPassword.**

> [!NOTE]
> Wywoływanie usługi AzureDTExec z nowymi wartościami dla swoich opcji generuje nowy potok z wyjątkiem opcji **/De[cript]**.

## <a name="next-steps"></a>Następne kroki

Po unikatowe potoki z execute SSIS package działania w nich są generowane i uruchamiane podczas wywoływania AzureDTExec, mogą być monitorowane w portalu fabryki danych. Można również przypisać wyzwalacze fabryki danych do nich, jeśli chcesz zorganizować/zaplanować je przy użyciu fabryki danych. Aby uzyskać więcej informacji, zobacz [Uruchamianie pakietów SSIS jako działań usługi Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

> [!WARNING]
> Oczekuje się, że wygenerowany potok będzie używany tylko przez usługę AzureDTExec. Jego właściwości lub parametry mogą ulec zmianie w przyszłości, więc nie modyfikuj ich ani nie używaj ich ponownie do innych celów. Modyfikacje mogą przerwać azuredtexec. Jeśli tak się stanie, usuń potoku. Usługa AzureDTExec generuje nowy potok przy następnym wywołaniu.