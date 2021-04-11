---
title: Lista funkcji Azure Media Player
description: Odwołanie do funkcji dla Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 4b0666b439c284fd402b3f6e04bbaed14d6aa358
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448767"
---
# <a name="feature-list"></a>Lista funkcji #
Poniżej znajduje się Lista przetestowanych funkcji i nieobsługiwanych funkcji:

| Cecha | SPRAWDZA | CZĘŚCIOWO PRZETESTOWANE | NIEPRZETESTOWANEGO | ICH | UWAGI |
| ------- | ------ | ---------------- | -------- | ----------- | ----- |
| **Odtwarzanie**                                |        |                  |          |             |                                                                                                                      |
| Podstawowe odtwarzanie na żądanie                | X      |                  |          |             | Obsługuje strumienie tylko z Azure Media Services                                                                      |
| Podstawowe odtwarzanie na żywo                     | X      |                  |          |             | Obsługuje strumienie tylko z Azure Media Services                                                                      |
| AES                                     | X      |                  |          |             | Obsługuje usługę dostarczania kluczy Azure Media Services                                                                   |
| Wiele technologii DRM                               |        | X                |          |             |                                                                                                                      |
| PlayReady                               | X      |                  |          |             | Obsługuje usługę dostarczania kluczy Azure Media Services                                                                   |
| Widevine                                |        | X                |          |             | Obsługuje pola Widevine PSSH opisane w manifeście                                                                    |
| FairPlay                                |        | X                |          |             | Obsługuje usługę dostarczania kluczy Azure Media Services                                                                   |
| **Technologie techniczne**                                   |        |                  |          |             |                                                                                                                      |
| MSE/EME (AzureHtml5JS)                  | X      |                  |          |             |                                                                                                                      |
| Alternatywa błyskowa (błysk)                | X      |                  |          |             | Nie wszystkie funkcje są dostępne w tej technologii technicznej.                                                                         |
| Silverlight rezerwowego Silverlight      | X      |                  |          |             | Nie wszystkie funkcje są dostępne w tej technologii technicznej.                                                                         |
| Przekazywanie natywne HLS (HTML5)         |        | X                |          |             | Nie wszystkie funkcje są dostępne z powodu ograniczeń platformy.                                            |
| **Funkcje**                                |        |                  |          |             |                                                                                                                      |
| Obsługa interfejsu API                             | X      |                  |          |             | Zobacz listę znanych problemów                                                                                                |
| Podstawowy interfejs użytkownika                                | X      |                  |          |                                                                                                                                    |
| Inicjowanie za poorednictwem języka JavaScript       | X      |                  |          |             |                                                                                                                      |
| Inicjowanie przez tag wideo        |        | X                |          |             |                                                                                                                      |
| Adresowanie segmentu — na podstawie czasu         | X      |                  |          |             |                                                                                                                      |
| Adresowanie segmentu — oparte na indeksie        |        |                  |          | X           |                                                                                                                      |
| Adresowanie segmentu na podstawie bajtu         |        |                  |          | X           |                                                                                                                      |
| Azure Media Services ponownej edycji adresu URL       |        | X                |          |             |                                                                                                                      |
| Ułatwienia dostępu — podpisy i napisy  | X      |                 |          |             |  WebVTT (na żądanie), CEA 708 (na żądanie i na żywo) i IMSC1 (na żądanie i na żywo)                                                       |
| Ułatwienia dostępu — klawisze skrótu                 | X      |                  |          |             |                                                                                                                      |
| Ułatwienia dostępu — duży kontrast           |        | X                |          |             |                                                                                                                      |
| Dostępność — fokus kart               |        | X                |          |             |                                                                                                                      |
| Komunikaty o błędach                         |        | X                |          |             | Komunikaty o błędach są niespójne w ramach konferencji Tech.                                                                         |
| Wyzwalanie zdarzeń                        | X      |                  |          |             |                                                                                                                      |
| Diagnostyka                             |        | X                |          |             | Informacje diagnostyczne są dostępne tylko na AzureHtml5JS technicznej i częściowo dostępne w technologii Silverlight. |
| Dostosowywalne zamówienie techniczne                 |        | X                |          |             |                                                                                                                      |
| Algorytmy heurystyczne — podstawowe                      | X      |                  |          |             |                                                                                                                      |
| Algorytmy heurystyczne — Dostosowywanie              |        |                  | X        |             | Dostosowanie jest dostępne tylko w przypadku AzureHtml5JS Tech.                                                          |
| Przerwy                         | X      |                  |          |             |                                                                                                                      |
| Wybierz szybkość transmisji bitów                          | X      |                  |          |             | Ten interfejs API jest dostępny tylko w AzureHtml5JS i błysku Tech.                                                    |
| Strumień audio                      |        | X                |          |             | Programistyczny przełącznik dźwiękowy jest obsługiwany przez AzureHtml5JS i błyskowe techniczne i jest dostępny w ramach wyboru interfejsu użytkownika w przypadku AzureHtml5JS, Flash i natywnego języka HTML5 (w przeglądarce Safari).  Większość platform wymaga, aby te same dane prywatne kodera-dekoder przełączać strumienie audio (ten sam koder-dekoder, kanał, częstotliwość próbkowania itp.). |
| Lokalizacja interfejsu użytkownika                         |        | X                |          |             |                                                                                                                      |
| Odtwarzanie przez wiele wystąpień                 |        |                  |          | X           | Ten scenariusz może obsłużyć niektóre czynności techniczne, ale nie jest aktualnie obsługiwany i nietestowany. Można to również zrobić, aby korzystać z elementów iframe |
| Obsługa reklam                             |        | X                |          |             | AMP obsługuje wstawianie ofert liniowych w wersji wstępnej i końcowej z niezgodnych serwerów usługi AD do VOD w AzureHtml5JS Tech |
| Analiza                               |        | X                |          |             | AMP umożliwia nasłuchiwanie zdarzeń analitycznych i diagnostycznych w celu wysłania do wybranego zaplecze analizy.  Wszystkie zdarzenia i właściwości nie są dostępne w ramach techniczna ze względu na ograniczenia platformy.                                                                            |
| Karnacje niestandardowe                            |        |                  | X        |             | Ten scenariusz można osiągnąć przez włączenie dla kontrolek ustawień wartości false w AMP i użycie własnego kodu HTML i CSS.           |
| Szybka kontrola paska wyszukiwania                      |        |                  |          | X           |                                                                                                                      |
| Trick-Play                              |        |                  |          | X           |                                                                                                                      |
| Tylko audio                              | X      |                  |          |           | Obsługiwane w AzureHtml5JS. Progresywne odtwarzanie w formacie MP3 może współpracowało z przepisami HTML5 Tech., jeśli Platforma obsługuje tę funkcję.                                                                                                        |
| Tylko wideo                              | X      |                  |          |           | Obsługiwane w AzureHtml5JS.                                                                                                        |
| Prezentacja wielookresowa               |        |                  |          | X                                                                                                                                  |
| Wiele kątów aparatu                  |        |                  |          | X           |                                                                                                                      |
| Szybkość odtwarzania                          |        | X                |          |             | Szybkość odtwarzania jest obsługiwana w większości scenariuszy poza przypadkiem mobilnym ze względu na częściową usterkę w programie Chrome                 |

## <a name="next-steps"></a>Następne kroki ##
- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)