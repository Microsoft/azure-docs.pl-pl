---
title: Usługa Microsoft Translator
titlesuffix: Azure Cognitive Services
description: Zintegruj translator z aplikacjami, witrynami sieci Web, narzędziami i innymi rozwiązaniami, aby zapewnić obsługę środowiska użytkownika w wielu językach.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: translator, tłumaczenie tekstu, tłumaczenie maszynowe, usługa tłumaczenia
ms.openlocfilehash: ec76aa7554110b7440eb825f2d5e86ae2da6baa2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657726"
---
# <a name="what-is-the-translator-service"></a>Co to jest usługa translator?

Translator to oparta na chmurze usługa tłumaczenia maszynowego, która jest częścią usługi [Azure Cognitive Services platformą](../../index.yml?panel=ai&pivot=products) interfejsów API poznawczej służącej do kompilowania inteligentnych aplikacji. Usługi translator można łatwo zintegrować z aplikacjami, witrynami sieci Web, narzędziami i rozwiązaniami. Pozwala to na dodawanie środowisk użytkownika w języku [90 i dialektów](./language-support.md) , które mogą być używane do tłumaczenia tekstu w dowolnym systemie operacyjnym.

Ta dokumentacja zawiera następujące typy artykułów:  

* Przewodniki [**Szybki Start**](quickstart-translator.md) to instrukcje umożliwiające wykonywanie żądań do usługi.  
* [**Przewodniki z**](translator-how-to-signup.md) instrukcjami dotyczącymi używania usługi w bardziej specyficzny lub dostosowany sposób.  
* [**Koncepcje**](character-counts.md) zawierają szczegółowe wyjaśnienia funkcji i funkcji usługi.  
* [**Samouczki**](tutorial-wpf-translation-csharp.md) są więcej przewodnikami, które pokazują, jak korzystać z usługi jako składnika w szerszym zakresie rozwiązań dla biznesu.  


## <a name="about-microsoft-translator"></a>Microsoft Translator — informacje

Translator umożliwia korzystanie z wielu produktów i usług firmy Microsoft i jest używany przez tysiące firm na całym świecie w aplikacjach i przepływach pracy.

Tłumaczenie mowy obsługiwane przez translatora jest również dostępne w [usłudze Azure Speech Service](../speech-service/index.yml). Łączy ona funkcje z interfejs API tłumaczenia mowy w usłudze Translator i Custom Speech Service do ujednoliconej i w pełni dostosowywalnej usługi. 

## <a name="language-support"></a>Obsługa języków

Translator zapewnia obsługę wielu języków w przypadku tłumaczenia tekstu, przeciągania, wykrywania języka i słowników. Aby uzyskać pełną listę, zobacz [obsługiwane języki](language-support.md). Możesz również uzyskać dostęp do listy programowo przy użyciu [interfejsu API REST](./reference/v3-0-languages.md).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neuronowe tłumaczenie maszynowe w usłudze Microsoft Translator

Neuronowe tłumaczenie maszynowe (NMT) to nowy standard w dziedzinie wysokiej jakości tłumaczeń maszynowych opartych na sztucznej inteligencji. Zastępuje on przestarzałą technologię statystycznego tłumaczenia maszynowego (SMT), która osiągnęła pułap swoich możliwości w połowie lat 2010.

Technologia NMT zapewnia lepsze tłumaczenia niż technologia SMT nie tylko z punktu widzenia oceny jakości pierwotnego tłumaczenia, ale także dlatego, że brzmią one bardziej płynnie i po ludzku. Główną przyczyną tej płynności jest fakt, że technologia NMT używa do tłumaczenia słów pełnego kontekstu zdania. Technologia SMT uwzględniała tylko bezpośredni kontekst kilku słów poprzedzających każde słowo i następujących po nim.

Modele NMT są podstawą interfejsu API i nie są widoczne dla użytkowników końcowych. Jedyną zauważalną różnicą jest lepsza jakość tłumaczeń, szczególnie w przypadku takich języków jak chiński, japoński i arabski.

Dowiedz się więcej o tym, [jak działa NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="improve-translations-with-custom-translator"></a>Ulepszanie tłumaczeń przy użyciu translatora niestandardowego

 [Translatorem niestandardowym](customization.md), rozszerzeniem usługi translatora, można użyć do dostosowania systemu tłumaczenia neuronowych i podniesienia poziomu tłumaczenia określonej terminologii i stylu.

Dzięki usłudze translator niestandardowy można budować systemy tłumaczenia, aby obsługiwały terminologię używaną we własnej firmie lub branży. Dostosowany system tłumaczenia można łatwo zintegrować z istniejącymi aplikacjami, przepływami pracy, witrynami sieci Web i urządzeniami za pośrednictwem zwykłego translatora przy użyciu parametru Category.

## <a name="next-steps"></a>Następne kroki

- [Utwórz usługę translatora](./translator-how-to-signup.md) , aby uzyskać klucze dostępu i punkt końcowy.
- Skorzystaj z naszego [przewodnika Szybki Start](quickstart-translator.md) , aby szybko wywołać usługę translatora.
- [Dokumentacja interfejsu API](./reference/v3-0-reference.md) zawiera dokumentację techniczną interfejsów API.
- [Szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
