---
title: Przetwarzanie danych w Azure Data Factory
description: Omówienie przetwarzanie danych w Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f922e7a2755a6e26a0d9f93f2668753e2f4dad5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98738173"
---
# <a name="what-is-data-wrangling"></a>Co to jest przetwarzanie danych?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Organizacja musi mieć możliwość eksplorowania ich krytycznych danych firmowych na potrzeby przygotowywania i przetwarzanie danych w celu zapewnienia dokładnej analizy złożonych danych, które stale rosną codziennie. Przygotowanie danych jest wymagane, aby organizacje mogły korzystać z danych w różnych procesach firmowych i skrócić czas do wartości.

Data Factory umożliwia iteracyjne Przygotowywanie danych w skali chmury przy użyciu Power Query. Data Factory integruje się z usługą [Power Query online](/power-query/) i udostępnia funkcje Power Query M jako działania potoku.

Data Factory tłumaczy M wygenerowany przez Power Query edytora zestawu połączonych online w kodzie platformy Spark na potrzeby wykonywania skalowania w chmurze przez przetłumaczenie M na przepływy danych Azure Data Factory. Dane przetwarzanie Power Query i przepływy danych są szczególnie przydatne w przypadku inżynierów danych lub "integratorów danych obywatela".

> [!NOTE]
> Działanie Power Query w usłudze Azure Data Factory jest obecnie dostępne w publicznej wersji zapoznawczej

## <a name="use-cases"></a>Przypadki zastosowań

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>Szybka interaktywna Eksploracja i przygotowanie danych

Wiele inżynierów danych i integratorów danych obywatela mogą interaktywnie eksplorowanie i przygotowywanie zestawów DataSet w skali chmury. Ze względu na wielkość, różnorodność i szybkość danych w jeziorach danych, użytkownicy potrzebują efektywnej metody eksplorowania i przygotowywania zestawów danych. Na przykład może być konieczne utworzenie zestawu danych, który zawiera wszystkie informacje demograficzne klienta dla nowych klientów od 2017. Nie masz mapowania na znany element docelowy. Przeszukasz, przetwarzanie i przygotowywanie zestawy danych, aby spełnić wymagania przed opublikowaniem go w usłudze Lake. Przetwarzanie jest często używany w przypadku mniej formalnych scenariuszy analitycznych. Zestawy danych jego mogą służyć do wykonywania transformacji i operacji uczenia maszynowego.

### <a name="code-free-agile-data-preparation"></a>Niezależne od kodu przygotowanie danych Agile

Integrator danych obywatela poświęca więcej niż 60% czasu na wyszukiwanie i przygotowywanie danych. Są one niezbędne do wykonania kodu w celu zwiększenia produktywności operacyjnej. Umożliwienie integratorom danych obywatelom wzbogacanie, kształtowanie i publikowanie danych przy użyciu znanych narzędzi, takich jak Power Query online w skalowalny sposób znacząco zwiększają produktywność. Przetwarzanie w Azure Data Factory umożliwia znanego Power Query edytora zestawu narzędzi online, aby umożliwić integratorom danych obywatelom szybkie rozwiązywanie błędów, standaryzację danych i tworzenie wysokiej jakości danych w celu wspierania podejmowania decyzji biznesowej.

### <a name="data-validation-and-exploration"></a>Sprawdzanie poprawności i Eksploracja danych

Wizualnie Skanuj dane w sposób niezależny od kodu, aby usunąć wszelkie elementy odstające, anomalie i zachować zgodność z kształtem w celu uzyskania szybkiej analizy.

## <a name="supported-sources"></a>Obsługiwane źródła

| Łącznik | Format danych | Typ uwierzytelniania |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parquet | Klucz konta |
| [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md) | CSV | Jednostka usługi |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parquet | Klucz konta, nazwa główna usługi |
| [Azure SQL Database](connector-azure-sql-database.md) | - | Uwierzytelnianie SQL |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | Uwierzytelnianie SQL |

## <a name="the-mashup-editor"></a>Edytor mashupów

Po utworzeniu działania Power Query wszystkie źródłowe zestawy danych stają się zapytaniami zestawu danych i są umieszczane w folderze **ADFResource** . Domyślnie UserQuery będzie wskazywała na pierwsze zapytanie zestawu danych. Wszystkie przekształcenia należy wykonać w UserQuery, ponieważ zmiany w zapytaniach zestawu danych nie są obsługiwane ani nie zostaną utrwalone. Zmiana nazwy, Dodawanie i usuwanie zapytań nie jest obecnie obsługiwane.

![Przetwarzanie](media/wrangling-data-flow/editor.png)

Obecnie nie wszystkie Power Query funkcje M są obsługiwane dla przetwarzanie danych, mimo że są dostępne podczas tworzenia. Podczas kompilowania działań Power Query zostanie wyświetlony monit z następującym komunikatem o błędzie, jeśli funkcja nie jest obsługiwana:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Aby uzyskać więcej informacji na temat obsługiwanych transformacji, zobacz [Data przetwarzanie Functions](wrangling-functions.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć przetwarzanie danych Power Query różny](wrangling-tutorial.md).
