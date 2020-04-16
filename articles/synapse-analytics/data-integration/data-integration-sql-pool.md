---
title: Przykuwanie do puli SQL w usłudze Azure Synapse Analytics
description: Dowiedz się, jak pozyskiwania danych do puli SQL w usłudze Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: fbd8e03b1f8af7802133c35ae4860116aaea0c3c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430567"
---
# <a name="ingesting-data-into-a-sql-pool"></a>Posyłanie danych do puli SQL

W tym artykule dowiesz się, jak pozyskiwania danych z konta magazynu usługi Azure Data Lake Gen 2 do puli SQL przy użyciu usługi Azure Synapse Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure:** Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* **Konto magazynu platformy Azure:** używasz usługi Azure Data Lake Storage Gen 2 jako *źródłowego* magazynu danych. Jeśli nie masz konta magazynu, zobacz [Tworzenie konta usługi Azure Storage,](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) aby uzyskać kroki, aby je utworzyć.
* **Usługa Azure Synapse Analytics:** Używasz puli SQL jako magazynu danych *ujścia.* Jeśli nie masz wystąpienia usługi Azure Synapse Analytics, zobacz [Tworzenie puli SQL,](../../sql-database/sql-database-get-started-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) aby uzyskać kroki, aby je utworzyć.

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W usłudze Azure Synapse Analytics usługa połączona to miejsce definiowania informacji o połączeniu z innymi usługami. W tej sekcji dodasz usługę połączona usługi Azure Synapse Analytics i Azure Data Lake Storage Gen2.

1. Otwórz środowisko użytkownika usługi Azure Synapse Analytics i przejdź do karty **Zarządzanie.**
1. W **obszarze Połączenia zewnętrzne**wybierz pozycję Połączone **usługi**.
1. Aby dodać usługę połączony, kliknij przycisk **Nowy**.
1. Wybierz kafelek Usługi Azure Data Lake Storage Gen2 z listy i kliknij przycisk **Kontynuuj**.
1. Wprowadź poświadczenia uwierzytelniania. Klucz konta, jednostki usługi i tożsamości zarządzanej są obecnie obsługiwane typy uwierzytelniania. Kliknij przycisk Testuj połączenie, aby sprawdzić, czy poświadczenia są poprawne. Po zakończeniu kliknij **pozycję Utwórz.**
1. Powtórz kroki 3-5, ale zamiast usługi Azure Data Lake Storage Gen2 wybierz kafelek usługi Azure Synapse Analytics i wprowadź odpowiednie poświadczenia połączenia. W przypadku usługi Azure Synapse Analytics uwierzytelnianie SQL, tożsamość zarządzana i podmiot zabezpieczeń usługi są obecnie obsługiwane.

## <a name="create-pipeline"></a>Tworzenie potoku

Potok zawiera przepływ logiczny do wykonywania zestawu działań. W tej sekcji utworzysz potok zawierający działanie kopiowania, które pochłonie dane z usługi ADLS gen 2 do puli SQL.

1. Przejdź do karty **Orchestrate.** Kliknij ikonę plus obok nagłówka potoków i wybierz pozycję **Potok**.
1. W obszarze **Przenieś i przekształć** w okienku działań przeciągnij **pozycję Kopiuj dane** na kanwę potoku.
1. Kliknij aktywność kopiowania i przejdź do karty **Źródło.** Kliknij pozycję **Nowy,** aby utworzyć nowy źródłowy zestaw danych.
1. Wybierz usługę Azure Data Lake Storage gen2 jako magazyn danych i kliknij przycisk Kontynuuj.
1. Wybierz opcję Tekst rozdzielony jako format i kliknij przycisk Kontynuuj.
1. W okienku Właściwości zestawu wybierz utworzoną usługę połączony Z usługą ADLS. Określ ścieżkę pliku danych źródłowych i określ, czy pierwszy wiersz ma nagłówek. Schemat można zaimportować z magazynu plików lub przykładowego pliku. Po zakończeniu kliknij przycisk OK.
1. Przejdź do karty **Ujście.** Kliknij przycisk **Nowy,** aby utworzyć nowy zestaw danych ujścia.
1. Wybierz usługę Azure Synapse Analytics jako magazyn danych i kliknij przycisk Kontynuuj.
1. W okienku właściwości zestawu wybierz utworzoną usługę linked Azure Synapse Analytics. Jeśli piszesz do istniejącej tabeli, wybierz ją z listy rozwijanej. W przeciwnym razie zaznacz pole **wyboru Edytuj** i wprowadź nową nazwę tabeli. Kliknij przycisk OK po zakończeniu
1. Jeśli tworzysz tabelę, włącz **automatyczne tworzenie tabeli** w polu opcji tabeli.

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

Aby uzyskać więcej informacji na temat integracji danych dla Usługi Synapse Analytics, zobacz [Ingesting danych do usługi Azure Data Lake Storage Gen2](data-integration-data-lake.md) artykułu.
