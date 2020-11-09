---
title: Limity i granice — QnA Maker
description: QnA Maker ma limity meta dla części bazy wiedzy i usługi. Ważne jest, aby zachować bazę wiedzy w ramach tych limitów w celu testowania i publikowania.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 1a384eed542d0c09a973e0e68288f9fc1660cc96
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380910"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limity i granice bazy wiedzy QnA Maker

Podane poniżej limity QnA Maker są kombinacją [limitów warstwy cenowej platformy Azure wyszukiwanie poznawcze](../../search/search-limits-quotas-capacity.md) i [limity warstwy cenowej QNA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Musisz znać oba zestawy limitów, aby zrozumieć, jak wiele baz wiedzy można utworzyć dla każdego zasobu i jak duże mogą rosnąć poszczególne bazy wiedzy.

## <a name="knowledge-bases"></a>Bazy wiedzy

Maksymalna liczba baz wiedzy zależy od [limitów warstwy wyszukiwanie poznawcze platformy Azure](../../search/search-limits-quotas-capacity.md).

|**Warstwa usługi Azure Wyszukiwanie poznawcze** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**S3 (wysoka gęstość)**|
|---|---|---|---|---|---|----|
|Dozwolona maksymalna liczba opublikowanych baz wiedzy|2|14|49|199|199|2 999|

 Na przykład jeśli warstwa ma 15 dozwolonych indeksów, można opublikować 14 baz wiedzy (1 indeks na opublikowaną bazę wiedzy). Piętnasty indeks, `testkb` , jest używany dla wszystkich baz wiedzy na potrzeby tworzenia i testowania.

## <a name="extraction-limits"></a>Limity wyodrębniania

### <a name="file-naming-constraints"></a>Ograniczenia nazewnictwa plików

Nazwy plików nie mogą zawierać następujących znaków:

|Nie używaj znaku|
|--|
|Pojedynczy cytat `'`|
|Podwójny cudzysłów `"`|

### <a name="maximum-file-size"></a>Maksymalna wielkość pliku

|Format|Maksymalny rozmiar pliku (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maksymalna liczba plików

Maksymalna liczba plików, które można wyodrębnić, i maksymalny rozmiar pliku opiera się na **[QNA Maker limitów warstwy cenowej](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**.

> [!NOTE]
> QnA Maker Managed (wersja zapoznawcza) to bezpłatna usługa, która nie ma ograniczeń dotyczących liczby źródeł, które można dodać. Przepływność jest obecnie ograniczona do 10 transakcji na sekundę dla interfejsów API zarządzania i interfejsów API przewidywania.

### <a name="maximum-number-of-deep-links-from-url"></a>Maksymalna liczba linków głębokiego adresu URL

Maksymalna liczba linków głębokiego, które mogą być przeszukiwane w celu wyodrębnienia bazami ze strony adresu URL, to **20**.

## <a name="metadata-limits"></a>Limity metadanych

Metadane są prezentowane jako para kluczowa: wartość, na przykład `product:windows 10` . Jest on przechowywany i porównywany małymi literami.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Według warstwy cenowej usługi Azure Wyszukiwanie poznawcze

Maksymalna liczba pól metadanych na bazę wiedzy zależy od **[limitów warstwy wyszukiwanie poznawcze platformy Azure](../../search/search-limits-quotas-capacity.md)**.

|**Warstwa usługi Azure Wyszukiwanie poznawcze** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**S3 (wysoka gęstość)**|
|---|---|---|---|---|---|----|
|Maksymalna liczba pól metadanych na usługę QnA Maker (w ramach wszystkich artykułów bazy wiedzy)|1000|100 *|1000|1000|1000|1000|

### <a name="by-name-and-value"></a>Według nazwy i wartości

W poniższej tabeli wymieniono długość i dopuszczalne znaki nazwy i wartości metadanych.

|Element|Dozwolone znaki|Dopasowanie wzorca wyrażenia regularnego|Maks. liczba znaków|
|--|--|--|--|
|Nazwa (klucz)|Udostępnia<br>alfanumeryczne (litery i cyfry)<br>`_` podkreślenia<br> Nie może zawierać spacji.|`^[a-zA-Z0-9_]+$`|100|
|Wartość|Zezwala na wszystko z wyjątkiem<br>`:` średnikami<br>`|` (potok pionowy)<br>Dozwolona jest tylko jedna wartość.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limity zawartości bazy wiedzy
Ogólne limity zawartości w bazie wiedzy:
* Długość tekstu odpowiedzi: 25 000
* Długość tekstu pytania: 1 000
* Długość tekstu klucza metadanych: 100
* Długość tekstu wartości metadanych: 500
* Obsługiwane znaki dla nazwy metadanych: alfabety, cyfry i `_`
* Obsługiwane znaki dla wartości metadanych: wszystkie oprócz `:` i `|`
* Długość nazwy pliku: 200
* Obsługiwane formaty plików: ". tsv", "PDF", ". txt", "docx", "xlsx".
* Maksymalna liczba pytań alternatywnych: 300
* Maksymalna liczba par pytań i odpowiedzi: zależy od wybranej **[warstwy wyszukiwanie poznawcze platformy Azure](../../search/search-limits-quotas-capacity.md#document-limits)** . Para pytań i odpowiedzi mapuje do dokumentu na indeks Wyszukiwanie poznawcze platformy Azure.
* Adres URL/strona HTML: 1 000 000 znaków

## <a name="create-knowledge-base-call-limits"></a>Utwórz limity wywołań bazy wiedzy:
Reprezentują one limity dla każdej akcji tworzenia bazy wiedzy; oznacza to, że kliknięcie pozycji *Utwórz KB* lub wywołanie interfejsu API usługi.
* Zalecana maksymalna liczba pytań alternatywnych na odpowiedź: 300
* Maksymalna liczba adresów URL: 10
* Maksymalna liczba plików: 10

## <a name="update-knowledge-base-call-limits"></a>Aktualizuj limity wywołań bazy wiedzy
Reprezentują one limity dla każdej akcji aktualizacji; oznacza to, że kliknięcie przycisku *Zapisz i pouczenie* lub wywołanie interfejsu API UpdateKnowledgeBase.
* Długość poszczególnych nazw źródłowych: 300
* Zalecana maksymalna liczba dodanych lub usuniętych pytań alternatywnych: 300
* Maksymalna liczba dodanych lub usuniętych pól metadanych: 10
* Maksymalna liczba adresów URL, które można odświeżyć: 5

## <a name="next-steps"></a>Następne kroki

Dowiedz się, kiedy i jak zmienić [warstwy cenowe usług](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku).
