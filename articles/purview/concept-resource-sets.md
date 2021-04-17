---
title: Informacje o zestawach zasobów
description: W tym artykule wyjaśniono, czym są zestawy zasobów i jak usługa Azure Purview je tworzy.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 330a6e54ee88781f71c4a861051aab94f8eef81f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587905"
---
# <a name="understanding-resource-sets"></a>Informacje o zestawach zasobów

Ten artykuł pomaga zrozumieć, w jaki sposób usługa Azure Purview używa zestawów zasobów do mapowania zasobów danych na zasoby logiczne.
## <a name="background-info"></a>Informacje ogólne

Systemy przetwarzania danych na dużą skalę zwykle przechowują pojedynczą tabelę na dysku jako wiele plików. Ta koncepcja jest reprezentowana w usłudze Azure Purview przy użyciu zestawów zasobów. Zestaw zasobów to pojedynczy obiekt w katalogu, który reprezentuje dużą liczbę zasobów w magazynie.

Załóżmy na przykład, że klaster Spark utrwalił ramkę danych w źródle Azure Data Lake Storage (ADLS) Gen2. Chociaż na platformie Spark tabela wygląda jak pojedynczy zasób logiczny, na dysku prawdopodobnie znajdują się tysiące plików Parquet, z których każdy reprezentuje partycję całkowitej zawartości ramki danych. Dane IoT i dane dzienników sieci Web mają to samo wyzwanie. Wyobraź sobie, że masz czujnik, który wyprowadza pliki dziennika kilka razy na sekundę. Nie potrwa to długo, dopóki nie będziesz mieć setek tysięcy plików dziennika z tego pojedynczego czujnika.

Aby rozwiązać problem mapowania dużej liczby zasobów danych na pojedynczy zasób logiczny, usługa Azure Purview używa zestawów zasobów.

## <a name="how-azure-purview-detects-resource-sets"></a>Jak usługa Azure Purview wykrywa zestawy zasobów

Usługa Azure Purview obsługuje wykrywanie zestawów zasobów w Azure Blob Storage, ADLS Gen1 i ADLS Gen2.

Usługa Azure Purview automatycznie wykrywa zestawy zasobów podczas skanowania. Ta funkcja przejmuje wszystkie dane, które są pozytywowane przez skanowanie, i porównuje je z zestawem zdefiniowanych wzorców.

Załóżmy na przykład, że skanujesz źródło danych, którego adres URL to `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Usługa Azure Purview wyszukuje segmenty ścieżek i określa, czy pasują one do wbudowanych wzorców. Ma wbudowane wzorce identyfikatorów GUID, liczb, formatów dat, kodów lokalizacji (na przykład en-us) i tak dalej. W tym przypadku wzorzec liczby odpowiada *23*. Usługa Azure Purview zakłada, że ten plik jest częścią zestawu zasobów o nazwie `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Lub, w przypadku adresu URL, takiego jak , usługa Azure Purview dopasowuje wzorzec lokalizacji i wzorzec liczb, tworząc `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` zestaw zasobów o nazwie `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Korzystając z tej strategii, usługa Azure Purview mapuje następujące zasoby do tego samego zestawu `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` zasobów:

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Typy plików, które usługa Azure Purview nie będzie wykrywać jako zestawy zasobów

Purview celowo nie próbuje sklasyfikować większości typów plików dokumentów, takich jak Word, Excel lub PDF, jako zestawów zasobów. Wyjątkiem jest format CSV, ponieważ jest to wspólny format pliku partycjonowania.

## <a name="how-azure-purview-scans-resource-sets"></a>Jak usługa Azure Purview skanuje zestawy zasobów

Gdy usługa Azure Purview wykryje zasoby, które uważa za część zestawu zasobów, przełącza się z pełnego skanowania na przykładowe skanowanie. W przykładowym skanowaniu otwierany jest tylko podzbiór plików, które jego zdaniem znajdują się w zestawie zasobów. Dla każdego pliku, który jest otwierany, używa swojego schematu i uruchamia klasyfikatory. Usługa Azure Purview następnie znajduje najnowszy zasób wśród otwartych zasobów i używa schematu i klasyfikacji tego zasobu we wpisie dla całego zestawu zasobów w wykazie.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Co usługa Azure Purview przechowuje na temat zestawów zasobów

Oprócz pojedynczego schematu i klasyfikacji usługa Azure Purview przechowuje następujące informacje o zestawach zasobów:

- Dane z najnowszego zasobu partycji są skanowane w głębi systemu.
- Agregowanie informacji o zasobach partycji, które zawierają zestaw zasobów.
- Liczba partycji, która pokazuje liczbę znalezionych zasobów partycji.
- Liczba schematów, która pokazuje, ile unikatowych schematów znaleziono w przykładowym zestawie, w których głębokie skanowania. Ta wartość jest liczbą z wartości od 1 do 5 lub wartościami większymi niż 5, 5+.
- Lista typów partycji, gdy w zestawie zasobów znajduje się więcej niż jeden typ partycji. Na przykład czujnik IoT może wyprowadzać zarówno pliki XML, jak i JSON, chociaż oba te pliki są logicznie częścią tego samego zestawu zasobów.

## <a name="built-in-resource-set-patterns"></a>Wzorce wbudowanego zestawu zasobów

Usługa Azure Purview obsługuje następujące wzorce zestawu zasobów. Wzorce te mogą być wyświetlane jako nazwa w katalogu lub jako część nazwy pliku.
### <a name="regex-based-patterns"></a>Wzorce oparte na wyrażeniach regularne

| Nazwa wzorca | Nazwa wyświetlana | Opis |
|--------------|--------------|-------------|
| Guid (identyfikator GUID)         | {GUID}       | Globalnie unikatowy identyfikator zdefiniowany w dokumencie [RFC 4122](https://tools.ietf.org/html/rfc4122) |
| Liczba       | {N}          | Co najmniej jedna cyfra |
| Formaty daty/godziny | {Year} {Month} {Day} {N}     | Obsługujemy różne formaty daty/godziny, ale wszystkie są reprezentowane przez {Rok}[ogranicznik]{Miesiąc}[ogranicznik]{Dzień} lub serię {N}s. |
| 4ByteHex     | {HEX}        | 4-cyfrowy numer HEX. |
| Lokalizacja | {LOC}        | Obsługiwany jest tag języka zgodnie z definicją w [BCP 47](https://tools.ietf.org/html/bcp47), obie nazwy — i _ (na przykład en_ca i en-ca) |

### <a name="complex-patterns"></a>Wzorce złożone

| Nazwa wzorca | Nazwa wyświetlana | Opis |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Identyfikator pliku partycji platformy Spark |
| Date(yyyy/mm/dd)InPath  | {Rok}/{Miesiąc}/{Dzień} | Wzorzec rok/miesiąc/dzień obejmujący wiele folderów |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Jak są wyświetlane zestawy zasobów w wykazie usługi Azure Purview

Gdy usługa Azure Purview dopasowuje grupę zasobów do zestawu zasobów, próbuje wyodrębnić najbardziej użyteczne informacje do użycia jako nazwa wyświetlana w wykazie. Niektóre przykłady zastosowanej domyślnej konwencji nazewnictwa: 

### <a name="example-1"></a>Przykład 1

Kwalifikowana nazwa: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Nazwa wyświetlana: "nazwa danych wyjściowych platformy Spark"

### <a name="example-2"></a>Przykład 2

Kwalifikowana nazwa: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Nazwa wyświetlana: "moje partycjonowane dane"

### <a name="example-3"></a>Przykład 3

Kwalifikowana nazwa: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Nazwa wyświetlana: "data"

## <a name="customizing-resource-set-grouping-using-pattern-rules"></a>Dostosowywanie grupowania zestawu zasobów przy użyciu reguł wzorca

podczas skanowania konta magazynu usługa Azure Purview używa zestawu zdefiniowanych wzorców w celu określenia, czy grupa zasobów jest zestawem zasobów. W niektórych przypadkach grupowanie zestawu zasobów usługi Azure Purview może nie odzwierciedlać dokładnie Twoich danych. Te problemy mogą obejmować:

- Nieprawidłowe oznaczanie zasobu jako zestawu zasobów
- Umieszczanie zasobu w niewłaściwym zestawie zasobów
- Niepoprawne oznaczanie zasobu jako nie jako zestawu zasobów

Aby dostosować lub zastąpić sposób, w jaki usługa Azure Purview wykrywa, które zasoby są grupowane jako zestawy zasobów i jak są wyświetlane w wykazie, można zdefiniować reguły wzorców w centrum zarządzania. Aby uzyskać instrukcje krok po kroku i składnię, zobacz [reguły wzorca zestawu zasobów](how-to-resource-set-pattern-rules.md).
## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą Azure Purview, zobacz [Szybki start: tworzenie konta usługi Azure Purview.](create-catalog-portal.md)
