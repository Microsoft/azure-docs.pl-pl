---
title: Lista funkcji programu Azure Media Player
description: Odwołanie do funkcji dla programu Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e5595620a2f888b06ad5b35d2e8a008f23861463
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727231"
---
# <a name="feature-list"></a>Lista funkcji #
Oto lista przetestowanych funkcji i nieobsługiwałych funkcji:

|                                         | Testowane | CZĘŚCIOWO PRZETESTOWANE | Niesprawdzone | Nieobsługiwane | UWAGI                                                                                                                |
|:----------------------------------------|--------|------------------|----------|-------------|:---------------------------------------------------------------------------------------------------------------------|
| Odtwarzanie                                |        |                  |          |             |                                                                                                                      |
| Podstawowe odtwarzanie na żądanie                | X      |                  |          |             | Obsługuje tylko strumienie z usługi Azure Media Services                                                                      |
| Podstawowe odtwarzanie na żywo                     | X      |                  |          |             | Obsługuje tylko strumienie z usługi Azure Media Services                                                                      |
| AES                                     | X      |                  |          |             | Obsługa usługi dostarczania kluczy usługi Azure Media Services                                                                   |
| Multi-DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Obsługa usługi dostarczania kluczy usługi Azure Media Services                                                                   |
| Widevine                                |        | X                |          |             | Obsługuje pola Widevine PSSH opisane w manifeście                                                                    |
| FairPlay                                |        | X                |          |             | Obsługa usługi dostarczania kluczy usługi Azure Media Services                                                                   |
| Techników                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Powrót do lampy błyskowej (flashss)                | X      |                  |          |             | Nie wszystkie funkcje są dostępne w tej technologii.                                                                         |
| Silverlight Rezerwowe oświetlenie silverlightss      | X      |                  |          |             | Nie wszystkie funkcje są dostępne w tej technologii.                                                                         |
| Natywny przepusty HLS (Html5)         |        | X                |          |             | Nie wszystkie funkcje są dostępne w tej technologii ze względu na ograniczenia platformy.                                            |
| Funkcje                                |        |                  |          |             |                                                                                                                      |
| Obsługa interfejsu API                             | X      |                  |          |             | Zobacz listę znanych problemów                                                                                                |
| Podstawowy interfejs użytkownika                                | X      |                  |          |                                                                                                                                    |
| Inicjowanie za pomocą języka JavaScript       | X      |                  |          |             |                                                                                                                      |
| Inicjowanie za pomocą tagu wideo        |        | X                |          |             |                                                                                                                      |
| Adresowanie segmentów — oparte na czasie         | X      |                  |          |             |                                                                                                                      |
| Adresowanie segmentów — oparte na indeksach        |        |                  |          | X           |                                                                                                                      |
| Adresowanie segmentów — oparte na bajtach         |        |                  |          | X           |                                                                                                                      |
| Nagrywarka adresów URL usługi Azure Media Services       |        | X                |          |             |                                                                                                                      |
| Dostępność — napisy i napisy  |        | X                |          |             |  WebVTT obsługiwane na żądanie, na żywo CEA 708 częściowo przetestowane                                                       |
| Ułatwienia dostępu — skróty klawiszowe                 | X      |                  |          |             |                                                                                                                      |
| Dostępność — wysoki kontrast           |        | X                |          |             |                                                                                                                      |
| Ułatwienia dostępu — fokus na karcie               |        | X                |          |             |                                                                                                                      |
| Komunikat o błędzie                         |        | X                |          |             | Komunikaty o błędach są niespójne w technikach                                                                         |
| Wyzwalanie zdarzeń                        | X      |                  |          |             |                                                                                                                      |
| Diagnostyka                             |        | X                |          |             | Informacje diagnostyczne są dostępne tylko w technologii AzureHtml5JS i częściowo dostępne w technologii SilverlightSS. |
| Konfigurowalne zamówienie techniczne                 |        | X                |          |             |                                                                                                                      |
| Heurystyka - Podstawowe                      | X      |                  |          |             |                                                                                                                      |
| Heurystyka - Personalizacja              |        |                  | X        |             | Dostosowywanie jest dostępne tylko w technologii AzureHtml5JS.                                                          |
| Nieciągłości                         | X      |                  |          |             |                                                                                                                      |
| Wybierz szybkość transmisji bitów                          | X      |                  |          |             | Ten interfejs API jest dostępny tylko w serwisach technologicznych AzureHtml5JS i FlashSS.                                                    |
| Strumień multi-audio                      |        | X                |          |             | Programowy przełącznik audio jest obsługiwany w serwisach AzureHtml5JS i FlashSS i jest dostępny za pośrednictwem wyboru interfejsu użytkownika na platformie AzureHtml5JS, FlashSS i native Html5 (w przeglądarce Safari).  Większość platform wymaga tych samych prywatnych danych kodeka, aby przełączać strumienie audio (ten sam kodek, kanał, częstotliwość próbkowania itp.). |
| Lokalizacja interfejsu użytkownika                         |        | X                |          |             |                                                                                                                      |
| Odtwarzanie wieloadłowe                 |        |                  |          | X           | Ten scenariusz może działać dla niektórych techników, ale jest obecnie nieobsługiwane i niesprawdzone. Możesz również uzyskać to do pracy przy użyciu ramek iframe |
| Obsługa reklam                             |        | x                |          |             | AMP obsługuje wstawianie reklam liniowych przed- i po-roll z serwerów reklam zgodnych z VAST dla VOD w technologii AzureHtml5JS |
| Analiza                               |        | X                |          |             | AMP umożliwia słuchanie zdarzeń analitycznych i diagnostycznych w celu wysłania do wybranej przez użytkownika zaplecza Analytics.  Wszystkie zdarzenia i właściwości nie są dostępne w technikach ze względu na ograniczenia platformy.                                                                            |
| Skórki niestandardowe                            |        |                  | X        |             | Obróć kontrolki ustawień na false w AMP i używając własnego HTML i CSS.           |
| Szukaj szorowania paska                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play (Trick-Play)                              |        |                  |          | X           |                                                                                                                      |
| Tylko dźwięk                              |        |                  |          | X           | Może działać w niektórych technikach adaptacyjnego przesyłania strumieniowego, ale obecnie nie jest obsługiwany i nie działa w usłudze AzureHtml5JS. Progresywne odtwarzanie MP3 może współpracować z technologią HTML5, jeśli platforma go obsługuje.                                                                                                        |
| Tylko wideo                              |        |                  |          | X           | Może działać w niektórych technikach adaptacyjnego przesyłania strumieniowego, ale obecnie nie jest obsługiwany i nie działa w usłudze AzureHtml5JS.      |
| Prezentacja wieloekoletnia               |        |                  |          | X                                                                                                                                  |
| Wiele kątów kamery                  |        |                  |          | X           |                                                                                                                      |
| Szybkość odtwarzania                          |        | X                |          |             | Szybkość odtwarzania jest obsługiwana w większości scenariuszy z wyjątkiem przypadku mobilnego z powodu częściowego błędu w Chrome                 |

## <a name="next-steps"></a>Następne kroki ##
- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)