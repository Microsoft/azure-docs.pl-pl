---
title: 'Instrukcje: Tworzenie konfiguracji zestawu zasobów w zakresie'
description: Dowiedz się, jak utworzyć regułę konfiguracji zestawu zasobów w zakresie, aby zastąpić sposób grupowania zasobów w zestawy zasobów
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 8d7d482f38d58c8d6a8959acb51c94c0fb814697
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668439"
---
# <a name="create-scoped-resource-set-configuration-rules"></a>Tworzenie reguł konfiguracji zestawu zasobów w zakresie

Systemy przetwarzania danych w skali zwykle przechowują pojedynczą tabelę na dysku jako wiele plików. Pojęcie to jest reprezentowane w usłudze Azure kontrolą za pomocą zestawów zasobów. Zestaw zasobów jest pojedynczym obiektem w wykazie danych, który reprezentuje dużą liczbę zasobów w magazynie. Aby dowiedzieć się więcej, zobacz [Opis zestawów zasobów](concept-resource-sets.md).

Podczas skanowania konta magazynu usługa Azure kontrolą używa zestawu zdefiniowanych wzorców w celu ustalenia, czy grupa zasobów jest zestawem zasobów. W niektórych przypadkach grupowanie zestawu zasobów usługi Azure kontrolą może nie odzwierciedlać dokładnego charakteru danych. Reguły zestawu zasobów w zakresie umożliwiają dostosowanie lub zastąpienie sposobu, w jaki usługa Azure kontrolą wykrywa, które zasoby są pogrupowane jako zestawy zasobów i jak są wyświetlane w katalogu.

## <a name="how-to-create-a-scoped-resource-set-configuration"></a>Jak utworzyć konfigurację zestawu zasobów w zakresie

Wykonaj poniższe kroki, aby utworzyć nową konfigurację zestawu zasobów w zakresie:

1. Przejdź do centrum zarządzania. Z menu wybierz pozycję **zestawy zasobów z zakresem** . Kliknij pozycję **+ Nowy** , aby utworzyć nowy zestaw reguł konfiguracji.
        :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-rule.png" alt-text="Utwórz nową regułę zestawu zasobów w zakresie" border="true":::

1. Wprowadź zakres konfiguracji zestawu zasobów z zakresem. Wybierz typ konta magazynu i nazwę konta magazynu, dla którego chcesz utworzyć zestaw reguł. Każdy zestaw reguł jest stosowany względem zakresu ścieżki folderu określonego w polu **ścieżka folderu** . 
        :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-scope.png" alt-text="Utwórz nową regułę zestawu zasobów w zakresie" border="true":::

1. Aby wprowadzić regułę dla zakresu konfiguracji, wybierz pozycję **+ Nowa reguła**.
1. Wprowadź następujące pola, aby utworzyć regułę:
    1. **Nazwa reguły:** Nazwa reguły konfiguracji. To pole nie ma wpływu na zasoby, których dotyczy reguła.
    1. **Nazwa kwalifikowana:** Kwalifikowana ścieżka, która używa kombinacji tekstu, elementów dynamicznych i zastępczych, aby dopasować zasoby do reguły konfiguracji. Ta ścieżka jest określana względem zakresu reguły konfiguracji. Zapoznaj się z sekcją [składnia](#syntax) poniżej, aby uzyskać szczegółowe instrukcje dotyczące określania kwalifikowanych nazw. 
    1. **Nazwa wyświetlana:** Nazwa wyświetlana elementu zawartości. To pole jest opcjonalne. Użyj zwykłego tekstu i elementów statycznych, aby dostosować sposób wyświetlania zasobów w katalogu. Aby uzyskać bardziej szczegółowe instrukcje, zobacz sekcję [składnia](#syntax) poniżej.
    1. **Nie Grupuj jako zestawu zasobów:** Jeśli ta funkcja jest włączona, dopasowany zasób nie zostanie pogrupowany w zestawie zasobów. 
        :::image type="content" source="media/how-to-scoped-resource-sets/scoped-resource-set-rule-example.png" alt-text="Utwórz nową regułę zestawu zasobów w zakresie" border="true"::: 
1. Zapisz regułę, klikając przycisk **Dodaj**. 

## <a name="scoped-resource-set-syntax"></a><a name="syntax"></a> Składnia zestawu zasobów w zakresie

Podczas tworzenia reguł zestawu zasobów objętych zakresem należy użyć następującej składni, aby określić, które reguły zasobów mają zastosowanie.

### <a name="dynamic-replacers-single-brackets"></a>Dynamiczne odmieszczenia (pojedyncze nawiasy)

Pojedyncze nawiasy są używane jako **dynamiczne zamienniki** w regule zestawu zasobów w zakresie. Określ element zastępczy dynamicznego w nazwie kwalifikowanej przy użyciu formatu `{<replacerName:<replacerType>}` . W przypadku dopasowania dynamiczne zamienniki są używane jako warunek grupowania wskazujący, że zasoby powinny być reprezentowane jako zestaw zasobów. Jeśli zasoby są pogrupowane w zestawie zasobów, ścieżka kwalifikowana zestawu zasobów będzie zawierać miejsce, `{replacerName}` w którym został określony obiekt zastępczy.

Na przykład jeśli dwa zasoby `folder1/file-1.csv` i `folder2/file-2.csv` pasują do reguły `{folder:string}/file-{NUM:int}.csv` , zestaw zasobów będzie pojedynczym obiektem `{folder}/file-{NUM}.csv` .

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Przypadek specjalny: dynamiczne zamienniki, gdy nie grupują się w zestaw zasobów

Jeśli opcja *nie Grupuj jako zestaw zasobów* jest włączona dla reguły zestawu zasobów o określonym zakresie, Nazwa zastępcza jest polem opcjonalnym. `{:<replacerType>}` jest prawidłową składnią. Na przykład `file-{:int}.csv` pomyślnie pasuje do `file-1.csv` i `file-2.csv` i utworzy dwa różne zasoby, a nie zestaw zasobów.

### <a name="static-replacers-double-brackets"></a>Statyczne elementy zastępcze (podwójne nawiasy)

Podwójne nawiasy są używane jako **statyczne elementy zastępcze** w kwalifikowanej nazwie reguły zestawu zasobów w zakresie. Określ statyczny element zastępczy w nazwie kwalifikowanej przy użyciu formatu `{{<replacerName>:<replacerType>}}` . W przypadku dopasowania każdy zestaw unikatowych statycznych wartości zastępczych spowoduje utworzenie różnych grup zestawów zasobów.

Na przykład jeśli dwa zasoby `folder1/file-1.csv` i `folder2/file-2.csv` pasują do reguły `{{folder:string}}/file-{NUM:int}.csv` , tworzone są dwa zestawy zasobów `folder1/file-{NUM}.csv` i `folder2/file-{NUM}.csv` .

Statyczne elementy zastępcze mogą służyć do określania nazwy wyświetlanej dopasowania zasobów do reguły zestawu zasobów w zakresie. Użycie `{{<replacerName>}}` w polu Nazwa wyświetlana reguły spowoduje użycie dopasowanej wartości w nazwie elementu zawartości.

### <a name="available-replacement-types"></a>Dostępne typy zastępcze

Poniżej znajdują się dostępne typy, które mogą być używane w statycznych i dynamicznych zamiennikach:

| Typ | Struktura |
| ---- | --------- |
| ciąg | Seria 1 lub więcej znaków Unicode, w tym ograniczników takich jak spacje. |
| int | Seria 1 lub więcej znaków ASCII 0-9 może być poprzedzona 0 (np. 0,001). |
| guid | Seria 32 lub 8-4-4-4-12 ciąg reprezentujący identyfikator UUID jako defineddefa w https://tools.ietf.org/html/rfc4122 |
| data | Seria 6 lub 8 0-9 znaków ASCII z opcjonalnymi separatorami: RRRRMMDD, rrrr-mm-dd, YYMMDD, RR-mm-dd, określony w https://tools.ietf.org/html/rfc3339 |
| time | Seria 4 lub 6 0-9 znaków ASCII z opcjonalnymi separatorami: HHmm, gg: mm, HHmmss, gg: mm: SS określony w https://tools.ietf.org/html/rfc3339 |
| sygnatura czasowa | Seria 12 lub 14 0-9 znaków ASCII z opcjonalnymi separatorami: RRRR-MM-DDTgg: mm, yyyyMMddhhmm, RRRR-MM-DDTgg: mm: SS, rrrrmmddggmmss określony w https://tools.ietf.org/html/rfc3339 |
| boolean | Może zawierać wartość "true" lub "false", bez uwzględniania wielkości liter. |
| liczba | Seria 0 lub więcej znaków w kodzie ASCII 0-9 może być poprzedzona 0 (np. 0,001), a po tym opcjonalnie kropka "." i seria 1 lub więcej znaków ASCII 0-9, może być to 0 przyrostka (np. 100) | 
| hex | Seria 1 lub więcej znaków ASCII z zestawu 0-1 i A-F, wartość może być z prefiksem 0 |
| locale | Ciąg, który jest zgodny z składnią określoną w https://tools.ietf.org/html/rfc5646 |

## <a name="order-of-scoped-resource-set-rules-getting-applied"></a>Zostanie zastosowana kolejność reguł zestawu zasobów objętych zakresem.

Poniżej znajduje się kolejność operacji zastosowania reguł zestawu zasobów w zakresie:

1. Jeśli zasób jest zgodny z dwoma regułami, będzie miał pierwszeństwo. Na przykład reguły w zakresie `container/folder` zostaną zastosowane przed regułami w zakresie `container` . 
1. Kolejność reguł w określonym zakresie. Można to edytować w interfejsie użytkownika.
1. Jeśli zasób nie jest zgodny z żadną określoną regułą, stosowane są domyślne algorytmy heurystyczne zestawu zasobów.

## <a name="examples"></a>Przykłady

### <a name="example-1"></a>Przykład 1

Wyodrębnianie danych SAP do obciążeń pełnych i różnicowych

#### <a name="inputs"></a>Dane wejściowe

Plikach
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`


#### <a name="scoped-resource-set-rule"></a>Reguła zestawu zasobów w zakresie 

**Zakres:**https://myazureblob.blob.core.windows.net/bar/

**Nazwa wyświetlana:** "Klient zewnętrzny"

**Nazwa kwalifikowana:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Zestaw zasobów:** prawda

#### <a name="output"></a>Dane wyjściowe 

Jeden zasób zestawu zasobów

**Nazwa wyświetlana:** Klient zewnętrzny

**Nazwa kwalifikowana:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>Przykład 2

Dane IoT w formacie Avro

#### <a name="inputs"></a>Dane wejściowe 

Plikach
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rules"></a>Reguły zestawu zasobów w zakresie 

**Zakres:**https://myazureblob.blob.core.windows.net/bar/

Reguła 1

**Nazwa wyświetlana:** "Machine-89"

**Nazwa kwalifikowana:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Zestaw zasobów:** prawda

Reguła 2

**Nazwa wyświetlana:** "Machine-90"

**Nazwa kwalifikowana:**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

#### <a name="resource-set-true"></a>*Zestaw zasobów: prawda* 

#### <a name="outputs"></a>Dane wyjściowe 

2 zestawy zasobów 

Zestaw zasobów 1

**Nazwa wyświetlana:** machine-89

**Nazwa kwalifikowana:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Zestaw zasobów 2

**Nazwa wyświetlana:** machine-90

**Nazwa kwalifikowana:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>Przykład 3

Dane IoT w formacie Avro

#### <a name="inputs"></a>Dane wejściowe 

Plikach
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rule"></a>Reguła zestawu zasobów w zakresie 

**Zakres:**https://myazureblob.blob.core.windows.net/bar/

**Nazwa wyświetlana:** "Machine-{{MachineID}}"

**Nazwa kwalifikowana:**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**Zestaw zasobów:** prawda

#### <a name="outputs"></a>Dane wyjściowe 

Zestaw zasobów 1

**Nazwa wyświetlana:** machine-89

**Nazwa kwalifikowana:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Zestaw zasobów 2

**Nazwa wyświetlana:** machine-90

**Nazwa kwalifikowana:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>Przykład 4

Nie Grupuj do zestawów zasobów

#### <a name="inputs"></a>Dane wejściowe 

Plikach
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="scoped-resource-set-rule"></a>Reguła zestawu zasobów w zakresie 

**Zakres:**https://myazureblob.blob.core.windows.net/bar/

**Nazwa wyświetlana:** "Machine-{{MachineID}}"

**Nazwa kwalifikowana:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Zestaw zasobów:** FAŁSZ

#### <a name="outputs"></a>Dane wyjściowe 

4 poszczególne zasoby

Zasób 1

**Nazwa wyświetlana:** machine-89

**Nazwa kwalifikowana:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

Zasób 2

**Nazwa wyświetlana:** machine-89

**Nazwa kwalifikowana:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

Zasób 3

**Nazwa wyświetlana:** machine-89

**Nazwa kwalifikowana:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

Zasób 4

**Nazwa wyświetlana:** machine-90

**Nazwa kwalifikowana:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>Następne kroki

Rozpocznij od [zarejestrowania i skanowania konta magazynu Azure Data Lake Gen2](register-scan-adls-gen2.md).