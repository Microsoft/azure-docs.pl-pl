---
title: Migrowanie z rozpoznawanie mowy Bing do usługi mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przeprowadzić migrację z istniejącej subskrypcji rozpoznawanie mowy Bing do usługi mowy z platformy Azure Cognitive Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: 43679c52727f8cc84c7292592b68dddae7f1ea68
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362082"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrowanie z rozpoznawanie mowy Bing do usługi mowy

Ten artykuł służy do migrowania aplikacji z interfejs API rozpoznawania mowy Bing do usługi mowy.

W tym artykule opisano różnice między interfejsami API rozpoznawanie mowy Bing i usługą mowy oraz zaproponowano strategie migracji aplikacji. Klucz subskrypcji interfejs API rozpoznawania mowy Bing nie będzie działał z usługą mowy; będziesz potrzebować nowej subskrypcji usługi mowy.

Pojedynczy klucz subskrypcji usługi mowy umożliwia dostęp do następujących funkcji. Każda z tych funkcji jest mierzona oddzielnie, dlatego opłaty są naliczane tylko za funkcje, których używasz.

* [Zamiana mowy na tekst](speech-to-text.md)
* [Niestandardowa zamiana mowy na tekst](https://cris.ai)
* [Zamiana tekstu na mowę](text-to-speech.md)
* [Niestandardowe głosy funkcji zamiany tekstu na mowę](how-to-customize-voice-font.md)
* [Tłumaczenie mowy](speech-translation.md) (nie obejmuje funkcji [Tłumaczenie tekstu](../translator/translator-info-overview.md))

[Zestaw Speech SDK](speech-sdk.md) to funkcjonalne zastępowanie dla bibliotek klienta rozpoznawanie mowy Bing, ale używa innego interfejsu API.

## <a name="comparison-of-features"></a>Porównanie funkcji

Usługa mowy jest w dużym stopniu podobna do rozpoznawanie mowy Bing, z następującymi różnicami.

| Cechy | Rozpoznawanie mowy Bing | Usługa rozpoznawania mowy | Szczegóły |
|--|--|--|--|
| Zestaw SDK języka C# | :heavy_check_mark: | :heavy_check_mark: | Usługa Speech obsługuje systemy Windows 10, platforma uniwersalna systemu Windows (platformy UWP) i .NET Standard 2,0. |
| ZESTAW SDK JĘZYKA C++ | : heavy_minus_sign: | :heavy_check_mark: | Usługa Speech obsługuje systemy Windows i Linux. |
| Zestaw SDK Java | :heavy_check_mark: | :heavy_check_mark: | Usługa Speech obsługuje urządzenia z systemem Android i Speech. |
| Ciągłe rozpoznawanie mowy | 10 minut | Bez ograniczeń (z zestawem SDK) | Protokoły usługi WebSockets rozpoznawanie mowy Bing i Speech obsługują do 10 minut na wywołanie. Jednak zestaw Speech SDK automatycznie ponownie nawiązuje połączenie po przekroczeniu limitu czasu lub rozłączenia. |
| Wyniki częściowe lub pośrednie | :heavy_check_mark: | :heavy_check_mark: | Za pomocą protokołu WebSockets lub zestawu SDK. |
| Niestandardowe modele mowy | :heavy_check_mark: | :heavy_check_mark: | Rozpoznawanie mowy Bing wymaga oddzielnej subskrypcji Custom Speech. |
| Niestandardowe czcionki głosowe | :heavy_check_mark: | :heavy_check_mark: | Rozpoznawanie mowy Bing wymaga oddzielnej niestandardowej subskrypcji głosowej. |
| głosy 24-kHz | : heavy_minus_sign: | :heavy_check_mark: |
| Rozpoznawanie opcji mowy | Wymaga oddzielnego wywołania interfejsu API LUIS | Zintegrowany (z zestawem SDK) | Za pomocą usługi mowy można użyć klucza LUIS. |
| Proste rozpoznawanie intencji | : heavy_minus_sign: | :heavy_check_mark: |
| Transkrypcja partii długich plików audio | : heavy_minus_sign: | :heavy_check_mark: |
| Tryb rozpoznawania | Ręczne za pomocą identyfikatora URI punktu końcowego | Automatyczny | Tryb rozpoznawania nie jest dostępny w usłudze Speech. |
| Miejscowość punktu końcowego | Globalnie | Regionalne | Regionalne punkty końcowe poprawiają opóźnienia. |
| Interfejsy API REST | :heavy_check_mark: | :heavy_check_mark: | Interfejsy API REST usługi Speech są zgodne z rozpoznawanie mowy Bing (inny punkt końcowy). Interfejsy API REST obsługują funkcję zamiany tekstu na mowę i ograniczoną funkcjonalność zamiany mowy na tekst. |
| Protokoły WebSockets | :heavy_check_mark: | :heavy_check_mark: | Interfejs API usługi WebSockets w usłudze Speech jest zgodny z rozpoznawanie mowy Bing (inny punkt końcowy). W miarę możliwości Migruj do zestawu Speech SDK, aby uprościć swój kod. |
| Wywołania interfejsu API między usługami | :heavy_check_mark: | : heavy_minus_sign: | Dostępne w rozpoznawanie mowy Bing za pośrednictwem biblioteki usług C#. |
| Zestaw SDK open source | :heavy_check_mark: | : heavy_minus_sign: |

Usługa mowy korzysta z modelu cenowego opartego na czasie (a nie modelu opartego na transakcji). Aby uzyskać szczegółowe informacje, zobacz [Cennik usługi Speech](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

## <a name="migration-strategies"></a>Strategie migracji

Jeśli ty lub Twoja organizacja ma aplikacje w programowaniu lub środowisku produkcyjnym, które używają interfejs API rozpoznawania mowy Bing, należy je zaktualizować tak szybko, jak to możliwe. Zobacz [dokumentację usługi Speech](index.yml) dla dostępnych zestawów SDK, przykłady kodu i samouczki.

[Interfejsy API REST](rest-apis.md) usługi Speech są zgodne z interfejsami API rozpoznawanie mowy Bing. Jeśli obecnie używasz interfejsów API REST rozpoznawanie mowy Bing, musisz zmienić tylko punkt końcowy REST i przełączyć się na klucz subskrypcji usługi rozpoznawania mowy.

Protokoły WebSockets usługi Speech są również zgodne z tymi używanymi przez rozpoznawanie mowy Bing. Zalecamy, aby w przypadku nowego programowania używać zestawu Speech SDK zamiast obiektów WebSockets. Dobrym pomysłem jest również migrowanie istniejącego kodu do zestawu SDK. Jednak, podobnie jak w przypadku interfejsów API REST, istniejący kod, który używa rozpoznawanie mowy Bing za pośrednictwem sieci WebSockets, wymaga tylko zmiany w punkcie końcowym i zaktualizowanym kluczu.

Jeśli używasz biblioteki klienta rozpoznawanie mowy Bing dla określonego języka programowania, migracja do [zestawu Speech SDK](speech-sdk.md) wymaga wprowadzenia zmian w aplikacji, ponieważ interfejs API jest inny. Zestaw Speech SDK może uprościć kod, a jednocześnie zapewnia dostęp do nowych funkcji. Zestaw Speech SDK jest dostępny w wielu różnych językach programowania. Interfejsy API na wszystkich platformach są podobne i ułatwiają programowanie wielu platform.

Usługa mowy nie oferuje globalnego punktu końcowego. Ustal, czy aplikacja działa wydajniej, gdy używa jednego regionu punktu końcowego dla całego ruchu. W przeciwnym razie użyj geolokalizacji, aby określić najbardziej wydajny punkt końcowy. Wymagana jest osobna subskrypcja usługi mowy w każdym używanym regionie.

Jeśli aplikacja korzysta z długotrwałych połączeń i nie może użyć dostępnego zestawu SDK, możesz użyć połączenia z usługą WebSockets. Aby zarządzać limitem 10-minutowego limitu czasu, należy ponownie połączyć się w odpowiednim czasie.

Aby rozpocząć pracę z zestawem Speech SDK:

1. Pobierz [zestaw Speech SDK](speech-sdk.md).
1. Przechodzenie do [przewodników szybki start](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) i [samouczków](how-to-recognize-intents-from-speech-csharp.md)dotyczących usługi Speech. Zapoznaj się również z [przykładami kodu](samples.md) , aby uzyskać dostęp do nowych interfejsów API.
1. Zaktualizuj aplikację, aby korzystać z usługi Speech.

## <a name="support"></a>Pomoc techniczna

Rozpoznawanie mowy Bing klienci powinni skontaktować się z działem pomocy technicznej, otwierając [bilet pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Możesz również skontaktować się z nami, jeśli zapotrzebowanie na pomoc techniczną wymaga [planu pomocy](https://azure.microsoft.com/support/plans/)technicznej.

Aby uzyskać pomoc techniczną dotyczącą usługi mowy, zestawu SDK i interfejsu API, odwiedź [stronę pomocy technicznej](support.md)usługi mowy.

## <a name="next-steps"></a>Następne kroki

* [Wypróbuj bezpłatnie usługę mowy](overview.md#try-the-speech-service-for-free)
* [Szybki Start: Rozpoznawanie mowy w aplikacji platformy UWP przy użyciu zestawu Speech SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Zobacz też
* [Informacje o wersji usługi mowy](releasenotes.md)
* [Co to jest usługa mowy](overview.md)
* [Dokumentacja usługi mowy i zestawu Speech SDK](speech-sdk.md#get-the-speech-sdk)
