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
ms.date: 02/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: translator, tłumaczenie tekstu, tłumaczenie maszynowe, usługa tłumaczenia
ms.openlocfilehash: 72df700d38cad3ee31ef438d7df1c890acde9e6b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727978"
---
# <a name="what-is-the-translator-service"></a>Co to jest usługa translator?

Translator to oparta na chmurze usługa tłumaczenia maszynowego, która jest częścią usługi [Azure Cognitive Services platformą](../../index.yml?panel=ai&pivot=products) interfejsów API poznawczej służącej do kompilowania inteligentnych aplikacji. Usługi translator można łatwo zintegrować z aplikacjami, witrynami sieci Web, narzędziami i rozwiązaniami. Pozwala to na dodawanie środowisk użytkownika w wielu językach w przypadku [języków i dialektów 90](./language-support.md). Można go także używać na dowolnej platformie sprzętowej z dowolnym systemem operacyjnym na potrzeby tłumaczenia tekstu.

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

 Translatorem niestandardowym, rozszerzeniem usługi translatora, można użyć w połączeniu z tłumaczeniem, aby dostosować system tłumaczenia neuronowych i poprawić tłumaczenie dla konkretnej terminologii i stylu.

Dzięki usłudze translator niestandardowy można budować systemy tłumaczenia, aby obsługiwały terminologię używaną we własnej firmie lub branży. Dostosowany system tłumaczenia można łatwo zintegrować z istniejącymi aplikacjami, przepływami pracy, witrynami sieci Web i urządzeniami za pośrednictwem zwykłego translatora przy użyciu parametru Category.

Dowiedz się więcej na temat funkcji [Custom Translator](customization.md).

## <a name="next-steps"></a>Następne kroki

- [Utwórz usługę translatora](./translator-how-to-signup.md) , aby uzyskać klucze dostępu i punkt końcowy.
- Skorzystaj z naszego [przewodnika Szybki Start](quickstart-translator.md) , aby szybko wywołać usługę translatora.
- [Dokumentacja interfejsu API](./reference/v3-0-reference.md) zawiera dokumentację techniczną interfejsów API.
- [Szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
