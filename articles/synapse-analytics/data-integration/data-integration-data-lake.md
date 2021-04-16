---
title: Pozysuj do Azure Data Lake Storage Gen2
description: Dowiedz się, jak pozysać dane do Azure Data Lake Storage Gen2 w Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: e3024fe1a8fe524a1deddef23a67d86a600b9394
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567608"
---
# <a name="ingest-data-into-azure-data-lake-storage-gen2"></a>Pozysuj dane do Azure Data Lake Storage Gen2 

W tym artykule dowiesz się, jak pozysować dane z jednej lokalizacji do innej na koncie magazynu usługi Azure Data Lake Gen 2 (Azure Data Lake Gen 2) przy użyciu usługi Azure Synapse Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure:** jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto platformy [Azure.](https://azure.microsoft.com/free/)
* **Konto usługi Azure Storage:** używasz usługi Azure Data Lake Gen 2 jako *źródłowego* magazynu danych. Jeśli nie masz konta magazynu, zobacz Tworzenie konta usługi [Azure Storage,](../../storage/common/storage-account-create.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) aby uzyskać instrukcje dotyczące jego tworzenia.

## <a name="create-linked-services"></a>Tworzenie połączonych usług

W Azure Synapse Analytics połączona usługa służy do definiowania informacji o połączeniu z innymi usługami. W tej sekcji dodasz usługi Azure Data Lake Gen 2 Azure Synapse Analytics jako połączone usługi.

1. Otwórz Azure Synapse Analytics użytkownika i przejdź do karty **Zarządzanie.**
1. W **obszarze Połączenia zewnętrzne** wybierz pozycję Połączone **usługi.**
1. Aby dodać usługę połączona, wybierz pozycję **Nowy.**
1. Wybierz kafelek Azure Data Lake Storage Gen2 z listy i wybierz pozycję **Kontynuuj.**
1. Wprowadź poświadczenia uwierzytelniania. Klucz konta, jednostkę usługi i tożsamość zarządzaną są obecnie obsługiwanymi typami uwierzytelniania. Wybierz pozycję Testuj połączenie, aby sprawdzić, czy poświadczenia są poprawne. 
1. Po zakończeniu wybierz pozycję **Utwórz**.

## <a name="create-pipeline"></a>Tworzenie potoku

Potok zawiera logiczny przepływ wykonywania zestawu działań. W tej sekcji utworzysz potok zawierający działanie kopiowania, które pozysuje dane z usługi Azure Data Lake Gen 2 do dedykowanej puli SQL.

1. Przejdź do karty **Orchestrate (Orkiestruj).** Wybierz ikonę plusa obok nagłówka potoków, a następnie wybierz pozycję **Potok**.
1. W **obszarze Przenoszenie i przekształcanie** w okienku działań przeciągnij pozycję Kopiuj **dane** na kanwę potoku.
1. Wybierz działanie kopiowania i przejdź do **karty Źródło.** Wybierz **pozycję Nowy,** aby utworzyć nowy źródłowy zestaw danych.
1. Wybierz Azure Data Lake Storage Gen2 jako magazyn danych i wybierz pozycję Kontynuuj.
1. Wybierz pozycję DelimitedText (Tekst rozdzielany) jako format i wybierz pozycję continue (kontynuuj).
1. W okienku ustawianie właściwości wybierz utworzoną przez Ciebie usługę połączona z usługą ADLS. Określ ścieżkę pliku danych źródłowych i określ, czy pierwszy wiersz ma nagłówek. Schemat można zaimportować z magazynu plików lub przykładowego pliku. Po zakończeniu wybierz przycisk OK.
1. Przejdź do karty **Ujścia.** Wybierz **pozycję Nowy,** aby utworzyć nowy zestaw danych ujścia.
1. Wybierz Azure Data Lake Storage gen2 jako magazyn danych i wybierz pozycję Kontynuuj.
1. Wybierz pozycję DelimitedText (Tekst rozdzielany) jako format i wybierz pozycję continue (kontynuuj).
1. W okienku ustawianie właściwości wybierz utworzoną przez Ciebie usługę połączona z usługą ADLS. Określ ścieżkę folderu, w którym chcesz zapisywać dane. Po zakończeniu wybierz przycisk OK.

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

Aby uzyskać więcej informacji na temat integracji danych Azure Synapse Analytics, zobacz artykuł [Inging data into a dedicated SQL pool](data-integration-sql-pool.md) (Pozysowanie danych do dedykowanej puli SQL).