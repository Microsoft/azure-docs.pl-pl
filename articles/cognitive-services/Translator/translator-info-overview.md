---
title: Co to jest translator? -Translator
titlesuffix: Azure Cognitive Services
description: Zintegruj translator z aplikacjami, witrynami sieci Web, narzędziami i innymi rozwiązaniami, aby zapewnić obsługę środowiska użytkownika w wielu językach.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 12/09/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: e4a1f2d778fb2b811d4c38dd26956e2eab51258a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592665"
---
# <a name="what-is-the-translator"></a>Co to jest translator?

Translator można łatwo zintegrować z aplikacjami, witrynami sieci Web, narzędziami i rozwiązaniami. Umożliwia on dodawanie wielojęzycznych środowisk użytkowników w [ponad 60 językach](languages.md) i może być używany na dowolnej platformie sprzętowej z dowolnym systemem operacyjnym na potrzeby tłumaczenia tekstów w różnych językach.

Translator jest częścią [usługi Azure Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) zbieraniem algorytmów uczenia maszynowego i AI w chmurze oraz jest łatwo do użycia w projektach programistycznych.

## <a name="about-microsoft-translator"></a>Microsoft Translator — informacje

Translator jest opartą na chmurze usługą tłumaczenia maszynowego. Podstawowa usługa to translator, który daje wiele produktów i usług firmy Microsoft, i jest używany przez tysiące firm na całym świecie w swoich aplikacjach i przepływach pracy, co umożliwia ich zawartości dostęp do globalnej grupy odbiorców.

Tłumaczenie mowy obsługiwane przez translatora jest również dostępne w [usłudze Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Łączy ona funkcje z interfejs API tłumaczenia mowy w usłudze Translator i Custom Speech Service do ujednoliconej i w pełni dostosowywalnej usługi.Usługa rozpoznawania mowy zastępuje interfejs API tłumaczenia mowy w usłudze Translator, który zostanie zlikwidowany 15 października 2019 r.

## <a name="language-support"></a>Obsługa języków

Usługa Microsoft Translator zapewnia obsługę wielu języków na potrzeby tłumaczenia, transliteracji, wykrywania języka i słowników. Aby uzyskać pełną listę, zobacz [obsługiwane języki](language-support.md). Możesz również uzyskać dostęp do listy programowo przy użyciu [interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neuronowe tłumaczenie maszynowe w usłudze Microsoft Translator

Neuronowe tłumaczenie maszynowe (NMT) to nowy standard w dziedzinie wysokiej jakości tłumaczeń maszynowych opartych na sztucznej inteligencji. Zastępuje on przestarzałą technologię statystycznego tłumaczenia maszynowego (SMT), która osiągnęła pułap swoich możliwości w połowie lat 2010.

Technologia NMT zapewnia lepsze tłumaczenia niż technologia SMT nie tylko z punktu widzenia oceny jakości pierwotnego tłumaczenia, ale także dlatego, że brzmią one bardziej płynnie i po ludzku. Główną przyczyną tej płynności jest fakt, że technologia NMT używa do tłumaczenia słów pełnego kontekstu zdania. Technologia SMT uwzględniała tylko bezpośredni kontekst kilku słów poprzedzających każde słowo i następujących po nim.

Modele NMT są podstawą interfejsu API i nie są widoczne dla użytkowników końcowych. Jedyną zauważalną różnicą jest lepsza jakość tłumaczeń, szczególnie w przypadku takich języków jak chiński, japoński i arabski.

Dowiedz się więcej o [sposobie działania technologii NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>Dostosowywanie języka

Rozszerzenie podstawowej usługi Microsoft Translator może być używane w połączeniu z tłumaczeniem, aby pomóc w dostosowaniu systemu tłumaczenia neuronowych i ulepszaniu tłumaczenia dla konkretnej terminologii i stylu.

Korzystając z rozszerzenia Custom Translator, można tworzyć systemy tłumaczenia obsługujące terminologię stosowaną w Twojej firmie lub branży. Dostosowany system tłumaczenia zostanie następnie łatwo zintegrować z istniejącymi aplikacjami, przepływami pracy i witrynami sieci Web na wielu typach urządzeń za pośrednictwem zwykłego translatora przy użyciu parametru kategorii.

Dowiedz się więcej o [dostosowywaniu języków](customization.md)

## <a name="next-steps"></a>Następne kroki

- [Utwórz konto](translator-text-how-to-signup.md), aby uzyskać klucz dostępu.
- [Dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) zawiera dokumentację techniczną interfejsów API.
- [Szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
