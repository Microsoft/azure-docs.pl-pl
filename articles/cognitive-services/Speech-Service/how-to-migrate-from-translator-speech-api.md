---
title: Migrowanie z interfejs API tłumaczenia mowy w usłudze Translator do usługi mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak migrować aplikacje z interfejs API tłumaczenia mowy w usłudze Translator do usługi mowy.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 2fb03721baa80e77a5fd387600a272e6b1cfc7d3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013648"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Migrowanie z interfejs API tłumaczenia mowy w usłudze Translator do usługi mowy

Ten artykuł służy do migrowania aplikacji z interfejs API tłumaczenia mowy w usłudze Translator firmy Microsoft do [usługi Speech](index.yml). Ten przewodnik przedstawia różnice między usługami interfejs API tłumaczenia mowy w usłudze Translator i Speech, a także sugeruje strategie migracji aplikacji.

> [!NOTE]
> Klucz subskrypcji interfejs API tłumaczenia mowy w usłudze Translator nie zostanie zaakceptowany przez usługę mowy. Musisz utworzyć nową subskrypcję usługi mowy.

## <a name="comparison-of-features"></a>Porównanie funkcji

| Obiekt feature                                           | Interfejs API tłumaczenia mowy w usłudze Translator                                  | Usługa rozpoznawania mowy | Szczegóły                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tłumaczenie na tekst                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Tłumaczenie na mowę                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Globalny punkt końcowy                                   | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Usługa mowy nie oferuje globalnego punktu końcowego. Globalny punkt końcowy może automatycznie kierować ruch do najbliższego regionalnego punktu końcowego, zmniejszając opóźnienia w aplikacji.                                                    |
| Regionalne punkty końcowe                                | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limit czasu połączenia                             | 90 minut                                               | Bez ograniczeń z zestawem SDK. 10 minut z połączeniem z usługą WebSockets.                                                                                                                                                                                                                                                                                   |
| Klucz uwierzytelniania w nagłówku                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Wiele języków przetłumaczonych w pojedynczym żądaniu | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Dostępne zestawy SDK                                    | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Zobacz [dokumentację usługi Speech](index.yml) dla dostępnych zestawów SDK.                                                                                                                                                    |
| Połączenia obiektów WebSockets                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Interfejs API języków                                     | :heavy_check_mark:                                              | : heavy_minus_sign:                 | Usługa mowy obsługuje te same Języki, które opisano w artykule [Skorowidz języka translatora]() . |
| Filtr i znacznik niewulgarności                       | : heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| WAV/PCM jako dane wejściowe                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Inne typy plików jako dane wejściowe                         | : heavy_minus_sign:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Wyniki częściowe                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informacje o chronometrażu                                       | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                 |
| Identyfikator korelacji                                    | :heavy_check_mark:                                              | : heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Niestandardowe modele mowy                              | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Usługa mowy oferuje niestandardowe modele mowy, które umożliwiają dostosowanie rozpoznawania mowy do unikatowego słownika w organizacji.                                                                                                                                           |
| Niestandardowe modele tłumaczenia                         | : heavy_minus_sign:                                              | :heavy_check_mark:                 | Subskrybowanie interfejsu API tłumaczenia tekstu firmy Microsoft umożliwia korzystanie z [translatora niestandardowego](https://www.microsoft.com/translator/business/customization/) do korzystania z własnych danych w celu uzyskania dokładniejszych tłumaczeń.                                                 |

## <a name="migration-strategies"></a>Strategie migracji

Jeśli ty lub Twoja organizacja ma aplikacje w programowaniu lub środowisku produkcyjnym, które używają interfejs API tłumaczenia mowy w usłudze Translator, należy je zaktualizować do korzystania z usługi Speech. Zobacz dokumentację [usługi Speech](index.yml) dla dostępnych zestawów SDK, przykłady kodu i samouczki. Podczas migrowania należy wziąć pod uwagę następujące kwestie:

* Usługa mowy nie oferuje globalnego punktu końcowego. Ustal, czy aplikacja działa wydajniej, gdy używa jednego regionu punktu końcowego dla całego ruchu. W przeciwnym razie użyj geolokalizacji, aby określić najbardziej wydajny punkt końcowy.

* Jeśli aplikacja korzysta z długotrwałych połączeń i nie może korzystać z dostępnych zestawów SDK, można użyć połączenia z usługą WebSockets. Aby zarządzać limitem 10-minutowego limitu czasu, należy ponownie połączyć się w odpowiednim czasie.

* Jeśli aplikacja używa usługi translatora i interfejs API tłumaczenia mowy w usłudze Translator do włączenia niestandardowych modeli tłumaczenia, można dodać identyfikatory kategorii bezpośrednio przy użyciu usługi Speech.

* W przeciwieństwie do interfejs API tłumaczenia mowy w usłudze Translator usługa mowy umożliwia wykonywanie tłumaczeń w wielu językach w pojedynczym żądaniu.

## <a name="next-steps"></a>Następne kroki

* [Wypróbuj bezpłatnie usługę mowy](overview.md#try-the-speech-service-for-free)
* [Szybki Start: Rozpoznawanie mowy w aplikacji platformy UWP przy użyciu zestawu Speech SDK](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Zobacz także

* [Co to jest usługa mowy](overview.md)
* [Dokumentacja usługi mowy i zestawu Speech SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android)