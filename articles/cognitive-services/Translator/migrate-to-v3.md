---
title: Migrowanie do wersji v3 — translator
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera instrukcje ułatwiające Migrowanie z wersji 2 do V3 usługi Azure Cognitive Services translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: baaf907d2fafe23805e6b49582ecc7592ee05e0b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028237"
---
# <a name="translator-v2-to-v3-migration"></a>Migracja z usługi Translator do wersji v3

> [!NOTE]
> Wersja V2 została zaniechana 30 kwietnia 2018. Przeprowadź migrację aplikacji do wersji v3, aby korzystać z nowych funkcji dostępnych wyłącznie w wersji 3. Wersja 2 zostanie wycofana w dniu 24 maja 2021. 

Zespół usługi Microsoft Translator udostępnił w wersji 3 (V3) translatora. Ta wersja zawiera nowe funkcje, przestarzałe metody oraz nowy format służący do wysyłania i otrzymywania danych z usługi Microsoft Translator. Ten dokument zawiera informacje dotyczące zmieniania aplikacji w celu korzystania z wersji 3. 

Na końcu tego dokumentu znajdują się przydatne linki umożliwiające uzyskanie dodatkowych informacji.

## <a name="summary-of-features"></a>Podsumowanie funkcji

* Brak śladu w No-Trace v3 dotyczy wszystkich warstw cenowych w Azure Portal. Ta funkcja oznacza, że żaden tekst przesłany do interfejsu API V3 nie zostanie zapisany przez firmę Microsoft.
* Kod JSON-XML jest zastępowany przez kod JSON. Wszystkie dane wysyłane do usługi i odbierane z usługi są w formacie JSON.
* Wiele języków docelowych w pojedynczym żądaniu — Metoda translation akceptuje wiele języków "do" w celu tłumaczenia w pojedynczym żądaniu. Na przykład pojedyncze żądanie może mieć wartość "from" w języku angielskim i niemieckim, hiszpańskim i japońskim lub dowolną inną grupą języków.
* Słownik dwujęzyczny — Metoda słownika dwujęzycznego została dodana do interfejsu API. Ta metoda zawiera "Lookup" i "Przykłady".
* Transliteracja — Metoda transliteracji została dodana do interfejsu API. Ta metoda spowoduje przekonwertowanie słów i zdań w jednym skrypcie (np. Arabski) do innego skryptu (np. Łaciński).
* Języki — Nowa metoda "Języki" dostarcza informacje o języku w formacie JSON, do użycia z metodami "tłumaczyć", "dictionary" i "transliteracji".
* Nowe do przetłumaczenia — nowe możliwości zostały dodane do metody "Przetłumacz" w celu obsługi niektórych funkcji w interfejsie API v2 jako oddzielnych metod. Przykładem jest TranslateArray.
* Mówiąc z metody — funkcja zamiany tekstu na mowę nie jest już obsługiwana w usłudze Microsoft Translator. Funkcja zamiany tekstu na mowę jest dostępna w [usłudze Microsoft Speech Service](../speech-service/text-to-speech.md).

Poniższa lista metod v2 i V3 identyfikuje metody v3 i interfejsy API, które będą dostarczać funkcje dołączone do wersji 2.

| Metoda interfejsu API v2   | Zgodność interfejsu API v3 |
|:----------- |:-------------|
| `Translate`     | [Przetłumacz](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Przetłumacz](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Języki](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Języki](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Usługa Microsoft Speech](../speech-service/language-support.md#text-to-speech)         |
| `Speak`     | [Usługa Microsoft Speech](../speech-service/text-to-speech.md)          |
| `Detect`     | [Powinny](reference/v3-0-detect.md)         |
| `DetectArray`     | [Powinny](reference/v3-0-detect.md)         |
| `AddTranslation`     | Funkcja nie jest już obsługiwana       |
| `AddTranslationArray`    | Funkcja nie jest już obsługiwana          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Funkcja nie jest już obsługiwana         |
| `GetTranslationsArray`      | Funkcja nie jest już obsługiwana         |

## <a name="move-to-json-format"></a>Przenieś do formatu JSON

Tłumaczenie usługi Microsoft Translator w wersji 2 zostało zaakceptowane i zwróciło dane w formacie XML. W wersji 3 wszystkie dane wysyłane i odbierane przy użyciu interfejsu API są w formacie JSON. KOD XML nie zostanie już zaakceptowany ani zwrócony w wersji 3.

Ta zmiana wpłynie na kilka aspektów aplikacji zapisaną dla interfejsu API tłumaczenia tekstu w wersji 2. Przykład: interfejs API języków zwraca informacje o języku dla tłumaczenia tekstu, przeciągania i dwóch metod słownika. Możesz zażądać wszystkich informacji o języku dla wszystkich metod w jednym wywołaniu lub zażądać ich indywidualnie.

Metoda języka nie wymaga uwierzytelniania; klikając poniższe łącze, można zobaczyć wszystkie informacje o języku V3 w formacie JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, słownik, przetransliteracja](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Klucz uwierzytelniania

Klucz uwierzytelniania używany przez program v2 zostanie zaakceptowany w wersji 3. Nie musisz otrzymywać nowej subskrypcji. W przypadku aplikacji w okresie migracji yearlong będzie można mieszać wersje 2 i v3, ułatwiając udostępnianie nowych wersji, a w dalszym ciągu z wersji 2-XML do V3-JSON.

## <a name="pricing-model"></a>Model cen

Cena usługi Microsoft Translator V3 jest naliczana w taki sam sposób, jak w przypadku wersji 2. na znak, w tym spacje. Nowe funkcje w wersji 3 wprowadzają pewne zmiany, które są zliczane do rozliczania.

| V3 — Metoda   | Liczba znaków zliczona do rozliczenia |
|:----------- |:-------------|
| `Languages`     | Nie przesłano żadnych znaków, nie zliczane, bez opłat.          |
| `Translate`     | Licznik jest oparty na liczbie znaków przesyłanych do tłumaczenia oraz o liczbie języków, do których są tłumaczone znaki. 50 znaków i 5 żądanych języków zostanie 50x5.           |
| `Transliterate`     | Liczba znaków przesłanych do obsłużenia jest liczona.         |
| `Dictionary lookup & example`     | Liczba znaków przesłanych dla wyszukiwania słownika i przykładów, które są zliczane.         |
| `BreakSentence`     | Bez opłat.       |
| `Detect`     | Bez opłat.      |

## <a name="v3-end-points"></a>Punkty końcowe v3

Globalnie

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Metody tłumaczenia tekstu interfejsu API v3

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Zgodność i dostosowanie

> [!NOTE]
> 
> Centrum usługi Microsoft Translator zostanie wycofane w dniu 17 maja 2019. [Wyświetlanie ważnych informacji i dat migracji](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator v3 domyślnie używa tłumaczenia maszynowego neuronowych. W związku z tym nie można go używać z centrum usługi Microsoft Translator. Centrum translatora obsługuje tylko starsze tłumaczenia maszyn statystycznych. Dostosowanie dla tłumaczenia neuronowych jest teraz dostępne przy użyciu translatora niestandardowego. [Dowiedz się więcej o dostosowywaniu tłumaczenia maszynowego neuronowych](custom-translator/overview.md)

Translacja neuronowych z interfejsem API tekstu V3 nie obsługuje korzystania z standardowych kategorii (SMT, Speech, Tech, generalnn).

| Wersja | Punkt końcowy | Zgodność procesora Rodo | Korzystanie z centrum usługi translator | Korzystanie z translatora niestandardowego (wersja zapoznawcza) |
| :------ | :------- | :------------------------ | :----------------- | :------------------------------ |
|Translator — wersja 2|    api.microsofttranslator.com|    Nie    |Tak    |Nie|
|Translator w wersji 3|    api.cognitive.microsofttranslator.com|    Tak|    Nie|    Tak|

**Translator w wersji 3**
* Jest ogólnie dostępna i w pełni obsługiwana.
* Jest zgodny z Rodo jako procesor i spełnia wymagania certyfikacji ze wszystkich norm ISO 20001 i 20018 oraz 3. 
* Pozwala wywoływać systemy translacji sieci neuronowych dostosowane do niestandardowego translatora (wersja zapoznawcza), nowej funkcji dostosowywania usługi Translator NMT. 
* Nie zapewnia dostępu do niestandardowych systemów tłumaczenia utworzonych przy użyciu centrum Microsoft Translator.

Jeśli używasz punktu końcowego api.cognitive.microsofttranslator.com, używasz wersji 3 usługi Translator.

**Translator — wersja 2**
* Nie spełnia wymagań certyfikacji ze wszystkich norm ISO 20001, 20018 i SOC 3. 
* Nie zezwala na wywoływanie systemów translacji sieci neuronowych dostosowane do funkcji dostosowywania translatora.
* Zapewnia dostęp do niestandardowych systemów tłumaczenia utworzonych przy użyciu centrum usługi Microsoft Translator.
* Jeśli używasz punktu końcowego api.microsofttranslator.com, używasz wersji 2 translatora.

Żadna wersja translatora nie tworzy rekordu tłumaczenia. Twoje tłumaczenia nigdy nie są udostępniane nikomu. Więcej informacji na temat strony sieci Web usługi [Translator nie śledzenia](https://www.aka.ms/NoTrace) .

## <a name="links"></a>Linki

* [Zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement)
* [Microsoft Azure informacje prawne](https://azure.microsoft.com/support/legal)
* [Postanowienia dotyczące Usług Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyświetl dokumentację w wersji 3.0](reference/v3-0-reference.md)
