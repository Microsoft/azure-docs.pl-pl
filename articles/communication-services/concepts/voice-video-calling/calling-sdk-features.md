---
title: Azure Communication Services wywoływania zestawu SDK — omówienie
titleSuffix: An Azure Communication Services concept document
description: Zawiera omówienie zestawu SDK wywoływania.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 28072184d47beb32dc03e0d6ba52328bfceb5b73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364876"
---
# <a name="calling-sdk-overview"></a>Omówienie wywoływania zestawu SDK

Zestaw SDK wywoływania umożliwia użytkownikom końcowych korzystanie z komunikacji głosowej i wideo. Ta strona zawiera szczegółowe opisy funkcji wywoływania, w tym informacje o pomocy technicznej dotyczące platformy i przeglądarki. Aby od razu rozpocząć pracę, zobacz Calling quickstarts (Wywoływanie [przewodników Szybki start)](../../quickstarts/voice-video-calling/getting-started-with-calling.md) lub [Calling hero sample (Wywoływanie przykładu hero).](../../samples/calling-hero-sample.md) 

Po zakończeniu tworzenia aplikacji zapoznaj [](../known-issues.md) się ze stroną znanych problemów, aby znaleźć usterki, nad których pracujemy.

Najważniejsze funkcje wywołującego zestawu SDK:

- **Adresowanie** — Azure Communication Services [ogólne tożsamości,](../identity-model.md) które są używane do adresowania punktów końcowych komunikacji. Klienci używają tych tożsamości do uwierzytelniania w usłudze i komunikowania się ze sobą. Te tożsamości są używane w interfejsach API wywoływania, które zapewniają klientom wgląd w to, kto jest połączony z wywołaniem (lista).
- **Szyfrowanie** — wywołujący zestaw SDK szyfruje ruch i uniemożliwia manipulowanie siecią. 
- **Zarządzanie urządzeniami** i multimedia — zestaw SDK wywoływania udostępnia funkcje powiązania z urządzeniami audio i wideo, koduje zawartość w celu wydajnej transmisji przez plan danych komunikacji i renderuje zawartość do urządzeń wyjściowych i widoków, które określisz. Dostępne są również interfejsy API do udostępniania ekranu i aplikacji.
- **PSTN** — zestaw SDK wywołujący może odbierać i inicjować połączenia głosowe za pomocą tradycyjnego systemu telefonii przełączone publicznie [przy](../../quickstarts/telephony-sms/get-phone-number.md) użyciu numerów telefonów, które można uzyskać w Azure Portal lub programowo.
- **Spotkania z zespołami** — zestaw SDK wywołujący może [dołączać do spotkań w](../../quickstarts/voice-video-calling/get-started-teams-interop.md) aplikacji Teams i wchodzić w interakcje z planem danych głosu i wideo usługi Teams. 
- **Powiadomienia** — zestaw SDK wywoływania udostępnia interfejsy API umożliwiające klientom powiadomienie o przychodzącym wywołaniu. W sytuacjach, gdy aplikacja nie jest uruchomiona na [](../notifications.md) pierwszym planie, dostępne są wzorce do uruchamiania wyskakujących powiadomień ("wyskakujących") w celu informowania użytkowników końcowych o przychodzącym wywołaniu. 

## <a name="detailed-capabilities"></a>Szczegółowe możliwości 

Na poniższej liście przedstawiono zestaw funkcji, które są obecnie dostępne w Azure Communication Services zestawów SDK.

| Grupa funkcji | Możliwość                                                                                                          | Js  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Podstawowe możliwości | Umieść wywołanie "jeden do jednego" między dwoma użytkownikami                                                                           | ✔️   | ✔️            | ✔️
|                   | Umieść połączenie grupowe z więcej niż dwoma użytkownikami (do 350 użytkowników)                                                       | ✔️   | ✔️            | ✔️
|                   | Podwyższanie jakości połączenia "jeden do jednego" z dwoma użytkownikami do wywołania grupy z więcej niż dwoma użytkownikami                                 | ✔️   | ✔️            | ✔️
|                   | Dołączanie do wywołania grupy po jego zakończeniu                                                                              | ✔️   | ✔️            | ✔️
|                   | Zaproś innego uczestnika VoIP do dołączenia do trwającego połączenia grupowego                                                       | ✔️   | ✔️            | ✔️
|  Kontrolka wywołania średniego | Włączanie/wyłączanie wideo                                                                                              | ✔️   | ✔️            | ✔️
|                   | Wyciszenie/wyciszenie mikrofonu                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Przełączanie między aparatami                                                                                              | ✔️   | ✔️            | ✔️
|                   | Lokalne wstrzymanie/wstrzymanie                                                                                                  | ✔️   | ✔️            | ✔️
|                   | Aktywna prelegentka                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Wybieranie prelegenta do połączeń                                                                                            | ✔️   | ✔️            | ✔️
|                   | Wybieranie mikrofonu do połączeń                                                                                         | ✔️   | ✔️            | ✔️
|                   | Wyświetlanie stanu uczestnika<br/>*Bezczynny, Wczesne nośniki, Łączenie, Połączone, Wstrzymanie, Włączona, Włączona, Rozłączona*         | ✔️   | ✔️            | ✔️
|                   | Wyświetlanie stanu wywołania<br/>*Early Media, Incoming, Connecting, Ringing, Connected, Hold, Disconnecting, Disconnected* | ✔️   | ✔️            | ✔️
|                   | Pokaż, czy uczestnik jest wyciszone                                                                                      | ✔️   | ✔️            | ✔️
|                   | Wyświetlanie przyczyny, dla których uczestnik opuścił wywołanie                                                                       | ✔️   | ✔️            | ✔️
| Udostępnianie ekranu    | Udostępnianie całego ekranu z poziomu aplikacji                                                                 | ✔️   | ❌            | ❌
|                   | Udostępnianie określonej aplikacji (z listy uruchomionych aplikacji)                                                | ✔️   | ❌            | ❌
|                   | Udostępnianie karty przeglądarki internetowej z listy otwartych kart                                                                  | ✔️   | ❌            | ❌
|                   | Uczestnik może wyświetlać udział ekranu zdalnego                                                                            | ✔️   | ✔️            | ✔️
| Planu            | Lista uczestników                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Usuwanie uczestnika                                                                                                | ✔️   | ✔️            | ✔️
| Pstn              | Umieść wywołanie jeden do jednego z uczestnikiem PSTN                                                                     | ✔️   | ✔️            | ✔️
|                   | Umieść połączenie grupowe z uczestnikami PSTN                                                                           | ✔️   | ✔️            | ✔️
|                   | Poddaj wywołaniu jeden do jednego z uczestnikiem PSTN do wywołania grupy                                                 | ✔️   | ✔️            | ✔️
|                   | Wybieranie z wywołania grupy jako uczestnik PSTN                                                                    | ✔️   | ✔️            | ✔️
| Ogólne           | Testowanie mikrofonu, prelegenta i aparatu za pomocą usługi testowania dźwięku (dostępnej przez wywołanie 8:echo123)                   | ✔️   | ✔️            | ✔️
| Zarządzanie urządzeniami | Poproś o zgodę na używanie dźwięku i/lub wideo                                                                       | ✔️   | ✔️            | ✔️
|                   | Uzyskiwanie listy aparatów                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Ustawianie aparatu fotograficznego                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Pobierz wybraną kamerę                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Uzyskiwanie listy mikrofonów                                                                                                 | ✔️   | ❌           |❌  
|                   | Ustawianie mikrofonu                                                                                                      | ✔️   | ❌           | ❌  
|                   | Uzyskiwanie wybranego mikrofonu                                                                                             | ✔️   | ❌           | ❌  
|                   | Uzyskiwanie listy osób mówiących                                                                                                   | ✔️   | ❌           | ❌  
|                   | Ustawianie prelegenta                                                                                                         | ✔️   | ❌           | ❌  
|                   | Pobierz wybraną prelegentę                                                                                                | ✔️   | ❌           | ❌  
| Renderowanie wideo   | Renderowanie pojedynczego wideo w wielu miejscach (kamera lokalna lub strumień zdalny)                                                  | ✔️   | ✔️            | ✔️
|                   | Ustawianie/aktualizowanie trybu skalowania                                                                                           | ✔️   | ✔️            | ✔️
|                   | Renderowanie zdalnego strumienia wideo                                                                                          | ✔️   | ✔️            | ✔️

## <a name="calling-sdk-streaming-support"></a>Obsługa przesyłania strumieniowego zestawu SDK wywoływania
Zestaw SDK Communication Services obsługuje następujące konfiguracje przesyłania strumieniowego:

| Limit          |Internet | Android/iOS|
|-----------|----|------------|
|**Liczba strumieni wychodzących, które mogą być wysyłane jednocześnie** |Udostępnianie 1 wideo lub 1 ekranu | 1 wideo + 1 udostępnianie ekranu|
|**Liczba strumieni przychodzących, które mogą być renderowane jednocześnie** |Udostępnianie 1 wideo lub 1 ekranu| 6 wideo i 1 udostępnianie ekranu |

## <a name="calling-sdk-timeouts"></a>Wywoływanie limitów czasu zestawu SDK

Następujące limity czasu dotyczą Communication Services zestawów SDK:

| Akcja           | Limit czasu w sekundach |
| -------------- | ---------- |
| Uczestnik ponownego nawiązywania połączenia/usuwania | 120 |
| Dodawanie lub usuwanie nowej niechętności z wywołania (uruchamianie/zatrzymywanie wideo lub udostępnianie ekranu) | 40 |
| Limit czasu operacji transferu wywołań | 60 |
| Limit czasu ustanowienia wywołania 1:1 | 85 |
| Limit czasu ustanowienia wywołań grupowych | 85 |
| Limit czasu ustanowienia wywołań PSTN | 115 |
| Podniesienie limitu czasu wywołania 1:1 do grupy | 115 |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>Obsługa zestawu SDK wywoływania języka JavaScript przez system operacyjny i przeglądarkę

W poniższej tabeli przedstawiono zestaw obsługiwanych przeglądarek, które są obecnie dostępne. Obsługujemy trzy najnowsze wersje przeglądarki, chyba że określono inaczej.

| Platforma                         | Chrome | Safari*  | Edge (Chromium) |
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS**                         |  ✔️    | ✔️**   | ❌             |
| Windows**                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

*Obsługiwane są przeglądarki Safari w wersji 13.1 lub starszej, połączenia 1:1 nie są obsługiwane w przeglądarce Safari.

**Przeglądarka Safari 14+/macOS 11+ jest potrzebna do obsługi wychodzących plików wideo.

Udostępnianie ekranu wychodzącego jest obsługiwane tylko na platformach komputerowych (Windows, macOS i Linux), niezależnie od wersji przeglądarki, i nie jest obsługiwane na żadnej platformie mobilnej (Android, iOS, iPad i tablety).

Aplikacja systemu iOS w przeglądarce Safari nie może wyliczać/wybierać urządzeń mikrofonu i osoby mówiącej (na przykład Bluetooth); Jest to ograniczenie systemu operacyjnego i zawsze istnieje tylko jedno urządzenie.


## <a name="calling-client---browser-security-model"></a>Klient wywołujący — model zabezpieczeń przeglądarki

### <a name="user-webrtc-over-https"></a>Protokół WebRTC użytkownika za pośrednictwem protokołu HTTPS

Interfejsy API WebRTC, takie jak , wymagają, aby aplikacja, która wywołuje `getUserMedia` te interfejsy API, była obsługiwane za pośrednictwem protokołu HTTPS.

W przypadku tworzenia aplikacji lokalnych można użyć funkcji `http://localhost` .

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Osadzanie zestawu COMMUNICATION SERVICES SDK w ramce iframe

Nowe zasady [uprawnień (nazywane również zasadami funkcji)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) są adoptowany przez różne przeglądarki. Te zasady wpływają na scenariusze wywoływania, kontrolując sposób, w jaki aplikacje mogą uzyskać dostęp do aparatu i mikrofonu urządzenia za pośrednictwem elementu iframe między źródłami.

Jeśli chcesz hostować część aplikacji z innej domeny przy użyciu ramki iframe, musisz dodać atrybut z poprawną wartością `allow` do ramki iframe.

Na przykład ten ramek iframe umożliwia dostęp zarówno do aparatu, jak i mikrofonu:

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wywoływania](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Aby uzyskać więcej informacji, zobacz następujące artykuły:
- Zapoznaj się z ogólnymi [przepływami połączeń](../call-flows.md)
- Dowiedz się więcej o [typach wywołań](../voice-video-calling/about-call-types.md)
- [Planowanie rozwiązania PSTN](../telephony-sms/plan-solution.md)
