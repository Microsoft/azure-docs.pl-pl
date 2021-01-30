---
title: Galeria wtyczek Azure Media Player
description: Ten artykuł zawiera listę wtyczek dostępnych dla Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 71fa79cb8847d16ac0890f9aba647cb8f5e2e444
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089346"
---
# <a name="azure-media-player-plugin-gallery"></a>Galeria wtyczek Azure Media Player #

## <a name="plugins"></a>Wtyczki ##

| Nazwa wtyczki                         | Adres URL pokazu                    | Kod źródłowy                | Opis    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| Dodatkowe funkcje                 | | | |
| **Nowy!** AMP360Video                | [Wersja demonstracyjna](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | Wtyczka pozwala wizualizować 360 wideo w amp na komputerze lub na urządzeniach zgodnych z VR. Pełna dokumentacja jest dostępna [tutaj](https://doc.babylonjs.com/extensions/amp360video): |
|  Etykietka Sprite                         | [Wersja demonstracyjna](https://www.smwcentral.net/?p=section&a=details&id=10301)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Wtyczka Azure Media Player (AMP) do renderowania osi czasu miniatury obrazu wideo, który jest generowany na podstawie Azure Media Services (AMS) Media Encoder Standard (MES). |
| Nakładka diagnostyki                 | [Wersja demonstracyjna](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Ta wtyczka jest wyświetlana: wszystkie parametry klucza, statystyki wideo, wszystkie zdarzenia w cyklu życia odtwarzania wideo i informacje o ochronie DRM, takie jak identyfikator klucza, adresy URL pozyskiwania licencji, jeśli są chronione.                                                                                                                                                                      |
| Szybkość klatek i Kalkulator Timecode | Pokaz nie jest dostępny | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Ta wtyczka służy do obliczania współczynnika klatek wideo na podstawie `tfhd` / `trun` pól MP4 pierwszego fragmentu wideo MPEG-pauzowego, analizy wartości skali czasu z manifestu klienta MPEG-myślnik, a także umożliwia wygenerowanie Timecode dla danego czasu bezwzględnego z odtwarzacza (a także zapewnia bezwzględny czas odtwarzacza w przypadku Timecode). |
| <strike>Szybkość odtwarzania</strike>                      | [Wersja demonstracyjna](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Ta wtyczka umożliwia użytkownikom kontrolowanie szybkości wideo. *Uwaga Ta funkcja jest automatycznie dostępna w wersji AMP v 2.0.0 +, ale domyślnie wyłączona.* Aby dowiedzieć się, jak ją włączyć, zapoznaj się z [naszymi przykładami](https://github.com/Azure-Samples/azure-media-player-samples) . |
| Porada dotycząca czasu aktywowania                      | [Wersja demonstracyjna](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Wyświetla poradę czasową na pasku postępu przy umieszczeniu wskaźnika myszy w celu dokładnego wyszukiwania. *Uwaga: Ta wtyczka jest już zintegrowana* z firmą amp, ale jeśli interesuje Cię sposób, w jaki jest ona zaprogramowana, aby zapoznać się z jej doświadczeniem.                                                                                                                 |
| Nakładka tytułu                       | [Wersja demonstracyjna](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Nakładający się konfigurowalny tytuł wideo na ekran |
| Znaczniki osi czasu                    | [Wersja demonstracyjna](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Ta wtyczka zabiera tablicę razy i nakłada małe znaczniki na pasek postępu w tych godzinach. |
| Analiza                           | | | |
| Application Insights                | [Wpis w blogu](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Wtyczka, która śledzi metryki odtwarzacza i porty, aby Power BI intuicyjną graficzną reprezentację środowiska odtwarzacza przeglądający. |
| Google Analytics                    | Nie dotyczy                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Wtyczka usługi Google Analytics dla Azure Media Player |
| Diagnostyka                         | | | |
| Dane wyjściowe diagnostyki                  | [Wersja demonstracyjna](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Ta wtyczka wyświetla tablicę diagnostyki, aby zobaczyć ją w akcji przejdź do linku demonstracyjnego i otworzyć konsolę JavaScript. |
| Ułatwienia dostępu                      | | | |
| Powiększ                             | [Wersja demonstracyjna](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Ta wtyczka pokazuje skalę skalowania w poziomie na ekranie odtwarzaczy, dzięki czemu przeglądający mogą powiększać zawartość |
| Napisy na żywo                       | [Wpis w blogu platformy Azure](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/),[wpis SubPly](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | Nie dotyczy | *Aby uzyskać więcej informacji, zobacz wpis post.* Kompleksowy przepływ pracy zaprojektowany z myślą o wbudowanej usłudze Live Captions dla Azure Media Player, kliknij link z lewej strony, aby przejść do witryny SubPly i dowiedzieć się więcej o rozwiązaniu |
| Klawisze dostępu                            | <strike>[Demonstracja](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | Wtyczka klawisze dostępu pozwala użytkownikom kontrolować różne aspekty odtwarzacza z ogólnymi kombinacjami wtyczek, takimi jak F for pełnoekranowego, M dla wyciszenia i klawiszy strzałek dla kontrolki pasek postępu. *Uwaga: Ta wtyczka została już zintegrowana z AMP, ale śmiało, aby używać jej jako zasobu* |
| Funkcje społecznościowe                              | | | |
| Udostępnij                               | [Wersja demonstracyjna](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Ta wtyczka dodaje przycisk udostępniania do paska sterowania odtwarzacza, dzięki czemu przeglądający mogą udostępniać wideo, które są oglądane dla znajomych za pośrednictwem serwisu Facebook, Twitter lub LinkedIn. |

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)
