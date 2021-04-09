---
title: Dostosowywanie tłumaczenia — translator
titleSuffix: Azure Cognitive Services
description: Użyj centrum usługi Microsoft translator, aby skompilować własny system tłumaczenia maszynowego przy użyciu preferowanej terminologii i stylu.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 0930024a791fe8b76c90e8ab4249a070b22a1c16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98898040"
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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Skonfiguruj dostosowany system językowy przy użyciu translatora niestandardowego](./custom-translator/overview.md)
