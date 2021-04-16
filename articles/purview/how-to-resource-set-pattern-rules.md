---
title: Jak utworzyć reguły wzorca zestawu zasobów
description: Dowiedz się, jak utworzyć regułę wzorca zestawu zasobów w celu zastąpienia sposobu grupowania zasobów w zestawy zasobów
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: b9d6ca88d5e9d49d3973193059197a1aa171e3e8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568710"
---
# <a name="create-resource-set-pattern-rules"></a>Tworzenie reguł wzorca zestawu zasobów

Systemy przetwarzania danych na dużą skalę zwykle przechowują pojedynczą tabelę na dysku jako wiele plików. Ta koncepcja jest reprezentowana w usłudze Azure Purview przy użyciu zestawów zasobów. Zestaw zasobów to pojedynczy obiekt w wykazie danych, który reprezentuje dużą liczbę zasobów w magazynie. Aby dowiedzieć się więcej, zobacz [Understanding resource sets (Opis zestawów zasobów).](concept-resource-sets.md)

Podczas skanowania konta magazynu usługa Azure Purview używa zestawu zdefiniowanych wzorców w celu określenia, czy grupa zasobów jest zestawem zasobów. W niektórych przypadkach grupowanie zestawu zasobów usługi Azure Purview może nie odzwierciedlać dokładnie twoich danych. Reguły wzorca zestawu zasobów umożliwiają dostosowywanie lub zastępowanie sposobu, w jaki usługa Azure Purview wykrywa, które zasoby są grupowane jako zestawy zasobów i jak są wyświetlane w wykazie.

Reguły wzorca są obecnie obsługiwane w następujących typach źródeł:
- Usługa Azure Data Lake Storage 2. generacji
- Azure Blob Storage
- Azure Files


## <a name="how-to-create-a-resource-set-pattern-rule"></a>Jak utworzyć regułę wzorca zestawu zasobów

Wykonaj poniższe kroki, aby utworzyć nową regułę wzorca zestawu zasobów:

1. Przejdź do centrum zarządzania. Wybierz **pozycję Reguły wzorca** z menu w obszarze nagłówka Zestawy zasobów. Wybierz **pozycję + Nowy,** aby utworzyć nowy zestaw reguł.

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-rule.png" alt-text="Tworzenie nowej reguły wzorca zestawu zasobów" border="true":::

1. Wprowadź zakres reguły wzorca zestawu zasobów. Wybierz typ konta magazynu i nazwę konta magazynu, dla których chcesz utworzyć zestaw reguł. Każdy zestaw reguł jest stosowany względem zakresu ścieżki folderu określonego w **polu Ścieżka folderu.**

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-scope.png" alt-text="Tworzenie konfiguracji reguł wzorca zestawu zasobów" border="true":::

1. Aby wprowadzić regułę dla zakresu konfiguracji, wybierz **pozycję + Nowa reguła.**

1. Wprowadź w następujących polach, aby utworzyć regułę:

   1. **Nazwa reguły:** Nazwa reguły konfiguracji. To pole nie ma wpływu na zasoby, których dotyczy reguła.

   1. **Kwalifikowana nazwa:** Kwalifikowana ścieżka, która używa kombinacji tekstu, dynamicznych zastępować i statycznych elementów zamienianych w celu dopasowania zasobów do reguły konfiguracji. Ta ścieżka jest względna względem zakresu reguły konfiguracji. Zobacz [sekcję składni](#syntax) poniżej, aby uzyskać szczegółowe instrukcje dotyczące sposobu określania kwalifikowanych nazw.

   1. **Nazwa wyświetlana:** Nazwa wyświetlana zasobu. To pole jest opcjonalne. Użyj zwykłego tekstu i statycznych elementów zamienianych, aby dostosować sposób wyświetlania zasobu w katalogu. Aby uzyskać bardziej szczegółowe instrukcje, zobacz [sekcję składni](#syntax) poniżej.

   1. **Nie grupuj jako zestawu zasobów:** Jeśli ta opcja jest włączona, dopasowane zasoby nie będą grupowane w zestaw zasobów.

      :::image type="content" source="media/how-to-resource-set-pattern-rules/scoped-resource-set-rule-example.png" alt-text="Utwórz nową regułę konfiguracji." border="true":::

1. Zapisz regułę, klikając pozycję **Dodaj**.

## <a name="pattern-rule-syntax"></a><a name="syntax"></a> Składnia reguły wzorca

Podczas tworzenia reguł wzorca zestawu zasobów użyj następującej składni, aby określić, do których reguł zasobów mają zastosowanie.

### <a name="dynamic-replacers-single-brackets"></a>Dynamiczne zamieniacze (pojedyncze nawiasy)

Pojedyncze nawiasy są używane jako **dynamiczne zamieniacze** w zasadach wzorca. Określ dynamiczny zamieniacz w kwalifikowanej nazwie przy użyciu formatu `{<replacerName:<replacerType>}` . W przypadku dopasowania dynamiczne zamieniacze są używane jako warunek grupowania, który wskazuje, że zasoby powinny być reprezentowane jako zestaw zasobów. Jeśli zasoby są pogrupowane w zestaw zasobów, kwalifikowana ścieżka zestawu zasobów będzie zawierać miejsce, w którym `{replacerName}` określono zastąpienie.

Jeśli na przykład dwa zasoby zostaną dopasowane do reguły , zestaw zasobów `folder1/file-1.csv` `folder2/file-2.csv` będzie `{folder:string}/file-{NUM:int}.csv` pojedynczą jednostką `{folder}/file-{NUM}.csv` .

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Przypadek specjalny: dynamiczne zamieniacze, gdy nie są grupowanie do zestawu zasobów

Jeśli *opcja Nie grupuj jako zestaw zasobów* jest włączona dla reguły wzorca, nazwa zamieniania jest polem opcjonalnym. `{:<replacerType>}` jest prawidłową składnią. Na przykład można `file-{:int}.csv` pomyślnie dopasować i `file-1.csv` utworzyć dwa różne zasoby zamiast `file-2.csv` zestawu zasobów.

### <a name="static-replacers-double-brackets"></a>Statyczne elementy zamieniane (podwójne nawiasy)

Podwójne nawiasy są używane jako **statyczne elementy zamieniane** w kwalifikowanej nazwie reguły wzorca. Określ statyczny zamieniacz w kwalifikowanej nazwie przy użyciu formatu `{{<replacerName>:<replacerType>}}` . W przypadku dopasowania każdy zestaw unikatowych statycznych wartości zamieniających spowoduje utworzenie różnych grupowania zestawu zasobów.

Jeśli na przykład dwa zasoby zostaną dopasowane do reguły , zostaną utworzone `folder1/file-1.csv` `folder2/file-2.csv` dwa `{{folder:string}}/file-{NUM:int}.csv` zestawy zasobów i `folder1/file-{NUM}.csv` `folder2/file-{NUM}.csv` .

Za pomocą statycznych zastępować można określić nazwę wyświetlaną zasobu pasującego do reguły wzorca. Użycie `{{<replacerName>}}` w nazwie wyświetlanej reguły spowoduje użycie dopasowanej wartości w nazwie zasobu.

### <a name="available-replacement-types"></a>Dostępne typy zastępcze

Poniżej przedstawiono dostępne typy, które mogą być używane w statycznych i dynamicznych zamienianiach:

| Typ | Struktura |
| ---- | --------- |
| ciąg | Seria 1 lub więcej znaków Unicode, w tym ograniczniki, takie jak spacje. |
| int | Seria 1 lub więcej od 0 do 9 znaków ASCII, może mieć prefiks 0 (np. 0001). |
| guid | Seria 32 lub 8-4-4-4-12 reprezentacja ciągu UUID zgodnie z definicjądefa w [dokumencie RFC 4122.](https://tools.ietf.org/html/rfc4122) |
| data | Seria 6 lub 8 0–9 znaków ASCII z opcjonalnie separatorami: rrrrmdd, rrrr-mm-dd, rrmmdd, rrrr-mm-dd, określona w [dokumencie RFC 3339](https://tools.ietf.org/html/rfc3339). |
| time | Seria 4 lub 6 0–9 znaków ASCII z opcjonalnie separatorami: HHmm, HH:mm, HHmmss, HH:mm:ss określona w [dokumencie RFC 3339.](https://tools.ietf.org/html/rfc3339) |
| sygnatura czasowa | Seria 12 lub 14 0–9 znaków ASCII z opcjonalnymi separatorami: rrrr-mm-ddTHH:mm, rrrrmmddhhmm, rrrr-mm-ddTHH:mm:ss, rrrrmmddHHmmss określona w [dokumencie RFC 3339.](https://tools.ietf.org/html/rfc3339) |
| boolean | Może zawierać wartość "true" lub "false", bez uwzględniania liter. |
| liczba | Seria 0 lub więcej od 0 do 9 znaków ASCII, może mieć prefiks 0 (np. 0001), po którym opcjonalnie może być kropka "." i seria 1 lub więcej 0–9 znaków ASCII, może być 0 przyrostków (np. .100) |
| hex | Seria 1 lub więcej znaków ASCII z zestawu 0–1 i A–F, wartość może mieć prefiks 0 |
| locale | Ciąg, który odpowiada składni określonej w [dokumencie RFC 5646.](https://tools.ietf.org/html/rfc5646) |

## <a name="order-of-resource-set-pattern-rules-getting-applied"></a>Kolejność stosowania reguł wzorca zestawu zasobów

Poniżej przedstawiono kolejność operacji stosowania reguł wzorca:

1. Bardziej szczegółowe zakresy będą mieć priorytet, jeśli element zawartości pasuje do dwóch reguł. Na przykład reguły w zakresie będą stosowane `container/folder` przed regułami w zakresie `container` .

1. Kolejność reguł w określonym zakresie. Można to edytować w interfejsie użytkownika.

1. Jeśli zasób nie pasuje do określonej reguły, stosowane są heurystyczne ustawienia domyślnego zestawu zasobów.

## <a name="examples"></a>Przykłady

### <a name="example-1"></a>Przykład 1

Wyodrębnianie danych SAP do obciążeń pełnych i różnicowych

#### <a name="inputs"></a>Dane wejściowe

Pliki:

- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`

#### <a name="pattern-rule"></a>Reguła wzorca

**Zakres:**`https://myazureblob.blob.core.windows.net/bar/`

**Nazwa wyświetlana:** "Klient zewnętrzny"

**Kwalifikowana nazwa:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Zestaw zasobów:** true

#### <a name="output"></a>Dane wyjściowe

Jeden zasób zestawu zasobów

**Nazwa wyświetlana:** Klient zewnętrzny

**Kwalifikowana nazwa:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>Przykład 2

Dane IoT w formacie avro

#### <a name="inputs"></a>Dane wejściowe

Pliki:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rules"></a>Reguły wzorca

**Zakres:**`https://myazureblob.blob.core.windows.net/bar/`

Reguła 1

**Nazwa wyświetlana:** "machine-89"

**Kwalifikowana nazwa:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Zestaw zasobów:** true

Reguła 2

**Nazwa wyświetlana:** "machine-90"

**Kwalifikowana nazwa:**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

#### <a name="resource-set-true"></a>*Zestaw zasobów: true*

#### <a name="outputs"></a>Dane wyjściowe

2 zestawy zasobów

Zestaw zasobów 1

**Nazwa wyświetlana:** machine-89

**Kwalifikowana nazwa:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Zestaw zasobów 2

**Nazwa wyświetlana:** machine-90

**Kwalifikowana nazwa:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>Przykład 3

Dane IoT w formacie avro

#### <a name="inputs"></a>Dane wejściowe

Pliki:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>Reguła wzorca

**Zakres:**`https://myazureblob.blob.core.windows.net/bar/`

**Nazwa wyświetlana:** "Machine-{{machineid}}"

**Kwalifikowana nazwa:**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**Zestaw zasobów:** true

#### <a name="outputs"></a>Dane wyjściowe

Zestaw zasobów 1

**Nazwa wyświetlana:** machine-89

**Kwalifikowana nazwa:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Zestaw zasobów 2

**Nazwa wyświetlana:** machine-90

**Kwalifikowana nazwa:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>Przykład 4

Nie grupuj w zestawy zasobów

#### <a name="inputs"></a>Dane wejściowe

Pliki:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>Reguła wzorca

**Zakres:**`https://myazureblob.blob.core.windows.net/bar/`

**Nazwa wyświetlana:**`Machine-{{machineid}}`

**Kwalifikowana nazwa:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Zestaw zasobów:** false

#### <a name="outputs"></a>Dane wyjściowe

4 pojedyncze zasoby

Zasób 1

**Nazwa wyświetlana:** machine-89

**Kwalifikowana nazwa:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

Zasób 2

**Nazwa wyświetlana:** machine-89

**Kwalifikowana nazwa:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

Zasób 3

**Nazwa wyświetlana:** machine-89

**Kwalifikowana nazwa:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

Zasób 4

**Nazwa wyświetlana:** machine-90

**Kwalifikowana nazwa:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>Następne kroki

Rozpoczynanie pracy [od zarejestrowania i zeskanowania konta magazynu usługi Azure Data Lake Gen2.](register-scan-adls-gen2.md)