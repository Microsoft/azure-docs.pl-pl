---
title: Migrowanie lokalnych zadań usług integracyjnych programu SQL Server (SSIS) do usługi Azure Data Factory
description: W tym artykule opisano sposób migracji zadań usług integracyjnych programu SQL Server (SSIS) do potoków/działań/wyzwalaczy usługi Azure Data Factory przy użyciu programu SQL Server Management Studio.
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
ms.openlocfilehash: 6e357e98d6c5190c6dfef675dc1ab9cf30a717c1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455091"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Migrowanie zadań programu SQL Server Agent do usługi ADF przy za pomocą usługi SSMS

Podczas [migracji lokalnych obciążeń usług sql server integration services (SSIS) do ssis w usłudze ADF,](scenario-ssis-migration-overview.md)po migracji pakietów SSIS, można przeprowadzić migrację wsadową zadań programu SQL Server Agent z typem kroku zadania pakietu SQL Server Integration Services Package do potoków/uruchomień usługi Azure Data Factory/Schedule za pośrednictwem **Kreatora migracji zadań programu SQL**Server Management Studio (SSMS) SSIS .

Ogólnie rzecz biorąc, dla wybranych zadań agenta SQL z odpowiednimi typami kroków zadań **Kreator migracji zadań SSIS** może:

- mapowanie lokalnego pakietu SSIS do miejsca migracji pakietów, do których ssis jest dostępny w ujrzeniu ADF.
    > [!NOTE]
    > Lokalizacja pakietu systemu plików jest obsługiwana tylko.
- migrowanie odpowiednich zadań z odpowiednimi krokami zadań do odpowiednich zasobów ADF, jak poniżej:

|Obiekt zadania agenta SQL  |Zasób usługi ADF  |Uwagi|
|---------|---------|---------|
|Zadanie agenta SQL|Rurociągu     |Nazwa potoku zostanie *wygenerowana dla \<nazwy zadania>*. <br> <br> Wbudowane zadania agenta nie mają zastosowania: <li> Zadanie konserwacji serwera SSIS <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|Krok zadania SSIS|Wykonywanie działania pakietu SSIS|<li> Nazwa działania będzie \<nazwą kroku>. <li> Konto serwera proxy używane w kroku zadania zostanie zmigrowane jako uwierzytelnianie tego działania przez system Windows. <li> *Opcje wykonywania* z wyjątkiem *Użyj 32-bitowego środowiska uruchomieniowego* zdefiniowanego w kroku zadania zostaną zignorowane w migracji. <li> *Weryfikacja* zdefiniowana w kroku zadania zostanie zignorowana w migracji.|
|schedule      |wyzwalacz harmonogramu        |Nazwa wyzwalacza harmonogramu zostanie *wygenerowana dla \<nazwy harmonogramu>*. <br> <br> Poniższe opcje w harmonogramie zadań programu SQL Agent zostaną zignorowane podczas migracji: <li> Interwał drugiego poziomu. <li> *Automatyczne uruchamianie po uruchomieniu programu SQL Server Agent* <li> *Zacznij, gdy procesory staną się bezczynne* <li> *dzień powszedni* i *dzień weekendowy*<time zone> <br> Poniżej znajdują się różnice po tym, jak harmonogram zadań programu SQL Agent zostanie przeniesiony do wyzwalacza harmonogramu usługi ADF: <li> Wyzwalacz harmonogramu ADF kolejny przebieg jest niezależny od stanu wykonywania wyzwalanego przebiegu. <li> Konfiguracja cyklu wyzwalacza wyzwalacza adf różni się od dziennej częstotliwości w zadaniu agenta SQL.|

- generowanie szablonów usługi Azure Resource Manager (ARM) w lokalnym folderze wyjściowym i wdrażanie w fabryce danych bezpośrednio lub później ręcznie. Aby uzyskać więcej informacji na temat szablonów Menedżera zasobów usługi ADF, zobacz [Typy zasobów Microsoft.DataFactory](https://docs.microsoft.com/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Wymagania wstępne

Funkcja opisana w tym artykule wymaga programu SQL Server Management Studio w wersji 18.5 lub nowszej. Aby uzyskać najnowszą wersję programu SSMS, zobacz [Pobieranie programu SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).

## <a name="migrate-ssis-jobs-to-adf"></a>Migrowanie zadań SSIS do usługi ADF

1. W programie SSMS w Eksploratorze obiektów wybierz pozycję AGENT PROGRAMU SQL Server, wybierz pozycję Zadania, a następnie kliknij prawym przyciskiem myszy i wybierz polecenie **Migruj zadania SSIS do usługi ADF**.
![Menu](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Zaloguj się na platformie Azure, wybierz subskrypcję platformy Azure, fabrykę danych i środowisko uruchomieniowe integracji. Usługa Azure Storage jest opcjonalna, która jest używana w kroku mapowania lokalizacji pakietu, jeśli zadania SSIS do migracji mają pakiety systemu plików SSIS.
![Menu](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Mapuj ścieżki pakietów SSIS i plików konfiguracyjnych w zadaniach SSIS do ścieżek docelowych, do których mogą uzyskać dostęp zmigrowane potoki. W tym kroku mapowania można:

    1. Wybierz folder źródłowy, a następnie **dodaj mapowanie**.
    1. Aktualizowanie ścieżki folderu źródłowego. Prawidłowe ścieżki to ścieżki folderów lub ścieżki folderów nadrzędnych pakietów.
    1. Aktualizowanie ścieżki folderu docelowego. Wartość domyślna to ścieżka względna do domyślnego konta magazynu, które jest zaznaczone w kroku 1.
    1. Usuwanie zaznaczonego mapowania za pomocą **opcji Usuń mapowanie**.
![krok](media/how-to-migrate-ssis-job-ssms/step2.png)
![2 krok 2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Wybierz odpowiednie zadania do migracji i skonfiguruj ustawienia odpowiedniego *działania wykonanego pakietu SSIS*.

    - *Ustawienie domyślne*, domyślnie stosuje się do wszystkich wybranych kroków. Aby uzyskać więcej informacji o każdej właściwości, zobacz *ustawienia kartę* wykonywania [pakietu SSIS działania,](how-to-invoke-ssis-package-ssis-activity.md) gdy lokalizacja pakietu jest *system plików (pakiet)*.
    ![krok 3-1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Ustawienie kroku,* skonfiguruj ustawienie dla wybranego kroku.
        
        **Zastosuj ustawienie domyślne:** jest zaznaczona wartość domyślna. Usuń zaznaczenie, aby skonfigurować ustawienie tylko dla wybranego kroku.  
        Aby uzyskać więcej informacji o innych właściwościach, zobacz *kartę Ustawienia* działania Wykonywanie [pakietu SSIS,](how-to-invoke-ssis-package-ssis-activity.md) gdy lokalizacją pakietu jest *system plików (pakiet).*
    ![krok 3-2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Generowanie i wdrażanie szablonu ARM.
    1. Wybierz lub wprowadź ścieżkę wyjściową dla szablonów ARM zmigrowanych potoków podajnika ADF. Folder zostanie utworzony automatycznie, jeśli nie istnieje.
    2. Wybierz opcję **Wdrażanie szablonów ARM w fabryce danych:**
        - Wartość domyślna nie jest zaznaczona. Wygenerowane szablony ARM można wdrożyć później ręcznie.
        - Wybierz, aby wdrożyć wygenerowane szablony ARM bezpośrednio w fabryce danych.
    ![Step4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Migruj, a następnie sprawdź wyniki.
![krok 5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Następne kroki

[Uruchamianie i monitorowanie potoku](how-to-invoke-ssis-package-ssis-activity.md)
