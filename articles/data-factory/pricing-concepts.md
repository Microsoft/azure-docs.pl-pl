---
title: Opis cen usługi Azure Data Factory za pomocą przykładów
description: W tym artykule wyjaśniono i przedstawiono model cenowy usługi Azure Data Factory ze szczegółowymi przykładami
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414942"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Opis cen w fabryce danych za pomocą przykładów

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule wyjaśniono i przedstawiono model cenowy usługi Azure Data Factory ze szczegółowymi przykładami.

> [!NOTE]
> Ceny stosowane w poniższych przykładach mają hipotetyczne wartości i nie mają na celu sugerowania rzeczywistych cen.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Kopiowanie danych z usługi AWS S3 do magazynu obiektów blob platformy Azure co godzinę

W tym scenariuszu chcesz skopiować dane z usługi AWS S3 do magazynu obiektów blob platformy Azure zgodnie z harmonogramem godzinowym.

Aby wykonać scenariusz, należy utworzyć potok z następującymi elementami:

1. Działanie kopiowania z wejściowym zestawem danych dla danych, które mają być kopiowane z AWS S3.

2. Wyjściowy zestaw danych dla danych w usłudze Azure Storage.

3. Wyzwalacz harmonogramu do wykonywania potoku co godzinę.

   ![Scenariusz1](media/pricing-concepts/scenario1.png)

| **Operacje** | **Typy i jednostki** |
| --- | --- |
| Tworzenie usługi połączonej | 2 Jednostka odczytu/zapisu  |
| Tworzenie zestawów danych | 4 Jednostki odczytu/zapisu (2 dla tworzenia zestawu danych, 2 dla odwołań do usługi połączonej) |
| Utwórz potok | 3 Jednostki odczytu/zapisu (1 do tworzenia potoku, 2 dla odwołań do zestawu danych) |
| Pobierz potok | 1 Jednostka odczytu/zapisu |
| Uruchom potok | 2 Przebiegi działania (1 dla uruchomienia wyzwalacza, 1 dla przebiegów działań) |
| Kopiuj założenie danych: czas wykonania = 10 min | 10 \* 4 Środowisko uruchomieniowe integracji platformy Azure (domyślne ustawienie DIU = 4) Aby uzyskać więcej informacji na temat jednostek integracji danych i optymalizacji wydajności kopiowania, zobacz [ten artykuł](copy-activity-performance.md) |
| Monitorowanie założenia potoku: wystąpił tylko 1 bieg | 2 Monitorowanie rekordów przebiegu ponowiony (1 dla przebiegu potoku, 1 dla działania uruchamiane) |

**Łączna cena scenariusza: $0.16811**

- Operacje fabryczne danych = **$0.0001**
  - Odczyt/zapis = 10\*00001 = $0.0001 [1 R/W = $0.50/50000 = 0.00001]
  - Monitorowanie =\*2 000005 = $0.00001 [1 Monitorowanie = $0.25/50000 = 0.000005]
- Wykonanie aranżacji &amp; potoku = **$0.168**
  - Przebiegi działania =\*001 2 = 0,002 [1 run = $1/1000 = 0.001]
  - Działania przenoszenia danych = $0.166 (proporcjonalnie do 10 minut czasu wykonywania. $0.25/godz. w czasie wykonywania integracji platformy Azure)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Kopiowanie danych i przekształcanie za pomocą usługi Azure Databricks co godzinę

W tym scenariuszu chcesz skopiować dane z usługi AWS S3 do magazynu obiektów blob platformy Azure i przekształcić dane za pomocą usługi Azure Databricks zgodnie z harmonogramem godzinowym.

Aby wykonać scenariusz, należy utworzyć potok z następującymi elementami:

1. Jedno działanie kopiowania z wejściowym zestawem danych dla danych, które mają być kopiowane z usługi AWS S3, oraz wyjściowym zestawem danych dla danych w magazynie platformy Azure.
2. Jedna aktywność usługi Azure Databricks dla transformacji danych.
3. Jeden wyzwalacz harmonogramu do wykonywania potoku co godzinę.

![Scenariusz2](media/pricing-concepts/scenario2.png)

| **Operacje** | **Typy i jednostki** |
| --- | --- |
| Tworzenie usługi połączonej | 3 Jednostka odczytu/zapisu  |
| Tworzenie zestawów danych | 4 Jednostki odczytu/zapisu (2 dla tworzenia zestawu danych, 2 dla odwołań do usługi połączonej) |
| Utwórz potok | 3 Jednostki odczytu/zapisu (1 do tworzenia potoku, 2 dla odwołań do zestawu danych) |
| Pobierz potok | 1 Jednostka odczytu/zapisu |
| Uruchom potok | 3 Przebiegi działania (1 dla uruchomienia wyzwalacza, 2 dla przebiegów działań) |
| Kopiuj założenie danych: czas wykonania = 10 min | 10 \* 4 Środowisko uruchomieniowe integracji platformy Azure (domyślne ustawienie DIU = 4) Aby uzyskać więcej informacji na temat jednostek integracji danych i optymalizacji wydajności kopiowania, zobacz [ten artykuł](copy-activity-performance.md) |
| Monitorowanie założenia potoku: wystąpił tylko 1 bieg | 3 Monitorowanie rekordów przebiegu ponowiony (1 dla przebiegu potoku, 2 dla działania uruchamiane) |
| Wykonywanie działania Databricks Założenie: czas wykonania = 10 min | 10 min Wykonanie działania zewnętrznego potoku |

**Łączna cena scenariusza: $0.16916**

- Operacje fabryczne danych = **$0.00012**
  - Odczyt/zapis = 11\*00001 = $0.00011 [1 R/W = $0.50/50000 = 0.00001]
  - Monitorowanie =\*3 000005 = $0.00001 [1 Monitorowanie = $0.25/50000 = 0.000005]
- Wykonanie aranżacji &amp; potoku = **$0.16904**
  - Przebiegi działania =\*001 3 = 0,003 [1 run = $1/1000 = 0.001]
  - Działania przenoszenia danych = $0.166 (proporcjonalnie do 10 minut czasu wykonywania. $0.25/godz. w czasie wykonywania integracji platformy Azure)
  - Działanie potoku zewnętrznego = $0.000041 (proporcjonalnie do 10 minut czasu wykonywania. $0.00025/hour w usłudze Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Kopiowanie danych i przekształcanie z parametrami dynamicznymi co godzinę

W tym scenariuszu chcesz skopiować dane z usługi AWS S3 do magazynu obiektów blob platformy Azure i przekształcić za pomocą usługi Azure Databricks (z parametrami dynamicznymi w skrypcie) zgodnie z harmonogramem godzinowym.

Aby wykonać scenariusz, należy utworzyć potok z następującymi elementami:

1. Jedno działanie kopiowania z wejściowym zestawem danych dla danych, które mają być kopiowane z AWS S3, wyjściowego zestawu danych dla danych w magazynie platformy Azure.
2. Jedno działanie odnośnika do dynamicznego przekazywania parametrów do skryptu transformacji.
3. Jedna aktywność usługi Azure Databricks dla transformacji danych.
4. Jeden wyzwalacz harmonogramu do wykonywania potoku co godzinę.

![Scenariusz3](media/pricing-concepts/scenario3.png)

| **Operacje** | **Typy i jednostki** |
| --- | --- |
| Tworzenie usługi połączonej | 3 Jednostka odczytu/zapisu  |
| Tworzenie zestawów danych | 4 Jednostki odczytu/zapisu (2 dla tworzenia zestawu danych, 2 dla odwołań do usługi połączonej) |
| Utwórz potok | 3 Jednostki odczytu/zapisu (1 do tworzenia potoku, 2 dla odwołań do zestawu danych) |
| Pobierz potok | 1 Jednostka odczytu/zapisu |
| Uruchom potok | 4 Przebiegi działania (1 dla uruchomienia wyzwalacza, 3 dla przebiegów działań) |
| Kopiuj założenie danych: czas wykonania = 10 min | 10 \* 4 Środowisko uruchomieniowe integracji platformy Azure (domyślne ustawienie DIU = 4) Aby uzyskać więcej informacji na temat jednostek integracji danych i optymalizacji wydajności kopiowania, zobacz [ten artykuł](copy-activity-performance.md) |
| Monitorowanie założenia potoku: wystąpił tylko 1 bieg | 4 Monitorowanie rekordów przebiegu ponowiony (1 dla przebiegu potoku, 3 dla działania uruchamiane) |
| Wykonanie działania odnośnika Założenie: czas wykonania = 1 min | 1 min Wykonanie działania potoku |
| Wykonywanie działania Databricks Założenie: czas wykonania = 10 min | 10 min Wykonanie działania zewnętrznego potoku |

**Łączna cena scenariusza: $0.17020**

- Operacje fabryczne danych = **$0.00013**
  - Odczyt/zapis = 11\*00001 = $0.00011 [1 R/W = $0.50/50000 = 0.00001]
  - Monitorowanie =\*4 000005 = $0.00002 [1 Monitorowanie = $0.25/50000 = 0.000005]
- Wykonanie aranżacji &amp; potoku = **$0.17007**
  - Przebiegi działania =\*001 4 = 0,004 [1 run = $1/1000 = 0.001]
  - Działania przenoszenia danych = $0.166 (proporcjonalnie do 10 minut czasu wykonywania. $0.25/godz. w czasie wykonywania integracji platformy Azure)
  - Działanie potoku = $0.00003 (proporcjonalnie do 1 minuty czasu wykonywania. $0.002/godz. w czasie wykonywania integracji platformy Azure)
  - Działanie potoku zewnętrznego = $0.000041 (proporcjonalnie do 10 minut czasu wykonywania. $0.00025/hour w usłudze Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Używanie deugowania przepływu danych mapowania dla normalnego dnia roboczego

Jako inżynier danych jesteś odpowiedzialny za projektowanie, tworzenie i testowanie przepływów danych mapowania każdego dnia. Zaloguj się do interfejsu użytkownika usługi ADF rano i włączyć tryb debugowania przepływów danych. Domyślny czas wygaśnięcia dla sesji debugowania wynosi 60 minut. Pracujesz przez cały dzień przez 8 godzin, więc sesja debugowania nigdy nie wygasa. W związku z tym opłata za dzień będzie:

**8 (godziny) x 8 (rdzenie zoptymalizowane pod kątem mocy obliczeniowej) x $0.193 = $12.35**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Przekształcanie danych w magazynie obiektów blob za pomocą przepływów danych mapowania

W tym scenariuszu chcesz przekształcić dane w magazynie obiektów Blob wizualnie w przepływach danych mapowania usługi ADF zgodnie z harmonogramem godzinowym.

Aby wykonać scenariusz, należy utworzyć potok z następującymi elementami:

1. A Działanie przepływu danych z logiką transformacji.

2. Wejściowy zestaw danych dla danych w usłudze Azure Storage.

3. Wyjściowy zestaw danych dla danych w usłudze Azure Storage.

4. Wyzwalacz harmonogramu do wykonywania potoku co godzinę.

| **Operacje** | **Typy i jednostki** |
| --- | --- |
| Tworzenie usługi połączonej | 2 Jednostka odczytu/zapisu  |
| Tworzenie zestawów danych | 4 Jednostki odczytu/zapisu (2 dla tworzenia zestawu danych, 2 dla odwołań do usługi połączonej) |
| Utwórz potok | 3 Jednostki odczytu/zapisu (1 do tworzenia potoku, 2 dla odwołań do zestawu danych) |
| Pobierz potok | 1 Jednostka odczytu/zapisu |
| Uruchom potok | 2 Przebiegi działania (1 dla uruchomienia wyzwalacza, 1 dla przebiegów działań) |
| Założenia przepływu danych: czas wykonania = 10 min + 10 min CZASU WYGAŚNIĘCIA | 10 \* 16 rdzeni obliczeń ogólnych z TTL 10 |
| Monitorowanie założenia potoku: wystąpił tylko 1 bieg | 2 Monitorowanie rekordów przebiegu ponowiony (1 dla przebiegu potoku, 1 dla działania uruchamiane) |

**Całkowita cena scenariusza: $1.4631**

- Operacje fabryczne danych = **$0.0001**
  - Odczyt/zapis = 10\*00001 = $0.0001 [1 R/W = $0.50/50000 = 0.00001]
  - Monitorowanie =\*2 000005 = $0.00001 [1 Monitorowanie = $0.25/50000 = 0.000005]
- Wykonanie aranżacji &amp; potoku = **$1.463**
  - Przebiegi działania =\*001 2 = 0,002 [1 run = $1/1000 = 0.001]
  - Działania przepływu danych = $1.461 proporcjonalnie przez 20 minut (10 min czasu wykonania + 10 min TTL). $0.274/hour w usłudze Azure Integration Runtime z 16 rdzeniami ogólnego obliczeń

## <a name="next-steps"></a>Następne kroki

Teraz, gdy rozumiesz ceny usługi Azure Data Factory, możesz rozpocząć pracę!

- [Tworzenie fabryki danych przy użyciu interfejsu użytkownika usługi Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)

- [Tworzenie wizualne w fabryce danych platformy Azure](author-visually.md)
