---
title: Zarządzanie pakietami za pomocą magazynu pakietów Azure-SSIS Integration Runtime
description: Dowiedz się, jak zarządzać pakietami przy użyciu magazynu pakietów Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 05/25/2020
ms.openlocfilehash: fa1ab60ae5690bd3782e4c849ca7cb5c29d640c2
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84199056"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Zarządzanie pakietami za pomocą magazynu pakietów Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aby podnieść & przenieść obciążenia SQL Server Integration Services lokalnych (SSIS) do chmury, możesz udostępnić Azure-SSIS Integration Runtime (IR) w Azure Data Factory (ADF). Aby uzyskać więcej informacji, zobacz temat [Inicjowanie obsługi Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure). Azure-SSIS IR obsługuje:

- Uruchomione pakiety wdrożone do wykazu usług SSIS (SSISDB) hostowane przez Azure SQL Database Server/wystąpienie zarządzane (model wdrażania projektu)
- Uruchomione pakiety wdrożone w systemie plików, Azure Files lub SQL Server Database (MSDB) hostowane przez wystąpienie zarządzane Azure SQL (model wdrażania pakietów)

Korzystając z modelu wdrażania pakietów, możesz wybrać, czy chcesz zainicjować obsługę Azure-SSIS IR z magazynami pakietów, które udostępniają warstwę zarządzania pakietami na podstawie systemu plików/Azure Files/MSDB hostowanego przez wystąpienie zarządzane usługi Azure SQL. Magazyn pakietów Azure-SSIS IR umożliwia importowanie/eksportowanie/usuwanie/uruchamianie pakietów oraz monitorowanie/zatrzymywanie uruchomionych pakietów za pośrednictwem SQL Server Management Studio (SSMS) podobnego do [starszego magazynu pakietów SSIS](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). 

## <a name="connect-to-azure-ssis-ir"></a>Połącz z Azure-SSIS IR

Po zainicjowaniu Azure-SSIS IR możesz nawiązać z nim połączenie, aby przeglądać jego magazyny pakietów w programie SSMS.

![Połącz z Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

W oknie **Eksplorator obiektów** narzędzia SSMS wybierz pozycję **Azure-SSIS Integration Runtime** z menu rozwijanego **Połącz** . Następnie zaloguj się do platformy Azure i wybierz odpowiednią subskrypcję, ADF i Azure-SSIS IR, do której są dołączone Twoje magazyny pakietów. Azure-SSIS IR pojawi się z **uruchomionymi pakietami** i węzłami **przechowywanych pakietów** poniżej. Rozwiń węzeł **przechowywane pakiety** , aby zobaczyć Twoje magazyny pakietów poniżej. Rozwiń magazyny pakietów, aby wyświetlić poniżej foldery i pakiety. Może zostać wyświetlony monit o wprowadzenie poświadczeń dostępu dla magazynów pakietów, jeśli program SSMS nie będzie mógł nawiązać połączenia automatycznie. Na przykład po rozwinięciu sklepu z pakietem w bazie danych MSDB może zostać wyświetlony monit o połączenie się z wystąpieniem zarządzanym usługi Azure SQL.

![Nawiązywanie połączenia z wystąpieniem zarządzanym usługi Azure SQL](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Zarządzanie folderami i pakietami

Podczas przeglądania Azure-SSIS IR w programie SSMS kliknij prawym przyciskiem myszy wszystkie magazyny/foldery/pakiety pakietów, aby wyskakujące menu, a następnie wybierz pozycję **Nowy folder**, **Importuj pakiet**, **Eksportuj pakiet**, **Usuń**lub **Odśwież**.

   ![Zarządzanie folderami i pakietami](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Wybierz pozycję **Nowy folder** , aby utworzyć nowy folder dla importowanych pakietów.

   *  Wybierz pozycję **Importuj pakiet** , aby zaimportować pakiety z **systemu plików**, **SQL Server** (msdb) lub starszego **magazynu pakietów usług SSIS** do sklepu z pakietami.

      ![Importuj pakiet](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      W zależności od **lokalizacji pakietu** do zaimportowania wybierz odpowiedni **Server** / **Typ uwierzytelniania**serwera, w razie potrzeby wprowadź poświadczenia dostępu, wybierz **ścieżkę pakietu**, a następnie wprowadź **nazwę nowego pakietu**. Podczas importowania pakietów nie można zmienić ich poziomu ochrony. Aby to zmienić, użyj narzędzi SQL Server Data Tools (SSDT) lub `dtutil` narzędzia wiersza polecenia.

   *  Wybierz pozycję **Eksportuj pakiet** , aby wyeksportować pakiety z magazynu pakietów do **systemu plików**, **SQL Server** (msdb) lub starszego **magazynu pakietów usług SSIS**.

      ![Eksportuj pakiet](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      W zależności od **lokalizacji pakietu** do wyeksportowania wybierz odpowiedni **Server** / **Typ uwierzytelniania**serwera, wprowadź poświadczenia dostępu w razie potrzeby, a następnie wybierz **ścieżkę pakietu**. Podczas eksportowania pakietów, jeśli są one zaszyfrowane, wprowadź hasła, aby je najpierw odszyfrować, a następnie zmienić poziom ochrony, na przykład, aby nie zapisywać poufnych danych lub zaszyfrować wszystkie/poufne dane przy użyciu klucza użytkownika/hasła.

   *  Wybierz pozycję **Usuń** , aby usunąć istniejące foldery/pakiety ze sklepu pakietu.

   *  Wybierz pozycję **Odśwież** , aby wyświetlić nowo dodane foldery/pakiety w sklepie pakietu.

## <a name="execute-packages"></a>Wykonaj pakiety

Podczas przeglądania Azure-SSIS IR w programie SSMS kliknij prawym przyciskiem myszy wszystkie przechowywane pakiety, aby wypróbować menu, a następnie wybierz pozycję **Uruchom pakiet**.  Spowoduje to otwarcie okna dialogowego **Narzędzie do wykonywania pakietów** , w którym można skonfigurować wykonywanie pakietów na Azure-SSIS IR jako działania pakietu SSIS w potokach ADF.

![Strony narzędzie do wykonywania pakietów 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Narzędzie do wykonywania pakietów stronach 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

Strony **Ogólne**, **konfiguracje**, **Opcje wykonywania**i **rejestrowania** okna dialogowego **Narzędzie do wykonywania pakietów** odpowiadają karcie **Ustawienia** działania pakietu usług SSIS, gdzie można wprowadzić hasło szyfrowania dla pakietu, uzyskać informacje o dostępie do pliku konfiguracji pakietu, poświadczeń/właściwości wykonania pakietu, a także informacje o dostępie do folderu dziennika.  Strona **Ustawianie wartości** okna dialogowego **Narzędzie do wykonywania pakietów** odpowiada karcie **zastąpień właściwości** wykonywania pakietu SSIS, w której można wprowadzić istniejące właściwości pakietu do przesłonięcia. Aby uzyskać więcej informacji, zobacz [uruchamianie pakietów SSIS jako działania pakietu SSIS w potokach ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Po wybraniu przycisku **wykonywania** okna dialogowego **Narzędzie do wykonywania pakietów** nowy potok ADF z działaniem pakiet SSIS zostanie automatycznie wygenerowany i wyzwolony. Jeśli potok ADF z tymi samymi ustawieniami wykonywania pakietu już istnieje, zostanie ponownie uruchomiony, a nowy potok nie zostanie wygenerowany. Działanie potoku APD i Uruchom pakiet SSIS zostanie odpowiednio nazwane `Pipeline_SSMS_YourPackageName_HashString` i `Activity_SSMS_YourPackageName` .

![Przycisk narzędzie do wykonywania pakietów](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Działanie wykonywania pakietu SSIS](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Monitoruj i Zatrzymaj uruchomione pakiety

Podczas przeglądania Azure-SSIS IR w programie SSMS można rozwinąć węzeł **uruchomione pakiety** , aby zobaczyć aktualnie uruchomione pakiety poniżej.  Kliknij prawym przyciskiem myszy dowolne z nich, aby wyskakujące menu, a następnie wybierz pozycję **Zatrzymaj** lub **Odśwież**.

   ![Monitoruj i Zatrzymaj uruchomione pakiety](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Wybierz pozycję **Zatrzymaj** , aby anulować aktualnie uruchomiony potok ADF, który uruchamia pakiet jako działanie wykonaj pakiet SSIS.

   *  Wybierz pozycję **Odśwież** , aby wyświetlić nowo uruchomione pakiety z magazynów pakietów.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Monitorowanie Azure-SSIS IR i edytowanie magazynów pakietów

Podczas przeglądania Azure-SSIS IR w programie SSMS kliknij prawym przyciskiem myszy, aby wyskakujące menu, a następnie wybierz pozycję **Przejdź do portalu Azure Data Factory** lub **Odśwież**.

   ![Przejdź do portalu ADF](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Wybierz pozycję **Przejdź do portalu Azure Data Factory** , aby otworzyć stronę **Integration Runtime** (centrum monitorowania ADF), w której można monitorować Azure-SSIS IR. Na kafelku **magazyny pakietów** można zobaczyć liczbę magazynów pakietów, które są dołączone do Azure-SSIS IR.  Wybranie tego numeru spowoduje wyskakujące okienko, w którym można edytować połączone usługi ADF, które przechowują informacje o dostępie do magazynów pakietów.

      ![Edytuj sklepy pakietu](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Wybierz pozycję **Odśwież** , aby wyświetlić nowo dodane foldery/pakiety w sklepach i uruchomionych pakietach z magazynów pakietów.

## <a name="next-steps"></a>Następne kroki

Możesz ponownie uruchomić/edytować automatycznie generowane potoki APD przy użyciu działań wykonywania pakietów usług SSIS lub utworzyć nowe w portalu ADF. Aby uzyskać więcej informacji, zobacz [uruchamianie pakietów SSIS jako działania pakietu SSIS w potokach ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).