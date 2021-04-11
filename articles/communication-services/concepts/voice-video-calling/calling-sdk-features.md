---
title: Omówienie zestawu SDK wywołań usługi Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Zawiera omówienie wywołania zestawu SDK.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: ac9cef77569dffe461f7711195c5638e831aa218
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110108"
---
# <a name="calling-sdk-overview"></a>Omówienie wywoływania zestawu SDK

Wywoływany zestaw SDK umożliwia urządzeniom użytkowników końcowych korzystanie z funkcji komunikacji głosowej i wideo. Ta strona zawiera szczegółowe opisy funkcji wywołujących, w tym informacje o pomocy technicznej platformy i przeglądarki. Aby od razu rozpocząć pracę, zobacz [wywoływanie przewodników szybki start](../../quickstarts/voice-video-calling/getting-started-with-calling.md) lub [wywoływanie przykładu Hero](../../samples/calling-hero-sample.md). 

Po rozpoczęciu tworzenia oprogramowania Sprawdź, czy na [stronie znane problemy](../known-issues.md) znajdują się błędy, nad którymi pracujemy.

Najważniejsze funkcje zestawu SDK wywołującego:

- **Adresowanie** — usługi Azure Communication Services zapewniają [tożsamości](../identity-model.md) ogólne, które są używane do adresowania punktów końcowych komunikacji. Klienci używają tych tożsamości do uwierzytelniania w usłudze i komunikują się ze sobą. Te tożsamości są używane przy wywoływaniu interfejsów API, które zapewniają klientom wgląd w osoby, która jest połączona z wywołaniem (spisem).
- **Szyfrowanie** — zestaw SDK wywołujący szyfruje ruch i uniemożliwia manipulowanie przewodem. 
- **Zarządzanie urządzeniami i nośniki** — WYWOŁYWANY zestaw SDK udostępnia funkcje do tworzenia powiązań z urządzeniami audio i wideo, kodowania zawartość pod kątem wydajnej transmisji przez dane komunikacyjne oraz renderuje zawartość do określonych urządzeń i widoków wyjściowych. Interfejsy API są również udostępniane na potrzeby udostępniania ekranu i aplikacji.
- **PSTN** — WYWOŁYWANY zestaw SDK może odbierać i inicjować połączenia głosowe z tradycyjnym, publicznie przełączanym systemem telefonii, [przy użyciu numerów telefonów, które są uzyskiwane w Azure Portal](../../quickstarts/telephony-sms/get-phone-number.md) lub programowo.
- **Spotkania zespołów** — zestaw SDK wywołujący może [dołączyć do spotkań zespołów](../../quickstarts/voice-video-calling/get-started-teams-interop.md) i współpracujących z zespołami. 
- **Powiadomienia** — zestaw SDK wywołujący udostępnia interfejsy API, które umożliwiają klientom otrzymywanie powiadomień o wywoływaniu przychodzącym. W sytuacjach, gdy aplikacja nie jest uruchomiona na pierwszym planie, wzorce są dostępne do [wyskakujących powiadomień](../notifications.md) ("wyskakujących") w celu informowania użytkowników końcowych o wywołaniu przychodzącym. 

## <a name="detailed-capabilities"></a>Szczegółowe możliwości 

Na poniższej liście przedstawiono zestaw funkcji, które są obecnie dostępne w ramach zestawów SDK wywołujących usługi Azure Communications Services.

| Grupa funkcji | Możliwość                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Podstawowe możliwości | Umieszczanie wywołania jeden-do-jednego między dwoma użytkownikami                                                                           | ✔️   | ✔️            | ✔️
|                   | Umieść połączenie grupy z więcej niż dwoma użytkownikami (do 350 użytkowników)                                                       | ✔️   | ✔️            | ✔️
|                   | Podnieś poziom wywołania "jeden do jednego" z dwoma użytkownikami do wywołania grupy z więcej niż dwoma użytkownikami                                 | ✔️   | ✔️            | ✔️
|                   | Dołącz do wywołania grupy po jego rozpoczęciu                                                                              | ✔️   | ✔️            | ✔️
|                   | Zapraszanie innego uczestnika VoIP do dołączenia do trwającego wywołania grupy                                                       | ✔️   | ✔️            | ✔️
|  Środkowy formant wywołania | Włącz/Wyłącz wideo                                                                                              | ✔️   | ✔️            | ✔️
|                   | Wycisz/Wyłącz mikrofon                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Przełączanie między kamerami                                                                                              | ✔️   | ✔️            | ✔️
|                   | Blokada lokalna/wstrzymanie                                                                                                  | ✔️   | ✔️            | ✔️
|                   | Aktywny głośnik                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Wybieranie osoby mówiącej dla wywołań                                                                                            | ✔️   | ✔️            | ✔️
|                   | Wybieranie mikrofonu dla wywołań                                                                                         | ✔️   | ✔️            | ✔️
|                   | Pokaż stan uczestnika<br/>*Bezczynne, wczesne nośniki, łączenie, połączone, wstrzymane, w poczekalni, rozłączone*         | ✔️   | ✔️            | ✔️
|                   | Pokaż stan wywołania<br/>*Wczesny nośnik, przychodzący, łączący, dzwonienie, połączenie, wstrzymanie, rozłączanie, Rozłączono* | ✔️   | ✔️            | ✔️
|                   | Pokaż, jeśli uczestnik jest wyciszony                                                                                      | ✔️   | ✔️            | ✔️
|                   | Pokaż powód, dla którego Uczestnik pozostawił wywołanie                                                                       | ✔️   | ✔️            | ✔️
| Udostępnianie ekranu    | Udostępnianie całego ekranu z poziomu aplikacji                                                                 | ✔️   | ❌            | ❌
|                   | Udostępnianie określonej aplikacji (z listy uruchomionych aplikacji)                                                | ✔️   | ❌            | ❌
|                   | Udostępnianie karty przeglądarki sieci Web z listy otwartych kart                                                                  | ✔️   | ❌            | ❌
|                   | Uczestnik może wyświetlić udział ekranu zdalnego                                                                            | ✔️   | ✔️            | ✔️
| Spis            | Wyświetl listę uczestników                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Usuwanie uczestnika                                                                                                | ✔️   | ✔️            | ✔️
| PSTN              | Umieszczanie wywołania "jeden do jednego" z uczestnikiem sieci PSTN                                                                     | ✔️   | ✔️            | ✔️
|                   | Umieść połączenie grupy z uczestnikami PSTN                                                                           | ✔️   | ✔️            | ✔️
|                   | Podwyższanie poziomu wywołania "jeden do jednego" uczestnika PSTN do wywołania grupy                                                 | ✔️   | ✔️            | ✔️
|                   | Wybieranie numeru z wywołania grupy jako uczestnika sieci PSTN                                                                    | ✔️   | ✔️            | ✔️
| Ogólne           | Testowanie mikrofonu, głośników i aparatu za pomocą usługi testowania audio (dostępnej przez wywołanie 8: echo123)                   | ✔️   | ✔️            | ✔️
| Zarządzanie urządzeniami | Poproszenie o zgodę na korzystanie z audio i/lub wideo                                                                       | ✔️   | ✔️            | ✔️
|                   | Pobierz listę kamer                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Ustaw kamerę                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Pobierz wybrany aparat                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Pobierz listę mikrofonu                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Ustaw mikrofon                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Pobierz wybrany mikrofon                                                                                             | ✔️   | ✔️            | ✔️
|                   | Pobierz listę głośników                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Ustaw prelegenta                                                                                                         | ✔️   | ✔️            | ✔️
|                   | Pobierz wybranego głośnika                                                                                                | ✔️   | ✔️            | ✔️
| Renderowanie wideo   | Renderuj pojedyncze wideo w wielu miejscach (lokalny aparat fotograficzny lub zdalny strumień)                                                  | ✔️   | ✔️            | ✔️
|                   | Ustawianie/aktualizowanie trybu skalowania                                                                                           | ✔️   | ✔️            | ✔️
|                   | Renderowanie zdalnego strumienia wideo                                                                                          | ✔️   | ✔️            | ✔️

## <a name="calling-sdk-streaming-support"></a>Wywoływanie obsługi przesyłania strumieniowego SDK
Zestaw SDK wywoływania usług komunikacyjnych obsługuje następujące konfiguracje przesyłania strumieniowego:

| Limit          |Internet | System Android/iOS|
|-----------|----|------------|
|**Liczba strumieni wychodzących, które mogą być wysyłane jednocześnie** |1 udostępnianie wideo lub 1 ekran | 1 wideo + 1 udostępnianie ekranu|
|**Liczba strumieni przychodzących, które mogą być renderowane jednocześnie** |1 udostępnianie wideo lub 1 ekran| 6 wideo + 1 udostępnianie ekranu |

## <a name="calling-sdk-timeouts"></a>Trwa wywoływanie limitów czasu zestawu SDK

Następujące limity czasu dotyczą usług komunikacyjnych wywołujących zestawy SDK:

| Akcja           | Limit czasu w sekundach |
| -------------- | ---------- |
| Uczestnik ponownego łączenia/usuwania | 120 |
| Dodawanie lub usuwanie nowego modalności wywołania (uruchamianie/zatrzymywanie udostępniania wideo lub ekranu) | 40 |
| Limit czasu operacji transferu wywołań | 60 |
| limit czasu ustanowienia wywołania 1:1 | 85 |
| Limit czasu ustanowienia połączenia z grupą | 85 |
| Limit czasu ustanowienia połączenia PSTN | 115 |
| Podnieś poziom 1:1 wywołania do grupy limit czasu wywołania | 115 |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>Obsługa zestawu SDK w języku JavaScript przez system operacyjny i przeglądarkę

Poniższa tabela przedstawia zestaw obsługiwanych przeglądarek, które są obecnie dostępne. Obsługujemy najnowsze trzy wersje przeglądarki, chyba że wskazano inaczej.

| Platforma                         | Chrome | Safari  | Krawędź (chrom) |
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS * * *                         |  ✔️    | ✔️**   | ❌             |
| Windows * * *                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

* Obsługiwane są wersje Safari 13.1 +, 1:1 wywołania nie są obsługiwane w przeglądarce Safari.

* * Przeglądarka Safari 14 +/macOS 11 + wymagana do obsługi wychodzącego wideo.

Udostępnianie ekranu wychodzącego jest obsługiwane tylko na platformach klasycznych (Windows, macOS i Linux), niezależnie od wersji przeglądarki i nie jest obsługiwane przez żadną platformę mobilną (Android, iOS, iPad i tablety).

Aplikacja dla systemu iOS w przeglądarce Safari nie może wyliczyć/wybrać urządzeń z mikrofonu i głośników (na przykład Bluetooth); jest to ograniczenie systemu operacyjnego i istnieje zawsze tylko jedno urządzenie.


## <a name="calling-client---browser-security-model"></a>Wywoływanie modelu zabezpieczeń przeglądarki klienta

### <a name="user-webrtc-over-https"></a>Protokołu WebRTC użytkownika za pośrednictwem protokołu HTTPS

Interfejsy API protokołu WebRTC, takie jak `getUserMedia` Wymagaj, że aplikacja, która wywołuje te interfejsy API, jest obsługiwana za pośrednictwem protokołu HTTPS.

W przypadku lokalnego programowania można użyć programu `http://localhost` .

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Osadzanie zestawu SDK wywoływania usług komunikacyjnych w elemencie iframe

Nowe [zasady uprawnień (nazywane również zasadami funkcji)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) są wprowadzane przez różne przeglądarki. Te zasady wpływają na wywołania scenariuszy poprzez kontrolowanie sposobu, w jaki aplikacje mogą uzyskiwać dostęp do aparatu i mikrofonu urządzenia za pomocą elementu iframe między źródłami.

Jeśli chcesz użyć elementu IFRAME do hostowania części aplikacji z innej domeny, musisz dodać `allow` atrybut z poprawną wartością do elementu iframe.

Na przykład ten element IFRAME umożliwia dostęp do aparatu i mikrofonu:

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wywoływania](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Aby uzyskać więcej informacji, zobacz następujące artykuły:
- Zapoznaj się z ogólnymi [przepływami wywołań](../call-flows.md)
- Informacje o [typach wywołań](../voice-video-calling/about-call-types.md)
- [Planowanie rozwiązania PSTN](../telephony-sms/plan-solution.md)
