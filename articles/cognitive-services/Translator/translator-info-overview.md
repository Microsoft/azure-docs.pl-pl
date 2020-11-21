---
title: Usługa Microsoft Translator
titlesuffix: Azure Cognitive Services
description: Zintegruj translator z aplikacjami, witrynami sieci Web, narzędziami i innymi rozwiązaniami, aby zapewnić obsługę środowiska użytkownika w wielu językach.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 09/11/2020
ms.author: swmachan
ms.custom: cog-serv-seo-aug-2020
keywords: translator, tłumaczenie tekstu, tłumaczenie maszynowe, usługa tłumaczenia
ms.openlocfilehash: 8016c79cbe7f78bdaa18274f8873548e3982df2f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016479"
---
# <a name="what-is-the-translator-service"></a>Co to jest usługa translator?

Translator to oparta na chmurze usługa tłumaczenia maszynowego, która jest częścią usługi [Azure Cognitive Services platformą](../../index.yml?panel=ai&pivot=products) interfejsów API poznawczej służącej do kompilowania inteligentnych aplikacji. Usługi translator można łatwo zintegrować z aplikacjami, witrynami sieci Web, narzędziami i rozwiązaniami. Pozwala to na dodawanie środowiska użytkownika w wielu [językach w więcej niż 70 języków](./language-support.md)i może być używane na dowolnej platformie sprzętowej przy użyciu dowolnego systemu operacyjnego na potrzeby tłumaczenia tekstu.

## <a name="about-microsoft-translator"></a>Microsoft Translator — informacje

Usługa translator umożliwia korzystanie z wielu produktów i usług firmy Microsoft, które są używane przez tysiące firm na całym świecie w aplikacjach i przepływach pracy, dzięki czemu zawartość dociera do odbiorców globalnych.

Tłumaczenie mowy obsługiwane przez translatora jest również dostępne w [usłudze Azure Speech Service](../speech-service/index.yml). Łączy ona funkcje z interfejs API tłumaczenia mowy w usłudze Translator i Custom Speech Service do ujednoliconej i w pełni dostosowywalnej usługi. 

## <a name="language-support"></a>Obsługa języków

Translator zapewnia obsługę wielu języków w przypadku tłumaczenia tekstu, przeciągania, wykrywania języka i słowników. Aby uzyskać pełną listę, zobacz [obsługiwane języki](language-support.md). Możesz również uzyskać dostęp do listy programowo przy użyciu [interfejsu API REST](./reference/v3-0-languages.md).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neuronowe tłumaczenie maszynowe w usłudze Microsoft Translator

Neuronowe tłumaczenie maszynowe (NMT) to nowy standard w dziedzinie wysokiej jakości tłumaczeń maszynowych opartych na sztucznej inteligencji. Zastępuje on przestarzałą technologię statystycznego tłumaczenia maszynowego (SMT), która osiągnęła pułap swoich możliwości w połowie lat 2010.

Technologia NMT zapewnia lepsze tłumaczenia niż technologia SMT nie tylko z punktu widzenia oceny jakości pierwotnego tłumaczenia, ale także dlatego, że brzmią one bardziej płynnie i po ludzku. Główną przyczyną tej płynności jest fakt, że technologia NMT używa do tłumaczenia słów pełnego kontekstu zdania. Technologia SMT uwzględniała tylko bezpośredni kontekst kilku słów poprzedzających każde słowo i następujących po nim.

Modele NMT są podstawą interfejsu API i nie są widoczne dla użytkowników końcowych. Jedyną zauważalną różnicą jest lepsza jakość tłumaczeń, szczególnie w przypadku takich języków jak chiński, japoński i arabski.

Dowiedz się więcej o tym, [jak działa NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt).

## <a name="improve-translations-with-custom-translator"></a>Ulepszanie tłumaczeń przy użyciu translatora niestandardowego

Rozszerzenie usługi translator, alias niestandardowy może być używany w połączeniu z tłumaczeniem, aby ułatwić dostosowanie systemu tłumaczenia neuronowych oraz usprawnienie tłumaczenia określonej terminologii i stylu.

Korzystając z rozszerzenia Custom Translator, można tworzyć systemy tłumaczenia obsługujące terminologię stosowaną w Twojej firmie lub branży. Dostosowany system tłumaczenia zostanie następnie łatwo zintegrować z istniejącymi aplikacjami, przepływami pracy i witrynami sieci Web na wielu typach urządzeń za pośrednictwem zwykłego translatora przy użyciu parametru kategorii.

Dowiedz się więcej na temat funkcji [Custom Translator](customization.md).

## <a name="next-steps"></a>Następne kroki

- [Zarejestruj się](./translator-how-to-signup.md) , aby uzyskać klucz dostępu.
- Skorzystaj z naszego [przewodnika Szybki Start](quickstart-translator.md) , aby szybko wywołać usługę translatora.
- [Dokumentacja interfejsu API](./reference/v3-0-reference.md) zawiera dokumentację techniczną interfejsów API.
- [Szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)