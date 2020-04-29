---
title: Informacje o cenach Azure Data Factory w ramach przykładów
description: W tym artykule opisano i przedstawiono Azure Data Factory model cenowy ze szczegółowymi przykładami
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: 9d96e3f7d127f4839592e766537cbdb07cc697dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414942"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Informacje o cenach Data Factory w ramach przykładów

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano i przedstawiono model cen Azure Data Factory ze szczegółowymi przykładami.

> [!NOTE]
> Ceny używane w poniższych przykładach są hipotetyczne i nie są przeznaczone do oznaczania rzeczywistych cen.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Co godzinę Kopiuj dane z AWS S3 do usługi Azure Blob Storage

W tym scenariuszu chcesz skopiować dane z AWS S3 do magazynu obiektów blob platformy Azure zgodnie z harmonogramem godzinowym.

Aby zrealizować ten scenariusz, należy utworzyć potok z następującymi elementami:

1. Działanie kopiowania z wejściowym zestawem danych, które ma zostać skopiowane z AWS S3.

2. Wyjściowy zestaw danych w usłudze Azure Storage.

3. Wyzwalacz harmonogramu do wykonywania potoku co godzinę.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **Operacje** | **Typy i jednostki** |
| --- | --- |
| Utwórz połączoną usługę | 2 jednostka do odczytu i zapisu  |
| Tworzenie zestawów danych | 4 jednostki odczytu/zapisu (2 dla tworzenia zestawu danych, 2 dla odwołań połączonej usługi) |
| Utwórz potok | 3 jednostki odczytu/zapisu (1 dla tworzenia potoku, 2 dla odwołań do zestawu danych) |
| Pobierz potok | 1 jednostka odczytu/zapisu |
| Uruchom potok | 2 uruchomienia działania (1 dla uruchomienia wyzwalacza, 1 dla uruchomień działania) |
| Kopiowanie danych założeń: czas wykonywania = 10 min | 10 \* 4 Azure Integration Runtime (domyślne ustawienie DIU = 4) Aby uzyskać więcej informacji o jednostkach integracji danych i optymalizowaniu wydajności kopiowania, zobacz [ten artykuł](copy-activity-performance.md) . |
| Monitorowanie założeń potoku: wystąpił tylko 1 przebieg | 2 ponowione rekordy uruchomienia monitorowania (1 dla uruchomienia potoku, 1 dla uruchomienia działania) |

**Łączny Cennik scenariusza: $0,16811**

- Operacje Data Factory = **$0,0001**
  - Odczyt/zapis = 10\*00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorowanie = 2\*000005 = $0,00001 [1 Monitorowanie = $0,25/50000 = 0,000005]
- &amp; Wykonywanie aranżacji potoku = **$0,168**
  - Uruchomienia działania = 001\*2 = 0,002 [1 Run = $1/1000 = 0,001]
  - Działania przenoszenia danych = $0,166 (proporcjonalnie do 10 minut czasu wykonywania. $0,25/godz. w Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Kopiowanie danych i przekształcanie ich przy użyciu Azure Databricks co godzinę

W tym scenariuszu chcesz skopiować dane z AWS S3 do magazynu obiektów blob platformy Azure i przekształcić dane przy użyciu Azure Databricks zgodnie z harmonogramem godzinowym.

Aby zrealizować ten scenariusz, należy utworzyć potok z następującymi elementami:

1. Jedno działanie kopiowania z wejściowym zestawem danych, które ma zostać skopiowane z AWS S3, oraz wyjściowy zestaw danych dla dane w usłudze Azure Storage.
2. Jedno Azure Databricks działanie dla transformacji danych.
3. Jeden wyzwalacz harmonogramu do uruchomienia potoku co godzinę.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Operacje** | **Typy i jednostki** |
| --- | --- |
| Utwórz połączoną usługę | 3 jednostka do odczytu i zapisu  |
| Tworzenie zestawów danych | 4 jednostki odczytu/zapisu (2 dla tworzenia zestawu danych, 2 dla odwołań połączonej usługi) |
| Utwórz potok | 3 jednostki odczytu/zapisu (1 dla tworzenia potoku, 2 dla odwołań do zestawu danych) |
| Pobierz potok | 1 jednostka odczytu/zapisu |
| Uruchom potok | 3 uruchomienia działania (1 dla uruchomienia wyzwalacza, 2 dla uruchomień działania) |
| Kopiowanie danych założeń: czas wykonywania = 10 min | 10 \* 4 Azure Integration Runtime (domyślne ustawienie DIU = 4) Aby uzyskać więcej informacji o jednostkach integracji danych i optymalizowaniu wydajności kopiowania, zobacz [ten artykuł](copy-activity-performance.md) . |
| Monitorowanie założeń potoku: wystąpił tylko 1 przebieg | 3 ponowione rekordy uruchomienia monitorowania (1 dla uruchomienia potoku, 2 dla uruchomienia działania) |
| Założenie działania wykonywania elementów datakostki: czas wykonywania = 10 min | 10-minimalne wykonywanie działania zewnętrznego potoku |

**Łączny Cennik scenariusza: $0,16916**

- Operacje Data Factory = **$0,00012**
  - Odczyt/zapis = 11\*00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorowanie = 3\*000005 = $0,00001 [1 Monitorowanie = $0,25/50000 = 0,000005]
- &amp; Wykonywanie aranżacji potoku = **$0,16904**
  - Uruchomienia działania = 001\*3 = 0,003 [1 Run = $1/1000 = 0,001]
  - Działania przenoszenia danych = $0,166 (proporcjonalnie do 10 minut czasu wykonywania. $0,25/godz. w Azure Integration Runtime)
  - Działanie zewnętrznego potoku = $0,000041 (proporcjonalnie do 10 minut czasu wykonywania. $0.00025/godzinę w Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Kopiowanie danych i przekształcanie ich z parametrami dynamicznymi co godzinę

W tym scenariuszu należy skopiować dane z AWS S3 do magazynu obiektów blob platformy Azure i przekształcić je za pomocą Azure Databricks (z parametrami dynamicznymi w skrypcie) zgodnie z harmonogramem godzinowym.

Aby zrealizować ten scenariusz, należy utworzyć potok z następującymi elementami:

1. Jedno działanie kopiowania z wejściowym zestawem danych, które ma zostać skopiowane z AWS S3, wyjściowego zestawu danych dla dane w usłudze Azure Storage.
2. Jedno działanie wyszukiwania do dynamicznego przekazywania parametrów do skryptu transformacji.
3. Jedno Azure Databricks działanie dla transformacji danych.
4. Jeden wyzwalacz harmonogramu do uruchomienia potoku co godzinę.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Operacje** | **Typy i jednostki** |
| --- | --- |
| Utwórz połączoną usługę | 3 jednostka do odczytu i zapisu  |
| Tworzenie zestawów danych | 4 jednostki odczytu/zapisu (2 dla tworzenia zestawu danych, 2 dla odwołań połączonej usługi) |
| Utwórz potok | 3 jednostki odczytu/zapisu (1 dla tworzenia potoku, 2 dla odwołań do zestawu danych) |
| Pobierz potok | 1 jednostka odczytu/zapisu |
| Uruchom potok | 4 uruchomienia działania (1 dla uruchomienia wyzwalacza, 3 dla uruchomień działania) |
| Kopiowanie danych założeń: czas wykonywania = 10 min | 10 \* 4 Azure Integration Runtime (domyślne ustawienie DIU = 4) Aby uzyskać więcej informacji o jednostkach integracji danych i optymalizowaniu wydajności kopiowania, zobacz [ten artykuł](copy-activity-performance.md) . |
| Monitorowanie założeń potoku: wystąpił tylko 1 przebieg | 4 ponowione rekordy uruchomienia monitorowania (1 dla uruchomienia potoku, 3 dla uruchomienia działania) |
| Założenie wykonania działania wyszukiwania: czas wykonywania = 1 min | 1 min wykonania działania potoku |
| Założenie działania wykonywania elementów datakostki: czas wykonywania = 10 min | 10-minimalne wykonywanie działania zewnętrznego potoku |

**Łączny Cennik scenariusza: $0,17020**

- Operacje Data Factory = **$0,00013**
  - Odczyt/zapis = 11\*00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorowanie = 4\*000005 = $0,00002 [1 Monitorowanie = $0,25/50000 = 0,000005]
- &amp; Wykonywanie aranżacji potoku = **$0,17007**
  - Uruchomienia działania = 001\*4 = 0,004 [1 Run = $1/1000 = 0,001]
  - Działania przenoszenia danych = $0,166 (proporcjonalnie do 10 minut czasu wykonywania. $0,25/godz. w Azure Integration Runtime)
  - Działanie potoku = $0,00003 (proporcjonalnie do 1 minuty czasu wykonywania. $0.002/godzinę w Azure Integration Runtime)
  - Działanie zewnętrznego potoku = $0,000041 (proporcjonalnie do 10 minut czasu wykonywania. $0.00025/godzinę w Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Używanie debugowania przepływu danych mapowania dla normalnego dnia roboczego

Jako inżynier danych jest odpowiedzialny za projektowanie, kompilowanie i testowanie danych mapowania w każdym dniu. Zaloguj się do interfejsu użytkownika usługi ADF w rano i Włącz tryb debugowania dla przepływów danych. Domyślny czas wygaśnięcia sesji debugowania to 60 minut. Pracujesz w ciągu dnia przez 8 godzin, więc sesja debugowania nigdy nie wygasa. W związku z tym opłata za dzień będzie:

**8 (godz.) x 8 (rdzenie zoptymalizowane pod kątem obliczeń) x $0,193 = $12,35**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Przekształcanie danych w magazynie obiektów BLOB przy użyciu mapowania przepływów danych

W tym scenariuszu chcesz przekształcać dane w magazynie obiektów BLOB wizualnie w przepływie danych w usłudze ADF na potrzeby mapowania godzinowego.

Aby zrealizować ten scenariusz, należy utworzyć potok z następującymi elementami:

1. Działanie przepływu danych z logiką transformacji.

2. Zestaw danych wejściowych dla danych w usłudze Azure Storage.

3. Wyjściowy zestaw danych w usłudze Azure Storage.

4. Wyzwalacz harmonogramu do wykonywania potoku co godzinę.

| **Operacje** | **Typy i jednostki** |
| --- | --- |
| Utwórz połączoną usługę | 2 jednostka do odczytu i zapisu  |
| Tworzenie zestawów danych | 4 jednostki odczytu/zapisu (2 dla tworzenia zestawu danych, 2 dla odwołań połączonej usługi) |
| Utwórz potok | 3 jednostki odczytu/zapisu (1 dla tworzenia potoku, 2 dla odwołań do zestawu danych) |
| Pobierz potok | 1 jednostka odczytu/zapisu |
| Uruchom potok | 2 uruchomienia działania (1 dla uruchomienia wyzwalacza, 1 dla uruchomień działania) |
| Założenia przepływu danych: czas wykonywania = 10 min + 10 min. TTL | 10 \* 16 rdzeni ogólnych obliczeń z wartością TTL 10 |
| Monitorowanie założeń potoku: wystąpił tylko 1 przebieg | 2 ponowione rekordy uruchomienia monitorowania (1 dla uruchomienia potoku, 1 dla uruchomienia działania) |

**Łączny Cennik scenariusza: $1,4631**

- Operacje Data Factory = **$0,0001**
  - Odczyt/zapis = 10\*00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorowanie = 2\*000005 = $0,00001 [1 Monitorowanie = $0,25/50000 = 0,000005]
- &amp; Wykonywanie aranżacji potoku = **$1,463**
  - Uruchomienia działania = 001\*2 = 0,002 [1 Run = $1/1000 = 0,001]
  - Działania związane z przepływem danych = $1,461 proporcjonalnie do 20 minut (czas wykonywania w 10 minutach + 10 minut TTL). $0.274/godzinę w Azure Integration Runtime z 16 rdzeniami ogólnymi obliczeniowymi

## <a name="next-steps"></a>Następne kroki

Teraz, gdy rozumiesz Cennik Azure Data Factory, możesz rozpocząć pracę.

- [Tworzenie fabryki danych przy użyciu interfejsu użytkownika Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)

- [Tworzenie wizualne w Azure Data Factory](author-visually.md)
