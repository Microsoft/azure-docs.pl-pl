---
title: Listy fraz — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak dostarczyć usługę mowy `PhraseListGrammar` z listą fraz przy użyciu obiektu, aby poprawić wyniki rozpoznawania mowy na tekst.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5a21358edae4c61f35993770c22634da9ac83633
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401964"
---
# <a name="phrase-lists-for-speech-to-text"></a>Listy fraz dla zamiany mowy na tekst

Udostępniając usługę mowy z listą fraz, można poprawić dokładność rozpoznawania mowy. Listy fraz służą do identyfikowania znanych fraz w danych audio, takich jak imię i nazwisko osoby lub określona lokalizacja.

Na przykład, jeśli masz polecenie "Przenieś do" i możliwe miejsce docelowe "Ward", które może być wypowiedziane, możesz dodać wpis "Przenieś do okręgu". Dodanie frazy zwiększy prawdopodobieństwo, że po rozpoznaniu dźwięku, że "Przenieś do Warda" zostanie rozpoznany zamiast "Przejdź w kierunku".

Pojedyncze słowa lub pełne frazy można dodać do listy fraz. Podczas rozpoznawania wpis na liście fraz jest używany, jeśli dokładne dopasowanie dla całej frazy jest zawarte w audio jako osobna fraza. Jeśli dokładne dopasowanie do frazy nie zostanie znalezione, rozpoznawanie nie jest wspomagane.

>[!Note]
> Obecnie listy fraz obsługuje tylko angielski dla mowy do tekstu.

## <a name="how-to-use-phrase-lists"></a>Jak korzystać z list fraz

Poniższe przykłady ilustrują sposób tworzenia `PhraseListGrammar` listy fraz przy użyciu obiektu.

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> Maksymalna liczba list fraz, które usługa Mowy będzie używać do dopasowania mowy jest 1024 fraz.

Można również wyczyścić frazy skojarzone z wywołaniem `PhraseListGrammar` clear().

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Zmiany w `PhraseListGrammar` obiekcie mają wpływ na następne rozpoznanie lub po ponownym połączeniu z usługą mowy.

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja referencyjna SDK mowy](speech-sdk.md)
