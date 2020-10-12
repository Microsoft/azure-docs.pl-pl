---
title: Obsługa języka — QnA Maker
titleSuffix: Azure Cognitive Services
description: Lista kultur, Języki naturalne obsługiwane przez QnA Maker w bazie wiedzy. Nie mieszaj języków w tej samej bazie wiedzy.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/24/2019
ms.openlocfilehash: c990b6980dea871679b0b301e293e4fb94748db7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89650906"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Obsługa języka dla zasobów QnA Maker i baz wiedzy

Język dla usługi jest wybierany podczas tworzenia pierwszej bazy wiedzy w zasobie. Wszystkie dodatkowe bazy wiedzy w zasobie muszą znajdować się w tym samym języku. 

Język określa przydatność wyników QnA Maker zapewnia odpowiedzi na zapytania użytkownika. Zasób QnA Maker i wszystkie bazy wiedzy w tym zasobie obsługują jeden język. Pojedynczy język jest niezbędny do dostarczenia najlepszych wyników odpowiedzi na zapytanie.

## <a name="single-language-per-resource"></a>Pojedynczy język na zasób

Rozważ następujące źródła:

* Usługa QnA Maker i wszystkie jej bazy wiedzy obsługują tylko jeden język.
* Język jest jawnie ustawiany podczas tworzenia pierwszej bazy wiedzy usługi
* Język jest określany na podstawie plików i adresów URL dodanych podczas tworzenia bazy wiedzy
* Nie można zmienić języka dla żadnych innych baz wiedzy w usłudze
* Język jest używany przez usługę Wyszukiwanie poznawcze (ranga #1) i usługę QnA Maker (ranga #2) do generowania najlepszej odpowiedzi na zapytanie

## <a name="supporting-multiple-languages"></a>Obsługa wielu języków

Jeśli musisz obsługiwać system bazy wiedzy, który zawiera kilka języków, możesz:

* Użyj [usługi Translator](../../translator/translator-info-overview.md) , aby przetłumaczyć pytanie na jeden język przed wysłaniem pytania do bazy wiedzy. Pozwala to skupić się na jakości jednego języka i jakości alternatywnych pytań i odpowiedzi.
* Utwórz zasób QnA Maker i bazę wiedzy w tym zasobie dla każdego języka. Pozwala to na zarządzanie oddzielnymi innymi pytaniami i tekstem odpowiedzi, który jest bardziej złożonych dla każdego języka. Zapewnia to znacznie większą elastyczność, ale wymaga znacznie większego kosztu konserwacji, gdy pytania lub odpowiedzi zmieniają się we wszystkich językach.

Przejrzyj [języki obsługiwane](../overview/language-support.md) przez QNA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Obsługa każdego języka za pomocą zasobu QnA Maker

* Utwórz zasób QnA Maker dla każdego języka
* Dodaj tylko pliki i adresy URL dla tego języka
* Użyj konwencji nazewnictwa dla zasobu, aby zidentyfikować język. Przykład dotyczy `qna-maker-fr` wszystkich baz wiedzy dla dokumentów francuskich


## <a name="languages-supported"></a>Obsługiwane języki

Poniższa lista zawiera języki obsługiwane dla zasobu QnA Maker. 

|Język|
|--|
|Arabski|
|Armeński|
|Języku|
|Baskijski|
|Bułgarski|
|Kataloński|
|Chinese_Simplified|
|Chinese_Traditional|
|Chorwacki|
|Czeski|
|Duński|
|Niderlandzki|
|Angielski|
|Estoński|
|Fiński|
|Francuski|
|Galicyjski|
|Niemiecki|
|Grecki|
|Gudżarati|
|Hebrajski|
|Hindi|
|Węgierski|
|Islandzki|
|Indonezyjski|
|Irlandzki|
|Włoski|
|japoński|
|Kannada|
|Koreański|
|Łotewski|
|Litewski|
|Malayalam|
|Malajski|
|Norweski|
|Polski|
|Portugalski|
|Pendżabski|
|Rumuński|
|Rosyjski|
|Serbian_Cyrillic|
|Serbian_Latin|
|Słowacki|
|Słoweński|
|Hiszpański|
|Szwedzki|
|Tamilski|
|Telugu|
|Tajlandzki|
|Turecki|
|Ukraiński|
|Urdu|
|Wietnamski|

## <a name="query-matching-and-relevance"></a>Dopasowanie i zgodność zapytania
QnA Maker zależy od [analizatorów języka wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/rest/api/searchservice/language-support) w celu uzyskania wyników.

Gdy możliwości platformy Azure Wyszukiwanie poznawcze są dostępne dla obsługiwanych języków, QnA Maker ma dodatkową rangę, która znajduje się powyżej wyników usługi Azure Search. W tym modelu rangi używamy niektórych specjalnych funkcji semantycznych i opartych na programie Word w następujących językach.

|Języki z dodatkową rangą|
|--|
|Chiński|
|Czeski|
|Niderlandzki|
|Angielski|
|Francuski|
|Niemiecki|
|Węgierski|
|Włoski|
|japoński|
|koreański|
|Polski|
|Portugalski|
|Hiszpański|
|Szwedzki|

Ta dodatkowa klasyfikacja jest wewnętrzną pracą rangi QnA Maker.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wybór języka](../how-to/language-knowledge-base.md)
