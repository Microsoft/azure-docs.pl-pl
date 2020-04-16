---
title: Wykonywanie pakietów SSIS z SSDT
description: Dowiedz się, jak wykonywać pakiety SSIS na platformie Azure z SSDT.
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
ms.openlocfilehash: 11e76fea87c60ae2b56cc15d5827be6e1b2b5a01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399427"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>Wykonywanie pakietów SSIS na platformie Azure z narzędzia SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano funkcję projektów usług integracji programu SQL Server (SSIS) obsługujących platformę Azure w narzędziach SQL Server Data Tools (SSDT), która umożliwia uruchamianie pakietów w czasie wykonywania integracji platformy Azure-SSIS (IR) w usłudze Azure Data Factory (ADF).  Za pomocą tej funkcji można przetestować istniejące pakiety SSIS przed podniesieniem & ich zmiany/migracji na platformę Azure lub w celu opracowania nowych pakietów SSIS do uruchomienia na platformie Azure.

Za pomocą tej funkcji można utworzyć nową usługę Azure-SSIS IR lub dołączyć istniejącą do projektów SSIS, a następnie wykonać na niej pakiety.  Obsługujemy uruchomione pakiety, które mają zostać wdrożone w katalogu SSIS (SSISDB) w modelu wdrażania projektu i te, które mają zostać wdrożone w systemach plików/udziałach plików/plikach Azure w modelu wdrażania pakietów. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z tej funkcji, pobierz i zainstaluj najnowszy SSDT z rozszerzeniem SSIS Projects dla programu Visual Studio [stąd](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) lub jako samodzielny instalator [stąd](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer).

## <a name="azure-enable-ssis-projects"></a>Projekty SSIS z włączami platformą Azure
Na SSDT można tworzyć nowe projekty SSIS z obsługą platformy Azure przy użyciu szablonu **projektu Integration Services (Azure-Enabled).**

![Nowy projekt SSIS z obsługą platformy Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Alternatywnie można włączyć istniejące projekty SSIS platformy Azure, klikając prawym przyciskiem myszy węzeł projektu w panelu Eksplorator rozwiązań SSDT, aby wysuwać menu, a następnie wybierając element menu **z obsługą platformy Azure.**

![Włącz istniejący projekt SSIS na platformie Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Włączenie platformy Azure istniejących projektów SSIS wymaga, aby ustawić ich wersję serwera docelowego, aby była najnowsza obsługiwana przez usługę Azure-SSIS IR, która jest obecnie **SQL Server 2017**, więc jeśli jeszcze tego nie zrobiłeś, pojawi się okno dialogowe, aby to zrobić.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Łączenie projektów z obsługą platformy Azure z usługą SSIS w usłudze Azure Data Factory
Łącząc projekty z obsługą platformy Azure z usługą SSIS w usłudze ADF, można przekazać pakiety do usługi Azure Files i uruchomić je na platformie Azure-SSIS IR.  Aby to zrobić, wykonaj następujące kroki:

1. Kliknij prawym przyciskiem myszy węzeł projektu lub **połączonych zasobów platformy Azure** w panelu Eksplorator rozwiązań SSDT, aby wysunąć menu i wybrać pozycję menu **Połącz z usługą SSIS w usłudze Azure Data Factory,** aby uruchomić **kreatora połączeń usługi SSIS w Usłudze ADF .**

   ![Łączenie się z SSIS w podajniku ADF](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. Na stronie **Wprowadzenie SSIS w ADF** przejrzyj wprowadzenie i kliknij przycisk **Dalej,** aby kontynuować.

   ![SSIS we wprowadzeniu ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. Na stronie **Wybierz SSIS IR w Usłudze ADF** wybierz istniejący podajnik ADF i usługę Azure-SSIS IR, aby uruchamiać pakiety lub tworzyć nowe, jeśli ich nie masz.
   - Aby wybrać istniejącą usługę Azure-SSIS IR, najpierw wybierz odpowiednią subskrypcję platformy Azure i usługę ADF.
   - Jeśli wybierzesz istniejący podajnik ADF, który nie ma żadnych usług Azure-SSIS IR, kliknij przycisk **Utwórz SSIS IR,** aby utworzyć nowy na portalu/aplikacji usługi ADF.
   - Jeśli wybierzesz istniejącą subskrypcję platformy Azure, która nie ma żadnego ADF, kliknij przycisk **Utwórz SSIS IR,** aby uruchomić **Kreatora tworzenia środowiska wykonawczego integracji**, w którym możesz wprowadzić lokalizację i prefiks, aby automatycznie utworzyć nową grupę zasobów Azure, fabrykę danych i usługę SSIS IR w Twoim imieniu, nazwaną w następującym wzorze: **YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![Wybierz SSIS IR w podajniku ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Na stronie **Wybierz usługę Azure Storage** wybierz istniejące konto usługi Azure Storage, aby przekazać pakiety do usługi Azure Files lub utworzyć nowe, jeśli nie masz żadnego.
   - Aby wybrać istniejące konto usługi Azure Storage, najpierw wybierz odpowiednią subskrypcję platformy Azure.
   - Jeśli wybierzesz tę samą subskrypcję platformy Azure co urządzenie Azure-SSIS IR, które nie ma żadnego konta usługi Azure Storage, kliknij przycisk **Utwórz usługę Azure Storage,** abyśmy automatycznie utworzyli nową subskrypcję w Twoim imieniu w tej samej lokalizacji co usługa Azure-SSIS IR, nazwana przez połączenie prefiksu nazwy usługi Azure-SSIS IR i daty jego utworzenia.
   - Jeśli wybierzesz inną subskrypcję platformy Azure, która nie ma żadnego konta usługi Azure Storage, kliknij przycisk **Utwórz usługę Azure Storage,** aby utworzyć nową w witrynie Azure portal.
   
   ![Wybieranie pozycji Azure Storage](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. Kliknij przycisk **Połącz,** aby zakończyć połączenie.  Zostanie wyświetlone wybrane konto Usługi Azure-SSIS IR i usługi Azure Storage w węźle **Połączone zasoby platformy Azure** w panelu Eksplorator rozwiązań SSDT.  Będziemy również odświeżyć stan usługi Azure-SSIS IR, podczas gdy można zarządzać nim, klikając prawym przyciskiem myszy na jego węzeł, aby wyskoczyć menu, a następnie wybierając **start\Stop\Manage** element menu, który zabierze Cię do portalu usługi ADF/aplikacji, aby to zrobić.

## <a name="execute-ssis-packages-in-azure"></a>Wykonywanie pakietów SSIS na platformie Azure
### <a name="starting-package-executions"></a>Uruchamianie wykonywania pakietów
Po połączeniu projektów z usługą SSIS w usłudze ADF można wykonywać pakiety na platformie Azure-SSIS IR.  Dostępne są dwie opcje uruchamiania wykonywania pakietów:
-  Kliknij przycisk **Start** na pasku narzędzi SSDT, aby rozwijać menu i wybierz pozycję menu **Wykonaj w usłudze Azure** 

   ![Wykonywanie na platformie Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  Kliknij prawym przyciskiem myszy węzeł pakietu w panelu Eksplorator rozwiązań SSDT, aby wyskoczyć z menu i wybrać element menu **Wykonaj pakiet w usłudze Azure.**

   ![Wykonywanie pakietu na platformie Azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Wykonywanie pakietów na platformie Azure wymaga uruchomienia usługi Azure-SSIS IR, więc jeśli usługa Azure-SSIS IR zostanie zatrzymana, pojawi się okno dialogowe, aby go uruchomić.  Z wyłączeniem dowolnego czasu instalacji niestandardowej, ten proces powinien zostać zakończony w ciągu 5 minut, ale może potrwać około 20 - 30 minut dla usługi Azure-SSIS IR dołączania do sieci wirtualnej.  Po wykonaniu pakietów na platformie Azure można zatrzymać usługę Azure-SSIS IR, aby zarządzać jego kosztami bieżącymi, klikając prawym przyciskiem myszy jego węzeł w panelu Eksploratora rozwiązań SSDT, aby wyszukać menu, a następnie wybierając element menu **Start\Stop\Manage,** który umożliwia przejście do portalu/aplikacji usługi ADF.

### <a name="checking-package-execution-logs"></a>Sprawdzanie dzienników wykonywania pakietu
Po uruchomieniu wykonywania pakietu, będziemy formatować i wyświetlać jego dziennik w oknie Postępu SSDT.  W przypadku długo działającego pakietu okresowo aktualizujemy jego dziennik o minuty.  Możesz zatrzymać wykonywanie pakietu, klikając przycisk **Zatrzymaj** na pasku narzędzi SSDT, który natychmiast go anuluje.  Można również tymczasowo znaleźć dane nieprzetworzone dziennika w jego `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`Universal Naming Convention (UNC) ścieżka: , ale będziemy go oczyścić po jednym dniu.

### <a name="switching-package-protection-level"></a>Poziom ochrony pakietu przełączającego
Wykonywanie pakietów SSIS na platformie Azure nie obsługuje poziomów ochrony **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey.**  W związku z tym jeśli pakiety są skonfigurowane z tymi, tymczasowo przełączyć je do **EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, odpowiednio, z losowo generowanych haseł, gdy przekażemy pakiety do usługi Azure Files do wykonania na azure-SSIS IR.

> [!NOTE]
> Jeśli pakiety zawierają wykonywanie zadań pakietu, które odnoszą się do innych pakietów skonfigurowanych z **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** poziomy ochrony, należy ręcznie skonfigurować te inne pakiety do korzystania **z EncryptSensitiveWithPassword**/**EncryptAllWithPassword**, odpowiednio, przed wykonaniem pakietów.

Jeśli twoje pakiety są już skonfigurowane z poziomami ochrony **EncryptSensitiveWithPassword**/**EncryptAllWithPassword,** zachowamy je bez zmian, ale nadal będziemy używać losowo generowanych haseł, gdy przekażemy twoje pakiety do usługi Azure Files do wykonania na platformie Azure-SSIS IR.

### <a name="using-package-configuration-file"></a>Korzystanie z pliku konfiguracyjnego pakietu
Jeśli używasz plików konfiguracji pakietu w modelu wdrażania pakietu, aby zmienić wartości zmiennych w czasie wykonywania, automatycznie przekażemy te pliki z pakietami do usługi Azure Files do wykonania na platformie Azure-SSIS IR.

### <a name="using-execute-package-task"></a>Korzystanie z zadania wykonywania pakietu
Jeśli pakiety zawierają zadania wykonywania pakietów, które odnoszą się do innych pakietów przechowywanych w lokalnych systemach plików, należy wykonać następujące dodatkowe ustawienia:

1. Przekaż inne pakiety do usługi Azure Files na tym samym koncie usługi Azure Storage połączonym z twoimi projektami i uzyskaj nową ścieżkę UNC, np.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. Zastąp ścieżkę pliku tych innych pakietów w Menedżerze połączeń plików zadań pakietu execute, nową ścieżką UNC
   - Jeśli komputer z systemem SSDT nie może uzyskać dostępu do nowej ścieżki UNC, można zmienić ścieżkę pliku w panelu Właściwości Menedżera połączeń plików
   - Alternatywnie można użyć zmiennej dla ścieżki pliku, aby przypisać właściwą wartość w czasie wykonywania

Jeśli pakiety zawierają wykonywanie zadań pakietu, które odwołują się do innych pakietów w tym samym projekcie, nie jest konieczne dodatkowe ustawienia.

## <a name="next-steps"></a>Następne kroki
Po uzyskaniu satysfakcji z uruchamiania pakietów na platformie Azure z narzędzia SSDT można je wdrożyć i uruchomić jako wykonywanie działań pakietu SSIS w potokach usługi ADF, zobacz [Uruchamianie pakietów SSIS jako wykonywanie działań pakietu SSIS w potokach usługi ADF.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
