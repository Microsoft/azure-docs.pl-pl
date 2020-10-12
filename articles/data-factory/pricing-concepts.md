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
ms.date: 09/14/2020
ms.openlocfilehash: a80e0f1b62257fdbce6598c9cc4088701cc2ae9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983620"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Informacje o cenach usługi Data Factory w ramach przykładów

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

   ![Diagram przedstawia potok z wyzwalaczem harmonogramu. W potoku do kopiowania przepływów aktywności do wejściowego zestawu danych, który przepływa do połączonej usługi W warstwie S3 i działania kopiowania, również przepływy do wyjściowego zestawu danych, który przepływa do połączonej usługi Azure Storage.](media/pricing-concepts/scenario1.png)

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
  - Odczyt/zapis = 10 \* 00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorowanie = 2 \* 000005 = $0,00001 [1 Monitorowanie = $0,25/50000 = 0,000005]
- Wykonywanie aranżacji potoku &amp; = **$0,168**
  - Uruchomienia działania = 001 \* 2 = 0,002 [1 Run = $1/1000 = 0,001]
  - Działania przenoszenia danych = $0,166 (proporcjonalnie do 10 minut czasu wykonywania. $0,25/godz. w Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Kopiowanie danych i przekształcanie ich przy użyciu Azure Databricks co godzinę

W tym scenariuszu chcesz skopiować dane z AWS S3 do magazynu obiektów blob platformy Azure i przekształcić dane przy użyciu Azure Databricks zgodnie z harmonogramem godzinowym.

Aby zrealizować ten scenariusz, należy utworzyć potok z następującymi elementami:

1. Jedno działanie kopiowania z wejściowym zestawem danych, które ma zostać skopiowane z AWS S3, oraz wyjściowy zestaw danych dla dane w usłudze Azure Storage.
2. Jedno Azure Databricks działanie dla transformacji danych.
3. Jeden wyzwalacz harmonogramu do uruchomienia potoku co godzinę.

![Diagram przedstawia potok z wyzwalaczem harmonogramu. W potoku należy skopiować przepływy działania do wejściowego zestawu danych, wyjściowego zestawu danych i działania elementów datakostki, które działa na Azure Databricks. Wejściowy zestaw danych jest przenoszony do połączonej usługi W warstwie S3. Wyjściowy zestaw danych jest przepływem do połączonej usługi Azure Storage.](media/pricing-concepts/scenario2.png)

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
  - Odczyt/zapis = 11 \* 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorowanie = 3 \* 000005 = $0,00001 [1 Monitorowanie = $0,25/50000 = 0,000005]
- Wykonywanie aranżacji potoku &amp; = **$0,16904**
  - Uruchomienia działania = 001 \* 3 = 0,003 [1 Run = $1/1000 = 0,001]
  - Działania przenoszenia danych = $0,166 (proporcjonalnie do 10 minut czasu wykonywania. $0,25/godz. w Azure Integration Runtime)
  - Działanie zewnętrznego potoku = $0,000041 (proporcjonalnie do 10 minut czasu wykonywania. $0.00025/godzinę w Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Kopiowanie danych i przekształcanie ich z parametrami dynamicznymi co godzinę

W tym scenariuszu należy skopiować dane z AWS S3 do magazynu obiektów blob platformy Azure i przekształcić je za pomocą Azure Databricks (z parametrami dynamicznymi w skrypcie) zgodnie z harmonogramem godzinowym.

Aby zrealizować ten scenariusz, należy utworzyć potok z następującymi elementami:

1. Jedno działanie kopiowania z wejściowym zestawem danych, które ma zostać skopiowane z AWS S3, wyjściowego zestawu danych dla dane w usłudze Azure Storage.
2. Jedno działanie wyszukiwania do dynamicznego przekazywania parametrów do skryptu transformacji.
3. Jedno Azure Databricks działanie dla transformacji danych.
4. Jeden wyzwalacz harmonogramu do uruchomienia potoku co godzinę.

![Diagram przedstawia potok z wyzwalaczem harmonogramu. W potoku, kopiowania przepływu aktywności do wejściowego zestawu danych, wyjściowego zestawu danych i działania wyszukiwania, które przepływa do działania elementów datakostki, które działa na Azure Databricks. Wejściowy zestaw danych jest przenoszony do połączonej usługi W warstwie S3. Wyjściowy zestaw danych jest przepływem do połączonej usługi Azure Storage.](media/pricing-concepts/scenario3.png)

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
  - Odczyt/zapis = 11 \* 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorowanie = 4 \* 000005 = $0,00002 [1 Monitorowanie = $0,25/50000 = 0,000005]
- Wykonywanie aranżacji potoku &amp; = **$0,17007**
  - Uruchomienia działania = 001 \* 4 = 0,004 [1 Run = $1/1000 = 0,001]
  - Działania przenoszenia danych = $0,166 (proporcjonalnie do 10 minut czasu wykonywania. $0,25/godz. w Azure Integration Runtime)
  - Działanie potoku = $0,00003 (proporcjonalnie do 1 minuty czasu wykonywania. $0.002/godzinę w Azure Integration Runtime)
  - Działanie zewnętrznego potoku = $0,000041 (proporcjonalnie do 10 minut czasu wykonywania. $0.00025/godzinę w Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Używanie debugowania przepływu danych mapowania dla normalnego dnia roboczego

Jako inżynier danych sam jest odpowiedzialny za projektowanie, kompilowanie i testowanie danych mapowania w każdym dniu. Sam loguje się do interfejsu użytkownika funkcji ADF z rano i włącza tryb debugowania dla przepływów danych. Domyślny czas wygaśnięcia sesji debugowania to 60 minut. Działanie sam działa w ciągu dnia przez 8 godzin, dlatego sesja debugowania nigdy nie wygasa. W związku z tym opłaty za ten dzień będą następujące:

**8 (godz.) x 8 (rdzenie zoptymalizowane pod kątem obliczeń) x $0,193 = $12,35**

W tym samym czasie Krzysztof, inny inżynier danych, również loguje się do interfejsu użytkownika przeglądarki ADF w celu profilowania danych i pracy w projekcie ETL. Krzysztof nie działa w ramach programu ADF cały dzień, takich jak sam. Krzysztof musi używać debugera przepływu danych przez 1 godzinę w tym samym okresie i w tym samym dniu co powyżej. Są to opłaty naliczane za użycie debugowania:

**1 (godzina) x 8 (ogólne rdzenie przeznaczenie) x $0,274 = $2,19**

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
  - Odczyt/zapis = 10 \* 00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorowanie = 2 \* 000005 = $0,00001 [1 Monitorowanie = $0,25/50000 = 0,000005]
- Wykonywanie aranżacji potoku &amp; = **$1,463**
  - Uruchomienia działania = 001 \* 2 = 0,002 [1 Run = $1/1000 = 0,001]
  - Działania związane z przepływem danych = $1,461 proporcjonalnie do 20 minut (czas wykonywania w 10 minutach + 10 minut TTL). $0.274/godzinę w Azure Integration Runtime z 16 rdzeniami ogólnymi obliczeniowymi

## <a name="data-integration-in-azure-data-factory-managed-vnet"></a>Integracja danych w Azure Data Factory zarządzanej sieci wirtualnej
W tym scenariuszu chcesz usunąć oryginalne pliki na platformie Azure Blob Storage i skopiować dane z Azure SQL Database do usługi Azure Blob Storage. To wykonanie jest dwa razy dla różnych potoków. Czas wykonywania tych dwóch potoków nakłada się na siebie.
![Scenario4 ](media/pricing-concepts/scenario-4.png) do osiągnięcia scenariusza, należy utworzyć dwa potoki z następującymi elementami:
  - Działanie potoku — usuwanie działania.
  - Działanie kopiowania z wejściowym zestawem danych, który ma być kopiowany z usługi Azure Blob Storage.
  - Wyjściowy zestaw danych na Azure SQL Database.
  - Harmonogram wyzwala wykonywanie potoku.


| **Operacje** | **Typy i jednostki** |
| --- | --- |
| Utwórz połączoną usługę | 4 jednostka do odczytu i zapisu |
| Tworzenie zestawów danych | 8 jednostek odczytu/zapisu (4 dla tworzenia zestawu danych, 4 dla odwołań do połączonych usług) |
| Utwórz potok | 6 jednostek odczytu/zapisu (2 dla tworzenia potoku, 4 dla odwołań do zestawu danych) |
| Pobierz potok | 2 jednostka do odczytu i zapisu |
| Uruchom potok | 6 uruchomień działania (2 dla uruchomienia wyzwalacza, 4 dla uruchomień działania) |
| Wykonaj działanie usuwania: każdy czas wykonywania = 5 minut. Wykonanie działania usuwania w pierwszym potoku to od 10:00 czasu UTC do 10:05 czasu UTC. Wykonanie działania usuwania w drugim potoku to od 10:02 czasu UTC do 10:07 czasu UTC.|Łącznie 7 min wykonywania działania potoku w zarządzanej sieci wirtualnej. Działanie potoku obsługuje do 50 współbieżności w zarządzanej sieci wirtualnej. |
| Kopiowanie danych założeń: każdy czas wykonywania = 10 min. Wykonanie kopii w pierwszym potoku to od 10:06 czasu UTC do 10:15 czasu UTC. Wykonanie działania usuwania w drugim potoku to od 10:08 czasu UTC do 10:17 czasu UTC. | 10 * 4 Azure Integration Runtime (domyślne ustawienie DIU = 4) Aby uzyskać więcej informacji o jednostkach integracji danych i optymalizowaniu wydajności kopiowania, zobacz [ten artykuł](copy-activity-performance.md) . |
| Monitorowanie założeń potoku: wystąpiły tylko 2 uruchomienia | 6 ponownych prób uruchomienia monitorowania (2 dla uruchomienia potoku, 4 dla uruchomienia działania) |


**Łączny Cennik scenariusza: $0,45523**

- Operacje Data Factory = $0,00023
  - Odczyt/zapis = 20 * 00001 = $0,0002 [1 R/W = $0,50/50000 = 0,00001]
  - Monitorowanie = 6 * 000005 = $0,00003 [1 Monitorowanie = $0,25/50000 = 0,000005]
- Organizowanie potoku & wykonywania = $0,455
  - Uruchomienia działania = 0,001 * 6 = 0,006 [1 Run = $1/1000 = 0,001]
  - Działania przenoszenia danych = $0,333 (proporcjonalnie do 10 minut czasu wykonywania. $0,25/godz. w Azure Integration Runtime)
  - Działanie potoku = $0,116 (proporcjonalnie do 7 minut czasu wykonywania. $1/godzinę w Azure Integration Runtime)

> [!NOTE]
> Te ceny są wyłącznie na przykład.

**Często zadawane pytania**

P: Jeśli chcę uruchomić więcej niż 50 działań potoku, czy te działania mogą być wykonywane jednocześnie?

Odp.: dozwolone są maksymalnie 50 współbieżnych działań potokowych.  Działanie potoku 51th będzie umieszczane w kolejce do momentu otwarcia okna "wolne miejsce". Ta sama dla działania zewnętrznego. Dozwolone są maksymalnie 800 współbieżne działania zewnętrzne.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy rozumiesz Cennik Azure Data Factory, możesz rozpocząć pracę.

- [Tworzenie fabryki danych przy użyciu interfejsu użytkownika Azure Data Factory](quickstart-create-data-factory-portal.md)

- [Wprowadzenie do usługi Azure Data Factory](introduction.md)

- [Tworzenie wizualne w Azure Data Factory](author-visually.md)
