---
title: Wykonaj pakiety SSIS z SSDT
description: Dowiedz się, jak uruchamiać pakiety usług SSIS na platformie Azure z SSDT.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 1d8261d05f59c7f40ba6b1e2d59d2b15ad56de95
ms.sourcegitcommit: c052c99fd0ddd1171a08077388d221482026cd58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84424586"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Wykonywanie pakietów SSIS na platformie Azure z SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano funkcję projektów SQL Server Integration Services (SSIS) z włączoną obsługą platformy Azure w programie SQL Server Data Tools (SSDT), która umożliwia uruchamianie pakietów na Azure-SSIS Integration Runtime (IR) w Azure Data Factory (ADF).  Ta funkcja umożliwia testowanie istniejących pakietów usług SSIS przed podjęciem & Shift/migrowanie ich na platformę Azure lub opracowywanie nowych pakietów usług SSIS do uruchamiania na platformie Azure.

Za pomocą tej funkcji można utworzyć nowe Azure-SSIS IR lub dołączyć istniejące do projektów usług SSIS, a następnie wykonać na nim pakiety.  Obsługujemy uruchamianie pakietów, które mają zostać wdrożone w katalogu usług SSIS (SSISDB) w modelu wdrażania projektu i które mają zostać wdrożone w systemach plików/udziałach plików/Azure Files w modelu wdrażania pakietów. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby skorzystać z tej funkcji, Pobierz i zainstaluj najnowsze SSDT z rozszerzeniem projektów SSIS dla programu Visual Studio z tego [miejsca](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) lub jako autonomiczny Instalator w [tym miejscu](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Azure — Włączanie projektów SSIS
### <a name="create-new-azure-enabled-ssis-projects"></a>Utwórz nowe projekty SSIS obsługujące platformę Azure
W witrynie SSDT można tworzyć nowe projekty SSIS z obsługą platformy Azure przy użyciu szablonu **projektu usług Integration Services (z obsługą platformy Azure)** .

   ![Nowy projekt SSIS obsługujący platformę Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Po utworzeniu projektu z obsługą platformy Azure zostanie wyświetlony monit o połączenie z usługą SSIS w Azure Data Factory.

   ![Monit Azure-SSIS IR łączenia](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Jeśli chcesz od razu nawiązać połączenie z Azure-SSIS IR, aby uzyskać szczegółowe informacje, zapoznaj się z [Azure-SSIS IR](#irconnect) . Możesz również nawiązać połączenie później, klikając prawym przyciskiem myszy węzeł projektu w Eksplorator rozwiązań panelu SSDT, aby wyskakujące menu, a następnie wybierając pozycję **Połącz z usługą SSIS w Azure Data Factory** w obszarze menu programu **SSIS w Azure Data Factory** .

### <a name="azure-enable-existing-ssis-projects"></a>Azure — Włącz istniejące projekty SSIS
W przypadku istniejących projektów usług SSIS można je włączyć na platformie Azure, wykonując następujące czynności:

1. Kliknij prawym przyciskiem myszy węzeł projektu w Eksplorator rozwiązań panelu SSDT, aby wyskakujące menu, a następnie wybierz pozycję menu **projektu z obsługą platformy Azure** w podmenu **SSIS in Azure Data Factory** , aby uruchomić **Kreatora projektu z obsługą platformy Azure**.

   ![Azure — Włączanie istniejącego projektu SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. Na stronie **wybierz konfigurację programu Visual Studio** wybierz konfigurację programu Visual Studio, aby zastosować ustawienia wykonywania pakietu na platformie Azure. Dobrym sposobem jest utworzenie nowej konfiguracji programu Visual Studio dla chmury i platformy Azure — włączenie projektu do konfiguracji chmury. Z wieloma konfiguracjami można przypisać do parametrów różne wartości w zależności od różnych środowisk (lokalnie lub na platformie Azure). Aby uzyskać więcej informacji, zapoznaj się z [tym przykładem](#example) .

   ![Wybierz konfigurację programu Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Platforma Azure — włączenie istniejących projektów usług SSIS wymaga ustawienia wersji serwera docelowego jako najnowszej obsługiwanej przez Azure-SSIS IR, która jest obecnie **SQL Server 2017**. Dlatego jeśli jeszcze tego nie zrobiono, należy sprawdzić, czy pakiet zawiera dodatkowe składniki, które nie są obsługiwane w SQL Server 2017, i kliknij przycisk Dalej, aby kontynuować, jeśli nie ma żadnych obaw.

   ![Przełącz wersję serwera docelowego](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Zapoznaj się z [Azure-SSIS IR](#irconnect) , aby zakończyć połączenie z Azure-SSIS IR.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="irconnect"></a>Połącz projekty z obsługą platformy Azure z usługą SSIS w Azure Data Factory

Łącząc swoje projekty z platformą Azure z usługami SSIS w podajniku APD, można przekazać pakiety do Azure Files i uruchamiać je na Azure-SSIS IR. Aby to zrobić, wykonaj następujące kroki:

1. Na stronie **wprowadzenie do programu SSIS w ramach podajnika APD** Przejrzyj wprowadzenie i kliknij przycisk **dalej** , aby kontynuować.

   ![Wprowadzenie do programu SSIS w ramach podajnika APD](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. Na stronie **Wybierz środowisko IR usług SSIS w podajniku APD** wybierz istniejący zestaw danych i Azure-SSIS IR, aby uruchomić pakiety, lub Utwórz nowe, jeśli nie masz żadnego z nich.
   - Aby wybrać istniejący Azure-SSIS IR, najpierw wybierz odpowiednią subskrypcję platformy Azure i ADF.
   - W przypadku wybrania istniejącego podajnika APD, który nie ma żadnych Azure-SSIS IR, kliknij przycisk **Utwórz środowisko IR** , aby utworzyć nowy w portalu i aplikacji ADF.
   - Jeśli wybierzesz istniejącą subskrypcję platformy Azure, która nie ma żadnego podajnika APD, kliknij przycisk **Utwórz usługę SSIS IR** , aby uruchomić **Kreatora tworzenia Integration Runtime**, w którym możesz wprowadzić lokalizację i prefiks, aby automatycznie utworzyć nową grupę zasobów platformy Azure, Data Factory i SSIS IR w Twoim imieniu, o nazwie w następującym wzorcu: **YourPrefix-RG/DF/IR-YourCreationTime**.

   ![Wybierz środowisko IR usług SSIS w usłudze ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Na stronie **Wybieranie usługi Azure Storage** Wybierz istniejące konto usługi Azure Storage, aby przekazać pakiety do Azure Files, lub Utwórz nowe, jeśli nie masz żadnego z nich.
   - Aby wybrać istniejące konto usługi Azure Storage, najpierw wybierz odpowiednią subskrypcję platformy Azure.
   - W przypadku wybrania tej samej subskrypcji platformy Azure jako Azure-SSIS IR, która nie ma żadnego konta usługi Azure Storage, kliknij przycisk **Utwórz usługę Azure Storage** , aby móc automatycznie utworzyć nową nazwę w Twoim imieniu w tej samej lokalizacji co Azure-SSIS IR o nazwie przez połączenie prefiksu nazwy Azure-SSIS IR i jego daty utworzenia.
   - Jeśli wybierzesz inną subskrypcję platformy Azure, która nie ma konta usługi Azure Storage, kliknij przycisk **Utwórz usługę Azure Storage** , aby utworzyć nowy element na Azure Portal.

   ![Wybieranie pozycji Azure Storage](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Kliknij przycisk **Połącz** , aby zakończyć połączenie.  Zostanie wyświetlone wybrane Azure-SSIS IR i konto usługi Azure Storage w węźle **połączone zasoby platformy Azure** w panelu Eksplorator rozwiązań SSDT.  Odświeżamy również stan Azure-SSIS IR, podczas gdy będzie można nim zarządzać, klikając prawym przyciskiem myszy węzeł, aby wyskakujące menu, a następnie wybierając element menu **Start\Stop\Manage** , który przeprowadzi Cię przez portal/aplikację w celu wykonania tej czynności.

## <a name="execute-ssis-packages-in-azure"></a>Wykonywanie pakietów SSIS na platformie Azure
### <a name="azure-enabled-setting"></a>Ustawienie obsługujące platformę Azure
Przed wykonaniem pakietów na platformie Azure Możesz wybrać opcję konfigurowania ustawień wykonywania. Jeśli chcesz włączyć uwierzytelnianie systemu Windows dla pakietów usług SSIS, wykonaj następujące czynności:

1. Kliknij prawym przyciskiem myszy węzeł projektu w Eksplorator rozwiązań panelu SSDT, aby wyskakujące menu, a następnie wybierz pozycję menu **Ustawienia z obsługą platformy Azure** w podmenu **SSIS in Azure Data Factory** .

   ![Ustawienia z obsługą platformy Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Kliknij listę rozwijaną **Włącz uwierzytelnianie systemu Windows** i wybierz pozycję **prawda**. Następnie kliknij przycisk Edytuj dla **poświadczeń uwierzytelniania systemu Windows** , aby wprowadzić poświadczenia.

   ![Włącz uwierzytelnianie systemu Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Podaj poświadczenia w edytorze **poświadczeń uwierzytelniania systemu Windows** .

   ![Poświadczenia uwierzytelniania systemu Windows](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Rozpoczynanie wykonywania pakietów
Po nawiązaniu połączenia między projektami a programem SSIS w ramach podajnika APD można wykonywać pakiety na Azure-SSIS IR.  Dostępne są dwie opcje rozpoczęcia wykonywania pakietów:
-  Kliknij przycisk **Uruchom** na pasku narzędzi SSDT, aby rozwinąć menu i wybrać element menu **wykonaj w systemie Azure** 

   ![Wykonaj na platformie Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Kliknij prawym przyciskiem myszy węzeł pakiet w Eksplorator rozwiązań panelu SSDT, aby wyskakujące menu, a następnie wybierz pozycję **Wykonaj pakiet w menu platformy Azure** .

   ![Wykonaj pakiet na platformie Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Wykonywanie pakietów na platformie Azure wymaga uruchomionego Azure-SSIS IR, więc jeśli Azure-SSIS IR zostanie zatrzymana, okno dialogowe zostanie wyświetlona, aby je uruchomić.  Bez czasu instalacji niestandardowej ten proces powinien zostać zakończony w ciągu 5 minut, ale może potrwać około 20-30 minut w przypadku Azure-SSIS IR dołączenia do sieci wirtualnej.  Po wykonaniu pakietów na platformie Azure Możesz zatrzymać Azure-SSIS IR, aby zarządzać jego uruchomionym kosztem, klikając prawym przyciskiem myszy węzeł w Eksplorator rozwiązań panelu SSDT, aby wypróbować menu, a następnie wybierając element menu **Start\Stop\Manage** , który przeprowadzi Cię do portalu/aplikacji usługi ADF.

### <a name="checking-package-execution-logs"></a>Sprawdzanie dzienników wykonywania pakietów
Po rozpoczęciu wykonywania pakietu będziemy formatować i wyświetlać dziennik w oknie postępu SSDT.  W przypadku długiego uruchomionego pakietu będziemy okresowo aktualizować dziennik przez minuty.  Aby zatrzymać wykonywanie pakietu, kliknij przycisk **Zatrzymaj** na pasku narzędzi SSDT, który natychmiast go anuluje.  Możesz również tymczasowo znaleźć dane pierwotne dziennika w swojej ścieżce Universal Naming Convention (UNC): `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , ale zostanie ona oczyszczona po upływie jednego dnia.

### <a name="switching-package-protection-level"></a>Przełączanie poziomu ochrony pakietu
Wykonywanie pakietów SSIS na platformie Azure nie obsługuje **EncryptSensitiveWithUserKey** / poziomów ochrony EncryptSensitiveWithUserKey**EncryptAllWithUserKey** .  W związku z tym, jeśli Twoje pakiety są skonfigurowane z tymi, tymczasowo przełączymy je odpowiednio do **EncryptSensitiveWithPassword** / **EncryptAllWithPassword**, z losowo generowanymi hasłami podczas przekazywania pakietów do Azure Files do wykonania na Azure-SSIS IR.

> [!NOTE]
> Jeśli pakiety zawierają zadania wykonywania pakietów, które odwołują się do innych pakietów skonfigurowanych przy użyciu poziomów ochrony **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** , należy ręcznie skonfigurować te inne pakiety do używania **EncryptSensitiveWithPassword** / **EncryptAllWithPassword**, przed wykonaniem pakietów.

Jeśli pakiety zostały już skonfigurowane przy użyciu **EncryptSensitiveWithPassword** / poziomów ochrony EncryptSensitiveWithPassword**EncryptAllWithPassword** , firma Microsoft nie zmieni ich, ale nadal będzie używać losowo generowanych haseł podczas przekazywania pakietów do Azure Files do wykonania na Azure-SSIS IR.

### <a name="using-package-configuration-file"></a>Korzystanie z pliku konfiguracji pakietu
W przypadku używania plików konfiguracji pakietów w modelu wdrażania pakietów do zmiany wartości zmiennych w czasie wykonywania, automatycznie przekażemy te pliki do Azure Files do wykonania na Azure-SSIS IR.

### <a name="using-execute-package-task"></a>Korzystanie z zadania Execute Package
Jeśli pakiety zawierają zadania wykonywania pakietów, które odwołują się do innych pakietów przechowywanych w lokalnych systemach plików, należy wykonać następujące dodatkowe czynności konfiguracyjne:

1. Przekaż pozostałe pakiety do Azure Files w ramach tego samego konta usługi Azure Storage połączonego z projektami i uzyskaj nową ścieżkę UNC, np.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Zastąp ścieżkę pliku tych innych pakietów w Menedżerze połączeń plików zadania wykonywania pakietów z nową ścieżką UNC
   - Jeśli maszyna z systemem SSDT nie może uzyskać dostępu do nowej ścieżki UNC, możesz zmienić ścieżkę pliku w panelu Właściwości Menedżera połączeń plików
   - Alternatywnie można użyć zmiennej dla ścieżki pliku do przypisania właściwej wartości w czasie wykonywania

Jeśli pakiety zawierają zadania wykonywania pakietów, które odwołują się do innych pakietów w tym samym projekcie, nie jest wymagana żadna dodatkowa konfiguracja.

## <a name="switching-package-execution-environments-with-azure-enabled-projects"></a><a name="example"></a>Przełączanie środowisk wykonywania pakietów przy użyciu projektów obsługujących platformę Azure

Aby przełączyć środowiska wykonywania pakietów z projektami z obsługą platformy Azure, można utworzyć wiele konfiguracji programu Visual Studio, aby zastosować różne wartości parametrów specyficznych dla środowiska. Na przykład istnieje prosty pakiet usług SSIS z **zadaniem systemu plików** , które Ustawia atrybuty określonego pliku, można po prostu przeprowadzić migrację do chmury, wykonując następujące czynności:

1. Zdefiniuj parametr **FilePath** typu String, który jest ścieżką do pliku docelowego.

   ![Zdefiniuj parametr pakietu](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Skojarz **połączenie źródłowe** z tym parametrem. 

   ![Aktualizuj połączenie źródłowe](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Utwórz nową konfigurację programu Visual Studio dla chmury w programie Visual Studio Configuration Manager.

4. Zdefiniuj wartości tego parametru dla każdej konfiguracji programu Visual Studio.

   ![Przesłoń wartości parametrów](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Na platformie Azure ten projekt SSIS został włączony dla konfiguracji programu Visual Studio dla chmury.

6. Wykonaj ten pakiet na platformie Azure. Możesz łatwo przełączyć środowisko z powrotem do lokalnego, przełączając bieżącą konfigurację programu Visual Studio.

   ![Przełącz konfigurację programu Visual Studio](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

## <a name="next-steps"></a>Następne kroki
Po upewnieniu się, że pakiety na platformie Azure są zgodne z usługą SSDT, można je wdrożyć i uruchamiać jako działania pakietu SSIS w potokach ADF, zobacz [uruchamianie pakietów SSIS jako działania pakietu SSIS w potokach ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
