---
title: Obsługa języka — QnA Maker
titleSuffix: Azure Cognitive Services
description: Lista kultur, Języki naturalne obsługiwane przez QnA Maker w bazie wiedzy. Nie mieszaj języków w tej samej bazie wiedzy.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.openlocfilehash: 1edd5ffc2578a27a53c7e9a46a4a5f1cf61331ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97605063"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Obsługa języka dla zasobów QnA Maker i baz wiedzy

W tym artykule opisano opcje pomocy technicznej dotyczące języka dla QnA Maker zasobów i baz wiedzy. 

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Język dla usługi jest wybierany podczas tworzenia pierwszej bazy wiedzy w zasobie. Wszystkie dodatkowe bazy wiedzy w zasobie muszą znajdować się w tym samym języku. 

Język określa przydatność wyników QnA Maker zapewnia odpowiedzi na zapytania użytkownika. Zasób QnA Maker i wszystkie bazy wiedzy w tym zasobie obsługują jeden język. Pojedynczy język jest niezbędny do dostarczenia najlepszych wyników odpowiedzi na zapytanie.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

W QnA Maker zarządzanych można wybrać opcję Ustawienia języka na indywidualnym poziomie bazy wiedzy. To ustawienie można włączyć tylko w bazie wiedzy usługi. Po ustawieniu ustawień języka nie można zmienić dla usługi. 

Jeśli wybierzesz opcję Ustawienia językowe jako specyficzne dla bazy wiedzy, możesz utworzyć bazę wiedzy dla różnych języków w samej usłudze. 

---

## <a name="single-language-per-resource"></a>Pojedynczy język na zasób

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Rozważ następujące źródła:

* Usługa QnA Maker i wszystkie jej bazy wiedzy obsługują tylko jeden język.
* Język jest jawnie ustawiany podczas tworzenia pierwszej bazy wiedzy usługi.
* Język jest określany na podstawie plików i adresów URL dodanych podczas tworzenia bazy wiedzy.
* Nie można zmienić języka dla żadnych innych baz wiedzy w usłudze.
* Język jest używany przez usługę Wyszukiwanie poznawcze (ranga #1) i usługę QnA Maker (ranga #2) do generowania najlepszej odpowiedzi na zapytanie.

# <a name="qnamaker-managed-preview"></a>[Zarządzane QnAMaker (wersja zapoznawcza)](#tab/v2)
![Ustawienie języka w QnA Maker zarządzanym](../media/language-support/language-setting-managed.png)

Jeśli **nie zaznaczysz pola wyboru, aby włączyć ustawienie języka dla bazy wiedzy**, weź pod uwagę następujące kwestie: 
* Usługa QnA Maker i wszystkie jej bazy wiedzy obsługują tylko jeden język.
* Język jest jawnie ustawiany podczas tworzenia pierwszej bazy wiedzy usługi
* Język jest określany na podstawie plików i adresów URL dodanych podczas tworzenia bazy wiedzy
* Nie można zmienić języka dla żadnych innych baz wiedzy w usłudze
* Język jest używany przez usługę Wyszukiwanie poznawcze (ranga #1) i usługę QnA Maker (ranga #2) do generowania najlepszej odpowiedzi na zapytanie

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>Obsługa wielu języków w jednym QnA Maker zasobów

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)
Ta funkcja nie jest obsługiwana w obecnej, ogólnie dostępnej wersji. Wyewidencjonuj QnA Maker zarządzane, aby przetestować tę funkcję. 

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)
* Podczas tworzenia pierwszej bazy wiedzy w usłudze uzyskasz możliwość włączenia ustawienia języka dla bazy wiedzy. Zaznacz pole wyboru, aby utworzyć bazy wiedzy należące do różnych języków w ramach jednej usługi.
* Nie można zmodyfikować opcji ustawień języka dla usługi, gdy zostanie utworzona pierwsza baza wiedzy.
* W przypadku włączenia ustawień języka właściwych dla każdej bazy wiedzy, a nie dla usługi, należy użyć jednego indeksu testowego na bazę wiedzy. 

![Ustawienie języka w QnA Maker zarządzanym](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>Obsługa wielu języków w jednej bazie wiedzy

Jeśli musisz obsługiwać system bazy wiedzy, który zawiera kilka języków, możesz:

* Użyj [usługi Translator](../../translator/translator-info-overview.md) , aby przetłumaczyć pytanie na jeden język przed wysłaniem pytania do bazy wiedzy. Pozwala to skupić się na jakości jednego języka i jakości alternatywnych pytań i odpowiedzi.
* Utwórz zasób QnA Maker i bazę wiedzy w tym zasobie dla każdego języka. Pozwala to na zarządzanie oddzielnymi innymi pytaniami i tekstem odpowiedzi, który jest bardziej złożonych dla każdego języka. Zapewnia to znacznie większą elastyczność, ale wymaga znacznie większego kosztu konserwacji, gdy pytania lub odpowiedzi zmieniają się we wszystkich językach.


## <a name="languages-supported"></a>Obsługiwane języki

Poniższa lista zawiera języki obsługiwane dla zasobu QnA Maker. 

| Język |
|--|
| Arabski |
| Armeński |
| Języku |
| Baskijski |
| Bułgarski |
| Kataloński |
| Chinese_Simplified |
| Chinese_Traditional |
| Chorwacki |
| Czeski |
| Duński |
| Niderlandzki |
| Angielski |
| Estoński |
| Fiński |
| Francuski |
| Galicyjski |
| Niemiecki |
| Grecki |
| Gudżarati |
| Hebrajski |
| Hindi |
| Węgierski |
| Islandzki |
| Indonezyjski |
| Irlandzki |
| Włoski |
| japoński |
| Kannada |
| Koreański |
| Łotewski |
| Litewski |
| Malayalam |
| Malajski |
| Norweski |
| Polski |
| Portugalski |
| Pendżabski |
| Rumuński |
| Rosyjski |
| Serbian_Cyrillic |
| Serbian_Latin |
| Słowacki |
| Słoweński |
| Hiszpański |
| Szwedzki |
| Tamilski |
| Telugu |
| Tajlandzki |
| Turecki |
| Ukraiński |
| Urdu |
| Wietnamski |

## <a name="query-matching-and-relevance"></a>Dopasowanie i zgodność zapytania
QnA Maker zależy od [analizatorów języka wyszukiwanie poznawcze platformy Azure](/rest/api/searchservice/language-support) w celu uzyskania wyników.

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
> [Wybór języka](../index.yml)