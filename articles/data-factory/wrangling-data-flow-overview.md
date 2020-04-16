---
title: Rozsyłanie przepływów danych w fabryce danych platformy Azure
description: Omówienie przepływów danych wyjmowania w usłudze Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 71df574602518fdc252d9d86859ed772a17be443
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408978"
---
# <a name="what-are-wrangling-data-flows"></a>Co to są przepływy danych wrangling?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Organizacje muszą przygotowywać dane i wysuwać się w celu dokładnej analizy złożonych danych, które stale rosną każdego dnia. Przygotowanie danych jest wymagane, aby organizacje mogły używać danych w różnych procesach biznesowych i skrócić czas do wartości.

Wrangling przepływów danych w usłudze Azure Data Factory umożliwiają przygotowanie danych bez kodu w skali chmury iteratively. Przepływy danych Wrangling integrują się z [usługą Power Query Online](https://docs.microsoft.com/power-query/) i udostępniają funkcje dodatku Power Query M użytkownikom fabryki danych.

Wrangling przepływ danych tłumaczy M generowane przez Power Query Online Mashup Editor do kodu iskry do wykonywania skali chmury.

Wrangling przepływów danych są szczególnie przydatne dla inżynierów danych lub "integratorów danych obywatelskich".

## <a name="use-cases"></a>Przypadki zastosowań

### <a name="fast-interactive-data-exploration-and-preparation"></a>Szybkie interaktywne eksploracja i przygotowanie danych

Wielu inżynierów danych i integratorów danych obywatelskich może interaktywnie eksplorować i przygotowywać zestawy danych w skali chmury. Wraz ze wzrostem ilości, różnorodności i prędkości danych w jeziorach danych użytkownicy potrzebują skutecznego sposobu eksplorowania i przygotowywania zestawów danych. Na przykład może być konieczne utworzenie zestawu danych, który "ma wszystkie informacje demograficzne o klientach dla nowych klientów od 2017 r.". Nie mapujesz do znanego obiektu docelowego. Eksplorujesz, wrangling i prepping zestawów danych, aby spełnić wymagania przed opublikowaniem go w jeziorze. Wrangling przepływów danych są często używane dla mniej formalnych scenariuszy analizy. Zestaw danych prepped może służyć do wykonywania przekształceń i operacji uczenia maszynowego w dalszej.

### <a name="code-free-agile-data-preparation"></a>Bezkońcowe przygotowanie zwinnych danych

Integratorzy danych obywatelskich spędzają ponad 60% swojego czasu na poszukiwaniu i przygotowywaniu danych. Chcą to zrobić w sposób wolny od kodu, aby poprawić wydajność operacyjną. Umożliwienie integratorom danych obywatelskich wzbogacania, kształtowania i publikowania danych przy użyciu znanych narzędzi, takich jak Power Query Online, w skalowalny sposób drastycznie zwiększa ich produktywność. Wrangling przepływu danych w usłudze Azure Data Factory umożliwia znane Power Query Online mashup edytora, aby umożliwić integratorów danych obywateli, aby szybko naprawić błędy, standaryzować dane i tworzyć wysokiej jakości dane do obsługi decyzji biznesowych.

### <a name="data-validation"></a>Weryfikacja danych

Wizualnie skanuj dane w sposób wolny od kodu, aby usunąć wszelkie wartości odstające, anomalie i dostosować je do kształtu w celu szybkiej analizy.

## <a name="supported-sources"></a>Obsługiwane źródła

| Łącznik | Format danych | Typ uwierzytelniania |
| -- | -- | --|
| [Magazyn obiektów blob platformy Azure](connector-azure-blob-storage.md) | CSV, Parkiet | Klucz konta |
| [Usługa Azure Data Lake Storage 1. generacji](connector-azure-data-lake-store.md) | CSV | Jednostka usługi |
| [Usługa Azure Data Lake Storage 2. generacji](connector-azure-data-lake-storage.md) | CSV, Parkiet | Klucz konta, podmiot obsługujący |
| [Azure SQL Database](connector-azure-sql-database.md) | - | Uwierzytelnianie SQL |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | Uwierzytelnianie SQL |

## <a name="the-mashup-editor"></a>Edytor mashup

Podczas tworzenia przepływu danych wrangling, wszystkie zestawy danych źródłowych stają się kwerendami zestawu danych i są umieszczane w folderze **ADFResource.** Domyślnie UserQuery wskaże pierwsze zapytanie zestawu danych. Wszystkie przekształcenia powinny być wykonywane na UserQuery jak zmiany w kwerendach zestawu danych nie są obsługiwane ani nie będą one zachowywane. Zmiana nazwy, dodawanie i usuwanie zapytań nie jest obecnie obsługiwana.

![Wrangling](media/wrangling-data-flow/editor.png)

Obecnie nie wszystkie funkcje dodatku Power Query M są obsługiwane w przypadku wranglingu danych, mimo że są dostępne podczas tworzenia. Podczas tworzenia przepływów danych wrangling, zostanie wyświetlony monit z następującym komunikatem o błędzie, jeśli funkcja nie jest obsługiwana:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Aby uzyskać więcej informacji na temat obsługiwanych przekształceń, zobacz [wrangling funkcji przepływu danych](wrangling-data-flow-functions.md).

Obecnie wrangling przepływu danych obsługuje tylko zapis do jednego ujścia.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć przepływ danych.](wrangling-data-flow-tutorial.md)