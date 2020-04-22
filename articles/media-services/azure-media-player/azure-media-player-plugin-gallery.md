---
title: Galeria wtyczek programu Azure Media Player
description: Ten artykuł zawiera listę wtyczek dostępnych dla programu Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 206200f3ba0757cd738439b58c8b94874cf5a938
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726480"
---
# <a name="azure-media-player-plugin-gallery"></a>Galeria wtyczek programu Azure Media Player #

## <a name="plugins"></a>Wtyczki ##

| Nazwa wtyczki                         | Adres URL wersji demonstracyjnej                    | Kod źródłowy                | Opis    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| Dodatkowe funkcje                 | | | |
| **Nowy!** AMP360Fileo                | [Wersja demonstracyjna](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | Wtyczka pozwala wizualizować wideo 360 w Amp na pulpicie lub w urządzeniach zgodnych z VR. Pełna dokumentacja jest dostępna [tutaj:](https://doc\.babylonjs\.com/extensions/amp360video) |
|  Końcówka sprite'a                         | [Wersja demonstracyjna](http://www.skymedia.tv/asset/sprite)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Wtyczka programu Azure Media Player (AMP) do renderowania osi czasu ikonki obrazu miniatur wideo, który jest generowany z usługi Azure Media Services (AMS) Media Encoder Standard (MES). |
| Nakładka diagnostyczna                 | [Wersja demonstracyjna](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Ta wtyczka wyświetla: Wszystkie kluczowe parametry, statystyki wideo, wszystkie zdarzenia w cyklu życia odtwarzania wideo i informacje o ochronie DRM, takie jak identyfikator klucza, adresy URL nabycia licencji, jeśli są chronione.                                                                                                                                                                      |
| Liczba klatek na sekundę i kalkulator kodu czasowego | [Wersja demonstracyjna](http://mconvertitest001.blob.core.windows.net/public/example.html)                        | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Ta wtyczka oblicza liczbę klatek `tfhd` / `trun` na sekundę wideo na podstawie pól MP4 pierwszego fragmentu wideo MPEG-DASH, analizuje wartość skali czasu z manifestu klienta MPEG-DASH, a także zapewnia sposób generowania kodu czasowego dla danego czasu bezwzględnego od odtwarzacza (a także zapewnia graczowi bezwzględny czas biorąc pod uwagę kod czasowy) |
| <strike>Szybkość odtwarzania</strike>                      | [Wersja demonstracyjna](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Ta wtyczka pozwala widzom kontrolować, jaka prędkość filmu. *Uwaga: ta funkcja jest automatycznie dostępna w wersji AMP v2.0.0+, ale domyślnie wyłączona.* Aby dowiedzieć się, jak go włączyć, zapoznaj się z naszymi przykładami [tutaj](https://github.com/Azure-Samples/azure-media-player-samples) |
| Hover Wskazówka czasowa                      | [Wersja demonstracyjna](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Wyświetla przewrotkę czasu nad paskiem postępu myszy, aby najechać dokładnie na czas. *Uwaga: Ta wtyczka jest już zintegrowana z AMP,* ale jeśli chcesz zobaczyć, jak jest zaprogramowana, możesz się przyjrzeć.                                                                                                                       |
| Nakładka tytułu                       | [Wersja demonstracyjna](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin/")                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Nakładki konfigurowalny tytuł wideo na ekranie |
| Znaczniki osi czasu                    | [Wersja demonstracyjna](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Ta wtyczka ma w tablicy razy i nakłada małe markery na pasku postępu w tym czasie. |
| Analiza                           | | | |
| Application Insights                | [Blogu](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Wtyczka, która śledzi dane odtwarzacza i portuje go do usługi Power BI, aby uzyskać intuicyjną graficzną reprezentację środowiska gracza widzów. |
| Google Analytics                    | Nie dotyczy                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Wtyczka Google Analytics dla programu Azure Media Player |
| Diagnostyka                         | | | |
| Wyjście diagnostyczne                  | [Wersja demonstracyjna](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Ta wtyczka wyprowadza szereg diagnostyki z odtwarzacza, aby zobaczyć go w akcji przejdź do łącza demonstracyjnego i otwórz konsolę JavaScript. |
| Ułatwienia dostępu                      | | | |
| Powiększ                             | [Wersja demonstracyjna](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Ta wtyczka wyświetla przeciągnij-w skali zoom-in na ekranie odtwarzacza, dzięki czemu widzowie mogą powiększyć zawartość |
| Napisy na żywo                       | [Post w blogu platformy Azure](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/),[SubPly Post](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | Nie dotyczy | *Zobacz post, aby uzyskać więcej informacji.* End to End przepływ pracy przeznaczony do podpisów na żywo zbudowany wtyczki dla programu Azure Media Player, kliknij na lewym lin, aby przejść do witryny SubPly i dowiedzieć się więcej o rozwiązaniu |
| Skróty klawiszowe                            | <strike>[Demonstracja](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | Wtyczka klawiszy skrótowych umożliwia widzom sterowanie różnymi aspektami odtwarzacza za pomocą ogólnych kombinacji wtyczek, takich jak F dla pełnoekranowych, M dla klawiszy wyciszenia i klawiszy strzałek do sterowania paskiem postępu. *Uwaga: Ta wtyczka została już zintegrowana z AMP, ale możesz używać jej jako zasobu* |
| Społeczności                              | | | |
| Udostępnij                               | [Wersja demonstracyjna](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Ta wtyczka dodaje przycisk udostępniania do paska sterowania odtwarzacza, dzięki czemu widzowie mogą udostępniać film, który oglądają ze znajomymi za pośrednictwem Facebooka, Twittera lub Linkedin. |

## <a name="next-steps"></a>Następne kroki ##

- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)