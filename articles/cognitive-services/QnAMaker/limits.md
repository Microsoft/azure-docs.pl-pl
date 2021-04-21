---
title: Limity i granice — QnA Maker
description: QnA Maker ma meta limity dla części bazy wiedzy i usługi. Ważne jest, aby zachować bazę wiedzy w ramach tych limitów w celu testowania i publikowania.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: ad498575b029f918538909a9b5b2d52c71c1389c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816372"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>QnA Maker i granice bazy wiedzy

QnA Maker limity podane poniżej są kombinacją limitów warstwy [cenowej Azure Cognitive Search](../../search/search-limits-quotas-capacity.md) i limitów QnA Maker [cenowych.](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/) Musisz znać oba zestawy limitów, aby zrozumieć, ile baz wiedzy można utworzyć dla poszczególnych zasobów i jak duża może być każda baza wiedzy.

## <a name="knowledge-bases"></a>Bazy wiedzy

Maksymalna liczba baz wiedzy jest oparta na limitach [Azure Cognitive Search warstwy.](../../search/search-limits-quotas-capacity.md)

|**Azure Cognitive Search warstwy** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**S3 (wysoka gęstość)**|
|---|---|---|---|---|---|----|
|Dozwolona maksymalna liczba opublikowanych baz wiedzy|2|14|49|199|199|2,999|

 Jeśli na przykład warstwa ma 15 dozwolonych indeksów, możesz opublikować 14 baz wiedzy (1 indeks na opublikowaną bazę wiedzy). Indeks 15. jest używany do tworzenia i testowania wszystkich baz `testkb` wiedzy.

## <a name="extraction-limits"></a>Limity wyodrębniania

### <a name="file-naming-constraints"></a>Ograniczenia nazewnictwa plików

Nazwy plików mogą nie zawierać następujących znaków:

|Nie używaj znaku|
|--|
|Pojedynczy cudzysłów `'`|
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

Maksymalna liczba plików, które można wyodrębnić, oraz maksymalny rozmiar pliku zależy **[od QnA Maker limitów warstw cenowych.](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)**

> [!NOTE]
> QnA Maker zarządzana (wersja zapoznawcza) to bezpłatna usługa bez ograniczeń liczby źródeł, które można dodać. Przepływność jest obecnie ograniczona do 10 transakcji na sekundę dla interfejsów API zarządzania i interfejsów API przewidywania.

### <a name="maximum-number-of-deep-links-from-url"></a>Maksymalna liczba linków głębokich z adresu URL

Maksymalna liczba linków głębokich, które można przeszukiwać w celu wyodrębniania pytań i odpowiedzi ze strony adresu URL, wynosi **20**.

## <a name="metadata-limits"></a>Limity metadanych

Metadane są prezentowane jako tekstowa para klucz:wartość, taka jak `product:windows 10` . Jest ona przechowywana i porównywana małe literami.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Według Azure Cognitive Search cenowej

Maksymalna liczba pól metadanych na bazę wiedzy jest oparta na **[limitach Azure Cognitive Search metadanych.](../../search/search-limits-quotas-capacity.md)**

|**Azure Cognitive Search warstwy** | **Bezpłatna** | **Podstawowa** |**S1** | **S2**| **S3** |**S3 (wysoka gęstość)**|
|---|---|---|---|---|---|----|
|Maksymalna liczba pól metadanych na QnA Maker (we wszystkich KB)|1000|100*|1000|1000|1000|1000|

### <a name="by-name-and-value"></a>Według nazwy i wartości

W poniższej tabeli wymieniono długość i dopuszczalne znaki dla nazwy i wartości metadanych.

|Element|Dozwolone znaki|Dopasowanie wzorca wyrażenia regularnego|Maksymalna liczba znaków|
|--|--|--|--|
|Nazwa (klucz)|Pozwala<br>alfanumeryczne (litery i cyfry)<br>`_` (podkreślenie)<br> Nie może zawierać spacji.|`^[a-zA-Z0-9_]+$`|100|
|Wartość|Zezwala na wszystko z wyjątkiem<br>`:` (dwukropek)<br>`|` (pionowa kreska pionowa)<br>Dozwolona jest tylko jedna wartość.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limity zawartości bazy wiedzy
Ogólne limity zawartości w bazie wiedzy:
* Długość tekstu odpowiedzi: 25 000 znaków
* Długość tekstu pytania: 1000 znaków
* Długość tekstu klucza metadanych: 100 znaków
* Długość tekstu wartości metadanych: 500 znaków
* Obsługiwane znaki nazwy metadanych: alfabety, cyfry i `_`
* Obsługiwane znaki dla wartości metadanych: wszystkie z wyjątkiem `:` i `|`
* Długość nazwy pliku: 200
* Obsługiwane formaty plików: ".tsv", ".pdf", ".txt", ".docx", "xlsx".
* Maksymalna liczba pytań alternatywnych: 300
* Maksymalna liczba par pytanie-odpowiedź: zależy od **[wybranej Azure Cognitive Search pytania.](../../search/search-limits-quotas-capacity.md#document-limits)** Para pytań i odpowiedzi jest mapowany na dokument w Azure Cognitive Search indeksu.
* Adres URL/strona HTML: 1 milion znaków

## <a name="create-knowledge-base-call-limits"></a>Tworzenie limitów wywołań bazy wiedzy:
Reprezentują one limity dla każdej akcji tworzenia bazy wiedzy. oznacza to, że kliknij *pozycję Utwórz bazę wiedzy* lub wywołaj interfejs API CreateKnowledgeBase.
* Zalecana maksymalna liczba alternatywnych pytań na odpowiedź: 300
* Maksymalna liczba adresów URL: 10
* Maksymalna liczba plików: 10
* Maksymalna dozwolona liczba pytań i odpowiedzi na wywołanie: 1000

## <a name="update-knowledge-base-call-limits"></a>Aktualizowanie limitów wywołań bazy wiedzy
Reprezentują one limity dla każdej akcji aktualizacji; oznacza to, że kliknięcie *przycisku Zapisz i wytrenuj* lub wywołanie interfejsu API UpdateKnowledgeBase.
* Długość każdej nazwy źródła: 300
* Zalecana maksymalna liczba dodanych lub usuniętych pytań alternatywnych: 300
* Maksymalna liczba dodanych lub usuniętych pól metadanych: 10
* Maksymalna liczba adresów URL, które można odświeżyć: 5
* Maksymalna dozwolona liczba pytań i odpowiedzi na wywołanie: 1000

## <a name="add-unstructured-file-limits"></a>Dodawanie limitów plików bez struktury

> [!NOTE]
> * Jeśli musisz użyć większych plików, niż zezwala limit, możesz rozbić plik na mniejsze pliki przed wysłaniem ich do interfejsu API. 

Reprezentują one limity, gdy pliki bez struktury są używane do tworzenia bazy *wiedzy* lub wywołania interfejsu API CreateKnowledgeBase:
* Długość pliku: wyodrębnimy pierwsze 32000 znaków
* Maksymalnie 3 odpowiedzi na plik.

## <a name="prebuilt-question-answering-limits"></a>Wstępnie utworzone limity odpowiedzi na pytania

> [!NOTE]
> * Jeśli musisz użyć większych dokumentów, niż zezwala limit, możesz rozbić tekst na mniejsze fragmenty tekstu przed wysłaniem ich do interfejsu API. 
> * Dokument to pojedynczy ciąg znaków tekstowych.  

Reprezentują one limity, gdy wstępnie utworzony interfejs API jest używany do generowania odpowiedzi *lub* wywołania interfejsu API GenerateAnswer:
* Liczba dokumentów: 5
* Maksymalny rozmiar pojedynczego dokumentu: 5120 znaków
* Maksymalnie 3 odpowiedzi na dokument.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, kiedy i jak zmienić [warstwy cenowe usług.](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)
