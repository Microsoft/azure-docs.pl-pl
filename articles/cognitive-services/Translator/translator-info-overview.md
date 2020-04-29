---
title: Czym jest interfejs API tłumaczenia tekstu w usłudze Translator? — interfejs API tłumaczenia tekstu w usłudze Translator
titlesuffix: Azure Cognitive Services
description: Zintegruj interfejs API tłumaczenia tekstu w usłudze Translator z aplikacjami, witrynami internetowymi, narzędziami i innymi rozwiązaniami, aby umożliwić tworzenie środowisk użytkownika w wielu językach.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 12/09/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: f6b55163440b25bf69c76e9b1217c439b0884d5d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78331046"
---
# <a name="what-is-the-translator-text-api"></a>Czym jest interfejs API tłumaczenia tekstu w usłudze Translator?

Interfejs API tłumaczenia tekstu w usłudze Translator można łatwo zintegrować w aplikacjach, witrynach internetowych, narzędziach i rozwiązaniach. Umożliwia on dodawanie wielojęzycznych środowisk użytkowników w [ponad 60 językach](languages.md) i może być używany na dowolnej platformie sprzętowej z dowolnym systemem operacyjnym na potrzeby tłumaczenia tekstów w różnych językach.

Interfejs API tłumaczenia tekstu w usłudze Translator to część [kolekcji interfejsów API usług Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) platformy Azure, zbioru algorytmów uczenia maszynowego i sztucznej inteligencji w chmurze, który można łatwo wykorzystać w swoich projektach programistycznych.

## <a name="about-microsoft-translator"></a>Microsoft Translator — informacje

Microsoft Translator to oparta na chmurze usługa tłumaczenia maszynowego. Rdzeniem usługi jest interfejs API tłumaczenia tekstu w usłudze Translator, który współpracuje z wieloma produktami i usługami firmy Microsoft, a także jest używany przez tysiące firm na całym świecie w ich aplikacjach i przepływach pracy, co pozwala ich zawartości na dotarcie do odbiorców na całym świecie.

Tłumaczenie mowy obsługiwane przez interfejs API tłumaczenia tekstu w usłudze Translator jest również dostępne za pośrednictwem usługi [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Łączy ona funkcje z interfejs API tłumaczenia mowy w usłudze Translator i Custom Speech Service do ujednoliconej i w pełni dostosowywalnej usługi.Usługa rozpoznawania mowy zastępuje interfejs API tłumaczenia mowy w usłudze Translator, który zostanie zlikwidowany 15 października 2019 r.

## <a name="language-support"></a>Obsługa języków

Usługa Microsoft Translator zapewnia obsługę wielu języków na potrzeby tłumaczenia, transliteracji, wykrywania języka i słowników. Aby uzyskać pełną listę, zobacz [obsługiwane języki](language-support.md). Możesz również uzyskać dostęp do listy programowo przy użyciu [interfejsu API REST](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neuronowe tłumaczenie maszynowe w usłudze Microsoft Translator

Neuronowe tłumaczenie maszynowe (NMT) to nowy standard w dziedzinie wysokiej jakości tłumaczeń maszynowych opartych na sztucznej inteligencji. Zastępuje on przestarzałą technologię statystycznego tłumaczenia maszynowego (SMT), która osiągnęła pułap swoich możliwości w połowie lat 2010.

Technologia NMT zapewnia lepsze tłumaczenia niż technologia SMT nie tylko z punktu widzenia oceny jakości pierwotnego tłumaczenia, ale także dlatego, że brzmią one bardziej płynnie i po ludzku. Główną przyczyną tej płynności jest fakt, że technologia NMT używa do tłumaczenia słów pełnego kontekstu zdania. Technologia SMT uwzględniała tylko bezpośredni kontekst kilku słów poprzedzających każde słowo i następujących po nim.

Modele NMT są podstawą interfejsu API i nie są widoczne dla użytkowników końcowych. Jedyną zauważalną różnicą jest lepsza jakość tłumaczeń, szczególnie w przypadku takich języków jak chiński, japoński i arabski.

Dowiedz się więcej o [sposobie działania technologii NMT](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>Dostosowywanie języka

W połączeniu z interfejsem API tłumaczenia tekstu w usłudze Translator można użyć rozszerzenia podstawowej usługi Microsoft Translator, Custom Translator, aby ułatwić dostosowanie systemu tłumaczenia neuronowego i ulepszyć tłumaczenia pod kątem określonej terminologii i stylu.

Korzystając z rozszerzenia Custom Translator, można tworzyć systemy tłumaczenia obsługujące terminologię stosowaną w Twojej firmie lub branży. Dostosowany system tłumaczenia łatwo integruje się z istniejącymi aplikacjami, przepływami pracy i witrynami internetowymi na wielu typach urządzeń za pośrednictwem interfejsu API tłumaczenia tekstu w usłudze Microsoft Translator, używając parametru kategorii.

Dowiedz się więcej o [dostosowywaniu języków](customization.md)

## <a name="next-steps"></a>Następne kroki

- [Utwórz konto](translator-text-how-to-signup.md), aby uzyskać klucz dostępu.
- [Dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) zawiera dokumentację techniczną interfejsów API.
- [Szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
