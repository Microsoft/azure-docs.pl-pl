---
title: Omówienie biblioteki klienta rozmowy dla usług Azure Communications Services
titleSuffix: An Azure Communication Services concept document
description: Dowiedz się więcej o bibliotece klienta czatu dotyczącej usług Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b239cf6d253c1c2d2e36d213e92e0b218add3f8c
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94885992"
---
# <a name="chat-client-library-overview"></a>Omówienie biblioteki klienta czatu

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Za pomocą bibliotek klienckich czatu usługi Azure Communication Services można dodawać rozbudowane rozmowy w czasie rzeczywistym do aplikacji.

## <a name="chat-client-library-capabilities"></a>Możliwości w bibliotece klienta rozmowy

Na poniższej liście przedstawiono zestaw funkcji, które są obecnie dostępne w bibliotekach klienta rozmowy usług komunikacyjnych.

| Grupa funkcji | Możliwość                                                                                                          | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | --- | ----- | ---- | -----  |
| Podstawowe możliwości | Tworzenie wątku czatu między 2 lub większą liczbą użytkowników (do 250 użytkowników)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Aktualizowanie tematu wątku rozmowy                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |
|                   | Dodawanie lub usuwanie elementów członkowskich z wątku rozmowy                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Zdecyduj, czy Historia wiadomości czatu ma być udostępniana nowo dodanym członkom — *wszystko/brak/do określonego czasu* | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Pobierz listę wszystkich wątków członków rozmowy                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Usuwanie wątku rozmowy                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Pobierz listę członkostw wątku rozmowy użytkownika                                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Uzyskaj informacje dotyczące określonego wątku rozmowy                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Wysyłanie i odbieranie wiadomości w wątku rozmowy                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Edytuj zawartość wiadomości po jej wysłaniu                                                                   | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Usuwanie wiadomości                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Oznacz komunikat z priorytetem jako normalny lub wysoki w momencie wysłania                                               | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Wysyłanie i odbieranie potwierdzeń odczytania dla wiadomości, które zostały odczytane przez członków <br/> *Niedostępne, gdy w wątku rozmowy znajduje się więcej niż 20 elementów członkowskich*    | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Wysyłać i odbierać powiadomienia o wpisywaniu, gdy członek aktywnie pisze komunikat w wątku rozmowy <br/> *Niedostępne, gdy w wątku rozmowy znajduje się więcej niż 20 elementów członkowskich*      | ✔️   | ✔️   | ✔️    | ✔️    |
|                   | Pobierz wszystkie komunikaty w wątku rozmowy <br/> *Obsługiwane znaki emoji Unicode*                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Wyślij emojis jako część zawartości komunikatu                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|Sygnalizowanie w czasie rzeczywistym (włączone przez zastrzeżony pakiet sygnałów)| Otrzymuj powiadomienia, gdy użytkownik odbierze nową wiadomość w wątku rozmowy, do której należy.                                     | ✔️   | ❌    | ❌  | ❌  |
|                    | Otrzymuj powiadomienia, gdy komunikat był edytowany przez innego członka w wątku rozmowy, do którego należy                | ✔️   | ❌    | ❌    | ❌  |
|                    | Otrzymuj powiadomienia, gdy wiadomość została usunięta przez innego członka w wątku rozmowy, do której należy.                | ✔️   | ❌    | ❌    | ❌  |
|                    | Otrzymuj powiadomienia, gdy zostanie wpisany inny element członkowski wątku rozmowy                                                             | ✔️   | ❌    | ❌    | ❌  |
|                    | Otrzymuj powiadomienia, gdy inny element członkowski odczyta komunikat (potwierdzenie odczytu) w wątku rozmowy                               | ✔️   | ❌    | ❌    | ❌  |
| Zdarzenia             | Użyj Event Grid, aby subskrybować aktywność użytkowników w wątkach rozmowy i zintegrować niestandardowe usługi powiadomień lub logikę biznesową     | ✔️   | ✔️  | ✔️    | ✔️  |
| Monitorowanie        | Monitoruj użycie w odniesieniu do wysłanych komunikatów                                                                               | ✔️   | ✔️  | ✔️    | ✔️  |
|                    | Monitorowanie jakości i stanu żądań interfejsu API wykonywanych przez aplikację oraz Konfigurowanie alertów za pośrednictwem portalu                                                          | ✔️   | ✔️  | ✔️    | ✔️  |
|Dodatkowe funkcje | Użyj [interfejsy API usług Cognitive Services](../../../cognitive-services/index.yml) wraz z biblioteką klienta programu chat, aby włączyć funkcję inteligentnego *tłumaczenia języka & tonacji analizę komunikatu przychodzącego na kliencie, konwersji zamiany mowy na tekst w celu redagowania wiadomości w czasie, gdy członek mówi, itd.*                                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do rozmowy](../../quickstarts/chat/get-started.md)

Następujące dokumenty mogą być interesujące:

- Zapoznaj się z [pojęciami dotyczącymi rozmowy](../chat/concepts.md)