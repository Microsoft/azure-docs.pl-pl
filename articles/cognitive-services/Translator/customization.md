---
title: Dostosowywanie tłumaczenia — translator
titleSuffix: Azure Cognitive Services
description: Użyj centrum usługi Microsoft translator, aby skompilować własny system tłumaczenia maszynowego przy użyciu preferowanej terminologii i stylu.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 95cb4aa5827190abf125669f2423c808cf8c92a5
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368937"
---
# <a name="customize-your-text-translations"></a>Dostosowywanie tłumaczenia tekstu

Translator niestandardowy jest funkcją usługi translator, która umożliwia użytkownikom dostosowanie zaawansowanego tłumaczenia maszynowego neuronowych w usłudze Microsoft Translator podczas tłumaczenia tekstu przy użyciu translatora (tylko wersja 3).

Funkcja ta może również służyć do dostosowywania tłumaczenia mowy, gdy jest używany z [Cognitive Services mowy](../speech-service/index.yml).

## <a name="custom-translator"></a>Custom Translator

Dzięki usłudze translator niestandardowy można budować systemy tłumaczenia neuronowych, które znają terminologię używaną we własnej firmie i branży. Dostosowany system tłumaczenia zostanie następnie zintegrowany z istniejącymi aplikacjami, przepływami pracy i witrynami sieci Web.

### <a name="how-does-it-work"></a>Jak to działa?

Skorzystaj z wcześniej przetłumaczonych dokumentów (ulotek, stron sieci Web, dokumentacji itp.), aby skompilować system tłumaczenia, który odzwierciedla terminologię i styl specyficzny dla domeny, lepszy niż standardowy system tłumaczenia. Użytkownicy mogą przekazywać dokumenty TMX, XLIFF, TXT, DOCX i XLSX.  

System akceptuje również dane, które są równoległe na poziomie dokumentu, ale nie są jeszcze wyrównane na poziomie zdania. Jeśli użytkownicy mają dostęp do wersji tej samej zawartości w wielu językach, ale w oddzielnych dokumentach translator niestandardowy będzie mógł automatycznie dopasować zdania w dokumentach.  System może również używać danych w języku lub w obu językach, aby uzupełnić równoległe dane szkoleniowe w celu usprawnienia tłumaczenia.

Dostosowany system jest następnie dostępny za pośrednictwem zwykłego wywołania do translatora przy użyciu parametru kategorii.

Uwzględniając odpowiedni typ i ilość danych szkoleniowych, nie zdarza się, że występuje różnica między 5 a 10, lub jeszcze więcej punktów BLEU dotyczących jakości tłumaczenia przy użyciu translatora niestandardowego.

Więcej szczegółowych informacji na temat różnych poziomów dostosowywania opartych na dostępnych danych można znaleźć w [podręczniku użytkownika usługi tłumaczenia niestandardowego](./custom-translator/overview.md).


## <a name="microsoft-translator-hub"></a>Centrum usługi Microsoft Translator

> [!NOTE]
> Starsza wersja centrum usługi Microsoft Translator zostanie wycofana w dniu 17 maja 2019. [Wyświetlanie ważnych informacji i dat migracji](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Translator niestandardowy a centrum

| Cechy | Koncentrator | Custom Translator |
| ------- | :-: | :---------------: |
|Stan funkcji dostosowywania    | Ogólna dostępność    | Ogólna dostępność |
| Wersja interfejsu API tekstu    | Tylko wersja 2    | Tylko wersja 3 |
| Dostosowanie SMT    | Tak    | Nie |
| Dostosowanie NMT    | Nie    | Tak |
| Nowe ujednolicone usługi rozpoznawania mowy    | Nie    | Tak |
| [Brak śladu](https://www.aka.ms/notrace) | Tak    | Tak |

## <a name="collaborative-translations-framework"></a>Struktura tłumaczeń współpracy

> [!NOTE]
> Od 1 lutego 2018, addtranslation () i AddTranslationArray () nie są już dostępne do użycia z translatorem w wersji 2.0. Te metody zakończą się niepowodzeniem i nic nie zostanie zapisaniu. Translator 3.0 nie obsługuje tych metod.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Skonfiguruj dostosowany system językowy przy użyciu translatora niestandardowego](./custom-translator/overview.md)