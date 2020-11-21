---
title: Asystenci głosu w systemie Windows — omówienie
titleSuffix: Azure Cognitive Services
description: Omówienie asystentów głosowych w systemie Windows, w tym możliwości i dostępnych zasobów programistycznych.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 9e8b009ecc2181edfaad5da3d8d05ad0c1909051
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024759"
---
# <a name="voice-assistants-on-windows"></a>Asystenci głosu w systemie Windows

W systemie Windows 10 w wersji 2004 i w górę aplikacje asystenta głosowego mogą korzystać z interfejsów API ConversationalAgent systemu Windows w celu uzyskania pełnego środowiska asystenta z obsługą głosu.

## <a name="voice-assistant-features"></a>Funkcje asystenta głosowego

Aplikacje agentów głosowych mogą być uaktywniane za pomocą słowa kluczowego mówiony, aby zapewnić bezpłatne środowisko pracy głosowej. Aktywacja głosowa działa po zamknięciu aplikacji i po zablokowaniu ekranu.

Ponadto system Windows udostępnia zestaw ustawień prywatności aktywacji głosu, które umożliwiają użytkownikom kontrolę aktywacji głosu i wyższą aktywację blokady dla poszczególnych aplikacji.

Po aktywacji głosu system Windows będzie zarządzać wieloma aktywnymi agentami prawidłowo i powiadamiać każdego asystenta głosowego, jeśli zostanie przerwany lub zdezaktywowany. Dzięki temu aplikacje mogą prawidłowo zarządzać przerwami i innymi zdarzeniami między agentami.

## <a name="how-does-voice-activation-work"></a>Jak działa Aktywacja głosowa?

Środowisko uruchomieniowe aktywacji agenta (AAR) to trwający proces w systemie Windows, który zarządza aktywacją aplikacji dla słowa kluczowego mówionego lub przycisku. Zaczyna się od systemu Windows, o ile w systemie istnieje co najmniej jedna aplikacja, która jest zarejestrowana w systemie. Aplikacje współpracują z usługą AAR za pomocą interfejsów API ConversationalAgent w Windows SDK.

Gdy użytkownik mówi słowa kluczowego, oprogramowanie lub Spotter słowa kluczowego sprzętowego w systemie powiadamia AAR o wykryciu słowa kluczowego, podając identyfikator słowa kluczowego. AAR z kolei wysyła żądanie do BackgroundService, aby uruchomić aplikację z odpowiednim IDENTYFIKATORem aplikacji.

### <a name="registration"></a>Rejestracja

Gdy aplikacja jest uruchamiana po raz pierwszy, zostanie ona zarejestrowana przy użyciu interfejsów API ConversationalAgent. AAR rejestruje wszystkie konfiguracje w mapowaniu globalnym za pomocą słowa kluczowego sprzętu lub oprogramowania Spotter w systemie, umożliwiając im wykrycie słowa kluczowego aplikacji. Aplikacja również [rejestruje się w usłudze w tle](/windows/uwp/launch-resume/register-a-background-task).

Należy zauważyć, że oznacza to, że aplikacja nie może być aktywowana przez głos, dopóki nie została uruchomiona jednokrotnie, a rejestracja mogła zostać ukończona.

### <a name="receiving-an-activation"></a>Otrzymywanie aktywacji

Po odebraniu żądania od AAR usługa w tle uruchamia aplikację. Aplikacja otrzymuje sygnał przez metodę cyklu życia OnBackgroundActivated w programie `App.xaml.cs` z unikatowym argumentem zdarzenia. Ten argument informuje aplikację, że została aktywowana przez AAR i że powinna rozpocząć weryfikację słowa kluczowego.

Jeśli aplikacja pomyślnie sprawdzi słowo kluczowe, może to spowodować, że żądanie będzie widoczne na pierwszym planie. Gdy to żądanie zakończy się pomyślnie, aplikacja wyświetli interfejs użytkownika i kontynuuje interakcję z użytkownikiem.

AAR nadal sygnalizuje aktywne aplikacje, gdy słowo kluczowe jest wymawiane. Zamiast sygnalizowania przez metodę cyklu życiowego w `App.xaml.cs` , chociaż sygnalizuje to zdarzenie w interfejsach API ConversationalAgent.

### <a name="keyword-verification"></a>Weryfikacja słowa kluczowego

Słowo kluczowe Spotter, które wyzwala aplikację do uruchomienia, osiągnęło niskie zużycie mocy przez uproszczenie modelu słowa kluczowego. Pozwala to Spotter słowa kluczowego na "zawsze włączone" bez wpływu na siłę mocy, ale oznacza to również, że słowo kluczowe Spotter będzie prawdopodobnie miało dużą liczbę "fałszywych zaakceptowanych", gdzie wykrywa słowo kluczowe, mimo że nie wykryto słowa kluczowego. Dlatego system aktywacji głosu uruchamia aplikację w tle: aby umożliwić aplikacji sprawdzenie, czy słowo kluczowe zostało wypowiadane przed przerwaniem bieżącej sesji użytkownika. AAR zapisuje dźwięk od kilku sekund przed usunięciem słowa kluczowego i udostępnienie go aplikacji. Aplikacja może używać tego do uruchamiania bardziej niezawodnego Spotter słowa kluczowego na tym samym dźwięku.

## <a name="next-steps"></a>Następne kroki

- **Zapoznaj się z zaleceniami dotyczącymi projektowania:** Nasze [wskazówki dotyczące projektowania](windows-voice-assistants-best-practices.md) określają, że kluczowe działania są wymagane, aby zapewnić najlepsze możliwe środowisko aktywacji głosu w systemie Windows 10.
- **Odwiedź stronę wprowadzenie:** Zacznij [tutaj](how-to-windows-voice-assistants-get-started.md) , aby zapoznać się z instrukcjami dotyczącymi rozpoczęcia wdrażania asystentów głosowych w systemie Windows, od ustawienia środowiska deweloperskiego w Przewodnik po wprowadzeniu do implementacji.
- **Wypróbuj przykładową aplikację**: Aby korzystać z tych możliwości ręki, odwiedź stronę [przykładowej asystenta głosowego platformy UWP](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) i postępuj zgodnie z instrukcjami, aby uruchomić przykładowego klienta.