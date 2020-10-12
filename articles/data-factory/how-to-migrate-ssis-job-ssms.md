---
title: Migrowanie zadań lokalnych SQL Server Integration Services (SSIS) do Azure Data Factory
description: W tym artykule opisano, jak migrować zadania SQL Server Integration Services (SSIS) do Azure Data Factory potoki/działania/wyzwalacze przy użyciu SQL Server Management Studio.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: 6b95162d34b706b0bbb3e2940ea214e5a662655d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984909"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Migrowanie zadań agenta SQL Server do podajnika APD przy użyciu programu SSMS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Podczas [migrowania lokalnych obciążeń SQL Server Integration Services (SSIS) do usług SSIS w podajniku APD](scenario-ssis-migration-overview.md)po przeprowadzeniu migracji pakietów usług SSIS można przeprowadzić migrację wsadową zadań agentów SQL Server z typem kroku zadania SQL Server Integration Services pakiet do Azure Data Factory (ADF) potoki/działania/harmonogramy wyzwalaczy za pośrednictwem SQL Server Management Studio (SSMS) **Kreatora migracji zadań SSIS**.

Ogólnie rzecz biorąc dla wybranych zadań programu SQL Agent z odpowiednimi typami kroków zadań, **Kreator migracji zadań SSIS** może:

- Mapuj lokalizację lokalnego pakietu SSIS na miejsce, w którym migrowane są pakiety, które są dostępne dla usług SSIS w podajniku ADF.
    > [!NOTE]
    > Lokalizacja pakietu systemu plików jest obsługiwana tylko.
- Dokonaj migracji odpowiednich zadań z odpowiednimi krokami zadań do odpowiednich zasobów ADF w następujący sposób:

|Obiekt zadania programu SQL Agent  |Zasób ADF  |Uwagi|
|---------|---------|---------|
|Zadanie agenta SQL|proces     |Nazwa potoku zostanie *wygenerowana dla \<job name> *. <br> <br> Wbudowane zadania agenta nie mają zastosowania: <li> Zadanie konserwacji serwera SSIS <li> syspolicy_purge_history <li> collection_set_ * <li> mdw_purge_data_ * <li> sysutility_ *|
|Etap zadania SSIS|Działanie wykonywania pakietu SSIS|<li> Nazwa działania będzie mieć wartość \<step name> . <li> Konto serwera proxy używane w kroku zadania zostanie zmigrowane jako uwierzytelnianie systemu Windows dla tego działania. <li> *Opcje wykonywania* , z wyjątkiem *używania 32-bitowego środowiska uruchomieniowego* zdefiniowanego w kroku zadania, zostaną zignorowane w ramach migracji. <li> *Weryfikacja* zdefiniowana w kroku zadania zostanie zignorowana w ramach migracji.|
|schedule      |wyzwalacz harmonogramu        |Zostanie *wygenerowana \<schedule name> *Nazwa wyzwalacza harmonogramu. <br> <br> Poniższe opcje w harmonogramie zadań programu SQL Agent zostaną zignorowane w ramach migracji: <li> Interwał drugiego poziomu. <li> *Uruchom automatycznie, gdy zostanie uruchomiony agent SQL Server* <li> *Uruchom za każdym razem, gdy procesory staną się bezczynne* <li> dzień *tygodnia* i *dzień weekendu*<time zone> <br> Poniżej znajdują się różnice po migracji harmonogramu zadań programu SQL Agent do wyzwalacza harmonogramu ADF: <li> Wyzwalacz harmonogramu ADF po kolejnym uruchomieniu jest niezależny od stanu wykonywania wyzwalanego uruchomienia poprzedzającego. <li> Konfiguracja cyklu wyzwalacza harmonogramu usługi ADF różni się od częstotliwości codziennej w zadaniu agenta SQL.|

- Generuj szablony Azure Resource Manager (ARM) w lokalnym folderze wyjściowym i wdrażaj je bezpośrednio lub później ręcznie. Aby uzyskać więcej informacji na temat szablonów Menedżer zasobów ADF, zobacz [Microsoft. DataFactory — typy zasobów](https://docs.microsoft.com/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Wymagania wstępne

Funkcja opisana w tym artykule wymaga SQL Server Management Studio w wersji 18,5 lub nowszej. Aby uzyskać najnowszą wersję programu SSMS, zobacz [pobieranie SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).

## <a name="migrate-ssis-jobs-to-adf"></a>Migrowanie zadań SSIS do podajnika APD

1. W programie SSMS, w Eksplorator obiektów wybierz pozycję Agent SQL Server, wybierz pozycję zadania, a następnie kliknij prawym przyciskiem myszy i wybierz pozycję **Migruj zadania SSIS do ADF**.
![Zrzut ekranu przedstawia Eksplorator obiektów SQL Server Management Studio, w którym można wybrać zadania, następnie przeprowadzić migrację zadań S I s do D.](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Zaloguj się na platformie Azure, wybierz pozycję subskrypcja platformy Azure, Data Factory i Integration Runtime. Usługa Azure Storage jest opcjonalna, która jest używana w kroku mapowania lokalizacji pakietu, jeśli zadania SSIS do migracji mają pakiety systemu plików SSIS.
![DodajMenu](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Mapuj ścieżki pakietów SSIS i plików konfiguracji w zadaniach programu SSIS na ścieżki docelowe, w których można uzyskać dostęp do zmigrowanych potoków. W tym kroku mapowania można:

    1. Wybierz folder źródłowy, a następnie **Dodaj mapowanie**.
    1. Zaktualizuj ścieżkę folderu źródłowego. Prawidłowe ścieżki są ścieżkami folderów lub ścieżkami folderów nadrzędnych pakietów.
    1. Zaktualizuj ścieżkę folderu docelowego. Wartość domyślna to ścieżka względna do domyślnego konta magazynu, które jest wybrane w kroku 1.
    1. Usuń wybrane mapowanie za pośrednictwem **mapowania usuwania**.
![Zrzut ekranu przedstawia stronę Mapa i ścieżki konfiguracji mapy S, gdzie można dodać mapowanie. ](media/how-to-migrate-ssis-job-ssms/step2.png)
 ![ Zrzut ekranu przedstawia stronę Mapa S i ścieżki konfiguracji, na której można aktualizować ścieżki folderu źródłowego i docelowego.](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Wybierz odpowiednie zadania do migracji i skonfiguruj ustawienia odpowiedniego *działania wykonywanego pakietu SSIS*.

    - *Ustawienie domyślne*domyślnie stosuje się do wszystkich wybranych kroków. Aby uzyskać więcej informacji o każdej z tych właściwości, zobacz *kartę Ustawienia* dla [działania wykonaj pakiet SSIS](how-to-invoke-ssis-package-ssis-activity.md) , gdy lokalizacja pakietu to *system plików (pakiet)*.
    ![Zrzut ekranu przedstawia stronę Wybieranie zadań s I S, na której można skonfigurować ustawienia odpowiedniego wykonywanego działania pakietu SSIS.](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Ustawienie kroku*, konfigurowanie ustawienia dla wybranego kroku.
        
        **Zastosuj ustawienie domyślne**: wybrano wartość domyślną. Usuń zaznaczenie, aby skonfigurować ustawienie tylko dla wybranego kroku.  
        Aby uzyskać więcej informacji o innych właściwościach, zobacz *kartę Ustawienia* dla [działania wykonaj pakiet SSIS](how-to-invoke-ssis-package-ssis-activity.md) , gdy lokalizacja pakietu to *system plików (pakiet)*.
    ![Zrzut ekranu przedstawia stronę Wybieranie zadań s I S, w której można zastosować ustawienia domyślne.](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Generowanie i wdrażanie szablonu ARM.
    1. Wybierz lub wprowadź ścieżkę wyjściową dla szablonów ARM zmigrowanych potoków usługi ADF. Folder zostanie utworzony automatycznie, jeśli nie istnieje.
    2. Wybierz opcję **wdrażania szablonów ARM w fabryce danych**:
        - Ustawienie domyślne jest niezaznaczone. Wygenerowane szablony ARM można później wdrożyć ręcznie.
        - Wybierz, aby bezpośrednio wdrożyć wygenerowane szablony ARM w usłudze Data Factory.
    ![Zrzut ekranu przedstawia stronę Konfigurowanie migracji, na której można wybrać lub wprowadzić ścieżkę wyjściową szablonów ARM zmigrowanych potoków usługi ADF i wybrać opcję wdrażania szablonów usługi ARM w fabryce danych.](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Przeprowadź migrację, a następnie sprawdź wyniki.
![Zrzut ekranu przedstawia stronę wyników migracji, która wyświetla postęp migracji.](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Następne kroki

[Uruchamianie i monitorowanie potoku](how-to-invoke-ssis-package-ssis-activity.md)
