---
title: Pozyskaj do Azure Data Lake Storage Gen2
description: Dowiedz się, jak pozyskać dane do Azure Data Lake Storage Gen2 w usłudze Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: cc5c72c2d0db7c17fdbc29e7fb815f1d06134730
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033221"
---
# <a name="ingest-data-into-azure-data-lake-storage-gen2"></a>Pozyskiwanie danych w Azure Data Lake Storage Gen2 

W tym artykule dowiesz się, jak pozyskać dane z jednej lokalizacji do innej w ramach konta magazynu Azure Data Lake Gen 2 (Azure Data Lake Gen 2) przy użyciu usługi Azure Synapse Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) .
* **Konto usługi Azure Storage**: używasz Azure Data Lake Gen 2 jako magazynu danych *źródłowych* . Jeśli nie masz konta magazynu, zobacz [Tworzenie konta usługi Azure Storage](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , aby uzyskać instrukcje.

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W usłudze Azure Synapse Analytics, połączona Usługa polega na definiowaniu informacji o połączeniu z innymi usługami. W tej sekcji dodasz usługę Azure Synapse Analytics i Azure Data Lake Gen 2 jako połączone usługi.

1. Otwórz środowisko Azure Synapse Analytics i przejdź na kartę **Zarządzanie** .
1. W obszarze **połączenia zewnętrzne**wybierz pozycję **połączone usługi**.
1. Aby dodać połączoną usługę, wybierz pozycję **Nowy**.
1. Wybierz z listy kafelek Azure Data Lake Storage Gen2 i wybierz pozycję **Kontynuuj**.
1. Wprowadź poświadczenia uwierzytelniania. Klucz konta, nazwa główna usługi i tożsamość zarządzana są obecnie obsługiwanymi typami uwierzytelniania. Wybierz pozycję Testuj połączenie, aby sprawdzić, czy Twoje poświadczenia są poprawne. 
1. Po zakończeniu wybierz pozycję **Utwórz**.

## <a name="create-pipeline"></a>Tworzenie potoku

Potok zawiera przepływ logiczny dla wykonywania zestawu działań. W tej sekcji utworzysz potok zawierający działanie kopiowania, które pobiera dane z Azure Data Lake generacji 2 do puli SQL.

1. Przejdź do karty **aranżacja** . Wybierz ikonę znaku plus obok nagłówka potoki i wybierz pozycję **potok**.
1. W obszarze **Przenieś i Przekształć** w okienku działania przeciągnij pozycję **Kopiuj dane** na kanwę potoku.
1. Wybierz działanie Kopiuj i przejdź do karty **Źródło** . Wybierz pozycję **Nowy** , aby utworzyć nowy źródłowy zestaw danych.
1. Wybierz Azure Data Lake Storage Gen2 jako magazyn danych, a następnie wybierz pozycję Kontynuuj.
1. Wybierz pozycję DelimitedText jako format i wybierz pozycję Kontynuuj.
1. W okienku Ustawianie właściwości wybierz utworzoną usługę ADLS. Określ ścieżkę pliku danych źródłowych i określ, czy pierwszy wiersz ma nagłówek. Możesz zaimportować schemat z magazynu plików lub pliku przykładowego. Po zakończeniu wybierz przycisk OK.
1. Przejdź do karty **ujścia** . Wybierz pozycję **Nowy** , aby utworzyć nowy zestaw danych ujścia.
1. Wybierz Azure Data Lake Storage Gen2 jako magazyn danych, a następnie wybierz pozycję Kontynuuj.
1. Wybierz pozycję DelimitedText jako format i wybierz pozycję Kontynuuj.
1. W okienku Ustawianie właściwości wybierz utworzoną usługę ADLS. Określ ścieżkę do folderu, w którym chcesz zapisać dane. Po zakończeniu wybierz przycisk OK.

## <a name="debug-and-publish-pipeline"></a>Debuguj i Publikuj potok

Po zakończeniu konfigurowania potoku możesz wykonać przebieg debugowania przed opublikowaniem artefaktów, aby upewnić się, że wszystkie elementy są poprawne.

1. Aby debugować potok, wybierz na pasku narzędzi pozycję **Debuguj**. Na karcie **Dane wyjściowe** w dolnej części okna wyświetlany jest stan uruchomienia potoku. 
1. Po pomyślnym uruchomieniu potoku na górnym pasku narzędzi wybierz pozycję **Opublikuj wszystko**. Ta akcja powoduje opublikowanie jednostek (zestawów danych i potoków) utworzonych w usłudze Synapse Analytics.
1. Poczekaj na wyświetlenie komunikatu **Pomyślnie opublikowano**. Aby wyświetlić komunikaty powiadomień, wybierz przycisk dzwonka w prawym górnym rogu. 


## <a name="trigger-and-monitor-the-pipeline"></a>Wyzwalanie i monitorowanie potoku

W tym kroku ręcznie wyzwolimy potok opublikowany w poprzednim kroku. 

1. Na pasku narzędzi wybierz pozycję **Dodaj wyzwalacz** , a następnie wybierz pozycję **Wyzwól teraz**. Na stronie **Uruchomienie potoku** wybierz przycisk **Zakończ**.  
1. Przejdź do karty **monitorowanie** znajdującej się na lewym pasku bocznym. Widoczne jest uruchomienie potoku, które zostało wyzwolone za pomocą wyzwalacza ręcznego. Możesz użyć linków w kolumnie **Akcje** , aby wyświetlić szczegóły działania i ponownie uruchomić potok.
1. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **Wyświetl uruchomienia działań** w kolumnie **Akcje**. W tym przykładzie istnieje tylko jedno działanie, dlatego na liście jest widoczny tylko jeden wpis. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Akcje**. Wybierz pozycję **uruchomienia potoku** u góry, aby wrócić do widoku uruchomienia potoków. Aby odświeżyć widok, wybierz pozycję **Odśwież**.
1. Sprawdź, czy dane są poprawnie zapisywane w puli SQL.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat integracji danych dla Synapse Analytics, zobacz artykuł pozyskiwanie [danych w puli SQL](data-integration-sql-pool.md) .
