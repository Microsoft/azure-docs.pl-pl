---
title: Pozysuj dane do dedykowanej puli SQL
description: Dowiedz się, jak pozysać dane do dedykowanej puli SQL w Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: pipeline
ms.date: 11/03/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 79f0b1a6942f141f0a2d2d38adc5875d82e86d50
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567625"
---
# <a name="ingest-data-into-a-dedicated-sql-pool"></a>Pozysuj dane do dedykowanej puli SQL

W tym artykule dowiesz się, jak pozysować dane z konta magazynu usługi Azure Data Lake Gen 2 do dedykowanej puli SQL w usłudze Azure Synapse Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure:** jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto platformy [Azure.](https://azure.microsoft.com/free/)
* **Konto usługi Azure Storage:** używasz Azure Data Lake Storage Gen 2 jako magazynu *danych* źródłowych. Jeśli nie masz konta magazynu, zobacz Tworzenie konta usługi [Azure Storage,](../../storage/common/storage-account-create.md) aby uzyskać instrukcje dotyczące jego tworzenia.
* **Azure Synapse Analytics:** dedykowana pula SQL jest *magazynem* danych ujścia. Jeśli nie masz wystąpienia usługi Azure Synapse Analytics, zobacz [Tworzenie dedykowanej](../../azure-sql/database/single-database-create-quickstart.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) puli SQL, aby uzyskać instrukcje tworzenia tej puli.

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W Azure Synapse Analytics połączona usługa służy do definiowania informacji o połączeniu z innymi usługami. W tej sekcji dodasz Azure Synapse Analytics i Azure Data Lake Storage Gen2 usługę.

1. Otwórz Azure Synapse Analytics użytkownika i przejdź do karty **Zarządzanie.**
1. W **obszarze Połączenia zewnętrzne** wybierz pozycję Połączone **usługi.**
1. Aby dodać usługę połączona, wybierz pozycję **Nowy.**
1. Wybierz kafelek Azure Data Lake Storage Gen2 z listy i wybierz pozycję **Kontynuuj.**
1. Wprowadź poświadczenia uwierzytelniania. Klucz konta, jednostkę usługi i tożsamość zarządzaną są obecnie obsługiwanymi typami uwierzytelniania. Wybierz pozycję Testuj połączenie, aby sprawdzić, czy poświadczenia są poprawne. Po zakończeniu wybierz pozycję **Utwórz**.
1. Powtórz kroki 3–5, ale zamiast Azure Data Lake Storage Gen2 wybierz kafelek Azure Synapse Analytics i wprowadź odpowiednie poświadczenia połączenia. W Azure Synapse Analytics obsługiwane jest uwierzytelnianie SQL, tożsamość zarządzana i nazwa główna usługi.

## <a name="create-pipeline"></a>Tworzenie potoku

Potok zawiera logiczny przepływ wykonywania zestawu działań. W tej sekcji utworzysz potok zawierający działanie kopiowania, które pozysuje dane z ADLS Gen2 do dedykowanej puli SQL.

1. Przejdź do karty **Integracja.** Wybierz ikonę plusa obok nagłówka potoków, a następnie wybierz pozycję **Potok**.
1. W **obszarze Przenoszenie i przekształcanie** w okienku działań przeciągnij pozycję Kopiuj **dane** na kanwę potoku.
1. Wybierz działanie kopiowania i przejdź do **karty Źródło.** Wybierz **pozycję Nowy,** aby utworzyć nowy źródłowy zestaw danych.
1. Wybierz Azure Data Lake Storage gen2 jako magazyn danych i wybierz pozycję Kontynuuj.
1. Wybierz pozycję DelimitedText (Tekst rozdzielany) jako format i wybierz pozycję continue (kontynuuj).
1. W okienku ustawianie właściwości wybierz utworzoną przez Ciebie usługę połączona z usługą ADLS. Określ ścieżkę pliku danych źródłowych i określ, czy pierwszy wiersz ma nagłówek. Schemat można zaimportować z magazynu plików lub przykładowego pliku. Po zakończeniu wybierz przycisk OK.
1. Przejdź do karty **Ujścia.** Wybierz **pozycję Nowy,** aby utworzyć nowy zestaw danych ujścia.
1. Wybierz Azure Synapse Analytics jako magazyn danych i wybierz pozycję Kontynuuj.
1. W okienku ustawianie właściwości wybierz Azure Synapse Analytics utworzoną usługę. Jeśli piszesz w istniejącej tabeli, wybierz ją z listy rozwijanej. W przeciwnym razie zaznacz **pole edytuj** i wprowadź nazwę nowej tabeli. Po zakończeniu wybierz przycisk OK
1. Jeśli tworzysz tabelę, włącz opcję **Utwórz automatycznie tabelę** w polu opcji tabeli.

## <a name="debug-and-publish-pipeline"></a>Debugowanie i publikowanie potoku

Po zakończeniu konfigurowania potoku możesz wykonać uruchomienie debugowania przed opublikowaniem artefaktów, aby sprawdzić, czy wszystko jest poprawne.

1. Aby debugować potok, wybierz na pasku narzędzi pozycję **Debuguj**. Na karcie **Dane wyjściowe** w dolnej części okna wyświetlany jest stan uruchomienia potoku. 
1. Po pomyślnym uruchomieniu potoku na górnym pasku narzędzi wybierz pozycję **Opublikuj wszystko.** Ta akcja publikuje utworzone jednostki (zestawy danych i potoki) w usłudze Synapse Analytics service.
1. Poczekaj na wyświetlenie komunikatu **Pomyślnie opublikowano**. Aby wyświetlić komunikaty powiadomień, wybierz przycisk dzwonka w prawym górnym rogu. 


## <a name="trigger-and-monitor-the-pipeline"></a>Wyzwalanie i monitorowanie potoku

W tym kroku ręcznie wyzwolisz potok opublikowany w poprzednim kroku. 

1. Wybierz **pozycję Dodaj wyzwalacz** na pasku narzędzi, a następnie wybierz pozycję **Wyzwolij teraz.** Na stronie **Uruchomienie potoku** wybierz przycisk **Zakończ**.  
1. Przejdź do karty **Monitorowanie** znajdującej się na lewym pasku bocznym. Widoczne jest uruchomienie potoku, które zostało wyzwolone za pomocą wyzwalacza ręcznego. Możesz użyć linków w kolumnie **Akcje,** aby wyświetlić szczegóły działań i ponownie uruchomić potok.
1. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz link **Wyświetl uruchomienia działań** w kolumnie **Akcje**. W tym przykładzie istnieje tylko jedno działanie, więc na liście jest tylko jeden wpis. Aby uzyskać szczegółowe informacje na temat operacji kopiowania, wybierz link **Szczegóły** (ikona okularów) w kolumnie **Akcje**. Wybierz **pozycję Uruchomienia potoku** u góry, aby wrócić do widoku Uruchomienia potoków. Aby odświeżyć widok, wybierz pozycję **Odśwież**.
1. Sprawdź, czy dane są poprawnie zapisane w dedykowanej puli SQL.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat integracji Azure Synapse Analytics, zobacz artykuł [Inging data into Azure Data Lake Storage Gen2 ](data-integration-data-lake.md) (Pozysuj dane do Azure Data Lake Storage Gen2 danych).