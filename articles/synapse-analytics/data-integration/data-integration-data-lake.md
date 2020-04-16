---
title: Pozyskiwania do usługi Azure Data Lake Storage Gen2 w usłudze Azure Synapse Analytics
description: Dowiedz się, jak pozyskiwania danych do usługi Azure Data Lake Storage Gen2 w usłudze Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 4d7d7be523749797e5dbce0e50c307fc682974f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430580"
---
# <a name="ingesting-data-into-azure-data-lake-storage-gen2"></a>Pojmowanie danych w usłudze Azure Data Lake Storage Gen2 

W tym artykule dowiesz się, jak pozyskiwania danych z jednej lokalizacji do drugiej w usłudze Azure Data Lake Gen 2 (Azure Data Lake Gen 2) konto magazynu przy użyciu usługi Azure Synapse Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure:** Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* **Konto usługi Azure Storage:** używasz usługi Azure Data Lake Gen 2 jako *źródłowego* magazynu danych. Jeśli nie masz konta magazynu, zobacz [Tworzenie konta usługi Azure Storage,](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) aby uzyskać kroki, aby je utworzyć.

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W usłudze Azure Synapse Analytics usługa połączona to miejsce definiowania informacji o połączeniu z innymi usługami. W tej sekcji dodasz usługę Azure Synapse Analytics i usługę Azure Data Lake Gen 2 jako połączone usługi.

1. Otwórz środowisko użytkownika usługi Azure Synapse Analytics i przejdź do karty **Zarządzanie.**
1. W **obszarze Połączenia zewnętrzne**wybierz pozycję Połączone **usługi**.
1. Aby dodać usługę połączony, kliknij przycisk **Nowy**.
1. Wybierz kafelek Usługi Azure Data Lake Storage Gen2 z listy i kliknij przycisk **Kontynuuj**.
1. Wprowadź poświadczenia uwierzytelniania. Klucz konta, jednostki usługi i tożsamości zarządzanej są obecnie obsługiwane typy uwierzytelniania. Kliknij przycisk Testuj połączenie, aby sprawdzić, czy poświadczenia są poprawne. 
1. Po zakończeniu kliknij **pozycję Utwórz.**

## <a name="create-pipeline"></a>Tworzenie potoku

Potok zawiera przepływ logiczny do wykonywania zestawu działań. W tej sekcji utworzysz potok zawierający działanie kopiowania, które posyła dane z usługi Azure Data Lake Gen 2 do puli SQL.

1. Przejdź do karty **Orchestrate.** Kliknij ikonę plus obok nagłówka potoków i wybierz pozycję **Potok**.
1. W obszarze **Przenieś i przekształć** w okienku działań przeciągnij **pozycję Kopiuj dane** na kanwę potoku.
1. Kliknij aktywność kopiowania i przejdź do karty **Źródło.** Kliknij pozycję **Nowy,** aby utworzyć nowy źródłowy zestaw danych.
1. Wybierz usługę Azure Data Lake Storage Gen2 jako magazyn danych i kliknij przycisk Kontynuuj.
1. Wybierz opcję Tekst rozdzielony jako format i kliknij przycisk Kontynuuj.
1. W okienku Właściwości zestawu wybierz utworzoną usługę połączony Z usługą ADLS. Określ ścieżkę pliku danych źródłowych i określ, czy pierwszy wiersz ma nagłówek. Schemat można zaimportować z magazynu plików lub przykładowego pliku. Po zakończeniu kliknij przycisk OK.
1. Przejdź do karty **Ujście.** Kliknij przycisk **Nowy,** aby utworzyć nowy zestaw danych ujścia.
1. Wybierz usługę Azure Data Lake Storage gen2 jako magazyn danych i kliknij przycisk Kontynuuj.
1. Wybierz opcję Tekst rozdzielony jako format i kliknij przycisk Kontynuuj.
1. W okienku Właściwości zestawu wybierz utworzoną usługę połączony Z usługą ADLS. Określ ścieżkę folderu, w którym chcesz zapisywać dane. Po zakończeniu kliknij przycisk OK.

## <a name="debug-and-publish-pipeline"></a>Debugowanie i publikowanie potoku

Po zakończeniu konfigurowania potoku, można wykonać uruchomienie debugowania przed opublikowaniem artefaktów, aby sprawdzić, czy wszystko jest poprawne.

1. Aby debugować potok, wybierz na pasku narzędzi pozycję **Debuguj**. Na karcie **Dane wyjściowe** w dolnej części okna wyświetlany jest stan uruchomienia potoku. 
1. Gdy potok może działać pomyślnie, na górnym pasku narzędzi wybierz pozycję **Opublikuj wszystko**. Ta akcja publikuje jednostki (zestawy danych i potoki) utworzone w usłudze Synapse Analytics.
1. Poczekaj na wyświetlenie komunikatu **Pomyślnie opublikowano**. Aby wyświetlić komunikaty powiadomień, kliknij przycisk dzwonka w prawym górnym rogu. 


## <a name="trigger-and-monitor-the-pipeline"></a>Wyzwalanie i monitorowanie rurociągu

W tym kroku ręcznie wyzwolić potoku opublikowane w poprzednim kroku. 

1. Wybierz **pozycję Dodaj wyzwalacz** na pasku narzędzi, a następnie wybierz pozycję **Wyzwalaj teraz**. Na stronie **Uruchomienie potoku** wybierz przycisk **Zakończ**.  
1. Przejdź do karty **Monitor** znajdujący się na lewym pasku bocznym. Widoczne jest uruchomienie potoku, które zostało wyzwolone za pomocą wyzwalacza ręcznego. Łącza w kolumnie **Akcje** umożliwiają wyświetlanie szczegółów działania i ponowne uruchomienie potoku.
1. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **Wyświetl uruchomienia działań** w kolumnie **Akcje**. W tym przykładzie istnieje tylko jedno działanie, więc widzisz tylko jeden wpis na liście. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Akcje**. Wybierz **potok działa** u góry, aby wrócić do widoku Przebiegi potoku. Aby odświeżyć widok, wybierz pozycję **Odśwież**.
1. Sprawdź, czy dane są poprawnie zapisywane w puli SQL.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat integracji danych dla Usługi Synapse Analytics, zobacz [Ingesting danych do](data-integration-sql-pool.md) puli SQL artykułu.
