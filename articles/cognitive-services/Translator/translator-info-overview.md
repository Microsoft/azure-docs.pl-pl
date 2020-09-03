---
title: Co to jest usługa Translator? -Translator
titlesuffix: Azure Cognitive Services
description: Zintegruj translator z aplikacjami, witrynami sieci Web, narzędziami i innymi rozwiązaniami, aby zapewnić obsługę środowiska użytkownika w wielu językach.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 05/26/2020
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 206e1bc86404fa41b84647c50482dbe63301c0af
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89425963"
---
# <a name="what-is-translator"></a>Co to jest usługa Translator?

Azure Cognitive Services translator to oparta na chmurze usługa tłumaczenia maszynowego, która jest częścią [platformy Azure Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) interfejsów API poznawczej do tworzenia inteligentnych aplikacji. Usługi translator można łatwo zintegrować z aplikacjami, witrynami sieci Web, narzędziami i rozwiązaniami. Pozwala to na dodawanie środowiska użytkownika w wielu [językach w więcej niż 70 języków](languages.md)i może być używane na dowolnej platformie sprzętowej z dowolnym systemem operacyjnym na potrzeby tłumaczenia tekstu na tekst.

## <a name="about-microsoft-translator"></a>Microsoft Translator — informacje

Azure Cognitive Services translator to oparta na chmurze usługa tłumaczenia maszynowego. Usługa translator umożliwia korzystanie z wielu produktów i usług firmy Microsoft, które są używane przez tysiące firm na całym świecie w aplikacjach i przepływach pracy, dzięki czemu zawartość dociera do odbiorców globalnych.

Tłumaczenie mowy obsługiwane przez translatora jest również dostępne w [usłudze Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Łączy ona funkcje z interfejs API tłumaczenia mowy w usłudze Translator i Custom Speech Service do ujednoliconej i w pełni dostosowywalnej usługi. 

## <a name="language-support"></a>Obsługa języków

Usługa Microsoft Translator zapewnia obsługę wielu języków na potrzeby tłumaczenia, transliteracji, wykrywania języka i słowników. Aby uzyskać pełną listę, zobacz [obsługiwane języki](language-support.md). Możesz również uzyskać dostęp do listy programowo przy użyciu [interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neuronowe tłumaczenie maszynowe w usłudze Microsoft Translator

Neuronowe tłumaczenie maszynowe (NMT) to nowy standard w dziedzinie wysokiej jakości tłumaczeń maszynowych opartych na sztucznej inteligencji. Zastępuje on przestarzałą technologię statystycznego tłumaczenia maszynowego (SMT), która osiągnęła pułap swoich możliwości w połowie lat 2010.

Technologia NMT zapewnia lepsze tłumaczenia niż technologia SMT nie tylko z punktu widzenia oceny jakości pierwotnego tłumaczenia, ale także dlatego, że brzmią one bardziej płynnie i po ludzku. Główną przyczyną tej płynności jest fakt, że technologia NMT używa do tłumaczenia słów pełnego kontekstu zdania. Technologia SMT uwzględniała tylko bezpośredni kontekst kilku słów poprzedzających każde słowo i następujących po nim.

Modele NMT są podstawą interfejsu API i nie są widoczne dla użytkowników końcowych. Jedyną zauważalną różnicą jest lepsza jakość tłumaczeń, szczególnie w przypadku takich języków jak chiński, japoński i arabski.

Dowiedz się więcej o [sposobie działania technologii NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>Dostosowywanie języka

Rozszerzenie usługi translator, alias niestandardowy może być używany w połączeniu z tłumaczeniem, aby ułatwić dostosowanie systemu tłumaczenia neuronowych oraz usprawnienie tłumaczenia określonej terminologii i stylu.

Korzystając z rozszerzenia Custom Translator, można tworzyć systemy tłumaczenia obsługujące terminologię stosowaną w Twojej firmie lub branży. Dostosowany system tłumaczenia zostanie następnie łatwo zintegrować z istniejącymi aplikacjami, przepływami pracy i witrynami sieci Web na wielu typach urządzeń za pośrednictwem zwykłego translatora przy użyciu parametru kategorii.

Dowiedz się więcej o [dostosowywaniu języków](customization.md)

## <a name="next-steps"></a>Następne kroki

- [Zarejestruj się](translator-text-how-to-signup.md) , aby uzyskać klucz dostępu.
- [Dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) zawiera dokumentację techniczną interfejsów API.
- [Szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
