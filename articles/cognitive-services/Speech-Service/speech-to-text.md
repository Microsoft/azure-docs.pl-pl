---
title: Omówienie zamiany mowy na tekst — usługa mowy
titleSuffix: Azure Cognitive Services
description: Oprogramowanie zamiany mowy na tekst umożliwia transkrypcję strumieni audio w czasie rzeczywistym w postaci tekstu. Twoje aplikacje, narzędzia lub urządzenia mogą wykorzystywać, wyświetlać i podejmować działania dotyczące tego tekstu. Ten artykuł zawiera omówienie zalet i możliwości usługi zamiany mowy na tekst.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: Zamiana mowy na tekst, oprogramowanie zamiany mowy na tekst
ms.openlocfilehash: 5e593202a8cd68dfc4a92f991d573b4b06051b36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434320"
---
# <a name="what-is-speech-to-text"></a>Co to jest zamiana mowy na tekst?

Ten przegląd zawiera informacje o zaletach i możliwościach usługi zamiany mowy na tekst.
Zamiana mowy na tekst, nazywana również rozpoznawaniem mowy, umożliwia zapisanie strumieni audio w czasie rzeczywistym do tekstu. Aplikacje, narzędzia lub urządzenia mogą zużywać, wyświetlać i podejmować działania dotyczące tego tekstu jako dane wejściowe polecenia. Ta usługa jest oparta na tej samej technologii rozpoznawania, która jest wykorzystywana przez firmę Microsoft dla Cortany i produktów pakietu Office. Bezproblemowo współpracuje z ofertami usług <a href="./speech-translation.md" target="_blank">tłumaczenia </a> i <a href="./text-to-speech.md" target="_blank">zamiany tekstu na mowę </a> . Aby uzyskać pełną listę dostępnych języków zamiany mowy na tekst, zobacz [obsługiwane języki](language-support.md#speech-to-text).

Usługa zamiany mowy na tekst domyślnie używa modelu języka uniwersalnego. Ten model został przeszkolony przy użyciu danych firmy Microsoft i jest wdrażany w chmurze. Jest optymalny dla scenariuszy konwersacji i dyktowania. Przy użyciu zamiany mowy na tekst w celu rozpoznawania i transkrypcji w unikatowym środowisku można tworzyć i uczenie niestandardowych modeli akustycznych, językowych i wymowy. Dostosowanie jest pomocne w odniesieniu do hałasu otoczenia lub słownictwa specyficznego dla branży.

Dzięki dodatkowemu tekstowi referencyjnemu jako dane wejściowe, usługa zamiany mowy na tekst umożliwia również [ocenę](rest-speech-to-text.md#pronunciation-assessment-parameters) wymowy, aby ocenić wymowę mowy i dać nam opinię na temat dokładności i Fluency głosu. Dzięki ocenie wymowy, informacje o języku mogą być praktyczne, otrzymywać błyskawiczne informacje zwrotne i ulepszać ich wymowę, aby mogły mówić i być niebezpieczne. Osoby wykorzystujące wykładowcy mogą oszacować wymowę wielu głośników w czasie rzeczywistym. Ta funkcja obsługuje obecnie angielskie stany USA i skorelowanie z ocenami mowy prowadzonymi przez ekspertów.

> [!NOTE]
> Rozpoznawanie mowy Bing został zlikwidowany 15 października 2019. Jeśli Twoje aplikacje, narzędzia lub produkty używają interfejsów API rozpoznawanie mowy Bing, zostały utworzone przewodniki ułatwiające Migrowanie do usługi mowy.
> - [Migrowanie z rozpoznawanie mowy Bing do usługi mowy](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started"></a>Rozpoczęcie pracy

Zapoznaj się z [przewodnikiem Szybki Start](get-started-speech-to-text.md) , aby rozpocząć pracę z funkcją zamiany mowy na tekst. Usługa jest dostępna za pośrednictwem [zestawu Speech SDK](speech-sdk.md), [interfejsu API REST](rest-speech-to-text.md#pronunciation-assessment-parameters)i [wiersza polecenia usługi Speech](spx-overview.md).

## <a name="sample-code"></a>Przykładowy kod

Przykładowy kod dla zestawu Speech SDK jest dostępny w witrynie GitHub. Te przykłady obejmują typowe scenariusze, takie jak odczytywanie audio z pliku lub strumienia, rozpoznawanie ciągłe i jednokrotne oraz praca z modelami niestandardowymi.

- [Przykłady zamiany mowy na tekst (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Przykłady transkrypcji partii (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Przykłady oceny wymowy (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Dostosowywanie

Oprócz standardowego modelu usługi mowy można tworzyć modele niestandardowe. Dostosowanie pomaga w przezwyciężeniu barier rozpoznawania mowy, takich jak styl mowy, słownictwo i hałas w tle, zobacz [Custom Speech](./custom-speech-overview.md). Opcje dostosowywania różnią się w zależności od języka/ustawień regionalnych, zobacz [obsługiwane języki](./language-support.md) , aby sprawdzić pomoc techniczną.

## <a name="batch-transcription"></a>Transkrypcja wsadowa

Transkrypcja usługi Batch to zestaw operacji interfejsu API REST, które umożliwiają transkrypcja dużej ilości danych audio w magazynie. Możesz wskazać pliki audio z identyfikatorem URI sygnatury dostępu współdzielonego (SAS) i asynchronicznie otrzymywać wyniki transkrypcji. Zobacz [instrukcje](batch-transcription.md) , aby uzyskać więcej informacji na temat korzystania z interfejsu API transkrypcji usługi Batch.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Następne kroki

- [Uzyskaj bezpłatnie klucz subskrypcji usługi mowy](overview.md#try-the-speech-service-for-free)
- [Pobieranie zestawu Speech SDK](speech-sdk.md)